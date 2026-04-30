---
title: Cable M15 — Chiến lược GBP/USD (H4 → H1 → M15)
type: concept
created: 2026-04-30
updated: 2026-04-30
tags: [trading-strategy, price-action, multi-timeframe, trend-analysis, support-resistance, confluence, scalping, entry-technique, forex, volatility]
sources: [trading/strategies/m15-trend-confluence-idea.md, trading/entities/gbpusd-cable.md, trading/raw/articles/fastcashforex-gbpusd-trading-guide.md, trading/concepts/trend-following.md, trading/concepts/price-action.md]
confidence: high
---

# Cable M15 — Chiến lược GBP/USD Tối ưu

## Chiến lược này dành cho ai

Bạn giao dịch **GBP/USD** là chính. Bạn dùng khung **M15** để vào lệnh. Bạn muốn mỗi
ngày có 1-2 cơ hội chất lượng, không phải ngồi canh chart 8 tiếng. Chiến lược này được
thiết kế riêng cho **Cable** — tận dụng từng đặc điểm của cặp tiền này.

## Tại sao GBP/USD lại đặc biệt?

GBP/USD không giống EUR/USD. Nó giống một con ngựa hoang — chạy nhanh, đổi hướng
gấp, nhưng khi đã vào guồng thì phi thẳng. Hiểu được "tính cách" của nó là 50% chiến thắng:

| Đặc điểm | Con số | Ý nghĩa cho trader |
|----------|--------|-------------------|
| Biến động/ngày | 100-150 pips | Đủ rộng để R:R 1:2 với SL 35-50 pips |
| Thời gian trong trend | 40-50% | Trend following là chiến lược số 1 |
| Spread | 0.5-2.0 pips | Chấp nhận được, rẻ hơn mức biến động mang lại |
| Khung giờ vàng | London + NY overlap | 8h-12h ET — thanh khoản cao nhất |
| Tôn trọng S/R | Rất mạnh | Round numbers 00, 50 luôn được test kỹ |
| Ít fakeout | Hơn EUR/USD | Pattern breakout thường chạy sạch |

^[trading/entities/gbpusd-cable.md]

## Ba câu hỏi — Ba lớp lọc

Trước mỗi lệnh, bạn chỉ cần trả lời 3 câu hỏi. Nếu cả 3 đều "CÓ" → vào lệnh.
Chỉ 1 câu "KHÔNG" → đứng ngoài.

```
CÂU 1: XU HƯỚNG H4 ĐANG ĐI ĐÂU?     ← Nhìn H4, 30 giây
CÂU 2: GIÁ CÓ ĐANG Ở VÙNG ĐẸP?      ← Nhìn H4/D1, 20 giây
CÂU 3: M15 CÓ TÍN HIỆU VÀO CHƯA?     ← Nhìn M15, 20 giây
                                      → Tổng: hơn 1 phút để quyết định
```

### Câu 1: Xu hướng H4 đang đi đâu? (Trend Filter)

Đây là câu hỏi quan trọng nhất. GBP/USD khi có trend thì chạy rất xa — bạn chỉ
muốn đi CÙNG chiều với nó.

**Bước 1: Mở chart H4, kéo đường MA200**

- Giá **TRÊN** MA200 → chỉ tìm lệnh MUA
- Giá **DƯỚI** MA200 → chỉ tìm lệnh BÁN
- Giá lưỡng lự quanh MA200 → không giao dịch, đợi rõ hơn

> Mẹo: Không cần nhìn MA200 mỗi nến. Mỗi 4 tiếng kiểm tra 1 lần là đủ.

**Bước 2: Nhìn cấu trúc đỉnh/đáy H4**

Trên H4, tìm 2 đỉnh và 2 đáy gần nhất (trong ~50 nến = khoảng 2 tuần):

- Đỉnh sau **cao hơn** đỉnh trước + Đáy sau **cao hơn** đáy trước → UPTREND mạnh
- Chỉ 1 trong 2 điều kiện đúng → UPTREND yếu (vẫn trade được, nhưng cẩn thận)
- Không rõ ràng → SIDEWAY → không trade

> Tưởng tượng: Vẽ 4 điểm trên chart. Nếu nối chúng lại thành bậc thang đi lên → xu hướng tăng.

**Bước 3: Kiểm tra H1 xác nhận**

GBP/USD hay có những cú giật ngược. H1 giúp bạn tránh bị lừa:

- H1 phải CÙNG HƯỚNG với H4 (H4 tăng + H1 tăng = OK)
- Giá H1 phải trên MA20 (nếu uptrend) hoặc dưới MA20 (nếu downtrend)

> Nếu H4 tăng mà H1 đang giảm → đó là pullback. KHÔNG bán. Đợi H1 quay lại rồi mua.

**Tóm tắt Câu 1 bằng hình ảnh:**

```
H4: Giá > MA200? ─── KHÔNG ──→ ĐỨNG NGOÀI
      │
      CÓ
      │
H4: HH/HL rõ? ──── KHÔNG ──→ ĐỨNG NGOÀI
      │
      CÓ
      │
H1: Cùng hướng H4? ─ KHÔNG ─→ Đợi pullback kết thúc
      │
      CÓ
      │
      ▼
  → QUA CÂU 2
```

### Câu 2: Giá có đang ở vùng đẹp? (S/R Zone)

GBP/USD cực kỳ tôn trọng các vùng giá quan trọng. Một tín hiệu ở đúng vùng giá trị
gấp 10 lần tín hiệu ở giữa "biển".

**Vùng đẹp là gì?**

1. **Đỉnh/đáy cũ trên H4** — nơi giá từng quay đầu 2 lần trở lên
2. **Các mức tròn (00, 50)** — 1.3000, 1.3050, 1.3100... GBP/USD cực mê mấy số này
3. **Vùng S/R trên D1** — quan trọng nhưng hiếm khi chạm

Không cần vẽ chính xác từng pip. Lấy 1 vùng rộng ~30% ATR H4 quanh mức đó.

**Kiểm tra nhanh:** Giá hiện tại có cách 1 vùng S/R nào ≤ 0.5 ATR(H4) không?
- Nếu **CÓ** → qua Câu 3
- Nếu **KHÔNG** → không giao dịch, dù tín hiệu M15 có đẹp đến mấy

> Ví dụ: ATR H4 = 40 pips. Bạn tìm vùng S/R quanh 1.3000. Nếu giá đang ở 1.2990
> (cách 10 pips < 20 pips = 0.5 ATR) → đang trong vùng.

### Câu 3: M15 có tín hiệu vào chưa? (PA Trigger)

Đây là lúc bạn "bấm cò". Chỉ 4 tín hiệu — không hơn, để giữ mọi thứ đơn giản:

**Tín hiệu 1: Engulfing (nến nuốt nến) ⭐ Quan trọng nhất**

- Nến M15 màu xanh nuốt trọn nến đỏ trước đó (cả thân lẫn râu) → tín hiệu MUA
- Nến M15 màu đỏ nuốt trọn nến xanh trước đó → tín hiệu BÁN
- **Điều kiện**: Nến engulfing phải to — thân nến ≥ 50% chiều dài cả cây. Nến bé xíu nuốt nến bé xíu → bỏ qua.
- **Vào lệnh**: Ngay khi nến M15 đóng cửa

> Hình dung: Nến trước là người lùn, nến sau là người khổng lồ đứng che khuất hoàn toàn.

**Tín hiệu 2: Pin Bar (nến búa / nến sao băng)**

- **MUA**: Nến có râu dưới dài (≥ 60% cây nến), thân nhỏ (≤ 30%), râu trên ngắn, và nến đóng cửa TĂNG
- **BÁN**: Nến có râu trên dài, thân nhỏ, râu dưới ngắn, nến đóng cửa GIẢM
- **Râu phải hướng về vùng S/R** — nếu râu chỉ ra ngoài S/R nghĩa là giá đã từ chối vùng đó
- **Entry**: Đặt limit order ở 50% của cây pin bar, hoặc đợi nến tiếp theo phá qua đỉnh/đáy

> Hình dung: Giá cố đâm thủng vùng S/R nhưng bị đẩy ngược lại — cái râu dài là vết tích của cuộc chiến.

**Tín hiệu 3: Inside Bar Breakout (nến nằm trong → phá vỡ)**

- Nến hiện tại nằm gọn trong nến trước (high thấp hơn, low cao hơn)
- Nến tiếp theo **phá vỡ** lên trên (MUA) hoặc xuống dưới (BÁN)
- Nến "mẹ" phải to (range > ATR M15)
- **Entry**: Stop order ngay trên/sát dưới mức phá vỡ

> Hình dung: Thị trường đang nín thở (nến nằm trong). Khi nó thở ra → bùng nổ theo 1 hướng.

**Tín hiệu 4: Momentum Candle (nến trọc — gần như không có râu)**

- Nến M15 body ≥ 80% range (gần như không có râu trên lẫn râu dưới)
- Xuất hiện sau 1 nhịp pullback nhẹ về MA20 M15
- **Vào lệnh**: Ngay khi nến đóng
- Đây là tín hiệu trend CỰC MẠNH — giá đi 1 mạch không ai cản

> Hình dung: Đây là nến "tàu hỏa" — không có râu nghĩa là không ai dám đứng ngược lại.

**Tín hiệu nào bị LOẠI:**

- ❌ Pin bar có râu 2 bên (doji) — không rõ bên nào thắng
- ❌ Inside bar không có nến mẹ rõ ràng (nến mẹ phải to)
- ❌ Engulfing mà nến mới chỉ to hơn tí xíu — phải nuốt trọn rõ ràng
- ❌ Bất kỳ nến nào có spread > 3 pips trên GBP/USD — spread rộng = thanh khoản kém

## Cách đặt SL và TP cho GBP/USD

Đây là phần khác biệt quan trọng so với chiến lược chung. GBP/USD cần số cụ thể:

### Stop Loss

- Đặt SL **dưới đáy gần nhất** trên M15 (nếu MUA) hoặc **trên đỉnh gần nhất** (nếu BÁN)
- Cộng thêm buffer 5-8 pips (GBP/USD hay quét râu)
- **Con số điển hình**: 35-50 pips cho Cable M15

> Tại sao rộng hơn EUR/USD? Vì GBP/USD biến động hơn 60%. SL 25 pips trên EUR/USD
> tương đương SL 40 pips trên Cable.

### Take Profit

- **TP1 (2R)**: Đóng 50% vị thế. Move SL về hòa vốn.
  - Nếu SL = 40 pips → TP1 = 80 pips
- **TP2 (3R+)**: Để phần còn lại chạy. Hoặc chốt tại vùng S/R tiếp theo.
  - Nếu SL = 40 pips → TP2 = 120+ pips

### R:R phải ≥ 1:2

Nếu SL = 40 pips mà vùng S/R gần nhất chỉ cách 50 pips → R:R = 1.25 — **KHÔNG VÀO**.
Tìm setup khác hoặc đợi giá chạy xa hơn.

## Khi nào giao dịch — khi nào nghỉ

### ✅ VÀO LỆNH khi:

- Giờ London (3h-12h ET) hoặc London-NY overlap (8h-12h ET)
- Có đủ 3 lớp lọc (trend + zone + trigger)
- R:R ≥ 1:2

### ❌ KHÔNG GIAO DỊCH khi:

**Liên quan đến giờ giấc:**
- Asian session (19h-4h ET) — Cable ngủ, spread rộng, PA giả
- Ngày nghỉ ngân hàng UK — thanh khoản = 0
- Thứ 6 sau 12h ET — ai cũng đóng lệnh về cuối tuần
- 30 phút trước/sau tin tier-1 (NFP, BoE, CPI, FOMC)

**Liên quan đến thị trường:**
- H4 đang sideway (không có HH/HL rõ)
- H1 ngược hướng H4 và chưa có dấu hiệu quay lại
- Không có vùng S/R nào trong tầm (giá lơ lửng giữa trời)
- Spread > 3 pips

**Liên quan đến tài khoản:**
- Đã thua 5% tài khoản trong ngày (trừ khi tk < $200)
- Đang thua 4 lệnh liên tiếp
- Đang giữ EUR/USD cùng hướng (tương quan 0.85+ → rủi ro gấp đôi)

## Quy tắc quản lý vốn cho Cable

### 1. Rủi ro mỗi lệnh: 1%

```
Nếu tài khoản = $1,000
→ Rủi ro mỗi lệnh = $10
→ SL = 40 pips → Lot size = 0.025 (tính theo công thức)
```

Đừng cố định lot. Khi thắng → lot tự tăng. Khi thua → lot tự giảm. Đây là cách duy nhất
để tài khoản sống sót qua những ngày Cable "điên".

### 2. Daily Loss Limit: 5%

Nếu thua tổng cộng 5% tài khoản → tắt MT5, đi uống cà phê.
Ngày mai thị trường vẫn ở đó.

**Ngoại lệ**: Tài khoản dưới $200 → bỏ qua quy tắc này, chỉ giữ 1% risk/lệnh.

### 3. Chuỗi thua

- Thua 2 lệnh liên tiếp → giảm risk còn 0.5%/lệnh
- Thua 4 lệnh liên tiếp → nghỉ hết ngày, xem lại nhật ký
- Thắng 1 lệnh → reset chuỗi, risk về 1%

## Scoring — Chấm điểm nhanh trước khi vào

Mỗi lệnh cần **tối thiểu 4/7 điểm**. Bắt buộc có Trend (2 điểm).

| Điều kiện | Điểm |
|-----------|------|
| H4: Giá cùng phía MA200 | 2 |
| H4: HH/HL rõ ràng | 1 |
| H1: Cùng hướng + trên MA20 | 1 |
| Giá trong vùng S/R | 1 |
| Có PA trigger M15 rõ | 1 |
| Gần Round Number (00, 50) | +1 |
| **R:R ≥ 1:2** | Bắt buộc (không tính điểm) |

**Ví dụ chấm điểm thực tế:**

```
GBP/USD, 10:30 AM ET (LDN-NY overlap)
H4: Giá > MA200 ✓ (2 điểm)
H4: HH cao hơn + HL cao hơn ✓ (1 điểm)
H1: Cùng hướng H4, trên MA20 ✓ (1 điểm)
S/R: Giá cách 1.3050 ~15 pips (ATR H4=40, 0.5×40=20) ✓ (1 điểm)
M15: Bullish Engulfing rõ nét ✓ (1 điểm)
Round Number: 1.3050 là mức 50 ✓ (+1 điểm)
R:R = 40:100 = 1:2.5 ✓

→ TỔNG: 7/7 → VÀO LỆNH MUA
```

## Ví dụ thực tế một ngày giao dịch Cable

```
09:00 ET — Mở MT5, kiểm tra H4: GBP/USD trên MA200, HH/HL rõ → UPTREND
09:05 ET — Kiểm tra H1: Đang pullback nhẹ, chưa quay lại → ĐỢI
10:30 ET — H1 quay lại tăng, giá gần 1.3050 (round number + S/R H4 cũ)
10:45 ET — M15 xuất hiện Engulfing tăng, body to rõ → VÀO LỆNH MUA
         Entry: 1.3055 | SL: 1.3015 (40 pips = dưới đáy M15) | TP1: 1.3135 (80 pips)
12:15 ET — Giá chạm TP1 → đóng 50%, SL về hòa
15:30 ET — Giá chạm 1.3175 → đóng nốt 50% (TP2 = 120 pips = 3R)
         → Tổng lợi nhuận: 50%×2R + 50%×3R = 2.5R
```

## Những "tuyệt chiêu" Cable mà trader hay bỏ qua

### 1. Kiểm tra lịch nghỉ UK mỗi sáng

Ngày nghỉ ngân hàng UK = Cable chết. Đừng phí thời gian. Google "UK bank holidays 2026".

### 2. 1.3000 là mẹ của mọi vùng S/R

GBP/USD yêu số 1.3000 như cá yêu nước. Mỗi lần giá chạm 1.3000, hãy đặc biệt chú ý —
đây thường là nơi trend đảo chiều hoặc breakout lớn.

### 3. Đừng vừa ôm EUR/USD vừa ôm GBP/USD cùng hướng

Tương quan 0.85-0.90 nghĩa là nếu bạn LONG cả 2, bạn đang đánh cược 2 lần vào cùng 1
kết quả (USD yếu đi). Nếu USD bất ngờ mạnh lên, bạn thua cả 2 lệnh cùng lúc.

### 4. Tin BoE là "vua" của mọi tin

Thứ 5 tuần đầu mỗi tháng (thường), BoE công bố lãi suất. Cable có thể chạy 150-300 pips
trong 30 phút. Đừng giao dịch 30 phút trước/sau tin này.

### 5. Cable ghét thứ 6

Thứ 6 chiều (sau 12h ET), thanh khoản cạn vì trader đóng lệnh về weekend. Setup thứ 6
chiều có PA đẹp đến mấy cũng nên bỏ qua. Thứ 2 đầu phiên cũng vậy — thị trường chưa "tỉnh".

## So sánh: Cable M15 vs M15 Trend Confluence chung

| Tiêu chí | M15 Chung (generic) | Cable M15 (GBP/USD) |
|----------|--------------------|--------------------|
| Cặp tiền | Mọi cặp | **Chỉ GBP/USD** |
| SL M15 | Theo ATR | **35-50 pips cố định** |
| TP1 | 2R | **70-100 pips** |
| Session | LDN hoặc NY | **LDN bắt buộc, overlap ưu tiên** |
| Spread max | 30 points | **3 pips (Cable-specific)** |
| Round numbers | +1 điểm | **+1 điểm, nhưng chú trọng 00 và 50** |
| News filter | Tier-1 US | **Cả UK (4h30 ET) và US (8h30 ET)** |
| Ngày nghỉ | Không check | **Check UK bank holidays** |
| Correlation | Không check | **Cảnh báo nếu đang giữ EUR/USD cùng hướng** |

## Checklist 30 giây mỗi sáng

Trước khi bắt đầu phiên giao dịch:

- [ ] Hôm nay có phải ngày nghỉ UK không?
- [ ] Hôm nay có tin BoE / NFP / CPI / FOMC không?
- [ ] H4 trend đang hướng nào?
- [ ] Có vùng S/R nào gần giá không?

Trước mỗi lệnh:

- [ ] 3 câu hỏi lọc đều trả lời "CÓ"?
- [ ] R:R ≥ 1:2?
- [ ] Chưa chạm daily loss 5%?
- [ ] Không đang giữ EUR/USD cùng hướng?

## Tổng kết: 1 câu để nhớ

> **GBP/USD là con ngựa hoang. Đừng cố thuần nó. Hãy cưỡi nó khi nó đang phi, và xuống khi nó đang đứng yên.**

## Xem thêm

- [[gbpusd-cable]] — Tất tần tật về GBP/USD: đặc điểm, session, spread, correlation
- [[m15-trend-confluence-idea]] — Chiến lược M15 gốc (tổng quát cho mọi cặp)
- [[m15-trend-confluence-mql5]] — Code MQL5 (sẽ cập nhật thêm phần Cable-specific)
- [[trinity-confluence-idea]] — Trinity gốc (D1+H1, swing trading)
- [[trend-following]] — Trend following framework cho Cable
- [[support-resistance-zones]] — Cách xác định S/R — GBP/USD tôn trọng mạnh
- [[price-action]] — 4 tín hiệu PA chi tiết
- [[multi-timeframe-forex]] — Lý thuyết MTF (H4→H1→M15)
