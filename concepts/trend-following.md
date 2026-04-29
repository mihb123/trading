---
title: Trend Following - Chiến lược Giao dịch Theo Xu hướng
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [trading-strategy, trend-analysis, market-structure, technical-analysis, price-action, forex]
sources: [raw/articles/rayner-teo-trend-trading-strategy.md, concepts/market-structure.md]
confidence: high
---

# Trend Following — Chiến lược Giao dịch Theo Xu hướng

## Định nghĩa

**Trend Following** (giao dịch theo xu hướng) là chiến lược mua khi giá đang trong uptrend và bán khi giá đang trong downtrend. Nguyên lý cốt lõi: xu hướng tồn tại nhờ lòng tham và nỗi sợ của con người — khi có tham lam, áp lực mua đẩy giá lên; khi có sợ hãi, áp lực bán đẩy giá xuống.^[raw/articles/rayner-teo-trend-trading-strategy.md]

## Tại sao Trend Following hiệu quả?

1. **Cải thiện win rate**: Giao dịch cùng xu hướng giúp bạn bắt được những sóng lớn, kéo dài hơn
2. **Risk-to-reward tốt hơn**: Mỗi $1 rủi ro có thể kiếm $2, $3, hoặc $10
3. **Áp dụng được mọi thị trường**: Forex, futures, stocks, bonds, commodities — xu hướng xuất hiện ở khắp nơi
4. **Bản chất tâm lý bất biến**: Chỉ khi con người hết cảm xúc, trend mới ngừng hoạt động

> Những huyền thoại giao dịch như Jesse Livermore ($100M năm 1929), Richard Dennis ($400M), Ed Seykota (250,000% return 16 năm) — tất cả đều là trend followers.^[raw/articles/rayner-teo-trend-trading-strategy.md]

## Cách Định nghĩa Xu hướng Chuyên nghiệp

### Trend bằng Higher High / Higher Low (HH/HL)

Trong uptrend: giá tạo **higher highs** (HH) và **higher lows** (HL) liên tiếp. Đường xu hướng nối các HL là đường hỗ trợ động. Miễn là HL giữ vững, xu hướng tăng còn nguyên vẹn.

Trong downtrend: giá tạo **lower highs** (LH) và **lower lows** (LL) liên tiếp. Đường xu hướng nối các LH là đường kháng cự động.

### Trend Filter với Moving Average 200
Dùng MA200 làm bộ lọc xu hướng đơn giản:
- Giá **trên** MA200 → chỉ tìm entry **mua** (buy pullback)
- Giá **dưới** MA200 → chỉ tìm entry **bán** (sell pullback)

## 3 Loại Xu hướng (Rayner Teo)

### 1. Strong Trend
- Nến to, đều, ít râu
- Giá không hoặc hiếm khi chạm MA20
- Entry: breakout entry ngay khi giá breakout (mạnh đến mức khó pullback sâu)
- Stop Loss: dưới đáy sóng điều chỉnh gần nhất

### 2. Healthy Trend
- Xu hướng rõ ràng nhưng có pullback đều đặn
- Giá thường chạm/quẹt MA20 rồi bật lên
- Entry: chờ pullback về vùng hỗ trợ (MA20, trendline, swing low)
- Stop Loss: dưới đáy pullback

### 3. Weak Trend
- Nến nhỏ, hay do dự
- Giá đi ngang nhiều, khó xác định hướng
- **KHÔNG giao dịch** — chờ xu hướng mạnh hơn hoặc sideway rõ ràng

## Chiến Lược Entry Theo Xu hướng

### Entry theo từng loại trend:
| Loại trend | Entry | Stop Loss |
|------------|-------|-----------|
| Strong | Breakout ngay khi breakout | Dưới đáy sóng gần nhất |
| Healthy | Chờ pullback về vùng hỗ trợ | Dưới đáy pullback |
| Weak | KHÔNG trade | — |

### Trendline Entry
Kẻ đường xu hướng nối các swing low (uptrend) hoặc swing high (downtrend). Entry khi giá chạm trendline và xuất hiện tín hiệu price action (pin bar, engulfing).

### Pullback vào MA
Trong healthy trend: chờ giá pullback về MA20 hoặc MA50, xuất hiện nến rejection (pin bar tăng trong uptrend), vào lệnh.

## Stop Loss trong Trending Market

3 cách đặt stop loss theo Rayner Teo:
1. **Swing Low/High**: Dưới đáy swing low gần nhất (uptrend) hoặc trên đỉnh swing high gần nhất (downtrend)
2. **Trendline**: Dưới đường xu hướng
3. **Moving Average**: Dưới MA20 hoặc MA50

Quy tắc: stop loss càng xa thì vị trí entry càng phải đẹp (risk nhỏ). Dùng cấu trúc thị trường để đặt stop, không đặt stop cố định theo pip.

## Đa Khung Thời Gian

Kết hợp [[multi-timeframe-forex]] với trend following:
- **HTF (D1/H4)**: Xác định xu hướng chính — giá trên hay dưới MA200?
- **LTF (H1/M15)**: Tìm entry theo hướng HTF — pullback về trendline/MA, xuất hiện tín hiệu PA

## Xem thêm
- [[market-structure]] - Cấu trúc thị trường: HH/HL, LH/LL
- [[multi-timeframe-forex]] - Kết hợp đa khung thời gian
- [[price-action]] - Tín hiệu price action cho entry
- [[pullback-retracement]] - Giao dịch pullback trong xu hướng
- [[price-action-confluence]] - Sự hội tụ tín hiệu
