---
title: M15 Trend Confluence — Hướng dẫn Implement MQL5
type: query
created: 2026-04-30
updated: 2026-04-30
tags: [trading-strategy, mql5, bot, algorithm, price-action, multi-timeframe, confluence, scalping]
sources: [trading/strategies/m15-trend-confluence-idea.md, trading/strategies/trinity-confluence-mql5.md]
confidence: high
---

# M15 Trend Confluence — Hướng dẫn Implement MQL5

> File này dành cho lập trình viên MQL5. Đọc `m15-trend-confluence-idea.md` trước để hiểu ý tưởng.

## Kiến trúc Bot

```
M15TrendConfluenceBot.mq5
│
├── OnTick()
│   └── CheckNewBar_M15()      ← Chỉ xử lý khi nến M15 mới
│       └── EvaluateSetup()
│           ├── TrendFilter_H4()      ← MA200 + HH/HL trên H4
│           ├── TrendConfirm_H1()     ← Cùng hướng H4 + MA20 H1
│           ├── FindSRZone_H4_D1()    ← Swing points H4/D1
│           ├── DetectPATrigger_M15() ← Engulfing / Pin Bar / IB BO
│           ├── CheckRR()             ← R:R >= 1:2 bắt buộc
│           ├── DailyLossCheck()      ← 5% limit (+ skip cho tk nhỏ)
│           └── ExecuteTrade()
│
├── ManagePositions()     ← Trailing, partial close at 2R
│
├── DailyReset()          ← Reset daily P&L lúc 00:00
│
├── Helpers/
│   ├── FindSwingHigh/Low()
│   ├── GetATR()
│   ├── IsRoundNumber()
│   ├── IsTradingSession()
│   └── CountTouches()
```

## 1. THAM SỐ INPUT (ĐẦU VÀO)

```cpp
//+------------------------------------------------------------------+
//|                                  M15 Trend Confluence Bot        |
//+------------------------------------------------------------------+
sinput group "=== M15 Trend Confluence Bot ==="

// --- Trend Filter (H4 + H1) ---
sinput group "--- Trend Filter H4 ---"
input int      InpMAPeriod_H4      = 200;    // MA Period H4
input ENUM_MA_METHOD InpMAMethod   = MODE_SMA;
input ENUM_APPLIED_PRICE InpMAPrice= PRICE_CLOSE;
input int      InpStructureLookback= 50;     // HH/HL lookback H4 (nến)

sinput group "--- Trend Confirm H1 ---"
input int      InpMAFast_H1        = 20;     // MA20 H1 momentum filter
input double   InpMinATRRatio      = 0.5;    // ATR(H1)/ATR(H4) tối thiểu

// --- S/R Zones (H4 + D1) ---
sinput group "--- S/R Zones ---"
input int      InpSRLookback_H4    = 60;     // Lookback nến H4
input int      InpSRLookback_D1    = 30;     // Lookback nến D1
input int      InpSRMinTouches     = 2;      // Số lần chạm tối thiểu
input double   InpSRZonePct        = 0.3;    // Zone width (% ATR H4)
input double   InpSRMaxDistATR     = 0.5;    // Khoảng cách tối đa từ giá đến zone (ATR H4)

// --- PA Trigger (M15) ---
sinput group "--- PA Trigger M15 ---"
input double   InpPinBarBodyRatio  = 0.3;    // Pin Bar: body/total max
input double   InpPinBarWickRatio  = 0.6;    // Pin Bar: wick/total min
input double   InpEngulfBodyRatio  = 0.5;    // Engulfing: body/range min
input int      InpIBMotherLookback = 5;      // Inside Bar: lookback tìm nến mẹ

// --- Risk Management ---
sinput group "--- Risk Management ---"
input double   InpRiskPercent      = 1.0;    // % rủi ro mỗi lệnh
input double   InpMinRR            = 2.0;    // R:R tối thiểu (bắt buộc)
input double   InpTP1Multiplier    = 2.0;    // TP1 = risk * 2
input double   InpTP1ClosePct      = 50.0;   // % đóng tại TP1
input double   InpDailyLossPct     = 5.0;    // % loss tối đa mỗi ngày
input double   InpMinBalanceForDD  = 200.0;  // Số dư tối thiểu để áp daily limit
input int      InpMaxConsecLoss    = 2;      // Số lệnh thua liên tiếp → giảm risk
input double   InpReducedRiskPct   = 0.5;    // Risk giảm khi thua liên tiếp
input int      InpMaxConsecStop    = 4;      // Dừng hẳn sau N lệnh thua

// --- Filters ---
sinput group "--- Filters ---"
input int      InpMaxSpread        = 30;     // Spread tối đa (points)
input bool     InpUseSessionFilter = true;   // Lọc phiên giao dịch
input bool     InpRequireTrendH4   = true;   // Bắt buộc trend H4
input bool     InpRequireSR        = true;   // Bắt buộc S/R zone

// --- Identification ---
sinput group "--- Identification ---"
input int      InpMagicNumber      = 20260430;// Magic number
input string   InpComment          = "M15TF";// Order comment
```

## 2. GLOBAL VARIABLES

```cpp
// Daily loss tracking
double   g_dailyStartBalance  = 0;
double   g_dailyRealizedPnL   = 0;
datetime g_lastDailyReset     = 0;

// Consecutive loss tracking
int      g_consecutiveLosses  = 0;

// Last H4 bar time (for trend recalculation)
datetime g_lastH4Bar          = 0;

// Cached trend result
struct TrendResult {
    bool   isBullish;
    bool   isBearish;
    bool   isValid;
    double maValue;
    int    structureScore;
};
TrendResult g_cachedTrendH4;
```

## 3. ONTICK — MAIN LOGIC

```cpp
//+------------------------------------------------------------------+
//| Expert tick function                                             |
//+------------------------------------------------------------------+
void OnTick()
{
    // --- Daily reset ---
    DailyReset();

    // --- Cập nhật trend H4 khi có nến H4 mới (cache để tránh tính lại mỗi tick) ---
    datetime currentH4 = iTime(_Symbol, PERIOD_H4, 0);
    if (currentH4 != g_lastH4Bar)
    {
        g_lastH4Bar = currentH4;
        g_cachedTrendH4 = GetTrendH4();
    }

    // --- Chỉ xử lý khi có nến M15 mới ---
    static datetime lastM15Bar = 0;
    datetime currentM15Bar = iTime(_Symbol, PERIOD_M15, 0);
    if (currentM15Bar == lastM15Bar) return;
    lastM15Bar = currentM15Bar;

    // --- Đánh giá setup ---
    EvaluateSetup();

    // --- Quản lý vị thế đang mở ---
    ManagePositions();
}
```

## 4. DAILY RESET & LOSS LIMIT

```cpp
//+------------------------------------------------------------------+
//| Reset daily tracking at midnight server time                     |
//+------------------------------------------------------------------+
void DailyReset()
{
    MqlDateTime dt;
    TimeCurrent(dt);

    // Reset khi ngày mới (dựa trên server time)
    datetime todayStart = StringToTime(
        StringFormat("%04d.%02d.%02d 00:00", dt.year, dt.mon, dt.day));

    if (todayStart != g_lastDailyReset)
    {
        g_lastDailyReset = todayStart;
        g_dailyStartBalance = AccountInfoDouble(ACCOUNT_BALANCE);
        g_dailyRealizedPnL = 0;
    }
}

//+------------------------------------------------------------------+
//| Check daily loss limit (5% rule)                                 |
//+------------------------------------------------------------------+
bool IsDailyLossLimitHit()
{
    double balance = AccountInfoDouble(ACCOUNT_BALANCE);

    // Miễn áp dụng nếu số dư quá nhỏ
    if (balance < InpMinBalanceForDD)
        return false;

    // Tính % loss: dùng realized loss đã tracking + unrealized của lệnh đang mở
    double maxLossMoney = g_dailyStartBalance * (InpDailyLossPct / 100.0);

    // Nếu realized loss đã vượt limit → stop
    if (-g_dailyRealizedPnL >= maxLossMoney)
        return true;

    return false;
}

//+------------------------------------------------------------------+
//| Ghi nhận realized P&L khi lệnh đóng                              |
//+------------------------------------------------------------------+
void OnTradeClose(double profit)
{
    g_dailyRealizedPnL += profit;
}
```

## 5. TREND FILTER H4

```cpp
//+------------------------------------------------------------------+
//| Layer 1: Trend Filter H4                                         |
//+------------------------------------------------------------------+
TrendResult GetTrendH4()
{
    TrendResult r = {};

    // --- MA200 H4 ---
    double ma200 = iMA(_Symbol, PERIOD_H4, InpMAPeriod_H4, 0,
                        InpMAMethod, InpMAPrice, 1);
    double close = iClose(_Symbol, PERIOD_H4, 1);

    r.maValue = ma200;
    r.isBullish = (close > ma200);
    r.isBearish = (close < ma200);
    r.isValid = (r.isBullish || r.isBearish);

    if (!r.isValid) return r;

    // --- HH/HL Structure H4 ---
    double swingHigh1 = FindSwingHigh(PERIOD_H4, 3, InpStructureLookback);
    double swingHigh2 = FindSwingHigh(PERIOD_H4, 3 + InpStructureLookback,
                                       InpStructureLookback);
    double swingLow1  = FindSwingLow(PERIOD_H4, 3, InpStructureLookback);
    double swingLow2  = FindSwingLow(PERIOD_H4, 3 + InpStructureLookback,
                                      InpStructureLookback);

    if (swingHigh1 > 0 && swingHigh2 > 0 && swingLow1 > 0 && swingLow2 > 0)
    {
        bool hhHigher = (swingHigh1 > swingHigh2);
        bool hlHigher = (swingLow1 > swingLow2);

        if (r.isBullish)
        {
            if (hhHigher && hlHigher)      r.structureScore = 2;
            else if (hhHigher || hlHigher) r.structureScore = 1;
            else                           r.structureScore = 0;
        }
        else // Bearish
        {
            bool lhLower = (swingHigh1 < swingHigh2);
            bool llLower = (swingLow1 < swingLow2);

            if (lhLower && llLower)        r.structureScore = 2;
            else if (lhLower || llLower)    r.structureScore = 1;
            else                           r.structureScore = 0;
        }
    }

    return r;
}
```

## 6. TREND CONFIRMATION H1

```cpp
//+------------------------------------------------------------------+
//| Layer 1b: Trend Confirmation H1                                  |
//+------------------------------------------------------------------+
struct H1ConfirmResult {
    bool   sameDirection;    // H1 cùng hướng H4
    bool   aboveMA20;        // Giá trên MA20 (uptrend) / dưới MA20 (downtrend)
    bool   marketAlive;      // ATR H1 đủ lớn so với ATR H4
    double atrH1;
    double atrH4;
};

H1ConfirmResult GetH1Confirmation(bool h4Bullish)
{
    H1ConfirmResult r = {};

    double closeH1 = iClose(_Symbol, PERIOD_H1, 1);
    double openH1  = iOpen(_Symbol, PERIOD_H1, 1);

    // H1 direction: so sánh close vs open
    bool h1Bullish = (closeH1 > openH1);

    // Cùng hướng H4?
    if (h4Bullish)
        r.sameDirection = h1Bullish;
    else
        r.sameDirection = !h1Bullish;

    // MA20 H1 filter
    double ma20H1 = iMA(_Symbol, PERIOD_H1, InpMAFast_H1, 0,
                        MODE_SMA, PRICE_CLOSE, 1);

    if (h4Bullish)
        r.aboveMA20 = (closeH1 > ma20H1);
    else
        r.aboveMA20 = (closeH1 < ma20H1);

    // Market alive check: ATR(H1) / ATR(H4) >= threshold
    r.atrH1 = GetATR(PERIOD_H1, 14);
    r.atrH4 = GetATR(PERIOD_H4, 14);
    if (r.atrH4 > 0)
        r.marketAlive = (r.atrH1 / r.atrH4 >= InpMinATRRatio);

    return r;
}
```

## 7. S/R ZONES (H4 + D1)

```cpp
//+------------------------------------------------------------------+
//| Layer 2: S/R Zones from H4 and D1                                |
//+------------------------------------------------------------------+
struct SRZone {
    double price;
    double upper;
    double lower;
    int    touches;
    bool   isResistance;
    bool   valid;
    string source;  // "H4" or "D1"
};

SRZone FindNearestZone(double currentPrice, bool findResistance)
{
    SRZone bestZone = {};
    double bestDist = 1e10;
    double atrH4 = GetATR(PERIOD_H4, 14);
    double zoneWidth = atrH4 * InpSRZonePct;

    // --- Tìm trên H4 (ưu tiên) ---
    for (int i = 3; i < InpSRLookback_H4; i++)
    {
        double val = 0;
        bool isSwing = false;

        if (findResistance)
        {
            double h0 = iHigh(_Symbol, PERIOD_H4, i);
            double h1 = iHigh(_Symbol, PERIOD_H4, i-1);
            double h2 = iHigh(_Symbol, PERIOD_H4, i+1);
            // Swing high: đỉnh cao hơn 2 nến kế bên
            if (h0 >= h1 && h0 >= h2) { isSwing = true; val = h0; }
        }
        else
        {
            double l0 = iLow(_Symbol, PERIOD_H4, i);
            double l1 = iLow(_Symbol, PERIOD_H4, i-1);
            double l2 = iLow(_Symbol, PERIOD_H4, i+1);
            if (l0 <= l1 && l0 <= l2) { isSwing = true; val = l0; }
        }

        if (!isSwing) continue;

        int touches = CountTouches(val, zoneWidth, PERIOD_H4, InpSRLookback_H4);
        if (touches < InpSRMinTouches) continue;

        double dist = MathAbs(currentPrice - val);
        if (dist < bestDist)
        {
            bestDist = dist;
            bestZone.price = val;
            bestZone.upper = val + zoneWidth;
            bestZone.lower = val - zoneWidth;
            bestZone.touches = touches;
            bestZone.isResistance = findResistance;
            bestZone.valid = true;
            bestZone.source = "H4";
        }
    }

    // --- Tìm trên D1 (bổ sung, không ưu tiên bằng H4) ---
    for (int i = 3; i < InpSRLookback_D1; i++)
    {
        double val = 0;
        bool isSwing = false;

        if (findResistance)
        {
            double h0 = iHigh(_Symbol, PERIOD_D1, i);
            double h1 = iHigh(_Symbol, PERIOD_D1, i-1);
            double h2 = iHigh(_Symbol, PERIOD_D1, i+1);
            if (h0 >= h1 && h0 >= h2) { isSwing = true; val = h0; }
        }
        else
        {
            double l0 = iLow(_Symbol, PERIOD_D1, i);
            double l1 = iLow(_Symbol, PERIOD_D1, i-1);
            double l2 = iLow(_Symbol, PERIOD_D1, i+1);
            if (l0 <= l1 && l0 <= l2) { isSwing = true; val = l0; }
        }

        if (!isSwing) continue;

        double dist = MathAbs(currentPrice - val);
        // D1 zone chỉ thay thế H4 nếu H4 không tìm thấy, hoặc D1 gần hơn đáng kể
        if (dist < bestDist || !bestZone.valid)
        {
            int touches = 1; // D1 ít nến hơn, mỗi swing = 1 touch mặc định
            bestDist = dist;
            bestZone.price = val;
            bestZone.upper = val + zoneWidth;
            bestZone.lower = val - zoneWidth;
            bestZone.touches = touches;
            bestZone.isResistance = findResistance;
            bestZone.valid = true;
            bestZone.source = "D1";
        }
    }

    return bestZone;
}

//+------------------------------------------------------------------+
bool IsPriceNearZone(double price, const SRZone &zone, double atr)
{
    if (!zone.valid) return false;
    double maxDist = atr * InpSRMaxDistATR;
    return (MathAbs(price - zone.price) <= maxDist);
}

//+------------------------------------------------------------------+
int CountTouches(double levelPrice, double zoneWidth,
                 ENUM_TIMEFRAMES tf, int lookback)
{
    int count = 0;
    for (int i = 3; i < lookback; i++)
    {
        double low  = iLow(_Symbol, tf, i);
        double high = iHigh(_Symbol, tf, i);
        if (high >= (levelPrice - zoneWidth) &&
            low <= (levelPrice + zoneWidth))
            count++;
    }
    return count;
}
```

## 8. PA TRIGGER M15

```cpp
//+------------------------------------------------------------------+
//| Layer 3: PA Trigger M15                                          |
//+------------------------------------------------------------------+

//--- Bullish Engulfing M15 ---
bool IsBullishEngulfing_M15()
{
    double open1  = iOpen(_Symbol, PERIOD_M15, 1);
    double close1 = iClose(_Symbol, PERIOD_M15, 1);
    double high1  = iHigh(_Symbol, PERIOD_M15, 1);
    double low1   = iLow(_Symbol, PERIOD_M15, 1);

    double open2  = iOpen(_Symbol, PERIOD_M15, 2);
    double close2 = iClose(_Symbol, PERIOD_M15, 2);
    double high2  = iHigh(_Symbol, PERIOD_M15, 2);
    double low2   = iLow(_Symbol, PERIOD_M15, 2);

    // Nến 2: đỏ (giảm), Nến 1: xanh (tăng)
    if (close2 >= open2 || close1 <= open1) return false;

    double range1 = high1 - low1;
    double body1  = close1 - open1;

    // Body nến engulfing phải >= 50% range (không phải doji giả)
    if (range1 > 0 && (body1 / range1) < InpEngulfBodyRatio) return false;

    // Full engulf: body1 bao phủ body2
    bool bodyEngulf = (open1 <= close2 && close1 >= open2);

    // Full engulf: high/low cũng phải bao phủ
    bool fullEngulf = (low1 <= low2 && high1 >= high2);

    return bodyEngulf && fullEngulf;
}

//--- Bearish Engulfing M15 ---
bool IsBearishEngulfing_M15()
{
    double open1  = iOpen(_Symbol, PERIOD_M15, 1);
    double close1 = iClose(_Symbol, PERIOD_M15, 1);
    double high1  = iHigh(_Symbol, PERIOD_M15, 1);
    double low1   = iLow(_Symbol, PERIOD_M15, 1);

    double open2  = iOpen(_Symbol, PERIOD_M15, 2);
    double close2 = iClose(_Symbol, PERIOD_M15, 2);
    double high2  = iHigh(_Symbol, PERIOD_M15, 2);
    double low2   = iLow(_Symbol, PERIOD_M15, 2);

    // Nến 2: xanh (tăng), Nến 1: đỏ (giảm)
    if (close2 <= open2 || close1 >= open1) return false;

    double range1 = high1 - low1;
    double body1  = open1 - close1;

    if (range1 > 0 && (body1 / range1) < InpEngulfBodyRatio) return false;

    bool bodyEngulf = (open1 >= close2 && close1 <= open2);
    bool fullEngulf = (low1 <= low2 && high1 >= high2);

    return bodyEngulf && fullEngulf;
}

//--- Bullish Pin Bar M15 (Hammer-like) ---
bool IsBullishPinBar_M15()
{
    double open  = iOpen(_Symbol, PERIOD_M15, 1);
    double close = iClose(_Symbol, PERIOD_M15, 1);
    double high  = iHigh(_Symbol, PERIOD_M15, 1);
    double low   = iLow(_Symbol, PERIOD_M15, 1);

    double body   = MathAbs(close - open);
    double range  = high - low;
    double lowerWick = MathMin(open, close) - low;
    double upperWick = high - MathMax(open, close);

    if (range == 0) return false;
    if (close <= open) return false; // Nến phải tăng (xanh)

    // Râu dưới dài, thân nhỏ, râu trên ngắn
    return (lowerWick / range >= InpPinBarWickRatio &&
            body / range <= InpPinBarBodyRatio &&
            upperWick <= lowerWick * 0.3);
}

//--- Bearish Pin Bar M15 (Shooting Star) ---
bool IsBearishPinBar_M15()
{
    double open  = iOpen(_Symbol, PERIOD_M15, 1);
    double close = iClose(_Symbol, PERIOD_M15, 1);
    double high  = iHigh(_Symbol, PERIOD_M15, 1);
    double low   = iLow(_Symbol, PERIOD_M15, 1);

    double body   = MathAbs(close - open);
    double range  = high - low;
    double lowerWick = MathMin(open, close) - low;
    double upperWick = high - MathMax(open, close);

    if (range == 0) return false;
    if (close >= open) return false; // Nến phải giảm (đỏ)

    return (upperWick / range >= InpPinBarWickRatio &&
            body / range <= InpPinBarBodyRatio &&
            lowerWick <= upperWick * 0.3);
}

//--- Inside Bar Breakout M15 ---
bool IsBullishIBBreakout_M15()
{
    // Inside bar: nến M15 nằm trong range nến mẹ (nến trước đó)
    double high1 = iHigh(_Symbol, PERIOD_M15, 1);  // Nến mẹ
    double low1  = iLow(_Symbol, PERIOD_M15, 1);
    double high2 = iHigh(_Symbol, PERIOD_M15, 2);  // Inside bar
    double low2  = iLow(_Symbol, PERIOD_M15, 2);

    // Nến 2 nằm trong nến 1
    if (!(high2 <= high1 && low2 >= low1)) return false;

    // Nến hiện tại (0) breakout lên trên high của inside bar
    double currentClose = iClose(_Symbol, PERIOD_M15, 0);
    double currentHigh  = iHigh(_Symbol, PERIOD_M15, 0);

    return (currentClose > high2 && currentHigh > high2);
}

bool IsBearishIBBreakout_M15()
{
    double high1 = iHigh(_Symbol, PERIOD_M15, 1);
    double low1  = iLow(_Symbol, PERIOD_M15, 1);
    double high2 = iHigh(_Symbol, PERIOD_M15, 2);
    double low2  = iLow(_Symbol, PERIOD_M15, 2);

    if (!(high2 <= high1 && low2 >= low1)) return false;

    double currentClose = iClose(_Symbol, PERIOD_M15, 0);
    double currentLow   = iLow(_Symbol, PERIOD_M15, 0);

    return (currentClose < low2 && currentLow < low2);
}

//--- Momentum Candle M15 (Shaved Bar) ---
bool IsBullishMomentum_M15()
{
    double open  = iOpen(_Symbol, PERIOD_M15, 1);
    double close = iClose(_Symbol, PERIOD_M15, 1);
    double high  = iHigh(_Symbol, PERIOD_M15, 1);
    double low   = iLow(_Symbol, PERIOD_M15, 1);

    if (close <= open) return false; // Phải là nến tăng

    double range = high - low;
    double body  = close - open;

    if (range == 0) return false;

    // Body >= 80% range → gần như không có râu
    return (body / range >= 0.8);
}

bool IsBearishMomentum_M15()
{
    double open  = iOpen(_Symbol, PERIOD_M15, 1);
    double close = iClose(_Symbol, PERIOD_M15, 1);
    double high  = iHigh(_Symbol, PERIOD_M15, 1);
    double low   = iLow(_Symbol, PERIOD_M15, 1);

    if (close >= open) return false;

    double range = high - low;
    double body  = open - close;

    if (range == 0) return false;

    return (body / range >= 0.8);
}
```

## 9. EVALUATE SETUP (Toàn bộ logic)

```cpp
//+------------------------------------------------------------------+
//| Main setup evaluation                                            |
//+------------------------------------------------------------------+
struct SetupResult {
    bool   shouldEnter;
    int    orderType;      // ORDER_TYPE_BUY / ORDER_TYPE_SELL
    double entryPrice;
    double stopLoss;
    double takeProfit1;
    double takeProfit2;
    double lotSize;
    int    score;
    string reason;
};

SetupResult EvaluateSetup()
{
    SetupResult result = {};

    // --- Pre-trade filters ---
    if (!PreTradeFilters())
    {
        result.reason = "Pre-trade filters failed";
        return result;
    }

    double ask = SymbolInfoDouble(_Symbol, SYMBOL_ASK);
    double bid = SymbolInfoDouble(_Symbol, SYMBOL_BID);
    double atrH4 = GetATR(PERIOD_H4, 14);

    // --- Layer 1: Trend H4 ---
    TrendResult trend = g_cachedTrendH4;
    if (!trend.isValid && InpRequireTrendH4)
    {
        result.reason = "Trend H4: no clear direction";
        return result;
    }

    // --- Layer 1b: H1 Confirmation ---
    H1ConfirmResult h1conf = GetH1Confirmation(trend.isBullish);
    if (!h1conf.sameDirection || !h1conf.aboveMA20)
    {
        result.reason = "H1 confirmation: not aligned with H4";
        return result;
    }
    if (!h1conf.marketAlive)
    {
        result.reason = "Market dead: ATR(H1) too small vs ATR(H4)";
        return result;
    }

    // --- Layer 2: S/R Zones ---
    double price = trend.isBullish ? bid : ask;
    SRZone zone = FindNearestZone(price, !trend.isBullish);
    // Bullish → tìm support (near bid); Bearish → tìm resistance (near ask)

    bool nearZone = IsPriceNearZone(price, zone, atrH4);
    if (!nearZone && InpRequireSR)
    {
        result.reason = "S/R filter: not near any zone";
        return result;
    }

    // --- Layer 3: PA M15 ---
    bool bullishPA = IsBullishEngulfing_M15() || IsBullishPinBar_M15()
                  || IsBullishIBBreakout_M15() || IsBullishMomentum_M15();
    bool bearishPA = IsBearishEngulfing_M15() || IsBearishPinBar_M15()
                  || IsBearishIBBreakout_M15() || IsBearishMomentum_M15();

    if (!bullishPA && !bearishPA)
    {
        result.reason = "PA M15: no valid signal";
        return result;
    }

    // --- Determine direction ---
    int direction = 0;
    if (trend.isBullish && nearZone && bullishPA)
        direction = 1;  // BUY
    else if (trend.isBearish && nearZone && bearishPA)
        direction = -1; // SELL

    if (direction == 0)
    {
        result.reason = "Direction mismatch: layers conflict";
        return result;
    }

    // --- Calculate Confluence Score ---
    int score = 0;
    if (trend.isBullish || trend.isBearish) score += 2; // Trend H4
    score += trend.structureScore;                       // HH/HL
    if (h1conf.sameDirection && h1conf.aboveMA20) score += 1; // H1 confirm
    if (nearZone)                                 score += 1; // S/R
    if (bullishPA || bearishPA)                   score += 1; // PA M15

    int minScore = 4; // Tối thiểu 4/7
    if (score < minScore)
    {
        result.reason = StringFormat("Score too low: %d/%d", score, minScore);
        return result;
    }

    // --- Calculate SL/TP with R:R >= 1:2 ---
    double sl, entryPrice;
    if (direction == 1)
    {
        entryPrice = ask;

        // SL: dưới swing low M15 gần nhất + buffer
        double swingLowM15 = FindSwingLow(PERIOD_M15, 3, 30);
        if (swingLowM15 > 0)
            sl = swingLowM15 - (atrH4 * 0.1);
        else
            sl = bid - (atrH4 * 0.5); // Fallback

        // Tính risk
        double risk = entryPrice - sl;
        if (risk <= 0) { result.reason = "Invalid SL distance"; return result; }

        // TP1 = 2R, TP2 = 3R (TRÊN SWING HIGH H4 TIẾP THEO NẾU CÓ)
        double tp1 = entryPrice + (risk * InpTP1Multiplier);
        double tp2 = entryPrice + (risk * InpTP1Multiplier * 1.5);

        // Giới hạn TP2 không vượt quá resistance tiếp theo
        SRZone nextRes = FindNearestZone(tp2, true);
        if (nextRes.valid && nextRes.price < tp2)
            tp2 = nextRes.price;

        result.stopLoss    = sl;
        result.takeProfit1 = tp1;
        result.takeProfit2 = tp2;
    }
    else
    {
        entryPrice = bid;

        double swingHighM15 = FindSwingHigh(PERIOD_M15, 3, 30);
        if (swingHighM15 > 0)
            sl = swingHighM15 + (atrH4 * 0.1);
        else
            sl = ask + (atrH4 * 0.5);

        double risk = sl - entryPrice;
        if (risk <= 0) { result.reason = "Invalid SL distance"; return result; }

        double tp1 = entryPrice - (risk * InpTP1Multiplier);
        double tp2 = entryPrice - (risk * InpTP1Multiplier * 1.5);

        SRZone nextSup = FindNearestZone(tp2, false);
        if (nextSup.valid && nextSup.price > tp2)
            tp2 = nextSup.price;

        result.stopLoss    = sl;
        result.takeProfit1 = tp1;
        result.takeProfit2 = tp2;
    }

    // --- R:R check (bắt buộc) ---
    double riskDist  = MathAbs(entryPrice - sl);
    double rewardDist = MathAbs(result.takeProfit1 - entryPrice);
    double rr = (riskDist > 0) ? rewardDist / riskDist : 0;

    if (rr < InpMinRR)
    {
        result.reason = StringFormat("R:R %.2f < %.1f minimum", rr, InpMinRR);
        return result;
    }

    // --- Daily loss limit ---
    if (IsDailyLossLimitHit())
    {
        result.reason = StringFormat("Daily loss limit %.1f%% hit", InpDailyLossPct);
        return result;
    }

    // --- Consecutive loss check ---
    if (g_consecutiveLosses >= InpMaxConsecStop)
    {
        result.reason = StringFormat("Stopped: %d consecutive losses", g_consecutiveLosses);
        return result;
    }

    // --- Calculate lot size (adjusted for consecutive losses) ---
    double riskPct = InpRiskPercent;
    if (g_consecutiveLosses >= InpMaxConsecLoss)
        riskPct = InpReducedRiskPct;

    SLDistanceToPoints:
    double slPoints = MathAbs(entryPrice - sl) / _Point;
    double lotSize = CalculateLotSize(slPoints, riskPct);

    // --- Fill result ---
    result.shouldEnter = true;
    result.orderType   = (direction == 1) ? ORDER_TYPE_BUY : ORDER_TYPE_SELL;
    result.entryPrice  = entryPrice;
    result.lotSize     = lotSize;
    result.score       = score;
    result.reason      = StringFormat("Score %d/7: %s | R:R %.1f",
                                       score,
                                       (direction == 1) ? "BUY" : "SELL",
                                       rr);

    return result;
}

//+------------------------------------------------------------------+
bool PreTradeFilters()
{
    // Spread check
    long spread = SymbolInfoInteger(_Symbol, SYMBOL_SPREAD);
    if (spread > InpMaxSpread) return false;

    // Session filter
    if (InpUseSessionFilter && !IsTradingSession()) return false;

    // Already has position on this symbol?
    if (PositionSelect(_Symbol)) return false;

    return true;
}
```

## 10. EXECUTE TRADE

```cpp
//+------------------------------------------------------------------+
//| Execute trade with partial TP                                   |
//+------------------------------------------------------------------+
void ExecuteTrade(SetupResult &setup)
{
    if (!setup.shouldEnter) return;

    MqlTradeRequest request = {};
    MqlTradeResult result = {};

    request.action    = TRADE_ACTION_DEAL;
    request.symbol    = _Symbol;
    request.volume    = setup.lotSize;
    request.type      = setup.orderType;
    request.price     = setup.entryPrice;
    request.sl        = setup.stopLoss;
    request.tp        = setup.takeProfit1;  // TP1 ở mức order
    request.deviation = 30;                 // Cho phép slippage trên M15
    request.magic     = InpMagicNumber;
    request.comment   = InpComment + "_" + IntegerToString(setup.score);

    if (OrderSend(request, result))
    {
        PrintFormat("[M15 TF] %s | Score %d | Lot %.2f | SL %.5f | TP1 %.5f | R:R >= 2",
                     (setup.orderType == ORDER_TYPE_BUY) ? "BUY" : "SELL",
                     setup.score, setup.lotSize, setup.stopLoss, setup.takeProfit1);
    }
    else
    {
        Print("Order failed: retcode=", result.retcode, " error=", GetLastError());
    }
}
```

## 11. POSITION MANAGEMENT

```cpp
//+------------------------------------------------------------------+
//| Manage positions: partial close, trailing, consecutive loss      |
//+------------------------------------------------------------------+
void ManagePositions()
{
    if (!PositionSelect(_Symbol)) return;

    ulong ticket = PositionGetTicket(0);
    if (ticket == 0) return;

    double openPrice  = PositionGetDouble(POSITION_PRICE_OPEN);
    double currentSL  = PositionGetDouble(POSITION_SL);
    double volume     = PositionGetDouble(POSITION_VOLUME);
    int    type       = (int)PositionGetInteger(POSITION_TYPE);

    double ask = SymbolInfoDouble(_Symbol, SYMBOL_ASK);
    double bid = SymbolInfoDouble(_Symbol, SYMBOL_BID);

    double risk = (type == POSITION_TYPE_BUY)
                  ? (openPrice - currentSL)
                  : (currentSL - openPrice);

    if (risk <= 0) return;

    double currentDist = (type == POSITION_TYPE_BUY)
                         ? (bid - openPrice)
                         : (openPrice - ask);

    double rr = currentDist / risk;

    // --- 1R: Move SL to breakeven ---
    if (rr >= 1.0)
    {
        double beSL = openPrice;
        if ((type == POSITION_TYPE_BUY && currentSL < beSL) ||
            (type == POSITION_TYPE_SELL && currentSL > beSL))
        {
            MoveSL(ticket, beSL);
        }
    }

    // --- 2R (TP1): Close 50% + move SL to 1R ---
    if (rr >= InpTP1Multiplier)
    {
        // Kiểm tra đã partial close chưa (bằng cách kiểm tra volume)
        double initialVol = PositionGetDouble(POSITION_VOLUME);
        // NOTE: Cần lưu initial volume khi mở lệnh, ở đây đơn giản hóa

        // Partial close 50%
        double closeVol = NormalizeDouble(initialVol * (InpTP1ClosePct / 100.0), 2);
        if (closeVol > 0 && closeVol < initialVol)
        {
            MqlTradeRequest req = {};
            MqlTradeResult  res = {};
            req.action    = TRADE_ACTION_DEAL;
            req.symbol    = _Symbol;
            req.volume    = closeVol;
            req.type      = (type == POSITION_TYPE_BUY)
                            ? ORDER_TYPE_SELL : ORDER_TYPE_BUY;
            req.price     = (type == POSITION_TYPE_BUY) ? bid : ask;
            req.deviation = 30;
            req.magic     = InpMagicNumber;
            req.position  = ticket;
            req.comment   = InpComment + "_TP1";

            if (OrderSend(req, res))
            {
                // Move SL to 1R để lock profit
                double lockSL = (type == POSITION_TYPE_BUY)
                                ? (openPrice + risk) : (openPrice - risk);
                MoveSL(ticket, lockSL);

                PrintFormat("[M15 TF] TP1 hit: closed %.2f lots, SL moved to 1R lock",
                            closeVol);
            }
        }
    }

    // --- 3R+: Trailing stop 1R behind ---
    if (rr >= InpTP1Multiplier + 1.0)
    {
        double trailDist = risk * 1.0; // Trailing = 1R behind current price
        double newSL = (type == POSITION_TYPE_BUY)
                       ? (bid - trailDist)
                       : (ask + trailDist);

        if ((type == POSITION_TYPE_BUY && newSL > currentSL) ||
            (type == POSITION_TYPE_SELL && newSL < currentSL))
        {
            MoveSL(ticket, newSL);
        }
    }
}

//+------------------------------------------------------------------+
void MoveSL(ulong ticket, double newSL)
{
    MqlTradeRequest req = {};
    MqlTradeResult  res = {};
    req.action   = TRADE_ACTION_SLTP;
    req.symbol   = _Symbol;
    req.sl       = NormalizeDouble(newSL, (int)SymbolInfoInteger(_Symbol, SYMBOL_DIGITS));
    req.tp       = PositionGetDouble(POSITION_TP);
    req.magic    = InpMagicNumber;
    req.position = ticket;
    OrderSend(req, res);
}
```

## 12. ONTRADE — TRACK LOSSES

```cpp
//+------------------------------------------------------------------+
//| Trade transaction event                                          |
//+------------------------------------------------------------------+
void OnTrade()
{
    // Lấy lịch sử giao dịch gần nhất
    HistorySelect(TimeCurrent() - 86400, TimeCurrent());

    for (int i = HistoryDealsTotal() - 1; i >= 0; i--)
    {
        ulong ticket = HistoryDealGetTicket(i);
        if (ticket == 0) continue;

        long magic = HistoryDealGetInteger(ticket, DEAL_MAGIC);
        if (magic != InpMagicNumber) continue;

        long entry = HistoryDealGetInteger(ticket, DEAL_ENTRY);
        if (entry == DEAL_ENTRY_OUT || entry == DEAL_ENTRY_OUT_BY)
        {
            double profit = HistoryDealGetDouble(ticket, DEAL_PROFIT);
            double commission = HistoryDealGetDouble(ticket, DEAL_COMMISSION);
            double swap = HistoryDealGetDouble(ticket, DEAL_SWAP);
            double netProfit = profit + commission + swap;

            // Ghi nhận vào daily P&L
            g_dailyRealizedPnL += netProfit;

            // Track consecutive losses
            if (netProfit < 0)
                g_consecutiveLosses++;
            else
                g_consecutiveLosses = 0;

            PrintFormat("[M15 TF] Trade closed: P&L=%.2f | Daily P&L=%.2f | Consec losses=%d",
                        netProfit, g_dailyRealizedPnL, g_consecutiveLosses);
        }
    }
}
```

## 13. HELPER FUNCTIONS

```cpp
//+------------------------------------------------------------------+
//| Swing High / Low                                                 |
//+------------------------------------------------------------------+
double FindSwingHigh(ENUM_TIMEFRAMES tf, int startBar, int lookback)
{
    for (int i = startBar; i < startBar + lookback; i++)
    {
        double h0 = iHigh(_Symbol, tf, i);
        double h1 = iHigh(_Symbol, tf, i-1);
        double h2 = iHigh(_Symbol, tf, i+1);
        if (h0 >= h1 && h0 >= h2)
            return h0;
    }
    return 0;
}

double FindSwingLow(ENUM_TIMEFRAMES tf, int startBar, int lookback)
{
    for (int i = startBar; i < startBar + lookback; i++)
    {
        double l0 = iLow(_Symbol, tf, i);
        double l1 = iLow(_Symbol, tf, i-1);
        double l2 = iLow(_Symbol, tf, i+1);
        if (l0 <= l1 && l0 <= l2)
            return l0;
    }
    return 0;
}

//+------------------------------------------------------------------+
double GetATR(ENUM_TIMEFRAMES tf, int period)
{
    double atr[];
    ArraySetAsSeries(atr, true);
    if (CopyATR(_Symbol, tf, period, 0, 1, atr) > 0)
        return atr[0];
    return 0;
}

//+------------------------------------------------------------------+
bool IsRoundNumber(double price)
{
    double normalized = price / _Point;
    long lastTwo = (long)normalized % 100;
    return (lastTwo == 0 || lastTwo == 50);
}

//+------------------------------------------------------------------+
bool IsTradingSession()
{
    MqlDateTime dt;
    TimeCurrent(dt);

    int hour = dt.hour;
    int day  = dt.day_of_week;

    if (day == 6 || day == 0) return false;

    // London 8-17 GMT, NY 13-22 GMT
    // Chỉ trade LDN+NY overlap 13-17 và LDN/NY riêng
    return (hour >= 8 && hour < 22);
}

//+------------------------------------------------------------------+
double CalculateLotSize(double slPoints, double riskPercent)
{
    double balance = AccountInfoDouble(ACCOUNT_BALANCE);
    double riskMoney = balance * (riskPercent / 100.0);

    double tickValue = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_VALUE);
    double tickSize  = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_SIZE);
    double lotStep   = SymbolInfoDouble(_Symbol, SYMBOL_VOLUME_STEP);
    double minLot    = SymbolInfoDouble(_Symbol, SYMBOL_VOLUME_MIN);
    double maxLot    = SymbolInfoDouble(_Symbol, SYMBOL_VOLUME_MAX);

    if (tickValue == 0 || slPoints == 0 || tickSize == 0) return minLot;

    double slValue = slPoints * tickValue / tickSize;
    double lots = riskMoney / slValue;

    lots = MathFloor(lots / lotStep) * lotStep;
    lots = MathMax(minLot, MathMin(lots, maxLot));

    return NormalizeDouble(lots, 2);
}
```

## 14. BACKTEST CONFIG

### Cặp tiền khuyến nghị

| TT | Symbol | Lý do |
|----|--------|-------|
| 1 | EURUSD | Spread thấp, PA rõ trên M15, thanh khoản tốt |
| 2 | GBPUSD | Biến động mạnh, M15 có nhiều setup |
| 3 | USDJPY | Xu hướng H4 rõ, ít fakeout M15 |
| 4 | AUDUSD | Ổn định, PA chất lượng |

### Timeframe cho Backtest

```
- Period: M15 (entry timeframe)
- Model: Every tick (based on real ticks)
- Duration: 1-2 năm
- Spread: Current hoặc 10-15 cho EURUSD
```

### Parameters cần optimize (Genetic)

```
- InpPinBarBodyRatio:   [0.2, 0.25, 0.3, 0.35]
- InpPinBarWickRatio:   [0.55, 0.6, 0.65]
- InpEngulfBodyRatio:   [0.4, 0.5, 0.6]
- InpTP1Multiplier:     [1.5, 2.0, 2.5]  ← Chỉ optimize nếu muốn R:R > 1:2
- InpSRMinTouches:      [2, 3]
- InpStructureLookback:  [30, 40, 50]
- InpMinScore:          [4, 5]
```

### Metrics mục tiêu

| Metric | Target | Threshold |
|--------|--------|-----------|
| Win Rate | 50-55% | <40% = cần điều chỉnh |
| Profit Factor | >1.5 | <1.2 = không đủ edge |
| Max Drawdown | <15% | >25% = rủi ro quá cao |
| Sharpe Ratio | >1.0 | <0.5 = không ổn định |
| Avg RR (realized) | >1:1.5 | <1:1 = SL/TP không hợp lý |
| Trades/month | 15-30 | <8 = quá ít, >60 = over-trading |
| Max Consec Loss | <5 | >8 = cần filter thêm |

## 15. SO SÁNH CODE VỚI TRINITY GỐC

| Thành phần | Trinity gốc | M15 Cải tiến |
|-----------|------------|-------------|
| Trend filter | D1 (MA200 + HH/HL) | H4 (MA200 + HH/HL) + H1 confirm |
| Entry timeframe | H1 | M15 |
| PA patterns | Engulfing, Pin Bar (H1) | Engulfing, Pin Bar, IB BO, Momentum (M15) |
| S/R sources | D1 swing points | H4 (ưu tiên) + D1 (bổ sung) |
| R:R check | Không bắt buộc | `InpMinRR = 2.0` cứng |
| Daily loss limit | Không có | 5% + miễn cho tk nhỏ |
| Consecutive loss | Không có | Stop sau 4 lệnh thua |
| SL placement | H1 swing + S/R zone | M15 swing + buffer ATR(H4) |
| Position mgmt | 1R BE, 2R partial, 3R trail | Giống nhưng chặt hơn cho M15 |

## XEM THÊM

- [[m15-trend-confluence-idea]] — Ý tưởng giao dịch và triết lý
- [[trinity-confluence-idea]] — Trinity gốc (D1+H1)
- [[trinity-confluence-mql5]] — Code Trinity gốc để tham khảo
- [[multi-timeframe-forex]] — Lý thuyết đa khung thời gian
- [[trend-following]] — Trend following framework
- [[price-action]] — Tín hiệu Price Action
- [[support-resistance-zones]] — S/R zones
- [[candlestick-patterns]] — Mô hình nến
- [[pullback-retracement]] — Pullback entries
