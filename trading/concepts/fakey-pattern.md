---
title: Fakey Pattern - Mẫu hình Phá vỡ Giả Inside Bar
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [price-action, candlesticks, trading-strategy, forex, breakout-trading, technical-analysis]
sources: [trading/raw/articles/priceaction-com-nial-fuller.md, trading/concepts/price-action.md]
confidence: high
---

# Fakey Pattern — Mẫu hình Phá vỡ Giả Inside Bar

## Định nghĩa

Fakey pattern (còn gọi là **Inside Bar False Breakout**) là một mẫu hình Price Action gồm 2-3 nến, trong đó một Inside Bar bị phá vỡ (breakout) nhưng ngay sau đó giá đảo chiều và quay trở lại bên trong phạm vi (range) của nến mẹ (mother bar).^[trading/raw/articles/priceaction-com-nial-fuller.md]

Tên gọi "Fakey" xuất phát từ "fake" (giả) — mẫu hình này **đánh lừa** trader vì trông giống như breakout thực sự nhưng thực chất là bẫy (trap).

```
BUY SETUP (Bullish Fakey):
         ┌──┐
         │  │ ← Mother Bar (nến mẹ)
         │  │
  ┌──┐   │  │
  │  │   │  │ ← Inside Bar nằm trong mother bar
  │  │   │  │
  │  │ ┌┐│  │   ← Phá vỡ GIẢ xuống dưới (false breakdown)
  └──┘ ││└──┘
       ││      ← Nến nhỏ phá đáy inside bar rồi ĐẢO CHIỀU lên
       └┘
  
  → VÀO LỆNH BUY khi giá đóng CỬA TRÊN phạm vi mother bar
```

```
SELL SETUP (Bearish Fakey):
       ┌┐
       ││      ← Nến nhỏ phá đỉnh inside bar rồi ĐẢO CHIỀU xuống
  ┌──┐ ││┌──┐
  │  │ │││  │ ← Inside Bar
  │  │ │││  │
  │  │   │  │   ← Phá vỡ GIẢ lên trên (false breakout)
  │  │   │  │
  └──┘   └──┘ ← Mother Bar
  
  → VÀO LỆNH SELL khi giá đóng CỬA DƯỚI phạm vi mother bar
```

## Cơ chế Tâm lý (Psychology)

Fakey pattern khai thác **tâm lý đám đông** và **bẫy thanh khoản** (liquidity trap):

1. **Hình thành Inside Bar**: Thị trường tích lũy, trader chờ breakout
2. **Phá vỡ giả**: Giá breakout khỏi inside bar → trader đu theo hướng breakout
3. **Đảo chiều**: Giá nhanh chóng quay đầu → trader bị kẹt (trapped)
4. **Thoát lệnh**: Trapped trader buộc phải stop loss → càng đẩy giá đi xa hơn

## Cách Nhận diện Fakey Pattern

### Điều kiện cần:
1. **Mother Bar**: Nến có phạm vi rõ ràng (high-low)
2. **Inside Bar**: Nến nằm hoàn toàn trong phạm vi mother bar
3. **False Break**: Giá phá vỡ high hoặc low của inside bar
4. **Reversal**: Giá quay trở lại BÊN TRONG phạm vi mother bar
5. **Close**: Nến đóng cửa quay lại trong phạm vi mother bar

### Phân biệt với Breakout thật:

| Tiêu chí | Fakey (False) | Breakout Thật |
|----------|--------------|---------------|
| **Thời gian** | Phá vỡ và quay lại NHANH (1-2 nến) | Phá vỡ và tiếp tục đi |
| **Đóng cửa** | Quay lại trong range mother bar | Đóng NGOÀI phạm vi breakout |
| **Volume** | Volume thấp khi breakout | Volume tăng khi breakout |
| **Context** | Tại vùng S/R mạnh, đảo chiều | Trong trend mạnh, tiếp diễn |

## Các Loại Fakey Pattern

### 1. Fakey Thuận Xu hướng (With-Trend Fakey)

Đây là setup **mạnh nhất** và đáng tin cậy nhất:^[trading/raw/articles/priceaction-com-nial-fuller.md]

- **Bullish Fakey trong Uptrend**: Thị trường trong uptrend, xuất hiện fakey giảm (false break down) → buy
- **Bearish Fakey trong Downtrend**: Thị trường trong downtrend, xuất hiện fakey tăng (false break up) → sell

### 2. Fakey Ngược Xu hướng (Counter-Trend Fakey)

- Xuất hiện tại **key S/R level** quan trọng
- Độ tin cậy thấp hơn, cần confluence mạnh
- Thường là tín hiệu đảo chiều tại đỉnh/đáy

### 3. Fakey Trong Range (Range-Bound Fakey)

- Xuất hiện tại biên của kênh giá
- Buy fakey tại support của range
- Sell fakey tại resistance của range

## Quy trình Giao dịch Fakey

### Bước 1: Xác định Xu hướng (D1/H4)
- Uptrend: Chỉ tìm Bullish Fakey
- Downtrend: Chỉ tìm Bearish Fakey
- Range: Tìm cả hai tại biên

### Bước 2: Phát hiện Inside Bar
- Inside bar nằm gọn trong mother bar
- Ưu tiên inside bar hình thành gần key level

### Bước 3: Chờ False Break
- Giá phá vỡ inside bar
- Chờ nến TIẾP THEO đảo chiều và quay lại trong mother bar
- KHÔNG vào lệnh khi chưa có nến xác nhận

### Bước 4: Entry
- **Buy Fakey**: Đặt buy stop trên high của mother bar
- **Sell Fakey**: Đặt sell stop dưới low của mother bar
- Hoặc: Vào lệnh market khi nến đóng cửa quay lại trong mother bar (aggressive)

### Bước 5: Stop Loss
- **Buy**: Dưới low của false break bar (hoặc dưới mother bar)
- **Sell**: Trên high của false break bar (hoặc trên mother bar)

### Bước 6: Take Profit
- Target 1: Vùng S/R gần nhất
- Target 2: Vùng S/R tiếp theo
- Tỷ lệ R:R tối thiểu 1:2

## Confluence cho Fakey

Fakey có xác suất thắng cao nhất khi hội tụ với:^[trading/raw/articles/priceaction-com-nial-fuller.md]

✅ **Xu hướng rõ ràng** — fakey thuận xu hướng mạnh gấp 3 lần fakey ngược xu hướng  
✅ **Key S/R level** — fakey tại vùng giá quan trọng trên D1/H4  
✅ **Trendline touch** — giá chạm trendline và tạo fakey  
✅ **Moving Average** — fakey hình thành tại MA50/MA200  
✅ **Round number** — fakey tại các mức giá tròn (psychological levels)  

## Ví dụ Thực tế

**Bullish Fakey với Trend:**
- D1: Uptrend rõ ràng
- H4: Inside bar hình thành trong pullback
- H1: Giá false break xuống dưới inside bar, sau đó đảo chiều tăng
- Entry: Buy khi giá vượt high của mother bar
- Kết quả: Giá tiếp tục uptrend

**Bearish Fakey tại Resistance:**
- D1: Kháng cự mạnh từng giữ giá 3 lần
- H4: Inside bar hình thành sát resistance
- H1: Giá false break lên trên inside bar → đảo chiều giảm
- Entry: Sell khi giá phá low của mother bar
- Kết quả: Giá giảm về vùng hỗ trợ

## Ứng dụng Multi-Timeframe

| Timeframe | Vai trò |
|-----------|--------|
| **D1** | Xác định trend chính, key levels |
| **H4** | Phát hiện mother bar + inside bar |
| **H1/M30** | Xác nhận false break + entry trigger |

## Lỗi Thường Gặp

❌ **Vào lệnh quá sớm** — chưa có nến xác nhận reversal  
❌ **Giao dịch mọi fakey** — không có confluence, không lọc  
❌ **Bỏ qua xu hướng** — giao dịch counter-trend fakey không có key level  
❌ **SL quá chặt** — đặt SL ngay sát entry, dễ bị quét  
❌ **Không chờ inside bar** — nhầm lẫn giữa fakey và false breakout thông thường  

## So sánh với False Breakout Thông thường

| | Fakey Pattern | False Breakout Thường |
|--|--------------|---------------------|
| **Cấu trúc** | Cần Inside Bar + Mother Bar | Chỉ cần phá vỡ S/R rồi quay lại |
| **Độ tin cậy** | Cao (có inside bar = tích lũy) | Thấp hơn |
| **Entry** | Rõ ràng (break mother bar) | Không rõ ràng |
| **Xác nhận** | Nến đóng trong mother bar | Nến đóng ngược hướng breakout |

## Kết luận

Fakey pattern là một trong những mẫu hình Price Action **mạnh nhất** vì nó kết hợp:
1. Tích lũy (Inside Bar)
2. Bẫy (False Break)
3. Đảo chiều có xác nhận

Đây là pattern được Nial Fuller — một trong những chuyên gia Price Action hàng đầu thế giới — đặc biệt nhấn mạnh trong hệ thống giao dịch của ông.

## Xem thêm

- [[price-action]] - Tổng quan Price Action
- [[candlestick-patterns]] - Các mô hình nến
- [[price-action-confluence]] - Sự hội tụ trong Price Action
- [[multi-timeframe-forex]] - Chiến lược đa khung thời gian