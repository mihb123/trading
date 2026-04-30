---
title: Trinity Confluence - Ý tưởng giao dịch cốt lõi
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [trading-strategy, price-action, multi-timeframe, trend-analysis, support-resistance, confluence]
sources: [trading/concepts/trend-following.md, trading/concepts/multi-timeframe-forex.md, trading/concepts/support-resistance-zones.md, trading/concepts/price-action-confluence.md, trading/concepts/market-structure.md]
confidence: high
---

# Trinity Confluence — Ý tưởng giao dịch cốt lõi

## Nguồn cảm hứng

Chiến lược này là sự kết tinh từ toàn bộ kiến thức trong wiki, tổng hợp từ nhiều nguồn:

- **Rayner Teo** — Trend trading framework, 3 loại xu hướng (strong/healthy/weak)
- **Nial Fuller** — Confluence scoring, "where > what"
- **Alexander Elder** — Three Screen System (W1 → D1 → H4)
- **ChartSnipe** — Top-Down 4-Step Framework
- **ICT / TTrades** — CISD, Continuation OB, cấu trúc thị trường
- **TradingSetupsReview** — Trapped traders, dual timeframe SMA divergence
- **QuantVPS / Capital.com / CMC Markets** — Pin bars, inside bars, breakout-retest

## Vấn đề chiến lược đơn lẻ giải quyết

Hầu hết trader thất bại vì:

1. **Chỉ dùng 1 tín hiệu** — thấy pin bar là vào, không biết nó ở vùng nào
2. **Không có trend filter** — vào lệnh ngược xu hướng chính, xác suất thấp
3. **Không có S/R** — vào lệnh giữa range, không biết target ở đâu
4. **Không quản lý vị thế** — đặt SL theo cảm tính, chốt lời quá sớm

**Trinity Confluence** giải quyết cả 4 vấn đề bằng cách kết hợp 3 lớp lọc.

## Ý tưởng: 3 Lớp Lọc (Three Layers)

### Lớp 1: Trend Filter (D1) — "Bạn đang ở phe nào?"

**Triết lý:** 70% xác suất thắng đến từ việc giao dịch CÙNG xu hướng chính. Phần còn lại là kỹ thuật entry.

Dùng 2 công cụ để xác định xu hướng:
- **MA200**: Bộ lọc xu hướng thô — giá trên MA200 = chỉ mua, dưới MA200 = chỉ bán
- **HH/HL Structure**: Xác nhận cấu trúc — higher highs + higher lows = uptrend confirmed

> Đây là "Màn 1" của Elder's Triple Screen. Không trade nếu không biết trend D1 là gì.

### Lớp 2: S/R Zone (H4/D1) — "Vào ở đâu?"

**Triết lý:** Một tín hiệu PA ở vùng S/R quan trọng có giá trị gấp 10 lần tín hiệu ở giữa range.

- Xác định vùng hỗ trợ/kháng cự từ D1 swing highs/lows
- Vùng S/R càng nhiều lần chạm (3+) càng mạnh
- Đợi giá vào vùng — không đuổi theo

> Đây là "Màn 2" — tìm vùng giá tốt để vào. Nếu không có vùng S/R nào gần → không giao dịch hôm nay.

### Lớp 3: PA Trigger (H1) — "Khi nào bấm cò?"

**Triết lý:** Chỉ vào lệnh khi giá CHO THẤY tín hiệu tại vùng S/R, không phải khi bạn "cảm thấy" nó sẽ đảo chiều.

- **Bullish Engulfing**: Nến xanh nuốt nến đỏ tại vùng SUPPORT (cùng hướng D1)
- **Bearish Engulfing**: Nến đỏ nuốt nến xanh tại vùng RESISTANCE (cùng hướng D1)
- **Pin Bar (Hammer)**: Râu dài, thân nhỏ — rejection tại S/R
- **Shooting Star**: Râu trên dài tại kháng cự

> Đây là "Màn 3" — tín hiệu hành động. Chỉ vào sau khi nến H1 ĐÓNG CỬA xác nhận.

## Ba Lá Bài = Một Setup

```
+-------------------------------------------+
|          TRINITY CONFLUENCE               |
|                                           |
|  Lớp 1: TREND D1    │ MA200 + HH/HL      |
|  Lớp 2: S/R ZONE    │ Swing points D1    |
|  Lớp 3: PA TRIGGER  │ Engulfing / Pin Bar |
+-------------------------------------------+

CHỈ VÀO LỆNH KHI CẢ 3 LỚP XANH.
THIẾU 1 → KHÔNG GIAO DỊCH.
```

## Hành vi Thị trường (Market Dynamics)

Chiến lược này khai thác 3 hành vi lặp đi lặp lại của thị trường:

### 1. Trend Continues (Xu hướng tiếp diễn)
```
Giá đang trong uptrend → pullback về S/R → PA rejection → tiếp tục tăng
```
Đây là setup thường gặp nhất (60-70% tín hiệu). Thị trường có xu hướng "nhớ" các vùng giá — một khi đã xác định xu hướng, nó có xu hướng tiếp tục.

### 2. S/R Bounce (Giá bật từ vùng)
```
Giá chạm vùng S/R mạnh (đã test 3+ lần) → rejection → quay đầu
```
Vùng S/R càng nhiều lần giữ, càng khó bị phá vỡ. Đây là tâm lý đám đông: ai từng mua ở vùng đó và có lời sẽ mua lại.

### 3. Fake Breakout (Phá vỡ giả)
```
Giá overshoot nhẹ S/R → quay đầu nhanh → trapped traders bị kẹt
```
Trapped traders buộc phải cut loss, tạo thêm động lực cho phe đối diện. Fakey pattern và trapped traders đều dựa trên cơ chế này.

## Scoring System (Hệ thống chấm điểm)

Nếu bạn muốn linh hoạt hơn "tất cả hoặc không có gì":

| Yếu tố | Điểm | Mô tả |
|--------|------|-------|
| Trend D1 (MA200) | 2 | Yếu tố quan trọng nhất |
| HH/HL Structure D1 | 1 | Xác nhận cấu trúc |
| Giá trong vùng S/R | 1 | Cách S/R ≤0.5 ATR |
| PA Signal (Engulfing/Pin) | 1 | Nến H1 vừa đóng |
| Round Number gần đó | 1 | 1.1000, 1.1050, 150.00 |
| Volume spike | +1 | Nếu có dữ liệu volume |

**Điểm tối thiểu để vào lệnh: 3/6** (bắt buộc có Trend)
**Điểm lý tưởng: 4-5/6**

## Tại sao chiến lược này có edge?

1. **Trend filter loại bỏ 50% tín hiệu nhiễu** — không trade ngược D1
2. **S/R zone định nghĩa sẵn target** — biết chốt lời ở đâu, R:R tính trước
3. **PA trigger đảm bảo timing** — không vào lệnh "mù"
4. **Confluence đẩy win rate lên 55-65%** (backtest từ nhiều nguồn)
5. **Hoạt động trên mọi khung thời gian** — D1 + H4 + H1 cho swing; H1 + M15 + M5 cho day trade

## Khi nào KHÔNG giao dịch

- Weak trend (nến nhỏ, sideway) — D1 không có HH/HL rõ
- Giá ở giữa range, không gần S/R nào
- Sắp có tin tức tier-1 (NFP, CPI, FOMC, rate decision) < 2h
- Spread > 30 points
- Asian session (thanh khoản thấp)

## Liên kết với Wiki

- [[trend-following]] — Trend filter chi tiết
- [[multi-timeframe-forex]] — Top-Down analysis
- [[support-resistance-zones]] — S/R zones
- [[price-action-confluence]] — Confluence scoring
- [[market-structure]] — HH/HL structure
- [[candlestick-patterns]] — Engulfing, Pin Bar
- [[fakey-pattern]] — Fake breakout
- [[trapped-traders]] — Trapped trader mechanism
- [[price-action]] — Price action tổng quan
- [[pullback-retracement]] — Pullback entries
