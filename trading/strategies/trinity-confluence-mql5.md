---
title: Trinity Confluence - Hướng dẫn Implement MQL5
type: query
created: 2026-04-30
updated: 2026-04-30
tags: [trading-strategy, mql5, bot, algorithm, price-action, multi-timeframe, confluence]
sources: [trading/strategies/trinity-confluence-idea.md]
confidence: high
---

# Trinity Confluence — Hướng dẫn Implement MQL5

> File này dành cho lập trình viên MQL5. Đọc `trinity-confluence-idea.md` trước để hiểu ý tưởng.

## Kiến trúc Bot

```
TrinityConfluenceBot.mq5
│
├── OnTick()                    ← Gọi mỗi tick mới
│   └── CheckNewBar_H1()        ← Chỉ xử lý khi nến H1 mới
│       └── EvaluateSetup()
│           ├── TrendFilter_D1()
│           ├── FindSRZone() 
│           ├── DetectPATrigger_H1()
│           │   ├── IsBullishEngulfing()
│           │   ├── IsBearishEngulfing()
│           │   ├── IsBullishPinBar()
│           │   └── IsBearishPinBar()
│           ├── ConfluenceScore()
│           └── ExecuteTrade()
│
├── ManagePositions()           ← Trailing, partial close
│
├── Helpers/
│   ├── FindSwingHigh/Low()
│   ├── GetATR()
│   ├── IsRoundNumber()
│   ├── IsTradingSession()
│   └── IsNewsEvent()
```

## 1. THAM SỐ INPUT (ĐẦU VÀO)

```cpp
//+------------------------------------------------------------------+
//|                                               Input parameters  |
//+------------------------------------------------------------------+
sinput group "=== Trinity Confluence Bot ==="

// --- Trend Filter (D1) ---
sinput group "--- Trend Filter D1 ---"
input int      InpMAPeriod        = 200;     // MA Period (trend filter)
input ENUM_MA_METHOD InpMAMethod  = MODE_SMA;// MA Method
input ENUM_APPLIED_PRICE InpMAPrice= PRICE_CLOSE; // Applied price
input int      InpStructureLookback = 30;    // HH/HL lookback (nến D1)

// --- S/R Zones (D1 + H4) ---
sinput group "--- S/R Zones ---"
input int      InpSRLookback      = 100;    // S/R lookback (nến D1)
input int      InpSRMinTouches    = 2;      // Số lần chạm tối thiểu
input double   InpSRZonePct       = 0.3;    // Độ rộng zone (% ATR D1)
input int      InpSRDistanceATR   = 5;      // Khoảng cách tối đa từ giá đến zone (số ATR/10)

// --- PA Trigger (H1) ---
sinput group "--- PA Trigger H1 ---"
input double   InpPinBarBodyRatio = 0.3;    // Pin Bar: body/total <= ratio
input double   InpPinBarWickRatio = 0.6;    // Pin Bar: wick/total >= ratio
input bool     InpEngulfBodyOnly  = true;   // Engulfing: chỉ cần nuốt body
input int      InpPALookback      = 1;      // Số nến H1 để check tín hiệu

// --- Confluence Scoring ---
sinput group "--- Confluence ---"
input int      InpMinScore        = 3;      // Điểm tối thiểu để vào lệnh

// --- Risk Management ---
sinput group "--- Risk Management ---"
input double   InpRiskPercent     = 1.0;    // % rủi ro mỗi lệnh
input double   InpTP1Multiplier   = 2.0;    // TP1 = risk * multiplier
input double   InpTP2Multiplier   = 3.0;    // TP2 = risk * multiplier
input double   InpTP1ClosePct     = 50.0;   // % position đóng tại TP1

// --- Filters ---
sinput group "--- Filters ---"
input int      InpMaxSpread       = 30;     // Spread tối đa (points)
input bool     InpRequireTrend    = true;   // Bắt buộc trend filter
input bool     InpRequireSR       = true;   // Bắt buộc S/R zone
input bool     InpRequirePA       = true;   // Bắt buộc PA signal
input bool     InpUseSessionFilter= true;   // Lọc phiên giao dịch
input bool     InpUseNewsFilter   = true;   // Lọc tin tức

// --- Magic Number ---
sinput group "--- Identification ---"
input int      InpMagicNumber     = 20260430;// Magic number
input string   InpComment         = "Trinity";// Order comment
```

## 2. ONTICK — MAIN LOGIC

```cpp
//+------------------------------------------------------------------+
//| Expert tick function                                             |
//+------------------------------------------------------------------+
void OnTick()
{
    // Chỉ xử lý đầu nến H1 mới
    static datetime lastBar = 0;
    datetime currentBar = iTime(_Symbol, PERIOD_H1, 0);
    
    if (currentBar == lastBar)
        return;
    lastBar = currentBar;
    
    // Kiểm tra Magic Number — tránh interfere với bot khác
    if (!IsNewBar()) return;
    
    // Evaluate setup
    EvaluateSetup();
    
    // Quản lý position đang mở
    ManagePositions();
}

//+------------------------------------------------------------------+
bool IsNewBar()
{
    static datetime lastTime = 0;
    datetime time = iTime(_Symbol, PERIOD_H1, 0);
    if (time != lastTime) {
        lastTime = time;
        return true;
    }
    return false;
}
```

## 3. TREND FILTER (D1)

```cpp
//+------------------------------------------------------------------+
//| Layer 1: Trend Filter D1                                         |
//+------------------------------------------------------------------+
struct TrendResult {
    bool   isBullish;       // Chỉ mua
    bool   isBearish;       // Chỉ bán
    bool   isValid;         // Có xu hướng rõ ràng
    double maValue;         // MA200 value
    int    structureScore;  // 0-2: HH/HL strength
};

TrendResult GetTrendD1()
{
    TrendResult r = {};
    
    // --- MA200 Filter ---
    double ma200 = iMA(_Symbol, PERIOD_D1, InpMAPeriod, 0, InpMAMethod, InpMAPrice, 1);
    double close = iClose(_Symbol, PERIOD_D1, 1);
    
    r.maValue = ma200;
    r.isBullish = (close > ma200);
    r.isBearish = (close < ma200);
    r.isValid = true;
    
    // --- HH/HL Structure Check ---
    // Tìm 2 swing high và 2 swing low gần nhất
    double swingHigh1 = FindSwingHigh(PERIOD_D1, 10, InpStructureLookback);
    double swingHigh2 = FindSwingHigh(PERIOD_D1, 10 + InpStructureLookback, 
                                       InpStructureLookback * 2);
    double swingLow1  = FindSwingLow(PERIOD_D1, 10, InpStructureLookback);
    double swingLow2  = FindSwingLow(PERIOD_D1, 10 + InpStructureLookback,
                                      InpStructureLookback * 2);
    
    if (swingHigh1 > 0 && swingHigh2 > 0 && swingLow1 > 0 && swingLow2 > 0)
    {
        bool hhHigher = (swingHigh1 > swingHigh2);  // HH thật
        bool hlHigher = (swingLow1 > swingLow2);     // HL thật
        
        if (hhHigher && hlHigher) r.structureScore = 2;      // Uptrend confirmed
        else if (hhHigher || hlHigher) r.structureScore = 1; // Uptrend yếu
        else r.structureScore = 0;                            // Không phải uptrend
        
        // Kết hợp với bearish check
        bool lhLower = (swingHigh1 < swingHigh2);
        bool llLower = (swingLow1 < swingLow2);
        
        if (lhLower && llLower && r.isBearish) r.structureScore = 2; // Downtrend
    }
    
    return r;
}
```

## 4. S/R ZONES

```cpp
//+------------------------------------------------------------------+
//| Layer 2: S/R Zones from D1                                      |
//+------------------------------------------------------------------+
struct SRZone {
    double price;     // Giá center của zone
    double upper;     // Biên trên
    double lower;     // Biên dưới
    int    touches;   // Số lần chạm
    bool   isResistance; // true=resistance, false=support
    bool   valid;     // Zone có đủ touches?
};

SRZone FindSRZone(double currentPrice, bool findResistance)
{
    SRZone zone = {};
    double atr = GetATR(PERIOD_D1, 14);
    double zoneWidth = atr * InpSRZonePct;
    
    double bestScore = 1e10;
    int bestIdx = -1;
    
    // Duyệt swing highs (resistance) hoặc swing lows (support) trên D1
    for (int i = 2; i < InpSRLookback; i++)
    {
        double val = 0;
        bool isSwing = false;
        
        if (findResistance)
        {
            // Swing high: nến i cao hơn nến i-1 và i+1
            double h0 = iHigh(_Symbol, PERIOD_D1, i);
            double h1 = iHigh(_Symbol, PERIOD_D1, i-1);
            double h2 = iHigh(_Symbol, PERIOD_D1, i+1);
            isSwing = (h0 >= h1 && h0 >= h2);
            val = h0;
        }
        else
        {
            double l0 = iLow(_Symbol, PERIOD_D1, i);
            double l1 = iLow(_Symbol, PERIOD_D1, i-1);
            double l2 = iLow(_Symbol, PERIOD_D1, i+1);
            isSwing = (l0 <= l1 && l0 <= l2);
            val = l0;
        }
        
        if (!isSwing) continue;
        
        // Đếm touches
        int touches = CountTouches(val, zoneWidth, PERIOD_D1, InpSRLookback);
        if (touches < InpSRMinTouches) continue;
        
        // Tính khoảng cách từ giá hiện tại → zone
        double dist = MathAbs(currentPrice - val);
        if (dist < bestScore)
        {
            bestScore = dist;
            bestIdx = i;
            zone.price = val;
            zone.upper = val + zoneWidth;
            zone.lower = val - zoneWidth;
            zone.touches = touches;
            zone.isResistance = findResistance;
            zone.valid = true;
        }
    }
    
    return zone;
}

//+------------------------------------------------------------------+
int CountTouches(double levelPrice, double zoneWidth, 
                 ENUM_TIMEFRAMES tf, int lookback)
{
    int count = 0;
    for (int i = 2; i < lookback; i++)
    {
        double low  = iLow(_Symbol, tf, i);
        double high = iHigh(_Symbol, tf, i);
        
        // Giá chạm zone nếu high >= zone.lower && low <= zone.upper
        if (high >= (levelPrice - zoneWidth) && low <= (levelPrice + zoneWidth))
            count++;
    }
    return count;
}

//+------------------------------------------------------------------+
//| Kiểm tra giá có trong vùng S/R không?                            |
//+------------------------------------------------------------------+
bool IsPriceNearSR(double price, SRZone &zone, double atr)
{
    if (!zone.valid) return false;
    double maxDist = atr * InpSRDistanceATR / 10.0;
    return (MathAbs(price - zone.price) <= maxDist);
}
```

## 5. PA TRIGGER (H1)

```cpp
//+------------------------------------------------------------------+
//| Layer 3: PA Trigger H1                                          |
//+------------------------------------------------------------------+

//--- Bullish Engulfing ---
bool IsBullishEngulfing()
{
    double open1  = iOpen(_Symbol, PERIOD_H1, 1);
    double close1 = iClose(_Symbol, PERIOD_H1, 1);
    double open2  = iOpen(_Symbol, PERIOD_H1, 2);
    double close2 = iClose(_Symbol, PERIOD_H1, 2);
    
    // Nến 2: đỏ (giảm), Nến 1: xanh (tăng)
    if (close2 >= open2 || close1 <= open1) return false;
    
    if (InpEngulfBodyOnly)
    {
        // Body nến 1 bao phủ body nến 2
        return (open1 <= close2 && close1 >= open2);
    }
    else
    {
        // Full engulf: bao phủ cả high/low
        double low1  = iLow(_Symbol, PERIOD_H1, 1);
        double low2  = iLow(_Symbol, PERIOD_H1, 2);
        double high1 = iHigh(_Symbol, PERIOD_H1, 1);
        double high2 = iHigh(_Symbol, PERIOD_H1, 2);
        return (low1 <= low2 && high1 >= high2);
    }
}

//--- Bearish Engulfing ---
bool IsBearishEngulfing()
{
    double open1  = iOpen(_Symbol, PERIOD_H1, 1);
    double close1 = iClose(_Symbol, PERIOD_H1, 1);
    double open2  = iOpen(_Symbol, PERIOD_H1, 2);
    double close2 = iClose(_Symbol, PERIOD_H1, 2);
    
    // Nến 2: xanh (tăng), Nến 1: đỏ (giảm)
    if (close2 <= open2 || close1 >= open1) return false;
    
    if (InpEngulfBodyOnly)
        return (open1 >= close2 && close1 <= open2);
    else
    {
        double low1  = iLow(_Symbol, PERIOD_H1, 1);
        double low2  = iLow(_Symbol, PERIOD_H1, 2);
        double high1 = iHigh(_Symbol, PERIOD_H1, 1);
        double high2 = iHigh(_Symbol, PERIOD_H1, 2);
        return (low1 <= low2 && high1 >= high2);
    }
}

//--- Bullish Pin Bar (Hammer-like) ---
bool IsBullishPinBar()
{
    double open  = iOpen(_Symbol, PERIOD_H1, 1);
    double close = iClose(_Symbol, PERIOD_H1, 1);
    double high  = iHigh(_Symbol, PERIOD_H1, 1);
    double low   = iLow(_Symbol, PERIOD_H1, 1);
    
    double body   = MathAbs(close - open);
    double range  = high - low;
    double lowerWick = MathMin(open, close) - low;
    double upperWick = high - MathMax(open, close);
    
    if (range == 0) return false;
    if (close <= open) return false;  // Nến phải tăng
    
    return (lowerWick / range >= InpPinBarWickRatio &&
            body / range <= InpPinBarBodyRatio &&
            upperWick <= lowerWick * 0.3);
}

//--- Bearish Pin Bar (Shooting Star) ---
bool IsBearishPinBar()
{
    double open  = iOpen(_Symbol, PERIOD_H1, 1);
    double close = iClose(_Symbol, PERIOD_H1, 1);
    double high  = iHigh(_Symbol, PERIOD_H1, 1);
    double low   = iLow(_Symbol, PERIOD_H1, 1);
    
    double body   = MathAbs(close - open);
    double range  = high - low;
    double lowerWick = MathMin(open, close) - low;
    double upperWick = high - MathMax(open, close);
    
    if (range == 0) return false;
    if (close >= open) return false;  // Nến phải giảm
    
    return (upperWick / range >= InpPinBarWickRatio &&
            body / range <= InpPinBarBodyRatio &&
            lowerWick <= upperWick * 0.3);
}
```

## 6. CONFLUENCE SCORING

```cpp
//+------------------------------------------------------------------+
//| Confluence Score + Entry Decision                               |
//+------------------------------------------------------------------+
struct SetupResult {
    bool   shouldEnter;
    int    orderType;        // OP_BULLISH or OP_BEARISH
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
    
    // Filter 1: Pre-checks
    if (!PreTradeFilters()) 
    {
        result.reason = "Pre-trade filters failed";
        return result;
    }
    
    // Get current price
    double ask = SymbolInfoDouble(_Symbol, SYMBOL_ASK);
    double bid = SymbolInfoDouble(_Symbol, SYMBOL_BID);
    double atr = GetATR(PERIOD_D1, 14);
    
    // Layer 1: Trend
    TrendResult trend = GetTrendD1();
    if (!trend.isValid && InpRequireTrend)
    {
        result.reason = "Trend filter: no clear direction";
        return result;
    }
    
    // Layer 2: S/R Zones
    SRZone supportZone = FindSRZone(bid, false);
    SRZone resistanceZone = FindSRZone(ask, true);
    
    bool nearSupport = IsPriceNearSR(bid, supportZone, atr);
    bool nearResistance = IsPriceNearSR(ask, resistanceZone, atr);
    
    if (!nearSupport && !nearResistance && InpRequireSR)
    {
        result.reason = "S/R filter: not near any zone";
        return result;
    }
    
    // Layer 3: PA Signal
    bool bullishPA = IsBullishEngulfing() || IsBullishPinBar();
    bool bearishPA = IsBearishEngulfing() || IsBearishPinBar();
    
    if (!bullishPA && !bearishPA && InpRequirePA)
    {
        result.reason = "PA filter: no signal detected";
        return result;
    }
    
    // --- Determine direction ---
    int direction = 0; // 1=buy, -1=sell, 0=no
    if (trend.isBullish && nearSupport && bullishPA)       direction = 1;
    else if (trend.isBearish && nearResistance && bearishPA) direction = -1;
    
    if (direction == 0)
    {
        result.reason = "Direction mismatch: layers conflict";
        return result;
    }
    
    // --- Calculate Score ---
    int score = 0;
    if (trend.isBullish || trend.isBearish) score += 2;
    score += trend.structureScore;
    if (nearSupport || nearResistance)       score += 1;
    if (bullishPA || bearishPA)              score += 1;
    if (IsRoundNumber(direction > 0 ? bid : ask)) score += 1;
    
    if (score < InpMinScore)
    {
        result.reason = StringFormat("Score too low: %d/%d", score, InpMinScore);
        return result;
    }
    
    // --- Calculate SL/TP ---
    double sl, tp1, tp2;
    if (direction == 1)
    {
        double swingLow = FindSwingLow(PERIOD_H1, 2, 20);
        double zoneLow  = supportZone.lower;
        sl = MathMin(swingLow, zoneLow) - (atr * 0.1);
        
        double risk = bid - sl;
        tp1 = bid + (risk * InpTP1Multiplier);
        tp2 = bid + (risk * InpTP2Multiplier);
        
        // Không để TP vượt quá resistance tiếp theo
        if (resistanceZone.valid && resistanceZone.price < tp2)
            tp2 = resistanceZone.price;
    }
    else
    {
        double swingHigh = FindSwingHigh(PERIOD_H1, 2, 20);
        double zoneHigh  = resistanceZone.upper;
        sl = MathMax(swingHigh, zoneHigh) + (atr * 0.1);
        
        double risk = sl - ask;
        tp1 = ask - (risk * InpTP1Multiplier);
        tp2 = ask - (risk * InpTP2Multiplier);
        
        if (supportZone.valid && supportZone.price > tp2)
            tp2 = supportZone.price;
    }
    
    // --- Calculate Lot Size ---
    double slPoints = MathAbs(sl - (direction > 0 ? bid : ask)) / _Point;
    double lotSize = CalculateLotSize(slPoints);
    
    // --- Fill result ---
    result.shouldEnter = true;
    result.orderType = (direction == 1) ? OP_BUY : OP_SELL;
    result.entryPrice = (direction == 1) ? ask : bid;
    result.stopLoss = sl;
    result.takeProfit1 = tp1;
    result.takeProfit2 = tp2;
    result.lotSize = lotSize;
    result.score = score;
    result.reason = StringFormat("Score %d/6: Enter %s", score, 
                                  (direction == 1) ? "BUY" : "SELL");
    
    return result;
}

//+------------------------------------------------------------------+
bool PreTradeFilters()
{
    // Spread check
    double spread = SymbolInfoInteger(_Symbol, SYMBOL_SPREAD);
    if (spread > InpMaxSpread) return false;
    
    // Session filter
    if (InpUseSessionFilter && !IsTradingSession()) return false;
    
    // News filter
    if (InpUseNewsFilter && IsNewsEvent()) return false;
    
    // Already has position?
    if (PositionSelect(_Symbol)) return false;
    
    return true;
}
```

## 7. EXECUTE TRADE

```cpp
//+------------------------------------------------------------------+
//| Execute trade with partial TP                                   |
//+------------------------------------------------------------------+
void ExecuteTrade(SetupResult &setup)
{
    if (!setup.shouldEnter) return;
    
    MqlTradeRequest request = {};
    MqlTradeResult result = {};
    
    request.action   = TRADE_ACTION_DEAL;
    request.symbol   = _Symbol;
    request.volume   = setup.lotSize;
    request.type     = (setup.orderType == OP_BUY) ? ORDER_TYPE_BUY : ORDER_TYPE_SELL;
    request.price    = setup.entryPrice;
    request.sl       = setup.stopLoss;
    request.tp       = setup.takeProfit1;  // TP1 cho TP của order
    request.deviation= 10;
    request.magic    = InpMagicNumber;
    request.comment  = InpComment;
    
    if (OrderSend(request, result))
    {
        // Log success
        Print(result.comment);
    }
    else
    {
        Print("Order failed: ", result.retcode, " ", GetLastError());
    }
}
```

## 8. POSITION MANAGEMENT

```cpp
//+------------------------------------------------------------------+
//| Manage open positions: trailing, partial close                   |
//+------------------------------------------------------------------+
void ManagePositions()
{
    if (!PositionSelect(_Symbol)) return;
    
    ulong ticket = PositionGetTicket(0);
    if (ticket == 0) return;
    
    double openPrice = PositionGetDouble(POSITION_PRICE_OPEN);
    double currentSL = PositionGetDouble(POSITION_SL);
    double volume    = PositionGetDouble(POSITION_VOLUME);
    double profit    = PositionGetDouble(POSITION_PROFIT);
    int    type      = (int)PositionGetInteger(POSITION_TYPE);
    
    double ask = SymbolInfoDouble(_Symbol, SYMBOL_ASK);
    double bid = SymbolInfoDouble(_Symbol, SYMBOL_BID);
    
    double risk = (type == POSITION_TYPE_BUY) 
                  ? (openPrice - currentSL) 
                  : (currentSL - openPrice);
    
    if (risk <= 0) return;
    
    double currentDistance = (type == POSITION_TYPE_BUY)
                             ? (bid - openPrice)
                             : (openPrice - ask);
    
    double rr = currentDistance / risk;
    
    // --- Khi chạm 1R: Move SL về hòa ---
    if (rr >= 1.0 && currentSL == openPrice - risk) // Chỉ move 1 lần
    {
        MqlTradeRequest req = {};
        MqlTradeResult res = {};
        req.action = TRADE_ACTION_SLTP;
        req.symbol = _Symbol;
        req.sl     = (type == POSITION_TYPE_BUY) ? openPrice : openPrice;
        req.tp     = PositionGetDouble(POSITION_TP);
        req.magic  = InpMagicNumber;
        req.position = ticket;
        OrderSend(req, res);
    }
    
    // --- Khi chạm TP1: đóng 50% ---
    if (rr >= InpTP1Multiplier)
    {
        double closeVolume = volume * (InpTP1ClosePct / 100.0);
        if (closeVolume > 0)
        {
            MqlTradeRequest req = {};
            MqlTradeResult res = {};
            req.action    = TRADE_ACTION_DEAL;
            req.symbol    = _Symbol;
            req.volume    = NormalizeDouble(closeVolume, 2);
            req.type      = (type == POSITION_TYPE_BUY) 
                            ? ORDER_TYPE_SELL : ORDER_TYPE_BUY;
            req.price     = (type == POSITION_TYPE_BUY) ? bid : ask;
            req.deviation = 10;
            req.magic     = InpMagicNumber;
            req.position  = ticket;
            req.comment   = InpComment + "_TP1";
            OrderSend(req, res);
        }
        
        // Move SL lên 1R (lock profit)
        if (currentSL != (type == POSITION_TYPE_BUY ? openPrice + risk : openPrice - risk))
        {
            MqlTradeRequest req2 = {};
            MqlTradeResult res2 = {};
            req2.action = TRADE_ACTION_SLTP;
            req2.symbol = _Symbol;
            req2.sl     = (type == POSITION_TYPE_BUY) 
                          ? (openPrice + risk) : (openPrice - risk);
            req2.tp     = 0;
            req2.magic  = InpMagicNumber;
            req2.position = ticket;
            OrderSend(req2, res2);
        }
    }
    
    // --- Trailing: khi giá đi thêm 1R, SL trailing 0.5R ---
    if (rr >= InpTP1Multiplier + 1.0)
    {
        double newSL = (type == POSITION_TYPE_BUY)
                       ? (bid - (risk * 0.5))
                       : (ask + (risk * 0.5));
        
        double optimalSL = (type == POSITION_TYPE_BUY)
                           ? MathMax(currentSL, newSL)
                           : MathMin(currentSL, newSL);
        
        if ((type == POSITION_TYPE_BUY && optimalSL > currentSL) ||
            (type == POSITION_TYPE_SELL && optimalSL < currentSL))
        {
            MqlTradeRequest req = {};
            MqlTradeResult res = {};
            req.action = TRADE_ACTION_SLTP;
            req.symbol = _Symbol;
            req.sl     = optimalSL;
            req.tp     = 0;
            req.magic  = InpMagicNumber;
            req.position = ticket;
            OrderSend(req, res);
        }
    }
}
```

## 9. HELPER FUNCTIONS

```cpp
//+------------------------------------------------------------------+
//| Helper: Find Swing High/Low                                     |
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
    return iATR(_Symbol, tf, period, 0);
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
    
    int hour = dt.hour;    // Server time
    int day  = dt.day_of_week;
    
    // Không giao dịch cuối tuần
    if (day == 6 || day == 0) return false;
    
    // London: 8-17 GMT, NY: 13-22 GMT
    // Overlap: 13-17 GMT (tốt nhất)
    return (hour >= 8 && hour < 22);
}

//+------------------------------------------------------------------+
bool IsNewsEvent()
{
    // Cần tích hợp Economic Calendar API
    // Hoặc dùng file news.csv với lịch tin tức
    // Trả về true nếu có tin tier-1 trong vòng 2h tới
    return false; // Tạm thời skip
}

//+------------------------------------------------------------------+
double CalculateLotSize(double slPoints)
{
    double riskMoney = AccountInfoDouble(ACCOUNT_BALANCE) * (InpRiskPercent / 100.0);
    double tickValue = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_VALUE);
    double tickSize  = SymbolInfoDouble(_Symbol, SYMBOL_TRADE_TICK_SIZE);
    
    double lotStep   = SymbolInfoDouble(_Symbol, SYMBOL_VOLUME_STEP);
    double minLot    = SymbolInfoDouble(_Symbol, SYMBOL_VOLUME_MIN);
    double maxLot    = SymbolInfoDouble(_Symbol, SYMBOL_VOLUME_MAX);
    
    if (tickValue == 0 || slPoints == 0) return minLot;
    
    double slValue = slPoints * tickValue;  // Giá trị SL (tiền)
    double lots = riskMoney / slValue;
    
    lots = MathFloor(lots / lotStep) * lotStep;
    lots = MathMax(minLot, MathMin(lots, maxLot));
    
    return NormalizeDouble(lots, 2);
}
```

## 10. BACKTEST CONFIG

### Cặp tiền khuyến nghị

| Thứ tự | Symbol | Lý do |
|--------|--------|-------|
| 1 | EURUSD | Spread thấp, PA rõ, thanh khoản cao |
| 2 | GBPUSD | Biến động tốt, tôn trọng S/R |
| 3 | USDJPY | Xu hướng mạnh, ít fakeout |
| 4 | AUDUSD | PA ổn định |

### Cài đặt Backtest (MT5 Tester)

```
- Model: Every tick (dựa trên tick thực tế)
- Period: 2 năm gần nhất
- Spread: Current (hoặc 10-15 cho EURUSD)
- Optimization: Genetic
  - S/R touches: [2, 3]
  - MinScore: [3, 4, 5]
  - TP1 Multiplier: [1.5, 2.0, 2.5]
  - PinBar ratios: body [0.2, 0.3, 0.4], wick [0.5, 0.6, 0.7]
```

### Metrics cần theo dõi

| Metric | Target | Threshold |
|--------|--------|-----------|
| Win Rate | 55-65% | <45% = cần điều chỉnh |
| Profit Factor | >1.5 | <1.2 = không đủ edge |
| Max Drawdown | <15% | >25% = risk quá cao |
| Sharpe Ratio | >1.0 | <0.5 = không ổn định |
| Avg RR | >1:1.5 | <1:1 = risk management kém |
| Trades/month | 15-30 | <5 = quá ít, >50 = over-trading |

## 11. TỐI ƯU HÓA

### Các biến cần optimize (theo thứ tự ưu tiên)

1. **SR_MinTouches**: {2, 3} — 3 lần chạm = ít tín hiệu hơn nhưng chất lượng cao
2. **MinScore**: {3, 4} — score 4 có win rate cao hơn nhưng ít trade hơn
3. **TP1_Multiplier**: {1.5, 2.0} — ảnh hưởng trực tiếp đến R:R
4. **SR_ZonePct**: {0.2, 0.3, 0.4} — zone hẹp = ít tín hiệu, zone rộng = nhiễu
5. **MA_Period**: thử MA50, MA100, MA200

### Mở rộng về sau

- Thêm Volume filter (nếu có dữ liệu tick volume)
- Thêm ATR-based trailing (thay vì fixed trailing)
- Multi-position: cho phép nhiều lệnh nếu confluence score >= 5
- Time filter: chỉ trade LDN-NY overlap (13-17 GMT)

## XEM THÊM

- [[trinity-confluence-idea]] — Ý tưởng giao dịch và triết lý
- [[multi-timeframe-forex]] — Top-Down analysis framework
- [[trend-following]] — Trend trading rules
- [[support-resistance-zones]] — S/R zone identification
- [[price-action-confluence]] — Confluence scoring
- [[candlestick-patterns]] — Engulfing, Pin Bar patterns
- [[market-structure]] — HH/HL structure
