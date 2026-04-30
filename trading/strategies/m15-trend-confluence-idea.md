---
title: M15 Trend Confluence — Chiến lược 3 Khung Cải tiến (H4 → H1 → M15)
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [trading-strategy, price-action, multi-timeframe, trend-analysis, support-resistance, confluence, scalping, entry-technique]
sources: [trading/strategies/trinity-confluence-idea.md, trading/concepts/multi-timeframe-forex.md, trading/concepts/trend-following.md, trading/concepts/pullback-retracement.md, trading/concepts/support-resistance-zones.md, trading/concepts/price-action.md]
confidence: high
---

# M15 Trend Confluence — Chiến lược 3 Khung Cải tiến

## Sự khác biệt với Trinity Confluence gốc

Trinity Confluence gốc dùng D1 làm trend filter và H1 làm entry, phù hợp với swing trading
(giữ lệnh 1-5 ngày). Phiên bản cải tiến này **dịch chuyển xuống 1 bậc khung thời gian**:

| Thành phần | Trinity gốc | M15 Cải tiến | Lý do |
|-----------|-------------|-------------|-------|
| Trend Filter | D1 | **H4 + H1** | Bắt xu hướng nhanh hơn, nhiều cơ hội hơn |
| S/R Zones | H4/D1 | **H4/D1** (giữ nguyên) | Vùng lớn vẫn quan trọng |
| Entry Trigger | H1 | **M15** | Entry chính xác hơn 4x, SL ngắn hơn |
| R:R tối thiểu | 1.5 (linh hoạt) | **1:2** (cứng) | Bảo vệ edge, mỗi thắng bù 2 thua |
| Daily Loss Limit | Không có | **5% tài khoản** | Chặn chuỗi thua phá tài khoản |
| Phong cách | Swing (1-5 ngày) | **Intraday swing** (vài giờ - 1 ngày) | Nhanh hơn, phản hồi nhanh hơn |

## Ý tưởng cốt lõi: 3 Lớp Lọc × 3 Khung Thời Gian

```
+--------------------------------------------------+
|          M15 TREND CONFLUENCE                     |
|                                                   |
|  Lớp 1: TREND H4+H1  │ MA200 + HH/HL trên H4     |
|                       │ Trend mạnh/vừa trên H1    |
|  Lớp 2: S/R ZONE      │ Swing points H4/D1        |
|  Lớp 3: PA M15        │ Engulfing / Pin / IB BO   |
+--------------------------------------------------+

CHỈ VÀO LỆNH KHI CẢ 3 LỚP ĐỒNG THUẬN.
R:R TỐI THIỂU 1:2 — KHÔNG NGOẠI LỆ.
```

## Lớp 1: Trend Filter (H4 + H1) — "Bạn đang ở phe nào?"

### H4: Trend chính (Primary)
- **MA200 trên H4**: Giá trên MA200 → chỉ tìm LONG, dưới MA200 → chỉ tìm SHORT
- **HH/HL Structure**: Trên H4, xác định 2 swing high và 2 swing low gần nhất trong 50 nến
  - HH cao hơn + HL cao hơn → uptrend mạnh (2 điểm)
  - HH cao hơn HOẶC HL cao hơn → uptrend yếu (1 điểm) — vẫn được trade
  - Không rõ → không giao dịch

### H1: Trend xác nhận (Confirmation)
- **Cùng hướng H4**: H1 phải cùng hướng với H4. Nếu H4 tăng mà H1 giảm → pullback, KHÔNG short
- **Giá trên MA20 H1**: Bộ lọc momentum ngắn hạn — giá phải trên MA20 (uptrend) hoặc dưới MA20 (downtrend)
- **ATR H1 > 50% ATR H4**: Thị trường đang "sống", không phải sideway chết

> **Nguyên tắc vàng**: Higher timeframe luôn thắng. Nếu H4 LONG mà H1 đang SHORT — đó là pullback, chờ H1 quay lại.

## Lớp 2: S/R Zone (H4/D1) — "Vào ở đâu?"

Vùng S/R từ H4 và D1 — giữ nguyên như Trinity gốc vì các vùng lớn vẫn giữ giá trị:

- **Swing points H4** (ưu tiên hơn): Các đỉnh/đáy H4 được test 2+ lần
- **Swing points D1** (hỗ trợ): Các vùng D1 quan trọng — ít xuất hiện hơn nhưng mạnh hơn
- **Round numbers**: Các mức 00, 50 gần giá
- **Zone width**: 0.3 × ATR(H4)

Quy tắc: Giá phải cách S/R zone ≤ 0.5 ATR(H4) để được tính là "trong vùng".

## Lớp 3: PA Trigger (M15) — "Khi nào bấm cò?"

Vì M15 nhanh hơn H1 4 lần, tín hiệu PA cần bộ lọc chặt hơn:

### Tín hiệu M15 đạt chuẩn:

**1. Engulfing M15 (trọng số cao nhất)**
- Bullish: Nến xanh nuốt trọn nến đỏ trước đó — body và cả râu
- Bearish: Nến đỏ nuốt trọn nến xanh trước đó
- Điều kiện thêm: Nến engulfing phải có body ≥ 50% tổng range (không phải doji giả)
- Entry: Ngay khi nến M15 đóng cửa

**2. Pin Bar M15 (tại S/R)**
- Râu dài ≥ 60% tổng range, body ≤ 30% tổng range
- Râu phải hướng về phía S/R zone (rejection rõ ràng)
- Nến phải đóng ngược hướng râu (xác nhận đảo chiều)
- Entry: Tại 50% retracement của pin bar (limit order) hoặc khi nến tiếp theo breakout

**3. Inside Bar Breakout M15 (tại S/R)**
- Inside bar nằm trong range nến mẹ (H1 hoặc M15 trước đó)
- Breakout theo hướng trend H4
- Entry: Stop order phía trên high (LONG) hoặc dưới low (SHORT) của inside bar
- Yêu cầu: Volume/range của nến mẹ > ATR M15

**4. Momentum Candle M15 (trong trend mạnh)**
- Nến M15 có body ≥ 80% range, gần như không có râu
- Xuất hiện sau pullback nhẹ về MA20 trên M15
- Entry: Ngay khi nến đóng, SL dưới đáy pullback

### Tín hiệu bị LOẠI trên M15:
- ❌ Inside bar không có nến mẹ rõ ràng
- ❌ Pin bar có râu 2 bên (doji — không rõ hướng)
- ❌ Engulfing mà nến engulfing nhỏ hơn 50% range nến bị engulf
- ❌ Bất kỳ tín hiệu nào xuất hiện khi spread > 30 points

## Risk Management Cứng

### 1. R:R tối thiểu 1:2 — KHÔNG NGOẠI LỆ

Trước khi vào lệnh, phải xác định được SL và TP với tỉ lệ ≥ 1:2:

```
TP distance ≥ 2 × SL distance
```

- **SL**: Dưới swing low M15 (LONG) hoặc trên swing high M15 (SHORT) + 0.1 ATR buffer
- **TP1 (2R)**: Đóng 50% vị thế, move SL về hòa
- **TP2 (3R+)**: Phần còn lại chạy trailing, hoặc đến S/R zone tiếp theo

Nếu không tìm được setup nào có R:R ≥ 1:2 → không giao dịch.

### 2. Daily Loss Limit: 5% tài khoản

```
if (daily_realized_loss >= account_balance * 0.05)
    → DỪNG giao dịch trong ngày
```

- **Theo dõi** tổng loss đã thực hiện (realized P&L) trong ngày
- Reset về 0 vào đầu ngày giao dịch mới (00:00 server time)
- Khi chạm giới hạn → không mở lệnh mới, quản lý lệnh đang chạy bình thường

### 3. Miễn Daily Loss Limit cho tài khoản nhỏ

```
MIN_BALANCE_FOR_LIMIT = 200  // USD hoặc đơn vị tiền tệ tài khoản

if (account_balance < MIN_BALANCE_FOR_LIMIT)
    → Bỏ qua daily loss limit
    → Chỉ giữ R:R 1:2 và risk/lệnh 1%
```

Lý do: Với tài khoản quá nhỏ ($50-200), 5% = $2.5-10 — một lệnh thua là chạm limit, không thực tế.
Ngưỡng $200 là mức tối thiểu để 5% = $10, đủ cho 2-3 lệnh thua với risk 1%/lệnh.

### 4. Risk mỗi lệnh: 1% tài khoản

```
lot_size = (balance * 0.01) / (sl_distance_in_quote_currency)
```

- Cố định % rủi ro, không cố định lot
- Khi thắng → lot tăng tự nhiên; khi thua → lot giảm, bảo vệ tài khoản

### 5. Quy tắc chuỗi thua

- **Thua 2 lệnh liên tiếp**: Giảm risk xuống 0.5%/lệnh
- **Thua 4 lệnh liên tiếp**: Dừng giao dịch, review toàn bộ setup
- **Thắng 1 lệnh**: Reset chuỗi, risk về 1%

## Scoring System (Hệ thống chấm điểm)

| Yếu tố | Điểm | Ghi chú |
|--------|------|--------|
| Trend H4 cùng hướng (MA200) | 2 | Bắt buộc |
| HH/HL Structure H4 rõ | 1 | Cả HH và HL cùng hướng |
| H1 cùng hướng H4 | 1 | MA20 H1 xác nhận |
| Giá trong vùng S/R H4/D1 | 1 | Cách zone ≤ 0.5 ATR(H4) |
| PA Signal M15 rõ ràng | 1 | Engulfing / Pin Bar / IB breakout |
| Round Number gần đó | +1 | Cách ≤ 10 pips |
| R:R ≥ 1:2 khả thi | Bắt buộc | Không tính điểm — là điều kiện cứng |

**Điểm tối thiểu: 4/6** (bắt buộc có Trend H4 = 2 điểm)
**Điểm lý tưởng: 5-6/6**

## Pre-Trade Checklist (M15)

Trước mỗi lệnh, dành 30 giây kiểm tra:

- [ ] **H4 trend**: Giá trên hay dưới MA200? HH/HL rõ không?
- [ ] **H1 confirm**: H1 cùng hướng H4? Giá trên MA20?
- [ ] **S/R zone**: Có vùng S/R H4/D1 nào trong phạm vi 0.5 ATR(H4) không?
- [ ] **M15 trigger**: Có Engulfing / Pin Bar / IB breakout hợp lệ không?
- [ ] **R:R ≥ 1:2**: TP distance có ≥ 2× SL distance?
- [ ] **Daily loss**: Hôm nay chưa chạm 5% limit?
- [ ] **Chuỗi thua**: Có đang trong chuỗi thua 2+ lệnh không?
- [ ] **Spread**: Spread hiện tại ≤ 30 points?
- [ ] **Session**: Đang trong LDN hoặc NY session?

Nếu bất kỳ ô nào trống → không giao dịch.

## Khi nào KHÔNG giao dịch

- H4 đang sideway (không có HH/HL rõ trong 30 nến)
- H1 ngược hướng H4 (pullback chưa kết thúc)
- Không có S/R zone nào trong tầm
- Spread > 30 points
- Sắp có tin tier-1 (NFP, CPI, FOMC) < 1h
- Đã chạm daily loss limit 5%
- Đang trong chuỗi thua 4+ lệnh
- Asian session (thanh khoản thấp, PA không đáng tin)
- Thứ 6 cuối phiên NY (thanh khoản cạn)

## Hành vi thị trường chiến lược khai thác

### 1. H4 trend continuation với M15 pullback
H4 đang uptrend → H1 điều chỉnh nhẹ → M15 xuất hiện bullish engulfing tại S/R H4
→ vào LONG, SL dưới đáy M15, TP1 = 2R, TP2 = S/R H4 tiếp theo

### 2. H4 breakout với M15 retest
H4 phá vỡ vùng S/R → H1 xác nhận → M15 pullback retest vùng vừa phá + pin bar
→ vào theo hướng breakout, R:R thường đạt 1:3+

### 3. S/R rejection với M15 pin bar
Vùng S/R H4 mạnh (3+ lần test) → M15 xuất hiện pin bar từ chối rõ ràng
→ vào ngược hướng tiếp cận, SL ngoài râu pin bar, TP tại S/R đối diện

## Tại sao chiến lược này có edge tốt hơn Trinity gốc?

1. **Nhiều cơ hội hơn**: M15 cho 96 nến/ngày vs H1 chỉ 24 nến — tín hiệu xuất hiện thường xuyên hơn
2. **SL ngắn hơn**: Entry M15 cho phép SL chặt hơn → R:R tốt hơn với cùng TP
3. **Daily loss limit**: Chặn chuỗi thua kéo dài — bảo vệ tài khoản trong ngày xấu
4. **H4+H1 nhanh hơn D1**: Phát hiện đảo chiều xu hướng sớm hơn, không bị kẹt trong D1 sideway
5. **R:R 1:2 cứng**: Mỗi lệnh thắng bù 2 lệnh thua — với win rate 50% vẫn có lợi nhuận

## Implement MQL5

- [[m15-trend-confluence-mql5]] — Hướng dẫn implement MQL5 đầy đủ (H4+H1+M15)

## So sánh với Trinity Confluence gốc

| Tiêu chí | Trinity gốc | M15 Cải tiến |
|----------|------------|-------------|
| Trend filter | D1 | H4 + H1 |
| Entry timeframe | H1 | M15 |
| Tần suất tín hiệu | 3-8/tháng | 15-30/tháng |
| Thời gian giữ lệnh | 1-5 ngày | 2-24 giờ |
| R:R tối thiểu | Linh hoạt (~1.5) | Cứng 1:2 |
| Daily loss limit | Không | 5% |
| Win rate kỳ vọng | 55-65% | 50-55% |
| Phù hợp với | Swing trader kiên nhẫn | Intraday trader chủ động |

## Xem thêm

- [[trinity-confluence-idea]] — Chiến lược gốc (D1+H1 entry)
- [[multi-timeframe-forex]] — Lý thuyết đa khung thời gian
- [[trend-following]] — Trend following framework
- [[price-action]] — Tín hiệu Price Action
- [[support-resistance-zones]] — Xác định S/R zones
- [[pullback-retracement]] — Pullback entry techniques
- [[price-action-confluence]] — Confluence scoring
- [[candlestick-patterns]] — Mô hình nến
