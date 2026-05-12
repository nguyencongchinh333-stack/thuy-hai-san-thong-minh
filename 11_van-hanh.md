# 11 — Vận Hành

---

## 11.1 Đọc Màn Hình OLED SH1106

### Bố Cục Hiển Thị Bình Thường

```
┌────────────────────────────────┐
│ DO:6.8  pH:7.5   T:28.3°C     │  ← Dòng 1
│ EC:22ms ORP:310  Nc:85cm      │  ← Dòng 2
│ Quat:2  Blw:OFF  UV:ON        │  ← Dòng 3
│ An:09:00 [D:95] Ngay:45       │  ← Dòng 4
└────────────────────────────────┘

Giải thích:
  DO:6.8   → Oxy hòa tan 6.8 mg/L (tốt)
  pH:7.5   → pH nước ao (tối ưu tôm)
  T:28.3°C → Nhiệt độ nước
  EC:22ms  → Độ dẫn điện 22 mS/cm (≈ 11‰ độ mặn)
  ORP:310  → ORP 310 mV (tốt)
  Nc:85cm  → Mực nước 85 cm
  Quat:2   → Đang chạy 2 quạt sục khí
  Blw:OFF  → Blower tắt
  UV:ON    → Đèn UV đang chạy
  An:09:00 → Ca cho ăn tiếp theo lúc 9:00
  [D:95]   → 95% thức ăn còn lại (cần nạp lại khi < 20%)
  Ngay:45  → Ngày nuôi thứ 45
```

### Màn Hình Trạng Thái Cảnh Báo

```
CẢNH BÁO DO (cấp 2):
┌────────────────────────────────┐
│ >>> CANH BAO DO <<<            │
│ DO: 3.4 mg/L  [4 QUAT ON]    │
│ pH:7.4  EC:22  T:28.1         │
│ 10:25:44  Ngay 45             │
└────────────────────────────────┘

KHẨN CẤP DO (cấp 0):
┌────────────────────────────────┐
│ !!! KHAN CAP DO !!!            │
│ DO: 1.8 mg/L  O2:ON           │
│ TAT CA QUAT + BLOWER ON       │
│ GOI HO TRO NGAY!              │
└────────────────────────────────┘
```

### Chuyển Đổi Màn Hình

```
Nhấn nút (nếu có) hoặc xem qua MQTT:

Màn hình 1: DO, pH, EC, ORP, Nhiệt độ, Mực nước
Màn hình 2: Trạng thái relay (K0–K15)
Màn hình 3: Trạng thái relay (K16–K30)
Màn hình 4: Lịch cho ăn + đếm ca hôm nay
Màn hình 5: Turbidity, Flow rate, Áp suất
Màn hình 6: WiFi RSSI, MQTT status, Uptime
```

---

## 11.2 Lệnh MQTT Điều Khiển

### Điều Khiển Relay Đơn Lẻ

```bash
# Bật quạt K1
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/relay/1/set" \
  -m '{"state": 1}'

# Tắt quạt K1
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/relay/1/set" \
  -m '{"state": 0}'

# Bật bơm vôi K9 trong 60 giây rồi tự tắt
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/relay/9/set" \
  -m '{"state": 1, "duration": 60}'

# Bật tất cả quạt khẩn cấp
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/mode/set" \
  -m '{"mode": "EMERGENCY"}'
```

### Điều Khiển Chế Độ

```bash
# Chuyển sang chế độ tự động
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/mode/set" \
  -m '{"mode": "AUTO"}'

# Chuyển sang thủ công
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/mode/set" \
  -m '{"mode": "MANUAL"}'
```

### Điều Chỉnh Lịch Cho Ăn

```bash
# Cài đặt lịch cho ăn (tôm - 6 ca)
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/feed/set" \
  -m '{
    "species": "shrimp",
    "schedule": [
      {"hour": 6,  "min": 0,  "duration_sec": 5, "feeders": 15},
      {"hour": 9,  "min": 0,  "duration_sec": 5, "feeders": 15},
      {"hour": 12, "min": 0,  "duration_sec": 5, "feeders": 15},
      {"hour": 15, "min": 0,  "duration_sec": 5, "feeders": 15},
      {"hour": 18, "min": 0,  "duration_sec": 5, "feeders": 15},
      {"hour": 21, "min": 0,  "duration_sec": 5, "feeders": 15}
    ],
    "do_min_feed": 4.0,
    "do_half_feed": 3.0
  }'

# Cho ăn ngay (bỏ qua lịch)
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/feed/trigger" \
  -m '{"feeders": 15, "duration": 5}'
```

### Cài Đặt Ngưỡng

```bash
# Cập nhật ngưỡng DO
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/threshold/set" \
  -m '{
    "do_l5": 6.0,
    "do_l4": 5.0,
    "do_l3": 4.0,
    "do_l2": 3.0,
    "do_l1": 2.0,
    "ph_low_lime": 6.8,
    "ph_target": 7.5,
    "temp_heat_on": 25.0
  }'
```

### Đọc Trạng Thái

```bash
# Subscribe nhận dữ liệu cảm biến
mosquitto_sub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/sensor" -v

# Kết quả mẫu:
# aqua/aqua_001/sensor {
#   "do": 6.8, "ph": 7.5, "ec": 22.1, "orp": 315,
#   "temp": [28.3, 27.1, 28.8],
#   "level_cm": 85, "turbidity_ntu": 12,
#   "flow_in_lpm": 5.2, "flow_out_lpm": 4.8,
#   "pressure_bar": 0.085,
#   "ts": 1747584000
# }

# Subscribe nhận cảnh báo
mosquitto_sub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/alert/#" -v
```

---

## 11.3 Theo Dõi DO Sáng Sớm

### Quy Trình Kiểm Tra Sáng Sớm (5:00–7:00)

```
5:00 AM — Kiểm tra tự động (firmware):
  1. Đọc DO lần thứ nhất
  2. Nếu DO < 5.0 → bật blower K5+K6 ngay
  3. MQTT gửi: aqua/{id}/alert/dawn_check

5:30 AM — Kiểm tra thứ hai:
  1. Nếu DO không tăng sau 30 phút sục khí → CẢNH BÁO NGHIÊM TRỌNG
  2. Bật tất cả quạt + blower
  3. Push notification đến người vận hành

6:00 AM — Trước ca cho ăn đầu tiên:
  1. Kiểm tra DO > 4.0 mg/L → cho ăn bình thường
  2. Kiểm tra DO 3.0–4.0 → giảm 50% khẩu phần
  3. Kiểm tra DO < 3.0 → BỎ CA ĂN, tiếp tục sục khí

Người vận hành cần làm gì sáng sớm:
  □ Mở app NextFarm → xem DO hiện tại
  □ Kiểm tra quạt đang chạy mấy cái
  □ Kiểm tra màu nước ao (xanh lá nhạt = tốt)
  □ Quan sát tôm/cá có nổi đầu không (dấu hiệu thiếu oxy)
  □ Nếu tôm nổi đầu tại bờ → DO KHẨN CẤP → gọi hỗ trợ ngay
```

### Dấu Hiệu Tôm/Cá Thiếu Oxy Trực Quan

```
Mức nhẹ (DO 3–4 mg/L):
  - Tôm tập trung ở vùng có nhiều bọt khí (quanh quạt)
  - Cá bơi chậm hơn bình thường

Mức trung bình (DO 2–3 mg/L):
  - Tôm/cá nổi gần mặt nước
  - Miệng há hóp hóp (hô hấp tăng)

Mức khẩn cấp (DO < 2 mg/L):
  - Tôm/cá nổi đầu hàng loạt tại bờ
  - Cá nhảy lên mặt nước
  → BẬT NGAY TẤT CẢ THIẾT BỊ SỤC KHÍ
  → GỌI HỖ TRỢ KỸ THUẬT NGAY LẬP TỨC
```

---

## 11.4 Xử Lý Sự Cố

### Sự Cố 1: DO Thấp Liên Tục

```
Triệu chứng: DO < 4.0 mg/L > 2 giờ dù quạt đang chạy hết

Kiểm tra tuần tự:
  1. Quạt có chạy không? → Kiểm tra tiếng quạt, nhìn bọt khí
  2. Mật độ nuôi có quá cao không? → So sánh với lịch nuôi
  3. Thời điểm: Sáng sớm trước bình minh (hiện tượng tự nhiên)?
  4. Tảo có bùng phát không? → Nước màu xanh đậm/nâu đỏ
  5. Thời tiết: Trời흐m, áp suất thấp → DO tan ít hơn
  6. Nhiệt độ nước > 32°C → DO bão hòa thấp hơn

Xử lý:
  - Ngay: Bật tất cả quạt + blower + O₂ (K29)
  - Ngắn hạn: Giảm cho ăn 50%
  - Giảm mật độ nuôi nếu cần (san ao)
  - Kiểm tra và vệ sinh quạt (rêu bám)
  - Thay nước 20% nếu tảo bùng phát
```

### Sự Cố 2: pH Bất Thường

```
pH liên tục giảm (< 7.0):
  Nguyên nhân:
    - Phân hủy chất hữu cơ đáy ao
    - CO₂ tích tụ (thiếu sục khí)
    - Tảo chết hàng loạt (thay màu nước)
  Xử lý:
    - Firmware tự bơm vôi K9 (kiểm tra vôi còn không)
    - Tăng sục khí đuổi CO₂
    - Bơm bùn K26 nếu đáy ao nhiều phân hủy
    - Thay 20–30% nước nếu pH không cải thiện sau 2 giờ

pH tăng cao (> 8.5) buổi chiều:
  Nguyên nhân: Tảo quang hợp mạnh (buổi chiều)
  Xử lý:
    - Kiểm tra lại buổi tối (pH thường giảm trở lại)
    - Nếu > 9.0: Thay nước ngay, giảm lượng tảo
    - Dùng đường/mật rỉ (pha loãng thêm vi khuẩn)
    - KHÔNG bơm thêm vôi khi pH cao
```

### Sự Cố 3: Máy Cho Ăn Hỏng

```
Triệu chứng: MQTT báo "FEEDER_NO_RESPONSE" hoặc không có log ca ăn

Kiểm tra:
  1. Kiểm tra cầu chì máy cho ăn
  2. Kiểm tra relay K20–K23 bằng đo vạn năng
     (đo điện áp trên COM-NO khi bật)
  3. Kiểm tra dây nối motor
  4. Thử bật thủ công: relay on 3 giây

Xử lý tạm thời nếu 1 máy hỏng:
  - Tắt máy hỏng trong config
  - Tăng duration_sec 3 máy còn lại lên 133% (bù lượng ăn)
  - Gửi MQTT: {"feeders": 7, "duration": 7}  (bỏ qua máy K20)

Thức ăn cạn (phát hiện qua cân trọng lượng nếu có):
  - MQTT alert: "FEED_LOW_20PCT"
  - Người vận hành nạp thêm trong vòng 12 giờ
```

### Sự Cố 4: Cảm Biến Lỗi

```
EZO-DO báo giá trị bất thường (> 20 mg/L hoặc < 0):
  Nguyên nhân thường gặp:
    - Màng đầu dò bị rách hoặc khô
    - Cáp đứt hoặc đầu nối lỏng
    - Đầu dò cần hiệu chỉnh lại
  Xử lý:
    - Rút đầu dò lên, kiểm tra màng còn nguyên không
    - Hiệu chỉnh lại 2 điểm
    - Chế độ an toàn: firmware giữ nguyên cấp quạt hiện tại
    - KHÔNG giảm quạt khi DO sensor lỗi

VL53L0X báo 0 hoặc 8190 (giá trị lỗi):
  - Kiểm tra bề mặt sensor có sạch không (bụi, tơ nhện)
  - Kiểm tra cáp I2C
  - Firmware: Dùng giá trị mực nước gần nhất (last valid)

DS18B20 báo 85°C (giá trị lỗi khởi động) hoặc -127°C:
  - Kiểm tra điện trở pull-up 4.7kΩ
  - Kiểm tra đầu nối không bị rỉ sét
  - Nếu 1 trong 3 sensor lỗi → dùng 2 sensor còn lại

ADS1115 / Turbidity lỗi:
  - Kiểm tra kết nối I2C (địa chỉ 0x48)
  - Turbidity không ảnh hưởng trực tiếp đến sinh vật → tiếp tục vận hành
  - Thông báo kỹ thuật viên

Nếu nhiều cảm biến I2C lỗi đồng thời:
  - Kiểm tra nguồn 3.3V (AMS1117) — đo điện áp
  - Kiểm tra điện trở pull-up I2C (4.7kΩ còn không)
  - Reboot CB2S (bật tắt nguồn)
```

### Sự Cố 5: Mất Kết Nối MQTT

```
MQTT ngắt > 5 phút:
  - Firmware tiếp tục hoạt động LOCAL (tự động hoàn toàn)
  - OLED hiển thị "MQTT: OFFLINE"
  - Tất cả lịch cho ăn, điều khiển quạt vẫn chạy bình thường
  - Sau khi kết nối lại: gửi buffered alerts + sensor history

Không kết nối được WiFi:
  - CB2S vào chế độ AP (Access Point)
  - Kết nối điện thoại vào WiFi "NextFarm_Setup"
  - Cấu hình lại WiFi SSID/Pass

Kiểm tra MQTT:
  ping broker.hosco.com.vn
  telnet broker.hosco.com.vn 1883
  Nếu không ping được → kiểm tra router/internet
```

---

## 11.5 Bảo Trì Định Kỳ

### Bảo Trì Hàng Ngày

```
□ Kiểm tra DO sáng sớm 5:00–6:00 (qua app hoặc trực tiếp)
□ Kiểm tra máy cho ăn chạy đúng giờ (log MQTT)
□ Quan sát màu nước, hành vi tôm/cá
□ Kiểm tra mực nước ao (OLED / app)
□ Ghi nhật ký: DO, pH, EC sáng và chiều
```

### Bảo Trì Hàng Tuần

```
□ Vệ sinh đầu dò DO: Rút lên, rửa nhẹ bằng nước cất
□ Kiểm tra đầu dò pH: Màng còn ẩm? (bảo quản trong KCl)
□ Vệ sinh cánh quạt sục khí: Rong rêu bám giảm hiệu suất
□ Kiểm tra cáp cảm biến: Không bị kẹp, không bị cạy
□ Kiểm tra mức thức ăn trong máy cho ăn
□ Kiểm tra mức vôi và khoáng trong bồn
□ Kiểm tra log MQTT: Có alert nào bị bỏ qua không?
□ Test ELCB: Nhấn nút test, xác nhận hoạt động
```

### Bảo Trì Hàng Tháng

```
□ Hiệu chỉnh lại EZO-DO (2 điểm: không khí + 0 mg/L)
□ Hiệu chỉnh lại EZO-pH (pH 4.0 + pH 7.0)
□ Vệ sinh VL53L0X: Lau nhẹ bề mặt sensor
□ Kiểm tra và xiết lại đầu cốt điện trong tủ
□ Kiểm tra gioăng cao su tủ IP55 (chống thấm)
□ Vệ sinh filter lọc trước UV (nếu có)
□ Kiểm tra rỉ sét đầu nối ngoài trời
□ Backup cấu hình từ MQTT/app (export settings)
□ Test chức năng phục hồi mất điện (ngắt nguồn thử)
```

### Bảo Trì Theo Vụ Nuôi (Sau Thu Hoạch)

```
□ Vệ sinh toàn bộ đầu dò (acid/base cleaning theo hướng dẫn Atlas)
□ Thay màng đầu dò DO (nếu cũ > 6 tháng)
□ Kiểm tra điện trở cách điện toàn bộ cáp
□ Thay bóng UV (8,000 giờ / 1 năm)
□ Thay dầu bơm (theo khuyến nghị nhà sản xuất)
□ Kiểm tra van solenoid 24VAC (làm sạch core)
□ Cập nhật firmware phiên bản mới nhất
□ Sao lưu log toàn vụ nuôi lên cloud
```

---

## 11.6 Bảng Thông Số Nước Tham Chiếu

### Tôm Thẻ Chân Trắng

| Thông số | Tối ưu | Chấp nhận | Nguy hiểm |
|----------|:------:|:---------:|:---------:|
| DO (mg/L) | 5–8 | 4–5 | < 3 |
| pH | 7.5–8.5 | 7.0–8.8 | < 6.8 hoặc > 9.2 |
| Nhiệt độ (°C) | 26–30 | 23–32 | < 18 hoặc > 35 |
| Độ mặn (‰) | 10–20 | 5–25 | < 3 hoặc > 30 |
| EC (mS/cm) | 20–40 | 10–50 | — |
| ORP (mV) | 250–350 | 150–450 | < 100 |
| Turbidity (NTU) | < 30 | 30–60 | > 80 |

### Cá Tra

| Thông số | Tối ưu | Chấp nhận | Nguy hiểm |
|----------|:------:|:---------:|:---------:|
| DO (mg/L) | 4–7 | 3–4 | < 2 |
| pH | 6.5–8.0 | 6.0–8.5 | < 5.5 hoặc > 9.0 |
| Nhiệt độ (°C) | 26–30 | 22–33 | < 16 hoặc > 36 |
| Độ mặn (‰) | 0–5 | 0–10 | > 15 |
| ORP (mV) | 200–350 | 150–400 | < 100 |
| Turbidity (NTU) | < 50 | 50–100 | > 150 |

---

## 11.7 Nhật Ký Vận Hành Mẫu

```
Ngày: 2026-05-12  |  Ngày nuôi: 45  |  Loài: Tôm thẻ
Người trực: Nguyễn Văn A

SÁNG (6:00):
  DO: 5.4 mg/L ✓  pH: 7.6 ✓  T: 27.8°C ✓
  EC: 22 mS/cm ✓  Mực nước: 85 cm ✓
  Quạt: 1/4 đang chạy  Cho ăn 6:00: ✓ (5 giây × 4 máy)
  Ghi chú: Nước trong, tôm phân bố đều

CHIỀU (15:00):
  DO: 6.8 mg/L ✓  pH: 8.1 ✓ (tảo quang hợp)
  T: 29.5°C ✓  Màu nước: Xanh lá nhạt ✓
  Cho ăn 15:00: ✓  Thức ăn ước còn: 70%

TỐI (21:00):
  DO: 6.2 mg/L ✓  pH: 7.8 ✓  T: 27.5°C ✓
  Cho ăn 21:00: ✓
  Trạng thái hệ thống: BÌNH THƯỜNG

Sự kiện trong ngày: Không có
Bảo trì: Vệ sinh đầu dò DO lúc 8:00
```
