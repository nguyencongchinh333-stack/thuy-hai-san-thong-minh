# NextFarm DIY Controller — Thủy Hải Sản Thông Minh v1.0
### Hệ Thống Điều Khiển Ao Nuôi Tự Động — Tích Hợp NextFarm Platform

---

# PHẦN 1 — TỔNG QUAN

## 1.1 Áp Dụng Cho Các Mô Hình Nuôi

| Mô hình | Đặc thù điều khiển | Phù hợp |
|---------|------------------|---------|
| **Tôm thẻ chân trắng** | DO cực kỳ quan trọng, độ mặn, pH, quạt sục khí 24/7 | ✅ Rất phù hợp |
| **Cá tra / Cá basa** | Sục khí, cho ăn tự động, quản lý mực nước | ✅ Rất phù hợp |
| **Cá rô phi / Điêu hồng** | Sục khí, nhiệt độ, cho ăn tự động | ✅ Phù hợp |
| **Cá lóc / Cá trê** | Sục khí nhẹ, mật độ cao, cho ăn | ✅ Phù hợp |
| **Tôm sú** | DO + pH + độ mặn, quản lý chặt hơn tôm thẻ | ⚠️ Cần thêm cảm biến |

---

## 1.2 Tại Sao DO (Oxy Hòa Tan) Là Ưu Tiên Số 1

```
DO (mg/L)     Tình trạng ao            Hành động
─────────────────────────────────────────────────────────
> 6.0         Lý tưởng                 Duy trì bình thường
5.0 – 6.0     Bình thường              Theo dõi
4.0 – 5.0     ⚠️ CẢNH BÁO              Bật thêm quạt sục khí
3.0 – 4.0     🔴 NGUY HIỂM             Bật TẤT CẢ quạt + blower
< 3.0         🚨 KHẨN CẤP              Báo động app + O2 bình khẩn cấp
< 2.0         💀 TÔM/CÁ CHẾT HÀNG LOẠT Mất trắng trong vài giờ
─────────────────────────────────────────────────────────
Tôm thẻ: Cần DO > 5.0 mg/L (chặt hơn cá)
Cá tra:  Cần DO > 3.5 mg/L
```

> **Đặc thù thủy sản**: DO có thể giảm mạnh vào ban đêm (tảo ngừng quang hợp + vi sinh tiêu thụ oxy). Nguy hiểm nhất: 2:00–5:00 sáng.

---

## 1.3 Thiết Bị Điều Khiển (31 Kênh Relay)

| # | Kênh | Thiết Bị | Điện Áp | Quan Trọng |
|:-:|:----:|---------|---------|-----------|
| 1 | K0 | Bơm cấp nước chính | 220VAC | ⭐⭐⭐ |
| 2 | K1–K4 | Quạt sục khí cánh quạt ×4 | 220VAC | ⭐⭐⭐ Bắt buộc |
| 3 | K5–K6 | Máy thổi khí (air blower) ×2 | 220VAC | ⭐⭐⭐ |
| 4 | K7 | Bơm thải/xả nước | 220VAC | ⭐⭐ |
| 5 | K8 | Bơm tuần hoàn nội bộ | 220VAC | ⭐⭐ |
| 6 | K9 | Bơm châm vôi Ca(OH)₂ | 220VAC | ⭐⭐ |
| 7 | K10 | Bơm châm khoáng/hóa chất | 220VAC | ⭐⭐ |
| 8 | K11 | Đèn UV khử trùng nước cấp | 220VAC | ⭐⭐ |
| 9 | K12 | Bộ sưởi nước (heater) | 220VAC | ⭐ (mùa lạnh) |
| 10 | K13–K14 | Đèn chiếu sáng ao ban đêm | 220VAC | ⭐ |
| 11 | K15 | **Dự phòng** | — | — |
| 12 | K16–K19 | Van cấp nước từ ao lắng 1–4 | 24VAC | ⭐⭐ |
| 13 | K20–K23 | Máy cho ăn tự động ×4 | 220VAC | ⭐⭐⭐ |
| 14 | K24–K25 | Van xả đáy ao | 220VAC | ⭐⭐ |
| 15 | K26 | Bơm sục khí dự phòng khẩn cấp | 220VAC | ⭐⭐⭐ |
| 16 | K27 | Van cấp O₂ bình khẩn cấp | 220VAC | ⭐⭐⭐ |
| 17 | K28–K29 | Van xả thải + Bơm bùn đáy | 220VAC | ⭐ |
| 18 | K30–K31 | Dự phòng mở rộng | — | — |

---

## 1.4 Cảm Biến Đầu Vào

| # | Cảm Biến | Đo | Giá TL | Giá TT | Quan Trọng |
|:-:|---------|---|-------:|-------:|-----------|
| 1 | **DO — Oxy hòa tan** | mg/L | 850.000đ | 1.200.000đ | ⭐⭐⭐ Bắt buộc |
| 2 | **pH nước** | 0–14 | 250.000đ | 450.000đ | ⭐⭐⭐ Bắt buộc |
| 3 | Nhiệt độ nước | °C | 25.000đ | 30.000đ | ⭐⭐⭐ |
| 4 | Độ mặn/EC | ppt / mS/cm | 350.000đ | 550.000đ | ⭐⭐⭐ (tôm) |
| 5 | Mực nước | cm | 85.000đ | 100.000đ | ⭐⭐ |
| 6 | Turbidity (độ đục) | NTU | 250.000đ | 350.000đ | ⭐⭐ |
| 7 | NH₃ nước | mg/L | 350.000đ | 500.000đ | ⭐⭐ |
| 8 | Lưu lượng nước cấp | L/min | 185.000đ | 215.000đ | ⭐⭐ |
| 9 | Mức thức ăn | % | 25.000đ | 28.000đ | ⭐⭐ |
| 10 | Thời tiết (nhiệt độ/ẩm không khí) | °C/%RH | 75.000đ | 90.000đ | ⭐ |

---

# PHẦN 2 — KIẾN TRÚC HỆ THỐNG

## 2.1 Sơ Đồ Tổng Quan

```
┌─────────────────────────────────────────────────────────────┐
│              NEXTFARM PLATFORM — IoT Aquaculture            │
└───────────────────────────┬─────────────────────────────────┘
                            │ MQTT / WiFi
               ┌────────────┴────────────┐
               │    CB2S (BK7231N)       │
               └────────────┬────────────┘
                            │ I2C Bus
              ┌─────────────┴────────────────────┐
              │                                  │
       ┌──────┴──────┐                   ┌───────┴──────┐
       │ MCP23017 #1 │                   │ MCP23017 #2  │
       │  Kênh 0–15  │                   │  Kênh 16–31  │
       └──────┬──────┘                   └───────┬──────┘
              │                                  │
   ┌──────────┴─────────┐           ┌────────────┴──────────┐
   │ Relay Board 1+2    │           │  Relay Board 3+4      │
   │ Bơm, Quạt sục khí │           │  Van, Cho ăn, Dự phòng│
   │ Blower, UV, Heater │           │                       │
   └────────────────────┘           └───────────────────────┘
```

---

## 2.2 Logic Điều Khiển DO — Ưu Tiên Tuyệt Đối

```
Đọc DO mỗi 15 phút (liên tục 2:00–6:00 sáng)
                │
        DO > 6.0 mg/L → Tắt bớt quạt (tiết kiệm điện)
        DO 5.0–6.0   → Duy trì trạng thái hiện tại
        DO 4.0–5.0   → Bật 2 quạt sục khí
        DO 3.0–4.0   → Bật TẤT CẢ 4 quạt + 2 blower
        DO < 3.0     → Bật tất cả + bơm khẩn cấp K26
                       + Mở van O2 bình K27
                       + Gửi MQTT alert: "DO_KHAN_CAP"
                       + LCD cảnh báo đỏ nhấp nháy
                       + Gửi thông báo app NGAY LẬP TỨC
```

---

## 2.3 Logic Điều Khiển pH

```
pH ao tôm thẻ: 7.5–8.5 (tối ưu 7.8–8.2)
pH ao cá:      6.5–8.5 (tối ưu 7.0–8.0)

pH < 7.0 (axit) → Bơm châm vôi Ca(OH)₂ (K9)
                  Liều lượng: 10–20 kg/1000m³
                  Chạy bơm 30 giây → kiểm tra lại 2h

pH > 9.0 (kiềm) → Thay nước (bơm thải K7 + cấp K0)
                   Tăng sục khí (CO₂ từ hô hấp hạ pH)
```

---

## 2.4 Lịch Cho Ăn Tự Động

```
Tôm thẻ: 4–6 lần/ngày theo bảng ăn
  06:00 / 09:00 / 12:00 / 15:00 / 18:00 / 21:00

Cá tra: 2–3 lần/ngày
  07:00 / 12:00 / 17:00

Điều chỉnh lượng ăn theo:
  - Nhiệt độ nước (< 25°C giảm 30%, > 32°C giảm 20%)
  - DO (< 4 mg/L: tạm ngừng cho ăn — tôm không ăn khi thiếu oxy)
  - Kiểm tra sàng ăn: Nếu thức ăn còn → giảm lượng
```

---

## 2.5 Thay Nước Tự Động

```
Chu kỳ thay nước:
  Tôm thẻ: 10–20% thể tích ao/ngày (giai đoạn cuối)
  Cá tra:  5–10% thể tích ao/ngày

Quy trình:
  1. Bơm thải K7 hoạt động X phút (tính theo lưu lượng bơm)
  2. Mở van ao lắng K16–K19
  3. Bơm cấp K0 nạp nước vào
  4. Đo lại: pH, độ mặn, nhiệt độ — điều chỉnh nếu cần
```

---

# PHẦN 3 — BẢN ĐỒ KÊNH RELAY (31 Kênh)

| Kênh | Define | Thiết Bị | Điện Áp | MCP | GPIO |
|:----:|--------|---------|---------|-----|------|
| 0 | `K_BOM_CAP` | Bơm cấp nước chính | 220VAC | #1 GPA0 | 0x20 |
| 1 | `K_QUAT1` | Quạt sục khí cánh quạt 1 | 220VAC | #1 GPA1 | 0x20 |
| 2 | `K_QUAT2` | Quạt sục khí cánh quạt 2 | 220VAC | #1 GPA2 | 0x20 |
| 3 | `K_QUAT3` | Quạt sục khí cánh quạt 3 | 220VAC | #1 GPA3 | 0x20 |
| 4 | `K_QUAT4` | Quạt sục khí cánh quạt 4 | 220VAC | #1 GPA4 | 0x20 |
| 5 | `K_BLOWER1` | Máy thổi khí (air blower) 1 | 220VAC | #1 GPA5 | 0x20 |
| 6 | `K_BLOWER2` | Máy thổi khí (air blower) 2 | 220VAC | #1 GPA6 | 0x20 |
| 7 | `K_BOM_THAI` | Bơm thải/xả nước | 220VAC | #1 GPA7 | 0x20 |
| 8 | `K_BOM_TUAN` | Bơm tuần hoàn nội bộ | 220VAC | #1 GPB0 | 0x20 |
| 9 | `K_BOM_VOI` | Bơm châm vôi Ca(OH)₂ | 220VAC | #1 GPB1 | 0x20 |
| 10 | `K_BOM_HOA` | Bơm châm khoáng/hóa chất | 220VAC | #1 GPB2 | 0x20 |
| 11 | `K_DEN_UV` | Đèn UV khử trùng nước cấp | 220VAC | #1 GPB3 | 0x20 |
| 12 | `K_SUOI_NUOC` | Bộ sưởi nước (heater) | 220VAC | #1 GPB4 | 0x20 |
| 13 | `K_DEN_A` | Đèn chiếu sáng ao khu A | 220VAC | #1 GPB5 | 0x20 |
| 14 | `K_DEN_B` | Đèn chiếu sáng ao khu B | 220VAC | #1 GPB6 | 0x20 |
| 15 | `K_SPARE1` | **Dự phòng** | — | #1 GPB7 | 0x20 |
| 16 | `K_VAN_LANG1` | Van cấp từ ao lắng 1 | 24VAC | #2 GPA0 | 0x21 |
| 17 | `K_VAN_LANG2` | Van cấp từ ao lắng 2 | 24VAC | #2 GPA1 | 0x21 |
| 18 | `K_VAN_LANG3` | Van cấp từ ao lắng 3 | 24VAC | #2 GPA2 | 0x21 |
| 19 | `K_VAN_LANG4` | Van cấp từ ao lắng 4 | 24VAC | #2 GPA3 | 0x21 |
| 20 | `K_CHOAN1` | Máy cho ăn tự động góc 1 | 220VAC | #2 GPA4 | 0x21 |
| 21 | `K_CHOAN2` | Máy cho ăn tự động góc 2 | 220VAC | #2 GPA5 | 0x21 |
| 22 | `K_CHOAN3` | Máy cho ăn tự động góc 3 | 220VAC | #2 GPA6 | 0x21 |
| 23 | `K_CHOAN4` | Máy cho ăn tự động góc 4 | 220VAC | #2 GPA7 | 0x21 |
| 24 | `K_VAN_XA1` | Van xả đáy ao 1 | 220VAC | #2 GPB0 | 0x21 |
| 25 | `K_VAN_XA2` | Van xả đáy ao 2 | 220VAC | #2 GPB1 | 0x21 |
| 26 | `K_BOM_DU_PHONG` | Bơm sục khí dự phòng khẩn cấp | 220VAC | #2 GPB2 | 0x21 |
| 27 | `K_VAN_O2` | Van cấp O₂ bình khẩn cấp | 220VAC | #2 GPB3 | 0x21 |
| 28 | `K_BOM_BUN` | Bơm hút bùn đáy ao | 220VAC | #2 GPB4 | 0x21 |
| 29 | `K_VAN_THAI` | Van xả thải ra ngoài | 220VAC | #2 GPB5 | 0x21 |
| 30 | `K_SPARE2` | **Dự phòng mở rộng** | — | #2 GPB6 | 0x21 |
| 31 | `K_SPARE3` | **Dự phòng mở rộng** | — | #2 GPB7 | 0x21 |

---

# PHẦN 4 — THIẾT BỊ ĐO CHẤT LƯỢNG NƯỚC

> **Đây là phần quan trọng nhất đặc thù thủy hải sản** — Không có các thiết bị đo này, hệ thống chỉ là điều khiển ON/OFF đơn giản, không thể tự động hóa thực sự.
>
> **Quy ước:**
> - **Giá TL** = Hàng phổ thông Shopee/Lazada
> - **Giá TT** = Hàng chính hãng tại đại lý chuyên dụng

---

## 4.1 DO — Oxy Hòa Tan (Dissolved Oxygen) ⭐⭐⭐ BẮT BUỘC

> **Tại sao quan trọng nhất?** Tôm/cá có thể chết hàng loạt trong 1–3 giờ khi DO < 2 mg/L. Đây là thông số sống còn số 1.

### Ngưỡng DO Theo Loại Thủy Sản

| Loại | DO Tối Ưu | Cảnh Báo | Nguy Hiểm |
|------|:---------:|:--------:|:---------:|
| Tôm thẻ chân trắng | >6.0 mg/L | <5.0 | <3.0 |
| Tôm sú | >5.0 mg/L | <4.5 | <3.0 |
| Cá tra / Cá basa | >4.0 mg/L | <3.5 | <2.5 |
| Cá rô phi | >3.5 mg/L | <3.0 | <2.0 |
| Cá chẽm / Cá mú | >5.0 mg/L | <4.0 | <2.5 |

### Gói 1 — Tiết Kiệm: Điện Hóa Phổ Thông (TL: ~350.000đ / TT: ~500.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Đầu dò DO điện hóa | DO-10 hoặc tương đương | TQ | 280.000đ | 400.000đ | 1 | 280.000đ | 400.000đ |
| 2 | Module khuếch đại tín hiệu | Analog 0–5V | — | 45.000đ | 55.000đ | 1 | 45.000đ | 55.000đ |
| 3 | Dây cảm biến chống nước 5m | — | — | 25.000đ | 25.000đ | 1 | 25.000đ | 25.000đ |
| | | | | | | | **350.000đ** | **480.000đ** |

> ⚠️ Cần hiệu chuẩn 2 tuần/lần. Thay màng cực 3–6 tháng/lần. Kém chính xác khi độ mặn thay đổi.

### Gói 2 — Khuyến Nghị ✅: Atlas Scientific EZO-DO (TL: ~850.000đ / TT: ~1.200.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Đầu dò DO điện hóa mini | Probe DO | Atlas Scientific | 550.000đ | 800.000đ | 1 | 550.000đ | 800.000đ |
| 2 | Module EZO-DO (I2C/UART) | EZO-DO | Atlas Scientific | 250.000đ | 350.000đ | 1 | 250.000đ | 350.000đ |
| 3 | Dây cảm biến chống nước 1m | — | — | 35.000đ | 40.000đ | 1 | 35.000đ | 40.000đ |
| 4 | Ống bảo vệ đầu dò PVC | — | — | 15.000đ | 15.000đ | 1 | 15.000đ | 15.000đ |
| | | | | | | | **850.000đ** | **1.205.000đ** |

**Kết nối Atlas EZO-DO → CB2S:**
```
EZO-DO VCC → 3.3V
EZO-DO GND → GND
EZO-DO SDA → GPIO0 (I2C SDA)
EZO-DO SCL → GPIO1 (I2C SCL)
Địa chỉ I2C: 0x61 (mặc định)
```

> ✅ Giao tiếp I2C trực tiếp CB2S. Hiệu chuẩn đơn giản bằng không khí (1 điểm). Chính xác ±0.05 mg/L.

### Gói 3 — Cao Cấp: Quang Học (Optical DO) (TL: ~2.500.000đ / TT: ~3.500.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Đầu dò DO quang học | ODO Optical | YSI / Hach | 2.200.000đ | 3.000.000đ | 1 | 2.200.000đ | 3.000.000đ |
| 2 | Module xử lý tín hiệu | Analog/UART | — | 250.000đ | 400.000đ | 1 | 250.000đ | 400.000đ |
| 3 | Cáp chống nước 5m | — | — | 80.000đ | 100.000đ | 1 | 80.000đ | 100.000đ |
| | | | | | | | **2.530.000đ** | **3.500.000đ** |

> ✅ Không có màng điện cực → tuổi thọ 5+ năm. Không bị nhiễu bởi H₂S. Ít hiệu chuẩn nhất.

### Phụ Kiện Hiệu Chuẩn DO

| STT | Phụ Kiện | Công Dụng | Giá TL | Giá TT |
|:---:|---------|----------|-------:|-------:|
| 1 | Dung dịch Sodium Sulfite bão hòa | Hiệu chuẩn điểm 0% (0 mg/L) | 45.000đ | 55.000đ |
| 2 | Nước sạch + khuấy trong không khí | Hiệu chuẩn điểm bão hòa (100%) | Miễn phí | — |
| 3 | Màng điện cực dự phòng | Thay 3–6 tháng/lần | 85.000đ | 110.000đ |
| 4 | Dung dịch bảo quản đầu dò | Khi không dùng | 35.000đ | 45.000đ |

---

## 4.2 pH — Độ Axit / Kiềm ⭐⭐⭐ BẮT BUỘC

> **Tại sao quan trọng?** pH ảnh hưởng trực tiếp đến: độc tính NH₃, hấp thu oxy của tôm/cá, hoạt động vi khuẩn có lợi, hiệu quả thuốc/hóa chất.

### Ngưỡng pH Theo Loại Thủy Sản

| Loại | pH Tối Ưu | Chấp Nhận | Nguy Hiểm |
|------|:---------:|:---------:|:---------:|
| Tôm thẻ | 7.8–8.2 | 7.5–8.5 | <7.0 hoặc >9.0 |
| Tôm sú | 7.5–8.5 | 7.0–8.5 | <6.5 hoặc >9.5 |
| Cá tra | 7.0–8.0 | 6.5–8.5 | <6.0 hoặc >9.0 |
| Cá rô phi | 6.5–8.0 | 5.0–9.0 | <5.0 |

### Gói 1 — Tiết Kiệm (TL: ~120.000đ / TT: ~180.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Module pH khuếch đại | pH-4502C | TQ | 85.000đ | 120.000đ | 1 | 85.000đ | 120.000đ |
| 2 | Đầu dò pH thủy tinh | pH Electrode | TQ | 35.000đ | 60.000đ | 1 | 35.000đ | 60.000đ |
| | | | | | | | **120.000đ** | **180.000đ** |

> ⚠️ Đầu dò TQ tuổi thọ 6–12 tháng, độ chính xác ±0.1–0.3 pH. Đủ dùng cho ao cá.

### Gói 2 — Khuyến Nghị ✅: Atlas Scientific EZO-pH (TL: ~380.000đ / TT: ~580.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Đầu dò pH thủy tinh | pH Probe | Atlas Scientific | 180.000đ | 280.000đ | 1 | 180.000đ | 280.000đ |
| 2 | Module EZO-pH (I2C/UART) | EZO-pH | Atlas Scientific | 180.000đ | 280.000đ | 1 | 180.000đ | 280.000đ |
| 3 | Dung dịch ngâm đầu dò 30mL | pH Storage | — | 25.000đ | 35.000đ | 1 | 25.000đ | 35.000đ |
| | | | | | | | **385.000đ** | **595.000đ** |

**Kết nối Atlas EZO-pH → CB2S:**
```
EZO-pH VCC → 3.3V  |  GND → GND
EZO-pH SDA → GPIO0 |  SCL → GPIO1
Địa chỉ I2C: 0x63 (mặc định)
```

### Gói 3 — Công Nghiệp (TL: ~2.000.000đ / TT: ~3.500.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Đầu dò pH công nghiệp | pH WTW / HACH | WTW / HACH | 1.800.000đ | 3.000.000đ | 1 | 1.800.000đ | 3.000.000đ |
| 2 | Transmitter 4–20mA → ADS1115 | — | — | 250.000đ | 350.000đ | 1 | 250.000đ | 350.000đ |
| | | | | | | | **2.050.000đ** | **3.350.000đ** |

### Phụ Kiện Hiệu Chuẩn pH

| STT | Phụ Kiện | Công Dụng | Giá TL | Giá TT |
|:---:|---------|----------|-------:|-------:|
| 1 | Dung dịch buffer pH 4.0 (250mL) | Hiệu chuẩn điểm acid | 45.000đ | 60.000đ |
| 2 | Dung dịch buffer pH 7.0 (250mL) | Hiệu chuẩn điểm trung tính | 45.000đ | 60.000đ |
| 3 | Dung dịch buffer pH 10.0 (250mL) | Hiệu chuẩn điểm kiềm | 45.000đ | 60.000đ |
| 4 | Dung dịch bảo quản đầu dò pH | Ngâm KCl 3M khi không dùng | 35.000đ | 45.000đ |
| 5 | Nước cất hiệu chuẩn | Rửa đầu dò giữa các lần đo | 15.000đ | 20.000đ |

> 📌 Chu kỳ hiệu chuẩn pH: **1 lần/tháng** (gói 1) hoặc **1 lần/2 tháng** (gói 2)

---

## 4.3 Độ Mặn / EC (Salinity / Conductivity) ⭐⭐⭐ (Ao Tôm)

> **Tại sao quan trọng?** Sai độ mặn → tôm sốc thẩm thấu → giảm ăn → dễ bệnh → chết. Đặc biệt quan trọng khi pha nước ngọt vào ao hoặc mưa lớn.

### Ngưỡng Độ Mặn

| Loại | Tối Ưu | Chấp Nhận |
|------|:-------:|:---------:|
| Tôm thẻ chân trắng | 10–25 ppt | 5–35 ppt |
| Tôm sú | 15–30 ppt | 10–35 ppt |
| Cua biển | 15–25 ppt | 10–30 ppt |
| Cá chẽm (lúc giống) | 0–5 ppt | 0–10 ppt |
| Cá tra / Cá rô phi | <3 ppt (nước ngọt) | — |

### Gói 1 — Tiết Kiệm: EC Đơn Giản (TL: ~200.000đ / TT: ~280.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Module đo EC điện dẫn suất | SEN0169 | DFRobot | 150.000đ | 200.000đ | 1 | 150.000đ | 200.000đ |
| 2 | Đầu dò EC 2 điện cực | EC Probe K1.0 | — | 55.000đ | 80.000đ | 1 | 55.000đ | 80.000đ |
| | | | | | | | **205.000đ** | **280.000đ** |

### Gói 2 — Khuyến Nghị ✅: Atlas EZO-EC (TL: ~380.000đ / TT: ~580.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Module EZO-EC (I2C) | EZO-EC | Atlas Scientific | 220.000đ | 330.000đ | 1 | 220.000đ | 330.000đ |
| 2 | Đầu dò EC K1.0 (0.5–50mS/cm) | EC Probe K1.0 | Atlas Scientific | 165.000đ | 250.000đ | 1 | 165.000đ | 250.000đ |
| | | | | | | | **385.000đ** | **580.000đ** |

**Kết nối Atlas EZO-EC → CB2S:**
```
EZO-EC VCC → 3.3V  |  GND → GND
EZO-EC SDA → GPIO0 |  SCL → GPIO1
Địa chỉ I2C: 0x64 (mặc định)
Đơn vị đo: μS/cm → quy đổi ppt (salinity)
```

### Phụ Kiện Hiệu Chuẩn EC

| STT | Phụ Kiện | Công Dụng | Giá TL | Giá TT |
|:---:|---------|----------|-------:|-------:|
| 1 | Dung dịch chuẩn 1.413 μS/cm | Hiệu chuẩn EC | 35.000đ | 50.000đ |
| 2 | Dung dịch chuẩn 12.88 mS/cm | Hiệu chuẩn EC cao | 40.000đ | 55.000đ |
| 3 | Khúc xạ kế đo độ mặn (brix) | Đo thủ công đối chiếu | 180.000đ | 220.000đ |

---

## 4.4 Nhiệt Độ Nước ⭐⭐⭐ BẮT BUỘC

### Ngưỡng Nhiệt Độ

| Loại | Tối Ưu | Nguy Hiểm |
|------|:-------:|:---------:|
| Tôm thẻ | 23–30°C | <18°C hoặc >33°C |
| Cá tra | 26–30°C | <20°C hoặc >35°C |
| Cá rô phi | 25–30°C | <15°C hoặc >35°C |

### Chi Tiết Thiết Bị

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Đầu dò nhiệt độ nước chống nước | DS18B20 Waterproof | — | 25.000đ | 30.000đ | 3 | 75.000đ | 90.000đ |
| 2 | Điện trở pull-up 4.7kΩ | 1/4W | — | 500đ | 500đ | 3 | 1.500đ | 1.500đ |
| 3 | Dây cảm biến dài 3m | — | — | 8.000đ/m | 8.000đ/m | 3m | 24.000đ | 24.000đ |
| 4 | Bảo vệ đầu dò (ống đồng/INOX) | — | — | 15.000đ | 20.000đ | 3 | 45.000đ | 60.000đ |
| | | | | | | | **145.500đ** | **175.500đ** |

> Lắp 3 điểm: đầu ao (gần cống cấp), giữa ao, gần quạt sục khí để phát hiện vùng nhiệt độ khác nhau.

---

## 4.5 Độ Đục / Độ Trong Nước (Turbidity) ⭐⭐

> **Tại sao cần?** Độ đục cao = tảo bùng phát hoặc bùn khuấy lên = DO dao động mạnh. Giúp phát hiện sớm "nước xanh" hoặc "nước đục" cần xử lý.

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Cảm biến độ đục analog | SEN0189 | DFRobot | 150.000đ | 200.000đ | 1 | 150.000đ | 200.000đ |
| 2 | Module ADS1115 ADC | ADS1115 | TI | 35.000đ | 42.000đ | 1 | 35.000đ | 42.000đ |
| 3 | Hộp bảo vệ cảm biến chống nước | IP68 | — | 25.000đ | 30.000đ | 1 | 25.000đ | 30.000đ |
| | | | | | | | **210.000đ** | **272.000đ** |

**Ứng dụng:** Độ đục NTU > 200 → cảnh báo tảo bùng phát → tăng thay nước, dùng chế phẩm vi sinh.

---

## 4.6 ORP — Thế Oxy Hóa Khử (Oxidation-Reduction Potential) ⭐⭐

> **Tại sao cần?** ORP phản ánh khả năng oxy hóa của nước. ORP thấp (<200mV) = nước thiếu oxy, vi khuẩn kị khí hoạt động mạnh, sinh H₂S (khí độc đáy ao).

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Module EZO-ORP (I2C) | EZO-ORP | Atlas Scientific | 220.000đ | 330.000đ | 1 | 220.000đ | 330.000đ |
| 2 | Đầu dò ORP bạch kim | ORP Probe | Atlas Scientific | 180.000đ | 270.000đ | 1 | 180.000đ | 270.000đ |
| 3 | Dung dịch hiệu chuẩn ORP 225mV | Quinhydrone | — | 80.000đ | 100.000đ | 1 | 80.000đ | 100.000đ |
| | | | | | | | **480.000đ** | **700.000đ** |

**Kết nối Atlas EZO-ORP → CB2S:**
```
EZO-ORP SDA → GPIO0  |  SCL → GPIO1
Địa chỉ I2C: 0x62 (mặc định)
ORP tối ưu ao tôm: 250–350 mV
ORP < 200 mV → Cảnh báo, tăng sục khí + dùng zeolite hút NH₃
```

---

## 4.7 NH₃ / NH₄⁺ Hòa Tan ⭐⭐

> **Tại sao cần?** NH₃ từ chất thải tôm/cá tích tụ → độc ở pH cao và nhiệt độ cao. Độc gấp 100 lần NH₄⁺.

### So Sánh Các Phương Pháp Đo

| Phương Pháp | Ưu Điểm | Nhược Điểm | Chi Phí |
|-------------|---------|-----------|:-------:|
| Test kit hóa học (thủ công) | Rẻ, phổ biến, chính xác | Không tự động hóa được | 35.000đ/bộ |
| Điện cực chọn lọc ion NH₄⁺ | Tự động, liên tục | Đắt, cần hiệu chuẩn phức tạp | 1.500.000đ+ |
| Giám sát gián tiếp (DO+pH+cho ăn) | Đơn giản | Không đo trực tiếp | — |

**Khuyến nghị thực tế:**
- Dùng **test kit** để đo NH₃ thủ công 2–3 lần/tuần
- Gián tiếp kiểm soát qua: Quản lý cho ăn + thay nước + vi sinh định kỳ
- Nếu cần tự động: Dùng điện cực chọn lọc ion NH₄⁺

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Test kit NH₃ thủ công (50 lần test) | API NH3 | API | 85.000đ | 120.000đ | 2 | 170.000đ | 240.000đ |
| 2 | Điện cực NH₄⁺ + Module analog | ISE NH4 | — | 1.200.000đ | 1.800.000đ | 1 | 1.200.000đ | 1.800.000đ |

---

## 4.8 Mực Nước Ao ⭐⭐

### Gói 1 — Siêu Âm Từ Bờ (TL: ~110.000đ / TT: ~128.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Cảm biến siêu âm AJ-SR04M | AJ-SR04M | — | 25.000đ | 28.000đ | 1 | 25.000đ | 28.000đ |
| 2 | Hộp bảo vệ chống mưa | Nhựa IP54 | — | 20.000đ | 25.000đ | 1 | 20.000đ | 25.000đ |
| 3 | Cột gắn cảm biến trên bờ | Nhôm 1m | — | 35.000đ | 45.000đ | 1 | 35.000đ | 45.000đ |
| 4 | Dây tín hiệu chống nhiễu 5m | STP | — | 30.000đ | 30.000đ | 1 | 30.000đ | 30.000đ |
| | | | | | | | **110.000đ** | **128.000đ** |

### Gói 2 — Cảm Biến Áp Suất Ngầm (TL: ~205.000đ / TT: ~250.000đ)

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Cảm biến áp suất 0–10kPa (chìm) | MS5837-30BAW | — | 120.000đ | 150.000đ | 1 | 120.000đ | 150.000đ |
| 2 | ADS1115 ADC | ADS1115 | TI | 35.000đ | 42.000đ | 1 | 35.000đ | 42.000đ |
| 3 | Cáp tín hiệu chống nước 5m | — | — | 50.000đ | 58.000đ | 1 | 50.000đ | 58.000đ |
| | | | | | | | **205.000đ** | **250.000đ** |

---

## 4.9 Lưu Lượng Nước Cấp / Thải ⭐⭐

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Flow meter nước cấp DN25 | YF-B10 SS304 | — | 220.000đ | 250.000đ | 1 | 220.000đ | 250.000đ |
| 2 | Flow meter nước thải DN32 | YF-B10 SS304 | — | 220.000đ | 250.000đ | 1 | 220.000đ | 250.000đ |
| 3 | Dây tín hiệu 2 lõi 5m | UL2464 | — | 25.000đ | 25.000đ | 2 | 50.000đ | 50.000đ |
| | | | | | | | **490.000đ** | **550.000đ** |

> **Kết nối thẳng GPIO CB2S (3.3V, không cần optocoupler)** — giống hệ thống tưới nhà kính.

---

## 4.10 Gói Cảm Biến Tổng Hợp Đề Xuất

### Gói Cơ Bản — Ao Cá (TL: ~1.450.000đ / TT: ~1.946.000đ)

| Cảm Biến | Model | Giá TL | Giá TT |
|---------|-------|-------:|-------:|
| DO điện hóa | Atlas EZO-DO | 850.000đ | 1.205.000đ |
| pH | Module pH-4502C | 120.000đ | 180.000đ |
| Nhiệt độ nước ×2 | DS18B20 waterproof | 50.000đ | 60.000đ |
| Mực nước | AJ-SR04M | 110.000đ | 128.000đ |
| ADS1115 ADC | ADS1115 | 35.000đ | 42.000đ |
| Mức thức ăn ×4 | AJ-SR04M | 100.000đ | 112.000đ |
| OLED SH1106 1.3" | 2004 I2C | 70.000đ | 80.000đ |
| Test kit NH₃ | API | 85.000đ | 120.000đ |
| **Tổng** | | **1.420.000đ** | **1.927.000đ** |

### Gói Đầy Đủ — Ao Tôm (TL: ~3.800.000đ / TT: ~5.600.000đ)

| Cảm Biến | Model | Giá TL | Giá TT |
|---------|-------|-------:|-------:|
| DO quang học | Optical DO | 2.530.000đ | 3.500.000đ |
| pH Atlas EZO | EZO-pH | 385.000đ | 595.000đ |
| Độ mặn Atlas EZO | EZO-EC | 385.000đ | 580.000đ |
| ORP Atlas EZO | EZO-ORP | 480.000đ | 700.000đ |
| Nhiệt độ nước ×3 | DS18B20 titanium | 145.000đ | 175.000đ |
| Mực nước (áp suất) | MS5837-30BAW | 205.000đ | 250.000đ |
| Turbidity | SEN0189 | 210.000đ | 272.000đ |
| Flow meter ×2 | YF-B10 SS304 | 490.000đ | 550.000đ |
| Mức thức ăn ×4 | AJ-SR04M | 100.000đ | 112.000đ |
| OLED SH1106 1.3" | 2004 I2C | 70.000đ | 80.000đ |
| Buffer pH 4+7+10 | — | 135.000đ | 180.000đ |
| Dung dịch DO 0% | Sodium Sulfite | 45.000đ | 55.000đ |
| Dung dịch EC chuẩn | 1413 μS/cm | 35.000đ | 50.000đ |
| **Tổng** | | **5.215.000đ** | **7.099.000đ** |

---

## 4.11 Lưu Ý Kết Nối Tất Cả Cảm Biến I2C

```
CB2S I2C Bus (GPIO0 SDA + GPIO1 SCL):
  0x20 — MCP23017 #1 (relay kênh 0–15)
  0x21 — MCP23017 #2 (relay kênh 16–31)
  0x27 — OLED SH1106 1.3"
  0x48 — ADS1115 (turbidity, pH-4502C, EC đơn giản)
  0x61 — Atlas EZO-DO
  0x62 — Atlas EZO-ORP
  0x63 — Atlas EZO-pH
  0x64 — Atlas EZO-EC
  → Tổng 8 địa chỉ — vừa đủ 1 bus I2C

GPIO riêng:
  GPIO8  — DS18B20 nhiệt độ (1-Wire, ×3 điểm dùng chung bus)
  GPIO12 — AJ-SR04M ECHO (mực nước)
  GPIO13 — AJ-SR04M TRIG
  GPIO14, 26 — Flow meter (YF-B10)

Lưu ý môi trường ao:
  - Tất cả dây cảm biến đi ống PVC riêng, không chung dây điện
  - Cáp chống nhiễu STP cho dây tín hiệu dài > 3m
  - Đầu nối IP68 tại điểm vào ao
  - Cảm biến Atlas EZO: Ngâm ít nhất 5 phút trước khi đọc giá trị ổn định
```

---

## 4.12 Chu Kỳ Hiệu Chuẩn & Bảo Trì Cảm Biến

| Cảm Biến | Hiệu Chuẩn | Bảo Trì | Thay Thế |
|---------|-----------|---------|---------|
| DO điện hóa | 2 tuần/lần | Rửa màng cực bằng nước cất | Thay màng 3–6 tháng |
| DO quang học | 6 tháng/lần | Lau sạch đầu quang | Không thay (5+ năm) |
| pH | 1 tháng/lần | Ngâm KCl 3M khi không dùng | Thay đầu dò 1–2 năm |
| EC/Độ mặn | 3 tháng/lần | Rửa nước cất sau dùng | Thay đầu dò 2–3 năm |
| ORP | 3 tháng/lần | Lau sạch | Thay đầu dò 2 năm |
| Nhiệt độ DS18B20 | Không cần | Rửa vỏ inox | Thay nếu sai > 1°C |
| Turbidity | Kiểm tra hàng tuần | Lau sạch đầu quang | Thay 1–2 năm |
| Mực nước | Kiểm tra định kỳ | Vệ sinh đầu siêu âm | Thay nếu hỏng |

---

# PHẦN 5 — PHẦN CỨNG & THÔNG SỐ KỸ THUẬT

## 5.1 Bộ Điều Khiển (Giống Nhà Kính & Chăn Nuôi)

| Thiết Bị | Thông Số | Giá TL | Giá TT |
|---------|---------|-------:|-------:|
| CB2S (BK7231N) | WiFi 2.4GHz, 3.3V, LibreTuya | 45.000đ | 50.000đ |
| MCP23017 × 2 | I2C, 16 GPIO/chip, 0x20+0x21 | 64.000đ | 76.000đ |
| Relay Board 8 kênh × 4 | 5VDC, 10A/250VAC, opto | 260.000đ | 280.000đ |
| Mean Well HDR-60-5 | 220V→5VDC/12A DIN rail | 220.000đ | 460.000đ |
| Biến áp 24VAC 50VA | 220V→24VAC (van ao lắng) | 185.000đ | 220.000đ |
| Tủ điện IP65 400×300×200 | Chống ẩm cao độ | 450.000đ | 680.000đ |
| **Tổng bộ điều khiển** | | **2.680.000đ** | **3.364.000đ** |

---

## 4.2 Cảm Biến Đặc Thù Thủy Sản

### DO Sensor — Oxy Hòa Tan (Bắt Buộc)

| Thông Số | Gói 1 (Phổ thông) | Gói 2 (Điện hóa) | Gói 3 (Quang học) |
|----------|:-:|:-:|:-:|
| Model | DO-10 (điện hóa) | Atlas DO EZO | Optical DO |
| Nguyên lý | Điện hóa (cần hiệu chuẩn thường) | Điện hóa (tự hiệu chuẩn) | Quang học (ít hiệu chuẩn) |
| Giao tiếp | Analog → ADS1115 | I2C/UART | I2C/UART |
| Dải đo | 0–20 mg/L | 0–20 mg/L | 0–20 mg/L |
| Độ chính xác | ±0.3 mg/L | ±0.05 mg/L | ±0.01 mg/L |
| Tuổi thọ đầu dò | 1–2 năm | 1–2 năm | 5+ năm |
| Điện áp | 12V | 3.3–5V | 5V |
| **Giá TL** | **350.000đ** | **850.000đ** | **2.500.000đ** |
| **Giá TT** | **500.000đ** | **1.200.000đ** | **3.500.000đ** |

> ⚠️ DO sensor điện hóa cần hiệu chuẩn 2 tuần/lần và thay màng cực mỗi 3–6 tháng. Sensor quang học ít bảo trì hơn nhưng đắt hơn.

### pH Sensor

| Thông Số | Gói 1 (Phổ thông) | Gói 2 ✅ (Khuyến nghị) | Gói 3 (Công nghiệp) |
|----------|:-:|:-:|:-:|
| Model | pH-4502C | Atlas pH EZO | Sonda pH HACH |
| Giao tiếp | Analog → ADS1115 | I2C/UART | RS485 Modbus |
| Độ chính xác | ±0.1 pH | ±0.002 pH | ±0.01 pH |
| Điện áp | 5–9V | 3.3–5V | 24V |
| Hiệu chuẩn | 2 điểm thủ công | Tự động 2–3 điểm | Tự động 3 điểm |
| **Giá TL** | **120.000đ** | **250.000đ** | **3.500.000đ** |
| **Giá TT** | **180.000đ** | **450.000đ** | **5.000.000đ** |

### Cảm Biến Độ Mặn / EC (Cho Tôm)

| Thông Số | Giá trị |
|----------|---------|
| Phạm vi đo | 0–40 ppt (tôm thẻ: 10–25 ppt, tôm sú: 15–30 ppt) |
| Nguyên lý | Đo điện dẫn suất EC → quy đổi sang ppt |
| Model khuyến nghị | DFRobot EC Meter Kit |
| Giao tiếp | Analog → ADS1115 |
| Giá TL | 350.000đ |
| Giá TT | 500.000đ |

### Cảm Biến Mực Nước

| Thông Số | Giá trị |
|----------|---------|
| Loại | Cảm biến áp suất 0–10kPa hoặc Ultrasonic AJ-SR04M |
| Ứng dụng | Kiểm soát mực nước chuẩn, cảnh báo rò rỉ |
| Lắp đặt | Đáy ao (áp suất) hoặc bờ ao phía trên (siêu âm) |
| Giá TL | 85.000đ (siêu âm) / 120.000đ (áp suất) |
| Giá TT | 100.000đ / 150.000đ |

---

## 4.3 Quạt Sục Khí Cánh Quạt (Paddlewheel Aerator)

> Thiết bị quan trọng nhất trong ao nuôi thủy sản

| Thông Số | 0.75kW | 1.1kW ✅ | 1.5kW | 2.2kW |
|----------|:-------:|:--------:|:------:|:------:|
| Điện áp | 220VAC 1 pha | 220VAC 1 pha | 220VAC/380V | 380V 3 pha |
| Lưu lượng oxy | ~1.5 kgO₂/h | ~2.2 kgO₂/h | ~3.0 kgO₂/h | ~4.5 kgO₂/h |
| Diện tích ao | ~500m² | ~1.000m² | ~1.500m² | ~2.000m² |
| Điều khiển | Relay 220VAC | Relay 220VAC | Relay + contactor | Contactor 3P |
| **Giá TL** | 3.200.000đ | 4.500.000đ | 6.500.000đ | 9.500.000đ |
| **Giá TT** | 3.800.000đ | 5.200.000đ | 7.500.000đ | 11.000.000đ |

> **Lưu ý**: Quạt 1.5kW trở lên nên dùng contactor riêng, không dùng thẳng relay 10A.
> **Số quạt cần**: Tôm thẻ: 1 quạt/1.500m² ao; Cá tra: 1 quạt/2.000m² ao

### Đấu Nối Quạt Sục Khí → Relay CB2S

```
Quạt ≤ 1.1kW (220VAC, ~5A):
  Relay Board NO → Contactor coil L
  Contactor chính → Quạt (bắt buộc có contactor riêng)

Quạt ≥ 1.5kW hoặc 3 pha:
  → KHÔNG dùng relay board trực tiếp
  → Relay CB2S điều khiển coil Contactor 3 pha (380V)
  → Contactor 3P → Quạt 3 pha
```

---

## 4.4 Máy Thổi Khí (Air Blower / Root Blower)

| Thông Số | Giá trị |
|----------|---------|
| Công dụng | Sục khí qua ống micro-bubble cho đáy ao, ít tiếng ồn hơn quạt cánh |
| Loại phổ thông | Root blower 0.75kW–2.2kW |
| Kết hợp | Dùng chung với quạt cánh hoặc thay thế hoàn toàn |
| Ưu điểm | Sục khí đều, không làm nước bị khuấy mạnh (tốt cho tôm con) |
| Nhược điểm | Đắt hơn quạt cánh, tiếng ồn lớn |
| **Giá TL** | 5.500.000 – 12.000.000đ |
| **Giá TT** | 6.500.000 – 14.000.000đ |

---

## 4.5 Máy Cho Ăn Tự Động Ao Nuôi

| Thông Số | Kiểu Quay (Rotary) | Kiểu Băng Tải | Kiểu Khí Nén |
|----------|:-:|:-:|:-:|
| Cơ cấu | Đĩa quay ném thức ăn | Băng tải phân phối | Khí nén thổi cám |
| Bán kính ném | 3–6m | 1–2m | 5–15m |
| Phù hợp | Ao < 2.000m² | Ao nhỏ, nuôi đăng quầng | Ao lớn > 3.000m² |
| Motor | DC 12V hoặc 220VAC | DC 12V | Motor khí nén |
| Điều khiển | Relay K20–K23 | Relay K20–K23 | Relay K20–K23 |
| **Giá TL** | 450.000đ | 280.000đ | 2.500.000đ |
| **Giá TT** | 550.000đ | 350.000đ | 3.000.000đ |

> Ao tôm thẻ cần 4 máy cho ăn (4 góc ao) để phân phối đều, tránh cạnh tranh thức ăn

---

# PHẦN 6 — BÁO GIÁ TỔNG HỢP

## 5.1 Tổng Quan Chi Phí (Gói 2 Khuyến Nghị — Ao 1.000–2.000m²)

| # | Hạng Mục | Giá TL | Giá TT |
|:-:|---------|-------:|-------:|
| A | Bộ điều khiển CB2S | 2.680.000đ | 3.364.000đ |
| B | Màn hình OLED SH1106 1.3" + cảm biến cơ bản | 1.835.000đ | 2.498.000đ |
| C | Bơm cấp nước chính 1HP | 2.800.000đ | 3.100.000đ |
| D | 4 quạt sục khí 1.1kW | 18.000.000đ | 20.800.000đ |
| E | 2 máy thổi khí blower 1.1kW | 13.000.000đ | 15.000.000đ |
| F | Bơm thải + bơm tuần hoàn | 3.200.000đ | 3.600.000đ |
| G | Bơm châm vôi + hóa chất | 1.600.000đ | 1.900.000đ |
| H | Đèn UV khử trùng 40W | 850.000đ | 1.000.000đ |
| I | 4 van ao lắng 24VAC | 2.084.000đ | 2.700.000đ |
| J | 4 máy cho ăn tự động (góc ao) | 2.200.000đ | 2.600.000đ |
| K | Van xả đáy + bơm bùn | 2.500.000đ | 3.000.000đ |
| L | Bơm dự phòng khẩn cấp + van O₂ | 3.500.000đ | 4.200.000đ |
| M | Vật tư phụ & tiêu hao | 1.500.000đ | 1.800.000đ |
| | **Tổng phần cứng** | **55.749.000đ** | **65.562.000đ** |
| | Nhân công lắp đặt | 8.000.000đ | 8.000.000đ |
| | Dự phòng 10% | 5.575.000đ | 6.556.000đ |
| | **TỔNG DỰ TOÁN** | **~69.324.000đ** | **~80.118.000đ** |

> Chi phí lớn nhất: Quạt sục khí (32% tổng) + Máy thổi khí (23% tổng)

---

## 5.2 So Sánh 3 Mô Hình Ao

| | Ao Nhỏ <500m² | Ao Vừa 500–2000m² ✅ | Ao Lớn >2000m² |
|--|:-:|:-:|:-:|
| Số quạt | 1–2 cái | 4 cái | 6–12 cái |
| Công suất quạt | 0.75kW | **1.1kW** | 1.5–2.2kW |
| Điều khiển quạt | Relay trực tiếp | Relay + Contactor | Contactor 3P riêng |
| Số máy cho ăn | 1 cái | 4 cái (4 góc) | 6–8 cái |
| CB2S đủ relay? | ✅ Đủ 31 kênh | ✅ Đủ 31 kênh | ⚠️ Cần thêm MCP23017 |
| **Tổng phần cứng TL** | ~28.000.000đ | ~55.749.000đ | ~120.000.000đ+ |

---

## 5.3 Chi Tiết Cảm Biến (Gói 2 — Khuyến Nghị)

| STT | Cảm Biến | Model | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|-------:|-------:|:--:|--------:|--------:|
| 1 | DO sensor điện hóa | Atlas DO EZO | 850.000đ | 1.200.000đ | 1 | 850.000đ | 1.200.000đ |
| 2 | pH sensor | Atlas pH EZO | 250.000đ | 450.000đ | 1 | 250.000đ | 450.000đ |
| 3 | Nhiệt độ nước DS18B20 chống nước | DS18B20 | 25.000đ | 30.000đ | 2 | 50.000đ | 60.000đ |
| 4 | Độ mặn/EC | DFRobot EC | 350.000đ | 500.000đ | 1 | 350.000đ | 500.000đ |
| 5 | Mực nước (áp suất) | MS5837-30BA | 120.000đ | 150.000đ | 1 | 120.000đ | 150.000đ |
| 6 | Lưu lượng nước cấp YF-B7 | YF-B7 SS304 | 185.000đ | 215.000đ | 1 | 185.000đ | 215.000đ |
| 7 | ADC 16-bit ADS1115 | ADS1115 | 35.000đ | 42.000đ | 1 | 35.000đ | 42.000đ |
| 8 | Mức thức ăn AJ-SR04M | AJ-SR04M | 25.000đ | 28.000đ | 4 | 100.000đ | 112.000đ |
| 9 | OLED SH1106 1.3" I2C | 2004 | 70.000đ | 80.000đ | 1 | 70.000đ | 80.000đ |
| | | | | | | **2.010.000đ** | **2.809.000đ** |

---

# PHẦN 7 — LẮP ĐẶT & VẬN HÀNH

## 6.1 Lưu Ý Lắp Đặt Đặc Thù Ao Nuôi

```
⚠️ Môi trường ao nuôi: Ẩm ướt cao + muối (ao tôm) + H₂S (khí độc đáy ao)
   → Toàn bộ kết nối điện PHẢI bọc kín IP68
   → Tủ điện IP65 đặt xa ao ít nhất 3m, che mưa
   → Cáp điện đi ống PVC kín hoặc ống thép mạ kẽm

Quạt sục khí:
   → Đặt trên bè nổi hoặc cột cố định bờ ao
   → Dây điện đi qua ống PVC chống nước, cable gland IP68
   → Cầu chì bảo vệ mỗi quạt tại tủ điện

Cảm biến DO/pH:
   → Đặt ở độ sâu 50cm, không gần cống thoát hoặc quạt sục khí
   → Dây cảm biến đi ống PVC riêng, không đi chung dây điện
   → Hiệu chuẩn DO: Mỗi 2 tuần / Hiệu chuẩn pH: Mỗi tháng

Máy cho ăn:
   → Đặt trên cột bờ ao hoặc bè nổi góc ao
   → Hướng ném ra giữa ao (không ném vào bờ)
   → Che chắn tránh mưa
```

---

## 6.2 Màn Hình LCD — Hiển Thị & Cảnh Báo

```
Màn hình bình thường:
  AUTO  DO:5.8mg  pH:7.9
  NMan:25.2C Sal:18ppt
  Quat:2/4  Blower:OFF
  An:06:00  ThuAn:85%

Cảnh báo DO thấp (nhấp nháy):
  !!! DO THAP - 3.2mg/L !!!
  Da bat tat ca quat+blower
  Kiem tra ao ngay!
  Goi: 090 xxx xxxx

Cảnh báo khẩn cấp DO < 2mg/L (nền đỏ):
  [!!!] KHAN CAP DO [!!!]
  DO: 1.8mg/L NGUY HIEM
  Da bat O2 binh du phong
  BAO DONG NGAY LAP TUC!
```

---

## 6.3 MQTT Alert Topics

```
aqua/{id}/alert/do        ← DO thấp / khẩn cấp
aqua/{id}/alert/ph        ← pH bất thường
aqua/{id}/alert/nhiet     ← Nhiệt độ bất thường
aqua/{id}/alert/mucnuoc   ← Mực nước bất thường
aqua/{id}/alert/thucán    ← Sắp hết thức ăn
aqua/{id}/alert/dien      ← Mất điện / phục hồi

aqua/{id}/sensor          ← DO, pH, nhiệt độ, độ mặn, mực nước
aqua/{id}/trang_thai      ← Trạng thái hệ thống
```

---

## 6.4 Phục Hồi Sau Mất Điện

> **Đặc biệt nguy hiểm với ao nuôi**: Mất điện = quạt sục khí tắt = DO giảm = tôm/cá chết

```
Ưu tiên phục hồi khi có điện:
  1. NGAY LẬP TỨC: Bật tất cả quạt sục khí + blower
     (Không chờ NTP hay WiFi)
  2. Đọc DO → nếu < 3mg/L: Bật bơm dự phòng K26 + Van O2 K27
  3. Gửi MQTT alert: "Mat dien X phut - Do: Y mg/L"
  4. Kiểm tra ca cho ăn bị lỡ → Chạy bù nếu còn trước 18:00

UPS khuyến nghị:
  Bộ điều khiển CB2S: UPS 500VA (2–4 giờ)
  Quạt sục khí: Máy phát điện dự phòng BẮBT BUỘC
  (Không UPS nào chạy được quạt 1.1kW × 4 = 4.4kW)
```

---

## 6.5 Bảo Trì Định Kỳ

| Tần suất | Công việc |
|----------|-----------|
| **Hàng ngày** | Kiểm tra DO 5:00 sáng, quan sát tôm/cá hoạt động |
| **3 ngày/lần** | Đo pH, nhiệt độ, độ mặn bằng máy cầm tay (so với cảm biến) |
| **Hàng tuần** | Vệ sinh đầu cảm biến DO (màng điện cực), kiểm tra quạt |
| **2 tuần/lần** | Hiệu chuẩn DO sensor, kiểm tra sàng ăn |
| **Hàng tháng** | Hiệu chuẩn pH, thay dung dịch hiệu chuẩn |
| **3 tháng** | Thay màng cực DO sensor, kiểm tra vòng bi quạt |
| **6 tháng** | Thay đầu DO sensor nếu cần, bảo dưỡng motor quạt |

---

# PHẦN 8 — ĐIỂM KHÁC BIỆT SO VỚI NHÀ KÍNH & CHĂN NUÔI

| | Nhà Kính | Chăn Nuôi | **Thủy Hải Sản** |
|--|--|--|--|
| Cảm biến ưu tiên | Nhiệt độ, ánh sáng | NH₃, CO₂ | **DO (Oxy hòa tan)** |
| Nguy hiểm chết nhanh nhất | Thiếu nước | NH₃ cao | **DO thấp (1–2 giờ)** |
| Thiết bị quan trọng nhất | Van tưới + bơm | Quạt thông gió | **Quạt sục khí cánh** |
| Chi phí thiết bị chính | 2–10 triệu | 5–15 triệu | **15–50 triệu** |
| Yêu cầu UPS | Không bắt buộc | Khuyến nghị | **Máy phát BẮT BUỘC** |
| Hiệu chuẩn cảm biến | Ít / Không cần | Hiệu chỉnh MQ-135 | **Thường xuyên (DO, pH)** |
| Tổng dự toán (ao 1000m²) | ~28M | ~45M | **~70M** |
| Relay control | Đơn giản (ON/OFF) | ON/OFF | **ON/OFF + Contactor riêng** |
| MQTT prefix | `farm/{id}/` | `livestock/{id}/` | `aqua/{id}/` |

---

## 7.1 Firmware — Phần Thay Đổi So Với Chăn Nuôi

```cpp
// Thay đổi chính trong firmware thủy hải sản:

// 1. Đọc DO từ Atlas EZO qua I2C
float docDO() {
  Wire.beginTransmission(DO_I2C_ADDR);
  Wire.write('R');  // Lệnh đọc
  Wire.endTransmission();
  delay(600);       // Atlas EZO cần 600ms
  Wire.requestFrom(DO_I2C_ADDR, 7);
  // Parse response → return float DO (mg/L)
}

// 2. Logic điều khiển quạt theo DO (ưu tiên tuyệt đối)
void kiemTraDO() {
  float do_val = docDO();
  if      (do_val < 2.0) baoДongKhanCap(do_val);
  else if (do_val < 3.0) batTatCaQuat_Blower_DuPhong();
  else if (do_val < 4.0) batTatCaQuat_Blower();
  else if (do_val < 5.0) batNuaQuat();
  else if (do_val > 6.5) tatBotQuat();  // Tiết kiệm điện
}

// 3. DO khẩn cấp — KHÔNG cần WiFi hay NTP
// Ngay khi boot: đọc DO ngay và quyết định bật quạt
void setup() {
  ...
  // ĐỌC DO NGAY sau init hardware — trước cả WiFi
  float do_boot = docDO();
  if (do_boot < 4.0) {
    for (int i = 1; i <= 4; i++) setRelay(i, true);  // Bật hết quạt
  }
  // Sau đó mới kết nối WiFi, MQTT...
}
```

---

## 7.2 Nguồn Mua Hàng Thiết Bị Thủy Sản

| Thiết Bị | Nguồn Mua | Ghi Chú |
|---------|-----------|---------|
| Quạt sục khí cánh quạt | Công ty thiết bị thủy sản, Shopee | Brands: Jecod, Resun, nội địa |
| Atlas Scientific DO/pH | Atlas-scientific.com, Shopee | Nhập khẩu, chính hãng Mỹ |
| DO sensor phổ thông | Shopee, Lazada | Hiệu chuẩn thường xuyên hơn |
| Root blower | Đại lý máy thủy sản, cơ khí | Brands: Robushi, Chinese |
| Máy cho ăn tự động | Shopee, đại lý thủy sản | Cho tôm cần 4 máy/ao |
| Contactor 220V 20A | Chint, LS, đại lý điện | Bắt buộc cho quạt ≥ 1.1kW |
| Ống PVC chống nước | Cửa hàng vật tư điện | IP68 cho môi trường ao |
| Bình O₂ dự phòng | Đại lý khí công nghiệp | Bắt buộc cho ao tôm |
| CB2S, MCP23017 | Shopee, ICDAYROI.COM | Như nhà kính/chăn nuôi |
| nextfarm.vn | nextfarm.vn — 090 224 3822 | Giải pháp chuyên nghiệp |

---

*Phiên bản: 1.0 (Draft) | Cập nhật: 2026-05-12*
*Dựa trên nền tảng NextFarm DIY Controller (Nhà Kính + Chăn Nuôi)*
# Cảm Biến Chất Lượng Nước & Hệ Thống Cảnh Báo
## Thủy Hải Sản — CB2S + Atlas EZO + ADS1115

---

# PHẦN 1 — SƠ ĐỒ KẾT NỐI TỔNG QUAN

## 1.1 Toàn Bộ Cảm Biến Kết Nối Vào CB2S

```
CB2S (BK7231N)
│
├── I2C Bus (GPIO0=SDA, GPIO1=SCL) ─── Pull-up 4.7kΩ lên 3.3V
│   ├── 0x20  MCP23017 #1       (Relay kênh 0–15)
│   ├── 0x21  MCP23017 #2       (Relay kênh 16–31)
│   ├── 0x27  OLED SH1106 1.3"
│   ├── 0x48  ADS1115 ADC       (Cảm biến analog)
│   │   ├── AIN0 ─── Turbidity (độ đục) [5V→3.3V qua R chia áp]
│   │   ├── AIN1 ─── Mực nước áp suất  [4–20mA qua R 150Ω]
│   │   ├── AIN2 ─── Dự phòng
│   │   └── AIN3 ─── Dự phòng
│   ├── 0x61  Atlas EZO-DO      (Oxy hòa tan mg/L)
│   ├── 0x62  Atlas EZO-ORP     (Thế oxy hóa khử mV)
│   ├── 0x63  Atlas EZO-pH      (Độ pH)
│   └── 0x64  Atlas EZO-EC      (Độ mặn / EC)
│
├── 1-Wire (GPIO8) ─── Pull-up 4.7kΩ lên 3.3V
│   ├── DS18B20 #1 (nhiệt độ nước — đầu ao)
│   ├── DS18B20 #2 (nhiệt độ nước — giữa ao)
│   └── DS18B20 #3 (nhiệt độ nước — cuối ao)
│
├── GPIO12 ─── AJ-SR04M ECHO  [5V → 3.3V qua R chia áp]
├── GPIO13 ─── AJ-SR04M TRIG  (OUTPUT, 3.3V OK)
├── GPIO14 ─── Flow meter cấp nước (xung Hall YF-B10, 3.3V OK)
└── GPIO26 ─── Flow meter thải nước (xung Hall YF-B10, 3.3V OK)
```

---

# PHẦN 2 — THU NHẬN TÍN HIỆU TỪNG CẢM BIẾN

## 2.1 Atlas EZO-DO — Oxy Hòa Tan

### Sơ Đồ Mạch

```
CB2S 3.3V ────────────────── EZO-DO VCC
CB2S GND  ────────────────── EZO-DO GND
CB2S GPIO0 (SDA) ─────────── EZO-DO SDA
CB2S GPIO1 (SCL) ─────────── EZO-DO SCL
                              │
                              EZO-DO → Đầu dò DO (cáp BNC)
                                        │
                                     [Đầu dò ngâm trong ao, sâu 50cm]
```

> **Không cần điện trở, tụ thêm** — EZO-DO chạy thẳng 3.3V I2C.

### Code Đọc Giá Trị

```cpp
#include <Wire.h>

#define ADDR_DO  0x61
#define ADDR_ORP 0x62
#define ADDR_PH  0x63
#define ADDR_EC  0x64

// Gửi lệnh đọc tới Atlas EZO
float docAtlasEZO(uint8_t addr) {
  Wire.beginTransmission(addr);
  Wire.write('R');           // Lệnh đọc
  Wire.endTransmission();

  delay(600);                // DO cần 600ms, pH/EC/ORP cần 300ms

  Wire.requestFrom(addr, (uint8_t)7);
  char buf[7] = {0};
  int i = 0;
  while (Wire.available() && i < 6) {
    char c = Wire.read();
    if (c != 0x01 && c != 0x02) buf[i++] = c; // Bỏ byte status
  }
  return atof(buf);
}

float doVal  = 0;   // mg/L
float phVal  = 0;   // 0–14
float ecVal  = 0;   // μS/cm → quy ra ppt
float orpVal = 0;   // mV

void docCamBienNuoc() {
  doVal  = docAtlasEZO(ADDR_DO);
  phVal  = docAtlasEZO(ADDR_PH);
  ecVal  = docAtlasEZO(ADDR_EC);
  orpVal = docAtlasEZO(ADDR_ORP);
}
```

### Lưu Ý Quan Trọng

```
1. Hiệu chuẩn DO:
   Lệnh: Wire.write("Cal"); → Wire.write("Cal,0");  (ngâm dung dịch 0%)
         Wire.write("Cal");                          (bão hòa không khí)

2. Đọc DO cần 600ms — không đọc liên tục quá nhanh
3. Đặt đầu dò ở độ sâu 50cm, xa quạt sục khí ≥2m
4. Khi vận chuyển/bảo quản: Ngâm trong nước cất (không ngâm khô)
```

---

## 2.2 Atlas EZO-pH — Độ pH

### Sơ Đồ Mạch

```
CB2S 3.3V ────── EZO-pH VCC
CB2S GND  ────── EZO-pH GND
CB2S GPIO0 ───── EZO-pH SDA  (I2C addr 0x63)
CB2S GPIO1 ───── EZO-pH SCL
                  │
              [BNC connector] ── Đầu dò pH thủy tinh
                                  │
                             [Ngâm ao 50cm, tránh ánh nắng]
```

### Code Hiệu Chuẩn pH 2 Điểm

```cpp
void hieuChuanPH_2diem() {
  // Bước 1: Ngâm đầu dò trong buffer pH 7.0, chờ 2 phút
  Wire.beginTransmission(ADDR_PH);
  Wire.write("Cal,mid,7.00");
  Wire.endTransmission();
  delay(300);

  // Bước 2: Ngâm đầu dò trong buffer pH 4.0 (hoặc 10.0), chờ 2 phút
  Wire.beginTransmission(ADDR_PH);
  Wire.write("Cal,low,4.00");
  Wire.endTransmission();
  delay(300);

  // Xong — lưu vào bộ nhớ EZO tự động
}
```

---

## 2.3 Atlas EZO-EC — Độ Mặn / Điện Dẫn Suất

### Sơ Đồ Mạch

```
CB2S 3.3V ────── EZO-EC VCC
CB2S GND  ────── EZO-EC GND
CB2S GPIO0 ───── EZO-EC SDA  (I2C addr 0x64)
CB2S GPIO1 ───── EZO-EC SCL
                  │
             [Đầu dò EC K1.0] ── Ngâm ao 30cm
```

### Quy Đổi EC → Độ Mặn (ppt)

```cpp
float doManppt = 0;

void quyDoiEC_sang_ppt() {
  // Công thức gần đúng cho nước biển:
  // ppt ≈ EC(mS/cm) × 0.55
  // Hoặc dùng hàm PSS-78 chuẩn hơn:
  float ec_mS = ecVal / 1000.0;   // μS/cm → mS/cm
  doManppt = ec_mS * 0.55;        // Ước tính
}
```

---

## 2.4 DS18B20 — Nhiệt Độ Nước (3 Điểm)

### Sơ Đồ Mạch — 3 Cảm Biến Trên 1 Bus 1-Wire

```
CB2S 3.3V ──┬──── [4.7kΩ] ──── GPIO8
            │                    │
            │              DS18B20 #1 (đầu ao)
            │                    │
            │              DS18B20 #2 (giữa ao)
            │                    │
            │              DS18B20 #3 (cuối ao)
            │                    │
CB2S GND ───┴────────────────────┘

Mỗi DS18B20 có địa chỉ ROM 64-bit riêng
→ 3 cảm biến trên 1 dây → đọc bằng địa chỉ ROM
```

### Code Đọc 3 DS18B20

```cpp
#include <OneWire.h>
#include <DallasTemperature.h>

OneWire ow(8);                    // GPIO8
DallasTemperature ds(&ow);

DeviceAddress addr_dau, addr_giua, addr_cuoi;
float nhietDo[3] = {0, 0, 0};    // [đầu, giữa, cuối]

void scanDS18B20() {
  // Lần đầu: Scan và lưu địa chỉ vào flash
  ds.getAddress(addr_dau,  0);
  ds.getAddress(addr_giua, 1);
  ds.getAddress(addr_cuoi, 2);
}

void docNhietDo() {
  ds.requestTemperatures();
  nhietDo[0] = ds.getTempC(addr_dau);
  nhietDo[1] = ds.getTempC(addr_giua);
  nhietDo[2] = ds.getTempC(addr_cuoi);
}

float nhietDoTrungBinh() {
  return (nhietDo[0] + nhietDo[1] + nhietDo[2]) / 3.0f;
}

float nhietDoCaoNhat() {
  return max({nhietDo[0], nhietDo[1], nhietDo[2]});
}
```

---

## 2.5 Cảm Biến Turbidity — Độ Đục (Analog → ADS1115)

### Sơ Đồ Mạch — Cần Chia Áp 5V → 3.3V

```
Turbidity SEN0189:
  VCC ──────────── 5V (từ nguồn 5VDC)
  GND ──────────── GND
  AOUT ─── [R1=10kΩ] ─── ADS1115 AIN0
                │
            [R2=20kΩ]
                │
               GND

Tính toán chia áp:
  V_AIN0 = 5V × 20k/(10k+20k) = 3.33V ✅ (ADS1115 chịu 3.6V max)
```

### Code Đọc Turbidity

```cpp
#include <Adafruit_ADS1X15.h>
Adafruit_ADS1115 ads;

float turbidityNTU = 0;

void docTurbidity() {
  int16_t raw = ads.readADC_SingleEnded(0);  // AIN0
  float volt = raw * 0.0001875f;              // ADS1115 gain ±6.144V: 0.1875mV/bit

  // SEN0189: 2.5V = 0 NTU (trong), ~1.0V = 3000 NTU (đục)
  // Công thức tuyến tính gần đúng:
  if (volt >= 2.5f) {
    turbidityNTU = 0;
  } else {
    turbidityNTU = -1120.4f * volt * volt + 5742.3f * volt - 4353.8f;
    turbidityNTU = max(0.0f, turbidityNTU);
  }
}
```

---

## 2.6 AJ-SR04M — Mực Nước Ao (Digital → GPIO)

### Sơ Đồ Mạch — ECHO 5V → 3.3V

```
AJ-SR04M:
  VCC  ─── 5V
  GND  ─── GND
  TRIG ─── GPIO13 CB2S (3.3V output OK)
  ECHO ─── [R1=1kΩ] ─── GPIO12 CB2S
                │
            [R2=2kΩ]
                │
               GND
  V_GPIO12 = 5V × 2/(1+2) = 3.33V ✅
```

### Code Đọc Mực Nước

```cpp
#define TRIG_PIN 13
#define ECHO_PIN 12
#define CHIEU_CAO_BO_AO_CM 200  // Chiều cao bờ ao đến đáy (cm)

float mucNuocCM = 0;

void docMucNuoc() {
  digitalWrite(TRIG_PIN, LOW);  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH); delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  long duration = pulseIn(ECHO_PIN, HIGH, 30000); // timeout 30ms
  float khoangCach = duration * 0.0343f / 2.0f;   // cm

  // Mực nước = chiều cao bờ - khoảng cách đo từ trên xuống
  mucNuocCM = CHIEU_CAO_BO_AO_CM - khoangCach;
  mucNuocCM = constrain(mucNuocCM, 0, CHIEU_CAO_BO_AO_CM);
}
```

---

## 2.7 YF-B10 SS304 — Lưu Lượng Nước Cấp/Thải

### Sơ Đồ Mạch — Kết Nối Thẳng 3.3V

```
YF-B10 cấp:            YF-B10 thải:
  VCC ─── 3.3V           VCC ─── 3.3V
  GND ─── GND            GND ─── GND
  SIG ─── GPIO14          SIG ─── GPIO26

Không cần chia áp hay optocoupler.
Tín hiệu 3.3V Hall pulse tương thích thẳng CB2S.
```

### Code Đọc Lưu Lượng

```cpp
volatile uint32_t pulseCapNuoc  = 0;
volatile uint32_t pulseThaiNuoc = 0;

void IRAM_ATTR isrCap()  { pulseCapNuoc++;  }
void IRAM_ATTR isrThai() { pulseThaiNuoc++; }

void initFlowMeters() {
  attachInterrupt(digitalPinToInterrupt(14), isrCap,  FALLING);
  attachInterrupt(digitalPinToInterrupt(26), isrThai, FALLING);
}

// 1 xung YF-B10 DN25 ≈ 3.5mL
float luuLuongCap_L  = 0;
float luuLuongThai_L = 0;

void tinhLuuLuong() {
  luuLuongCap_L  = pulseCapNuoc  * 0.0035f;  // L
  luuLuongThai_L = pulseThaiNuoc * 0.0035f;
}
```

---

## 2.8 Tổng Hợp Đọc Tất Cả Cảm Biến (Chu Kỳ)

```cpp
unsigned long lastDoc = 0;

void docTatCaCamBien() {
  if (millis() - lastDoc < 15000) return;  // Đọc mỗi 15 giây
  lastDoc = millis();

  // Atlas EZO — theo thứ tự, mỗi cái cần 600ms
  doVal  = docAtlasEZO(ADDR_DO);   delay(100);
  phVal  = docAtlasEZO(ADDR_PH);   delay(100);
  ecVal  = docAtlasEZO(ADDR_EC);   delay(100);
  orpVal = docAtlasEZO(ADDR_ORP);  delay(100);

  // DS18B20
  docNhietDo();

  // Analog (ADS1115)
  docTurbidity();

  // Mực nước
  docMucNuoc();

  // Quy đổi
  quyDoiEC_sang_ppt();
  tinhLuuLuong();

  // Kiểm tra ngưỡng và cảnh báo
  kiemTraNguongCanhBao();

  // Publish lên MQTT
  publishCamBienNuoc();
}
```

---

# PHẦN 3 — NGƯỠNG CẢNH BÁO TỪNG THÔNG SỐ

## 3.1 Bảng Ngưỡng Tổng Hợp

### Ao Tôm Thẻ Chân Trắng

| Thông Số | Đơn Vị | Bình Thường | ⚠️ Cảnh Báo | 🔴 Nguy Hiểm | 🚨 Khẩn Cấp |
|---------|:------:|:-----------:|:-----------:|:------------:|:-----------:|
| DO Oxy | mg/L | >5.5 | 4.0–5.5 | 3.0–4.0 | **<3.0** |
| pH | — | 7.8–8.2 | 7.5–7.8 hoặc 8.2–8.5 | 7.2–7.5 hoặc 8.5–9.0 | **<7.0 hoặc >9.0** |
| Nhiệt độ | °C | 25–30 | 23–25 hoặc 30–32 | 20–23 hoặc 32–34 | **<18 hoặc >35** |
| Độ mặn | ppt | 15–25 | 12–15 hoặc 25–28 | 10–12 hoặc 28–30 | **<8 hoặc >32** |
| Turbidity | NTU | <100 | 100–200 | 200–400 | **>400** |
| ORP | mV | 250–350 | 200–250 | 150–200 | **<150** |
| Mực nước | % đầy | 80–100% | 60–80% | 40–60% | **<40%** |

### Ao Cá Tra / Cá Basa

| Thông Số | Đơn Vị | Bình Thường | ⚠️ Cảnh Báo | 🔴 Nguy Hiểm | 🚨 Khẩn Cấp |
|---------|:------:|:-----------:|:-----------:|:------------:|:-----------:|
| DO Oxy | mg/L | >4.5 | 3.5–4.5 | 2.5–3.5 | **<2.5** |
| pH | — | 7.0–8.0 | 6.5–7.0 hoặc 8.0–8.5 | 6.0–6.5 | **<6.0 hoặc >9.0** |
| Nhiệt độ | °C | 27–31 | 24–27 hoặc 31–33 | 22–24 hoặc 33–35 | **<20 hoặc >36** |

---

## 3.2 Cấu Trúc Cảnh Báo 3 Mức

```
MỨC 1 — THÔNG BÁO (ℹ️ Xanh):
  LCD: Hiển thị thông số + dấu ⚠️ (không nhấp nháy)
  App: Gửi notification bình thường
  Hành động tự động: Theo dõi chặt hơn

MỨC 2 — CẢNH BÁO (⚠️ Vàng):
  LCD: Nhấp nháy dòng cảnh báo, bật LED vàng
  App: Gửi notification + âm thanh cảnh báo
  Hành động tự động: Bật thiết bị liên quan

MỨC 3 — KHẨN CẤP (🚨 Đỏ):
  LCD: Toàn màn hình đỏ, nhấp nháy liên tục
  App: Gửi alert khẩn cấp + âm thanh liên tục
  Hành động tự động: Bật hết thiết bị + bình O₂
  Gọi điện: Có thể kết hợp với Zalo OA gọi voice
```

---

# PHẦN 4 — HỆ THỐNG CẢNH BÁO CHI TIẾT

## 4.1 Logic Kiểm Tra Ngưỡng Trong Firmware

```cpp
// ═══════════════════════════════════════════════════════
//  CẤU HÌNH NGƯỠNG (cài từ app qua MQTT)
// ═══════════════════════════════════════════════════════
struct NguongCamBien {
  float do_canh_bao;    float do_nguy_hiem;    float do_khan_cap;
  float ph_min_cb;      float ph_max_cb;
  float ph_min_nd;      float ph_max_nd;
  float ph_min_kc;      float ph_max_kc;
  float nhiet_min_cb;   float nhiet_max_cb;    float nhiet_khan_cap;
  float do_man_min_cb;  float do_man_max_cb;
  float turbidity_cb;   float turbidity_nd;
  float orp_cb;         float orp_nd;
  float muc_nuoc_cb;    float muc_nuoc_nd;
};

NguongCamBien nguong = {
  // DO (mg/L) — ao tôm
  .do_canh_bao = 4.5, .do_nguy_hiem = 3.5, .do_khan_cap = 3.0,
  // pH
  .ph_min_cb = 7.5,   .ph_max_cb = 8.5,
  .ph_min_nd = 7.2,   .ph_max_nd = 8.8,
  .ph_min_kc = 7.0,   .ph_max_kc = 9.2,
  // Nhiệt độ (°C)
  .nhiet_min_cb = 23.0, .nhiet_max_cb = 32.0, .nhiet_khan_cap = 35.0,
  // Độ mặn (ppt)
  .do_man_min_cb = 12.0, .do_man_max_cb = 28.0,
  // Turbidity (NTU)
  .turbidity_cb = 200, .turbidity_nd = 400,
  // ORP (mV)
  .orp_cb = 200, .orp_nd = 150,
  // Mực nước (%)
  .muc_nuoc_cb = 70, .muc_nuoc_nd = 50,
};

// Trạng thái cảnh báo hiện tại
struct TrangThaiCanhBao {
  uint8_t do_muc;      // 0=OK, 1=cảnh báo, 2=nguy hiểm, 3=khẩn cấp
  uint8_t ph_muc;
  uint8_t nhiet_muc;
  uint8_t do_man_muc;
  uint8_t turbidity_muc;
  uint8_t orp_muc;
  uint8_t muc_nuoc_muc;
  unsigned long lan_gui_cuoi[7]; // Chống gửi lặp
};

TrangThaiCanhBao canhBao = {};

// ═══════════════════════════════════════════════════════
//  KIỂM TRA NGƯỠNG — Gọi sau mỗi lần đọc cảm biến
// ═══════════════════════════════════════════════════════
void kiemTraNguongCanhBao() {
  kiemTraDO();
  kiemTraPH();
  kiemTraNhietDo();
  kiemTraDoMan();
  kiemTraTurbidity();
  kiemTraORP();
  kiemTraMucNuoc();
}
```

---

## 4.2 Kiểm Tra DO — Chi Tiết Nhất

```cpp
void kiemTraDO() {
  uint8_t muc_moi;

  if      (doVal < nguong.do_khan_cap)   muc_moi = 3;  // KHẨN CẤP
  else if (doVal < nguong.do_nguy_hiem)  muc_moi = 2;  // NGUY HIỂM
  else if (doVal < nguong.do_canh_bao)   muc_moi = 1;  // CẢNH BÁO
  else                                   muc_moi = 0;  // OK

  // Xử lý theo mức
  switch (muc_moi) {
    case 3:  // KHẨN CẤP — DO < 3.0 mg/L
      // 1. Bật TẤT CẢ quạt sục khí
      for (int i = 1; i <= 4; i++) setRelay(i, true);
      // 2. Bật 2 máy thổi khí
      setRelay(5, true);  setRelay(6, true);
      // 3. Bật bơm dự phòng khẩn cấp
      setRelay(26, true);
      // 4. Mở van O₂ bình khẩn cấp 60 giây
      setRelay(27, true);
      // 5. Gửi alert ngay (không chờ anti-spam)
      guiCanhBao("DO", 3, doVal, "mg/L");
      break;

    case 2:  // NGUY HIỂM — DO 3.0–3.5 mg/L
      for (int i = 1; i <= 4; i++) setRelay(i, true);
      setRelay(5, true);  setRelay(6, true);
      guiCanhBaoAntiSpam("DO", 2, doVal, "mg/L", 5); // 5 phút/lần
      break;

    case 1:  // CẢNH BÁO — DO 3.5–4.5 mg/L
      // Bật 2 quạt đầu
      setRelay(1, true);  setRelay(2, true);
      guiCanhBaoAntiSpam("DO", 1, doVal, "mg/L", 15); // 15 phút/lần
      break;

    case 0:  // OK — Tiết kiệm điện nếu DO cao
      if (doVal > 6.5) {
        // Tắt bớt quạt nếu DO quá cao
        setRelay(3, false); setRelay(4, false);
      }
      break;
  }

  canhBao.do_muc = muc_moi;
  capNhatLCD();
}
```

---

## 4.3 Kiểm Tra pH

```cpp
void kiemTraPH() {
  uint8_t muc_moi = 0;

  // pH thấp (axit)
  if      (phVal < nguong.ph_min_kc)  muc_moi = 3;
  else if (phVal < nguong.ph_min_nd)  muc_moi = 2;
  else if (phVal < nguong.ph_min_cb)  muc_moi = 1;

  // pH cao (kiềm)
  if      (phVal > nguong.ph_max_kc)  muc_moi = max(muc_moi, (uint8_t)3);
  else if (phVal > nguong.ph_max_nd)  muc_moi = max(muc_moi, (uint8_t)2);
  else if (phVal > nguong.ph_max_cb)  muc_moi = max(muc_moi, (uint8_t)1);

  switch (muc_moi) {
    case 3:
    case 2:
      // pH quá thấp → Bật bơm châm vôi 30 giây
      if (phVal < nguong.ph_min_nd) {
        setRelay(9, true);   // K9 bơm vôi
        delay(30000);
        setRelay(9, false);
      }
      // pH quá cao → Tăng thay nước
      if (phVal > nguong.ph_max_nd) {
        setRelay(7, true);   // K7 bơm thải
        delay(60000);
        setRelay(7, false);
      }
      guiCanhBaoAntiSpam("pH", muc_moi, phVal, "", 10);
      break;

    case 1:
      guiCanhBaoAntiSpam("pH", 1, phVal, "", 20);
      break;
  }

  canhBao.ph_muc = muc_moi;
}
```

---

## 4.4 Kiểm Tra Nhiệt Độ, Độ Mặn, Turbidity, ORP, Mực Nước

```cpp
void kiemTraNhietDo() {
  float t = nhietDoCaoNhat();  // Dùng điểm cao nhất trong ao
  uint8_t muc = 0;

  if      (t > nguong.nhiet_khan_cap || t < 18.0) muc = 3;
  else if (t > nguong.nhiet_max_cb   || t < nguong.nhiet_min_cb) muc = 1;

  if (muc > 0) guiCanhBaoAntiSpam("NHIET", muc, t, "C", 20);
  canhBao.nhiet_muc = muc;
}

void kiemTraDoMan() {
  uint8_t muc = 0;
  if      (doManppt < 8  || doManppt > 32) muc = 3;
  else if (doManppt < 12 || doManppt > 28) muc = 2;
  else if (doManppt < 15 || doManppt > 25) muc = 1;

  // Độ mặn giảm đột ngột → Có thể mưa lớn đổ vào
  if (muc > 0) guiCanhBaoAntiSpam("DO_MAN", muc, doManppt, "ppt", 15);
  canhBao.do_man_muc = muc;
}

void kiemTraTurbidity() {
  uint8_t muc = 0;
  if      (turbidityNTU > nguong.turbidity_nd) muc = 2;
  else if (turbidityNTU > nguong.turbidity_cb) muc = 1;

  if (muc > 0) guiCanhBaoAntiSpam("TURBIDITY", muc, turbidityNTU, "NTU", 30);
  canhBao.turbidity_muc = muc;
}

void kiemTraORP() {
  uint8_t muc = 0;
  if      (orpVal < nguong.orp_nd) muc = 2;  // ORP thấp = vi khuẩn kị khí, H₂S
  else if (orpVal < nguong.orp_cb) muc = 1;

  if (muc > 0) guiCanhBaoAntiSpam("ORP", muc, orpVal, "mV", 20);
  canhBao.orp_muc = muc;
}

void kiemTraMucNuoc() {
  float mucNuocPct = (mucNuocCM / CHIEU_CAO_BO_AO_CM) * 100.0f;
  uint8_t muc = 0;
  if      (mucNuocPct < nguong.muc_nuoc_nd) muc = 2;
  else if (mucNuocPct < nguong.muc_nuoc_cb) muc = 1;

  if (muc == 2) {
    // Mực nước thấp → Tự động bật bơm cấp nước
    setRelay(0, true);  // K0 bơm cấp
  }
  if (muc > 0) guiCanhBaoAntiSpam("MUC_NUOC", muc, mucNuocPct, "%", 10);
  canhBao.muc_nuoc_muc = muc;
}
```

---

## 4.5 Hàm Gửi Cảnh Báo MQTT → Điện Thoại

```cpp
// Anti-spam: Không gửi lại cùng loại cảnh báo trong vòng X phút
void guiCanhBaoAntiSpam(const char* loai, uint8_t muc,
                         float gia_tri, const char* don_vi,
                         uint16_t chu_ky_phut) {
  // Tìm index
  static const char* LOAI[] = {"DO","pH","NHIET","DO_MAN","TURBIDITY","ORP","MUC_NUOC"};
  int idx = -1;
  for (int i = 0; i < 7; i++) {
    if (strcmp(loai, LOAI[i]) == 0) { idx = i; break; }
  }
  if (idx < 0) return;

  unsigned long now = millis();
  if (now - canhBao.lan_gui_cuoi[idx] < (unsigned long)chu_ky_phut * 60000UL) return;
  canhBao.lan_gui_cuoi[idx] = now;

  guiCanhBao(loai, muc, gia_tri, don_vi);
}

void guiCanhBao(const char* loai, uint8_t muc, float gia_tri, const char* don_vi) {
  const char* TEN_MUC[] = {"OK", "canh_bao", "nguy_hiem", "khan_cap"};
  const char* ICON[]    = {"✅", "⚠️", "🔴", "🚨"};

  StaticJsonDocument<256> doc;
  doc["loai"]     = loai;
  doc["muc"]      = TEN_MUC[muc];
  doc["gia_tri"]  = gia_tri;
  doc["don_vi"]   = don_vi;
  doc["thoi_gian"]= (uint32_t)time(nullptr);

  char topic[64];
  snprintf(topic, sizeof(topic), "aqua/%s/alert/%s",
           deviceID.c_str(), loai);

  char buf[256];
  serializeJson(doc, buf);
  mqtt.publish(topic, buf, false);

  // Log Serial
  Serial.printf("[CANH BAO %s] %s: %.2f%s — Muc: %s\n",
                ICON[muc], loai, gia_tri, don_vi, TEN_MUC[muc]);
}
```

**JSON gửi lên NextFarm app:**
```json
{
  "loai": "DO",
  "muc": "khan_cap",
  "gia_tri": 2.8,
  "don_vi": "mg/L",
  "thoi_gian": 1747123456
}
```

---

# PHẦN 5 — HIỂN THỊ OLED SH1106 1.3" & CÁC TRẠNG THÁI

## 5.1 Màn Hình Bình Thường

```
┌────────────────────┐
│AUTO DO:5.8 pH:8.1  │  ← Dòng 1: Chế độ + DO + pH
│T:27.5C Sal:18.2ppt │  ← Dòng 2: Nhiệt độ + Độ mặn
│ORP:285mV Nuc:92%   │  ← Dòng 3: ORP + Mực nước
│An:12:00  ThuAn:75% │  ← Dòng 4: Ca ăn tiếp + % thức ăn
└────────────────────┘
```

## 5.2 Màn Hình Cảnh Báo Mức 1 (⚠️)

```
┌────────────────────┐
│AUTO DO:4.3⚠ pH:8.1 │  ← DO có dấu ⚠ (không nhấp nháy)
│T:27.5C Sal:18.2ppt │
│ORP:285mV Nuc:92%   │
│Da bat 2 quat thong!│  ← Thông báo hành động
└────────────────────┘
```

## 5.3 Màn Hình Nguy Hiểm Mức 2 (🔴 nhấp nháy)

```
┌────────────────────┐   ← Dòng 1 nhấp nháy
│!NGUY HIEM! DO:3.4  │
│Da bat het 4 quat   │
│+2 blower. KT ao!   │
│Goi: 090 224 3822   │   ← Số điện thoại kỹ thuật
└────────────────────┘
```

## 5.4 Màn Hình Khẩn Cấp Mức 3 (🚨 nhấp nháy nhanh + LED đỏ)

```
┌────────────────────┐   ← TOÀN MÀN HÌNH NHẤP NHÁNH
│[!!!] KHAN CAP [!!!]│
│DO: 2.8mg/L QUA THAP│
│Da bat O2 binh DP   │
│BAO DONG NGAY!!!    │
└────────────────────┘
```

## 5.5 Code Hiển Thị LCD

```cpp
unsigned long lastLcdUpdate = 0;
bool lcdBlink = false;

void capNhatLCD() {
  if (millis() - lastLcdUpdate < 1000) return;  // Cập nhật 1 giây/lần
  lastLcdUpdate = millis();
  lcdBlink = !lcdBlink;

  // Xác định mức cảnh báo cao nhất
  uint8_t mucCaoNhat = max({
    canhBao.do_muc, canhBao.ph_muc, canhBao.nhiet_muc,
    canhBao.do_man_muc, canhBao.turbidity_muc,
    canhBao.orp_muc, canhBao.muc_nuoc_muc
  });

  if (mucCaoNhat == 3) {
    hienThiKhanCap();
  } else if (mucCaoNhat == 2) {
    hienThiNguyHiem();
  } else {
    hienThi BinhThuong();
  }
}

void hienThiBinhThuong() {
  char buf[21];

  lcd.setCursor(0, 0);
  snprintf(buf, sizeof(buf), "%s DO:%.1f pH:%.1f  ",
           cheDo == TU_DONG ? "AUTO" : "TAY ", doVal, phVal);
  lcd.print(buf);

  lcd.setCursor(0, 1);
  snprintf(buf, sizeof(buf), "T:%.1fC Sal:%.1fppt",
           nhietDoTrungBinh(), doManppt);
  lcd.print(buf);

  lcd.setCursor(0, 2);
  float mucNuocPct = (mucNuocCM / CHIEU_CAO_BO_AO_CM) * 100.0f;
  snprintf(buf, sizeof(buf), "ORP:%dmV Nuc:%.0f%%  ",
           (int)orpVal, mucNuocPct);
  lcd.print(buf);

  lcd.setCursor(0, 3);
  // Hiện ca cho ăn tiếp theo và % thức ăn còn
  lcd.print("An:xx:xx  ThuAn:xx%");
}

void hienThiNguyHiem() {
  if (lcdBlink) {
    lcd.setCursor(0, 0); lcd.print("!NGUY HIEM!         ");
    if (canhBao.do_muc >= 2) {
      char buf[21];
      snprintf(buf, sizeof(buf), "DO: %.1f mg/L       ", doVal);
      lcd.setCursor(0, 1); lcd.print(buf);
    }
    lcd.setCursor(0, 2); lcd.print("Da bat het thiet bi ");
    lcd.setCursor(0, 3); lcd.print("Kiem tra ao ngay!   ");
  } else {
    hienThiBinhThuong();  // Xen kẽ 1 giây bình thường để vẫn thấy số liệu
  }
}

void hienThiKhanCap() {
  // Nhấp nháy liên tục, không xen kẽ
  if (lcdBlink) {
    lcd.setCursor(0, 0); lcd.print("[!!!] KHAN CAP [!!!]");
    char buf[21];

    if (canhBao.do_muc == 3) {
      snprintf(buf, sizeof(buf), "DO: %.1fmg/L QUA THAP", doVal);
    } else if (canhBao.ph_muc == 3) {
      snprintf(buf, sizeof(buf), "pH: %.2f - NGUY HIEM", phVal);
    } else {
      snprintf(buf, sizeof(buf), "Kiem tra ngay!!!    ");
    }
    lcd.setCursor(0, 1); lcd.print(buf);
    lcd.setCursor(0, 2); lcd.print("Da bat O2 binh DP   ");
    lcd.setCursor(0, 3); lcd.print("BAO DONG NGAY!!!    ");
  } else {
    lcd.clear();  // Màn hình trắng xen kẽ để tạo hiệu ứng nhấp nháy mạnh
  }
}
```

---

# PHẦN 6 — MQTT TOPICS CẢM BIẾN & CẢNH BÁO

## 6.1 Publish Dữ Liệu Cảm Biến

**Topic:** `aqua/{deviceID}/sensor` — publish mỗi 15 giây

```json
{
  "do":    5.82,
  "ph":    8.12,
  "ec":    28450,
  "do_man_ppt": 15.6,
  "orp":   285,
  "nhiet_do": [27.5, 27.8, 27.3],
  "turbidity_ntu": 85,
  "muc_nuoc_cm": 180,
  "muc_nuoc_pct": 90,
  "flow_cap_L": 1250.5,
  "flow_thai_L": 850.2,
  "timestamp": 1747123456
}
```

## 6.2 Publish Cảnh Báo

**Topic:** `aqua/{deviceID}/alert/{loai}`

| Topic | Khi nào | Payload |
|-------|---------|---------|
| `.../alert/DO` | DO vượt ngưỡng | `{"muc":"khan_cap","gia_tri":2.8}` |
| `.../alert/pH` | pH bất thường | `{"muc":"nguy_hiem","gia_tri":6.8}` |
| `.../alert/NHIET` | Nhiệt độ bất thường | `{"muc":"canh_bao","gia_tri":33.2}` |
| `.../alert/DO_MAN` | Độ mặn sai | `{"muc":"canh_bao","gia_tri":8.5}` |
| `.../alert/TURBIDITY` | Nước đục | `{"muc":"canh_bao","gia_tri":350}` |
| `.../alert/ORP` | ORP thấp | `{"muc":"nguy_hiem","gia_tri":145}` |
| `.../alert/MUC_NUOC` | Mực nước thấp | `{"muc":"canh_bao","gia_tri":65}` |
| `.../alert/DIEN` | Mất điện / phục hồi | `{"su_kien":"mat_dien"}` |

## 6.3 Subscribe Nhận Lệnh Điều Chỉnh Ngưỡng Từ App

**Topic:** `aqua/{deviceID}/nguong/set`

```json
{
  "do_canh_bao": 4.5,
  "do_nguy_hiem": 3.5,
  "do_khan_cap": 3.0,
  "ph_min_cb": 7.5,
  "ph_max_cb": 8.5,
  "nhiet_min_cb": 23.0,
  "nhiet_max_cb": 32.0,
  "do_man_min_cb": 12.0,
  "do_man_max_cb": 28.0,
  "turbidity_cb": 200,
  "orp_cb": 200,
  "muc_nuoc_cb": 70
}
```

> Tất cả ngưỡng cài từ app — không cần sửa code.

---

# PHẦN 7 — CHỐNG NHIỄU & ĐỘ TIN CẬY CẢM BIẾN

## 7.1 Lọc Nhiễu (Smoothing) Cho Giá Trị Cảm Biến

```cpp
// Moving average 5 mẫu để loại bỏ spike bất thường
#define N_SAMPLES 5

class SensorFilter {
  float buf[N_SAMPLES] = {0};
  int   idx = 0;
  bool  full = false;
public:
  float add(float val) {
    // Loại outlier: Nếu lệch >30% so với giá trị trước → bỏ qua
    if (full && abs(val - buf[(idx-1+N_SAMPLES)%N_SAMPLES]) > val * 0.3f)
      return getAvg();

    buf[idx] = val;
    idx = (idx + 1) % N_SAMPLES;
    if (idx == 0) full = true;
    return getAvg();
  }

  float getAvg() {
    int n = full ? N_SAMPLES : idx;
    if (n == 0) return 0;
    float sum = 0;
    for (int i = 0; i < n; i++) sum += buf[i];
    return sum / n;
  }
};

SensorFilter filterDO, filterPH, filterEC, filterORP;

void docCamBienVoiFilter() {
  float rawDO  = docAtlasEZO(ADDR_DO);
  float rawPH  = docAtlasEZO(ADDR_PH);
  float rawEC  = docAtlasEZO(ADDR_EC);
  float rawORP = docAtlasEZO(ADDR_ORP);

  doVal  = filterDO.add(rawDO);
  phVal  = filterPH.add(rawPH);
  ecVal  = filterEC.add(rawEC);
  orpVal = filterORP.add(rawORP);
}
```

## 7.2 Phát Hiện Cảm Biến Hỏng

```cpp
void kiemTraCamBienHong() {
  bool co_loi = false;

  // DO: 0 hoặc âm hoặc > 20 = hỏng
  if (doVal <= 0 || doVal > 20) {
    mqtt.publish(("aqua/"+deviceID+"/loi").c_str(),
      "{\"cam_bien\":\"DO\",\"ma\":\"SENSOR_LOI\"}");
    co_loi = true;
  }

  // pH: <2 hoặc >12 = hỏng
  if (phVal < 2.0 || phVal > 12.0) {
    mqtt.publish(("aqua/"+deviceID+"/loi").c_str(),
      "{\"cam_bien\":\"pH\",\"ma\":\"SENSOR_LOI\"}");
    co_loi = true;
  }

  // Nhiệt độ: DS18B20 trả -127°C khi không tìm thấy
  for (int i = 0; i < 3; i++) {
    if (nhietDo[i] == DEVICE_DISCONNECTED_C) {
      char msg[64];
      snprintf(msg, sizeof(msg),
        "{\"cam_bien\":\"DS18B20_%d\",\"ma\":\"KHONG_TIM_THAY\"}", i);
      mqtt.publish(("aqua/"+deviceID+"/loi").c_str(), msg);
    }
  }

  // Hiển thị lỗi lên LCD
  if (co_loi) {
    lcd.setCursor(0, 3);
    lcd.print("!Cam bien loi - KT! ");
  }
}
```

---

# PHẦN 8 — BẢNG GIÁ ĐẦY ĐỦ CẢM BIẾN & MẠCH THU NHẬN

## 8.1 Chi Tiết Từng Thiết Bị Với Giá TL + TT

| STT | Thiết Bị | Model | Hãng | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|---------|-------|------|-------:|-------:|:--:|--------:|--------:|
| 1 | Module Atlas EZO-DO | EZO-DO | Atlas Scientific | 250.000đ | 350.000đ | 1 | 250.000đ | 350.000đ |
| 2 | Đầu dò DO điện hóa | DO Probe | Atlas Scientific | 550.000đ | 800.000đ | 1 | 550.000đ | 800.000đ |
| 3 | Module Atlas EZO-pH | EZO-pH | Atlas Scientific | 180.000đ | 280.000đ | 1 | 180.000đ | 280.000đ |
| 4 | Đầu dò pH thủy tinh | pH Probe | Atlas Scientific | 180.000đ | 280.000đ | 1 | 180.000đ | 280.000đ |
| 5 | Module Atlas EZO-EC | EZO-EC | Atlas Scientific | 220.000đ | 330.000đ | 1 | 220.000đ | 330.000đ |
| 6 | Đầu dò EC K1.0 | EC K1.0 | Atlas Scientific | 165.000đ | 250.000đ | 1 | 165.000đ | 250.000đ |
| 7 | Module Atlas EZO-ORP | EZO-ORP | Atlas Scientific | 220.000đ | 330.000đ | 1 | 220.000đ | 330.000đ |
| 8 | Đầu dò ORP bạch kim | ORP Probe | Atlas Scientific | 180.000đ | 270.000đ | 1 | 180.000đ | 270.000đ |
| 9 | DS18B20 waterproof 3m | DS18B20 | — | 30.000đ | 38.000đ | 3 | 90.000đ | 114.000đ |
| 10 | Cảm biến độ đục | SEN0189 | DFRobot | 150.000đ | 200.000đ | 1 | 150.000đ | 200.000đ |
| 11 | ADS1115 ADC 16-bit | ADS1115 | TI | 35.000đ | 42.000đ | 1 | 35.000đ | 42.000đ |
| 12 | AJ-SR04M mực nước + cột gắn | AJ-SR04M | — | 60.000đ | 73.000đ | 1 | 60.000đ | 73.000đ |
| 13 | Flow meter SS304 DN25 | YF-B10 | — | 220.000đ | 250.000đ | 2 | 440.000đ | 500.000đ |
| 14 | OLED SH1106 1.3" I2C | 2004 | — | 70.000đ | 80.000đ | 1 | 70.000đ | 80.000đ |
| 15 | Điện trở chia áp (R pack) | 1k+2k+10k+20k | — | 5.000đ | 5.000đ | 1 bộ | 5.000đ | 5.000đ |
| 16 | Dây tín hiệu chống nhiễu STP 5m | STP | — | 60.000đ | 60.000đ | 5 | 300.000đ | 300.000đ |
| 17 | Hộp bảo vệ cảm biến IP68 | — | — | 25.000đ | 30.000đ | 4 | 100.000đ | 120.000đ |
| | | | | | | | **3.195.000đ** | **4.524.000đ** |

## 8.2 Phụ Kiện Hiệu Chuẩn

| STT | Phụ Kiện | Giá TL | Giá TT | Chu kỳ Thay |
|:---:|---------|-------:|-------:|------------|
| 1 | Buffer pH 4.0 (250mL) | 45.000đ | 60.000đ | 6 tháng |
| 2 | Buffer pH 7.0 (250mL) | 45.000đ | 60.000đ | 6 tháng |
| 3 | Buffer pH 10.0 (250mL) | 45.000đ | 60.000đ | 6 tháng |
| 4 | Dung dịch DO 0% (Sodium Sulfite) | 45.000đ | 55.000đ | 3 tháng |
| 5 | Dung dịch ngâm DO | 35.000đ | 45.000đ | 6 tháng |
| 6 | Dung dịch KCl 3M ngâm pH | 35.000đ | 45.000đ | 6 tháng |
| 7 | EC chuẩn 1413 μS/cm (250mL) | 35.000đ | 50.000đ | 6 tháng |
| 8 | EC chuẩn 12.88 mS/cm (250mL) | 40.000đ | 55.000đ | 6 tháng |
| 9 | Dung dịch ORP quinhydrone | 80.000đ | 100.000đ | 3 tháng |
| 10 | Nước cất rửa cảm biến (1L) | 15.000đ | 20.000đ | Hàng tháng |
| 11 | Màng điện cực DO dự phòng | 85.000đ | 110.000đ | 3–6 tháng |
| 12 | Khúc xạ kế đo độ mặn cầm tay | 180.000đ | 220.000đ | Không thay |
| | **Tổng phụ kiện** | **685.000đ** | **880.000đ** | |

---

*Phiên bản: 1.0 | Cập nhật: 2026-05-12*
