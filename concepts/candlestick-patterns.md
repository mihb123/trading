---
title: Mô hình Nến (Candlestick Patterns) trong Giao dịch Forex
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [candlesticks, price-action, technical-analysis, forex, trading-strategy, reference]
sources: [raw/articles/wikipedia-candlestick-pattern.md, raw/articles/tradingstrategyguides-price-action.md, raw/articles/wikipedia-price-action-trading.md]
confidence: high
---

# Mô hình Nến (Candlestick Patterns) trong Giao dịch

## Lịch sử

Phân tích nến (candlestick analysis) có nguồn gốc từ Nhật Bản thế kỷ 18. **Munehisa Homma** (1724-1803), một thương nhân gạo từ Sakata, đã phát triển phương pháp theo dõi giá gạo bằng biểu đồ nến tại chợ Dojima ở Osaka. Ông xuất bản tác phẩm *"The Fountain of Gold — The Three Monkey Record of Money"* năm 1755. Tính theo thời giá hiện tại, ông đã kiếm được khoảng $10 tỷ USD.^[raw/articles/wikipedia-candlestick-pattern.md]

Steve Nison là người đã phổ biến biểu đồ nến Nhật ra thế giới phương Tây qua cuốn sách *"Japanese Candlestick Charting Techniques"*.

## Cấu trúc Một Cây Nến

Mỗi cây nến (candlestick) thể hiện 4 mức giá trong một khoảng thời gian:

```
        HIGH (cao nhất)
         |
    ┌────┴────┐  ← Upper Wick / Shadow (bóng trên)
    │  THÂN   │  ← Real Body (thân nến)
    │ (Body)  │     OPEN → CLOSE hoặc CLOSE → OPEN
    └────┬────┘  ← Lower Wick / Shadow (bóng dưới)
         |
        LOW (thấp nhất)
```

- **Thân nến (Body)**: Khoảng giữa giá mở cửa (Open) và đóng cửa (Close)
  - Thân xanh/trắng: Close > Open (tăng)
  - Thân đỏ/đen: Close < Open (giảm)
- **Bóng nến (Wick/Shadow)**: Đường mảnh trên và dưới thân
  - Bóng trên: High - Max(Open, Close)
  - Bóng dưới: Min(Open, Close) - Low

## Phân loại Mô hình Nến

Có **42 mẫu hình nến được công nhận**, chia thành 2 nhóm:^[raw/articles/wikipedia-candlestick-pattern.md]

### Nhóm Đơn giản (Simple Patterns) — 1-2 nến

### Nhóm Phức tạp (Complex Patterns) — 3+ nến

---

# Các Mô hình Nến Đảo chiều Tăng (Bullish Reversal)

## 1. Hammer (Búa)

```
    ┌─┐
    │ │ ← Thân nhỏ ở trên, màu không quan trọng
    │ │
    │ │
    │ │ ← Bóng dưới dài (≥ 2x thân)
    └─┘
```

- **Vị trí**: Xuất hiện ở **đáy** của xu hướng giảm
- **Đặc điểm**: Thân nhỏ nằm ở phía trên, bóng dưới dài gấp 2-3 lần thân, bóng trên rất nhỏ hoặc không có
- **Tâm lý**: Giá giảm mạnh nhưng bị lực mua đẩy ngược lên → đảo chiều tăng
- **Độ tin cậy**: Cần xác nhận bằng nến tăng tiếp theo
- **Timeframe tốt nhất**: H1, H4, D1

## 2. Bullish Engulfing (Nến Bao phủ Tăng)

```
    ┌──┐
    │  │ ← Nến 2: Thân xanh LỚN, bao phủ toàn bộ nến 1
    │  │┌┐
    │  │└┘
    └──┘
```

- **Cấu trúc**: Nến 1 giảm (đỏ/đen), Nến 2 tăng (xanh) có thân bao phủ HOÀN TOÀN thân nến 1
- **Vị trí**: Đáy của xu hướng giảm, hoặc tại vùng hỗ trợ mạnh
- **Tâm lý**: Phe bán đang thắng bỗng bị phe mua áp đảo hoàn toàn trong 1 nến
- **Độ tin cậy**: Rất cao khi xuất hiện tại key support với volume tăng

## 3. Piercing Line (Đường Xuyên)

```
    ┌─┐
    │ │ ← Nến 2: Thân xanh, mở cửa THẤP HƠN đáy nến 1
    │ │┌┐   và đóng cửa TRÊN 50% thân nến 1
    │ │││
    └─┘││
       └┘ ← Nến 1: Thân đỏ
```

- **Vị trí**: Đáy của xu hướng giảm
- **Đặc điểm**: Nến 2 mở cửa thấp hơn đáy nến 1 (gap down), nhưng đóng trên 50% thân nến 1
- **So sánh**: Yếu hơn Bullish Engulfing (vì không bao phủ toàn bộ), nhưng vẫn là tín hiệu mạnh

## 4. Morning Star (Sao Mai)

```
    ┌─┐
    │ │ ← Nến 3: Thân xanh lớn, đóng TRÊN 50% nến 1
    │ │
 ┌┐ │ │
 ││ │ │   ← Nến 2: Doji hoặc nến nhỏ (gap down)
 └┘ │ │
    │ │
┌─┐ │ │
│ │ │ │ ← Nến 1: Thân đỏ lớn
└─┘ └─┘
```

- **Cấu trúc 3 nến**: Nến 1 giảm → Nến 2 (ngôi sao) nhỏ, gap down → Nến 3 tăng mạnh
- **Vị trí**: Đáy của xu hướng giảm kéo dài
- **Độ tin cậy**: Rất cao — 3 nến xác nhận đảo chiều

---

# Các Mô hình Nến Đảo chiều Giảm (Bearish Reversal)

## 5. Shooting Star (Sao Băng)

```
         ┌─┐
         │ │ ← Bóng trên dài (≥ 2x thân)
         │ │
    ┌─┐  │ │
    │ │  │ │
    └─┘  │ │ ← Thân nhỏ ở dưới
```

- **Vị trí**: Xuất hiện ở **đỉnh** của xu hướng tăng
- **Đặc điểm**: Thân nhỏ nằm ở dưới, bóng trên dài 2-3 lần thân
- **Tâm lý**: Giá tăng mạnh nhưng bị lực bán đẩy ngược xuống → đảo chiều giảm
- **Lưu ý**: Là phiên bản ngược của Hammer

## 6. Hanging Man (Người Treo)

```
    ┌─┐
    │ │ ← Thân nhỏ ở trên
    │ │
    │ │ ← Bóng dưới dài
    │ │
    └─┘
```

- **Vị trí**: Xuất hiện ở **đỉnh** của xu hướng tăng (khác Hammer ở vị trí!)
- **Hình dạng**: Giống hệt Hammer
- **Ý nghĩa**: Trong uptrend, lực mua đang yếu dần — báo hiệu đảo chiều
- **Cần xác nhận**: Phải có nến giảm theo sau để xác nhận

## 7. Bearish Engulfing (Nến Bao phủ Giảm)

```
┌──┐
│  │┌┐ ← Nến 1: Thân xanh nhỏ
│  │││
│  │└┘
│  │ ← Nến 2: Thân đỏ LỚN, bao phủ toàn bộ nến 1
└──┘
```

- **Cấu trúc**: Nến 1 tăng (xanh), Nến 2 giảm (đỏ) bao phủ HOÀN TOÀN thân nến 1
- **Vị trí**: Đỉnh của xu hướng tăng
- **Tâm lý**: Phe mua đang thắng bỗng bị phe bán áp đảo hoàn toàn

## 8. Evening Star (Sao Hôm)

```
┌─┐
│ │ ← Nến 1: Thân xanh lớn
└─┘
┌─┐┌┐
│ │││ ← Nến 2: Doji hoặc nến nhỏ (gap up)
└─┘└┘
    ┌─┐
    │ │ ← Nến 3: Thân đỏ lớn, đóng DƯỚI 50% nến 1
    │ │
    └─┘
```

- **Cấu trúc 3 nến**: Nến 1 tăng → Nến 2 (ngôi sao) nhỏ, gap up → Nến 3 giảm mạnh
- **Vị trí**: Đỉnh của xu hướng tăng kéo dài
- **Độ tin cậy**: Rất cao — phiên bản ngược của Morning Star

---

# Các Mô hình Nến Tiếp diễn (Continuation)

## 9. Marubozu (Nến Trọc)

```
┌──────────┐
│          │ ← Thân LỚN, KHÔNG có bóng
│          │
│          │
│          │
└──────────┘
```

- **Đặc điểm**: Nến có thân lớn, không có bóng ở cả 2 đầu
- **Bullish Marubozu**: Thân xanh, mở = low, đóng = high → mua liên tục
- **Bearish Marubozu**: Thân đỏ, mở = high, đóng = low → bán liên tục
- **Ý nghĩa**: Momentum cực mạnh, xu hướng còn tiếp diễn

## 10. Three White Soldiers (Ba Chàng Lính Trắng)

```
┌─┐ ┌─┐ ┌─┐
│ │ │ │ │ │ ← 3 nến xanh liên tiếp
│ │ │ │ │ │   Mỗi nến mở trong thân nến trước
│ │ │ │ │ │   Mỗi nến đóng cao hơn nến trước
└─┘ └─┘ └─┘
```

- **Cấu trúc**: 3 nến tăng liên tiếp, mỗi nến mở cửa trong thân nến trước và đóng cao hơn
- **Vị trí**: Sau pullback trong uptrend, hoặc breakout từ consolidation
- **Ý nghĩa**: Áp lực mua liên tục, tích lũy không nghỉ → trend tiếp diễn

## 11. Rising Three Methods (Ba Bước Tăng)

```
┌─┐         ┌─┐
│ │ ┌┐┌┐┌┐ │ │ ← Nến 1 & 5: Xanh lớn
│ │ ││││││ │ │ ← Nến 2-4: Đỏ nhỏ, không phá đáy nến 1
│ │ └┘└┘└┘ │ │
└─┘         └─┘
```

- **Cấu trúc 5 nến**: Nến 1 tăng mạnh → Nến 2-4 giảm nhẹ (không phá vỡ đáy nến 1) → Nến 5 tăng mạnh
- **Ý nghĩa**: Tạm nghỉ trong uptrend (consolidation nhẹ), sau đó tiếp tục tăng

---

# Các Mô hình Nến Doji (Lưỡng lự)

## 12. Doji

```
    ──
    ││ ← Thân cực nhỏ (Open ≈ Close)
    ││
```

- **Đặc điểm**: Open và Close gần như bằng nhau → thân rất nhỏ hoặc không có
- **Ý nghĩa**: Cân bằng tuyệt đối giữa mua và bán → thị trường lưỡng lự

### Các Biến thể Doji:

| Loại Doji | Đặc điểm | Ý nghĩa |
|-----------|---------|---------|
| **Dragonfly Doji** | Bóng dưới dài, không bóng trên | Đảo chiều tăng tiềm năng |
| **Gravestone Doji** | Bóng trên dài, không bóng dưới | Đảo chiều giảm tiềm năng |
| **Long-legged Doji** | Bóng trên và dưới đều dài | Cực kỳ lưỡng lự |
| **Four Price Doji** | Open=High=Low=Close | Rất hiếm, thị trường đóng băng |

---

# Bảng Tổng hợp Nhanh

| Mô hình | Loại | Số nến | Vị trí | Độ tin cậy |
|---------|------|--------|--------|-----------|
| Hammer | Bullish Reversal | 1 | Đáy downtrend | ⭐⭐⭐ |
| Bullish Engulfing | Bullish Reversal | 2 | Đáy downtrend | ⭐⭐⭐⭐ |
| Morning Star | Bullish Reversal | 3 | Đáy downtrend | ⭐⭐⭐⭐⭐ |
| Shooting Star | Bearish Reversal | 1 | Đỉnh uptrend | ⭐⭐⭐ |
| Bearish Engulfing | Bearish Reversal | 2 | Đỉnh uptrend | ⭐⭐⭐⭐ |
| Evening Star | Bearish Reversal | 3 | Đỉnh uptrend | ⭐⭐⭐⭐⭐ |
| Marubozu | Continuation | 1 | Trong trend | ⭐⭐⭐ |
| Three White Soldiers | Continuation | 3 | Sau pullback | ⭐⭐⭐⭐ |
| Doji | Indecision | 1 | Bất kỳ | ⭐⭐ (cần context) |

---

## Nguyên tắc Giao dịch với Mô hình Nến

### 1. KHÔNG giao dịch mô hình đơn lẻ

Một mô hình nến đứng một mình KHÔNG đủ để vào lệnh. Luôn cần:
- ✅ Vị trí (location) — tại S/R quan trọng? Trong xu hướng?
- ✅ Confluence — ít nhất 2-3 yếu tố xác nhận
- ✅ Timeframe context — phù hợp với xu hướng khung lớn hơn?

### 2. Chờ nến đóng cửa (Wait for Close)

Nến đang hình thành có thể thay đổi. Một cây trông giống Pin Bar lúc đang chạy có thể đóng cửa thành nến bình thường. **Luôn chờ nến đóng trước khi vào lệnh.**

### 3. Kết hợp với Volume

- Volume cao tại nến đảo chiều = xác nhận mạnh
- Volume thấp = tín hiệu yếu, có thể là false signal

### 4. Context là Tất cả

Cùng một mô hình, vị trí khác nhau → ý nghĩa khác nhau:
- Bullish Engulfing ở đáy downtrend + hỗ trợ D1 = **tín hiệu mạnh**
- Bullish Engulfing ở giữa range = **vô nghĩa**
- Hammer ở đỉnh uptrend = **Hanging Man** → bearish!

### 5. Timeframe quan trọng

- **D1/W1**: Mô hình mạnh nhất, ít false signal
- **H4**: Rất tốt cho swing trading
- **H1**: Cần confluence chặt chẽ
- **<M30**: Quá nhiều nhiễu, không đáng tin cậy

---

## Ứng dụng trong Multi-Timeframe

Kết hợp mô hình nến với [[multi-timeframe-forex]]:

1. **D1**: Xác định mô hình nến đảo chiều lớn (Morning/Evening Star, Engulfing)
2. **H4**: Tìm vùng giá nơi mô hình xuất hiện
3. **H1/M30**: Chờ mô hình nến nhỏ hơn xác nhận entry (Pin Bar tại vùng đó)

## Xem thêm

- [[price-action]] - Phương pháp Price Action tổng quan
- [[multi-timeframe-forex]] - Chiến lược đa khung thời gian
- [[market-structure]] - Cấu trúc thị trường
- [[price-action-vs-indicators]] - So sánh với indicators