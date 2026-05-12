# 10 — Hướng Dẫn Lắp Đặt Ao Nuôi

---

## 10.1 Chuẩn Bị Trước Khi Lắp Đặt

### Danh Sách Dụng Cụ

| Dụng cụ | Dùng cho |
|---------|---------|
| Tua vít 4 cạnh + dẹt | Lắp tủ điện, đấu nối |
| Kìm bấm đầu cốt | Đầu cốt cáp điện |
| Máy khoan (bit 6mm, 10mm) | Lắp đỡ tủ, khoan bờ |
| Thước dây 10m | Đo khoảng cách ao |
| Máy đo điện (VOM) | Kiểm tra đấu nối |
| Máy đo cách điện | Kiểm tra cáp ngầm |
| Bộ hàn thiếc (tùy chọn) | Đầu nối cảm biến |
| Laptop / điện thoại | Upload firmware, cấu hình |

### Vật Tư Cần Chuẩn Bị

```
Cáp và đấu nối:
  □ Cáp điện lực 2.5 mm² (dài theo ao, tính dư 20%)
  □ Cáp điện lực 4.0 mm² (cho bơm, blower)
  □ Cáp đơn 0.3 mm² màu (cảm biến, tín hiệu)
  □ Cáp chống thấm 4 lõi 0.5 mm² (cảm biến ngâm nước)
  □ Ống luồn cáp PVC Ø20 (ngoài trời)
  □ Ống luồn cáp thép Ø20 (ngầm dưới đất)
  □ Đầu cốt ống đồng (nhiều kích cỡ)
  □ Băng tự hàn + băng keo điện
  □ Kẹp cáp chống thấm PG11, PG16

Cơ khí:
  □ Tủ điện nhựa IP55 (300×250×150 mm tối thiểu)
  □ Thanh ray DIN 35 mm
  □ Ống nhựa PVC Ø34 đường cảm biến xuống ao
  □ Cọc tiếp đất đồng 1.5 m
  □ Dây tiếp đất 6 mm² vàng-xanh
  □ Vít không gỉ M4, M6
```

---

## 10.2 Vị Trí Lắp Đặt Tủ Điều Khiển

### Tiêu Chí Chọn Vị Trí

```
┌──────────────────────────────────────────────────────┐
│                    AO NUÔI                            │
│                                                       │
│   [M.cho ăn]              [M.cho ăn]                 │
│       ↑ K20               ↑ K22                      │
│                                                       │
│   [Quạt 1] [Quạt 2]   [Quạt 3] [Quạt 4]            │
│    K1↑      K2↑         K3↑      K4↑                 │
│                                                       │
│         [Đầu dò DO]     [Blower K5/K6]               │
│         [Đầu dò pH]       (đường ống)                │
│                                                       │
│   [M.cho ăn]              [M.cho ăn]                 │
│       ↑ K21               ↑ K23                      │
└──────────────────────────────────────────────────────┘
         │                          │
    [VL53L0X]                  [Cảm biến]
    trên bờ                    DS18B20 ×3
         │
    ┌────┴───────────────────────────────┐
    │      TỦ ĐIỀU KHIỂN IP55           │
    │   Trên bờ, cao ≥ 1.2 m mặt đất   │
    │   Mái che mưa + thông thoáng      │
    │   Khoảng cách mép ao ≥ 3 m        │
    └────────────────────────────────────┘

Yêu cầu vị trí tủ:
  ✓ Trên bờ ao, cao ≥ 120 cm so với mặt đất (tránh lụt)
  ✓ Cách mép ao ≥ 3 m (an toàn điện-nước)
  ✓ Có mái che mưa (tấm fibro hoặc tôn)
  ✓ Hướng Bắc/Đông → tránh ánh nắng trực tiếp buổi chiều
  ✓ Gần cột điện hoặc nguồn 220VAC
  ✓ WiFi signal -70 dBm hoặc tốt hơn (đo trước khi lắp)
```

---

## 10.3 Lắp Đặt Quạt Sục Khí (K1–K4)

### Vị Trí và Hướng Quạt

```
Ao hình chữ nhật (phổ biến):

   ┌──────────────────────────────────────────┐
   │    →→→→→→→ Dòng chảy →→→→→→→→→→         │
   │                                          │
   │ [Q1]                          [Q3]       │
   │                 ↑ ↑                      │
   │               Trung tâm                  │
   │                 ↓ ↓                      │
   │ [Q2]                          [Q4]       │
   │                                          │
   │    ←←←←←←← Dòng chảy ←←←←←←←←←←         │
   └──────────────────────────────────────────┘

Quạt góc nghiêng 45° hướng về trung tâm ao
  → Tạo dòng chảy xoáy (circular flow)
  → Gom bùn về trung tâm đáy ao
  → Phân phối oxy đều toàn ao

Khoảng cách quạt–bờ: 2–3 m
Độ sâu cánh quạt: 15–20 cm dưới mặt nước
```

### Đấu Nối Điện Quạt

```
Cáp từ tủ đến quạt:
  Tiết diện: 2.5 mm² cho quạt ≤ 250W (khoảng cách ≤ 30 m)
  Tiết diện: 4.0 mm² cho quạt ≤ 250W (khoảng cách > 30 m)
  Bọc trong ống PVC 20 mm (phần trên bờ)
  Bọc trong ống thép 20 mm (phần ngầm dưới đất)
  Độ sâu ngầm: ≥ 30 cm

Contactor trung gian (bắt buộc):
  □ Lắp trong tủ hoặc hộp chống thấm gần quạt
  □ Cuộn coil 24VDC điều khiển từ relay K1–K4
  □ Tiếp điểm 25A cho quạt 250W × an toàn
  □ Tụ snubber 0.1 μF/400V song song tiếp điểm
```

---

## 10.4 Lắp Đặt Cảm Biến DO, pH, EC, ORP (Atlas EZO)

### Vị Trí Lắp Đầu Dò

```
                AO NUÔI (nhìn từ trên)
   ┌──────────────────────────────────────────┐
   │                                          │
   │                   ●  ← DO + ORP          │
   │            (giữa ao, xa quạt ≥ 2m)      │
   │                                          │
   │  ●  ← pH              ●  ← EC            │
   │ (góc cuối            (đầu vào nước cấp) │
   │  dòng chảy)                              │
   └──────────────────────────────────────────┘

Yêu cầu vị trí:
  EZO-DO:  Giữa ao, độ sâu 40–50 cm từ mặt nước
           Cách quạt sục khí ≥ 2 m (tránh bọt khí giả DO cao)
           Tránh ánh sáng trực tiếp (quang hợp tảo giả DO cao)

  EZO-pH:  Điểm cuối dòng chảy (đại diện pH tổng ao)
           Độ sâu 30 cm từ mặt nước
           Cách đường cấp vôi ≥ 3 m

  EZO-EC:  Điểm đại diện nhất (giữa ao)
           Tránh gần đường cấp nước ngọt/mặn
           Độ sâu 30–40 cm

  EZO-ORP: Cùng vị trí với DO (đo oxy hóa-khử cùng điểm)
```

### Hộp Bảo Vệ Đầu Dò

```
         Bờ ao
           │  ← Ống PVC Ø34 chứa cáp
           │
     [Phao nổi]
           │  ← Cáp 4 lõi cảm biến
    [Giá đỡ cảm biến]
    ┌──────────────┐
    │  DO probe    │  ← Sâu 40–50 cm từ mặt nước
    │  ORP probe   │
    │  pH probe    │
    │  EC probe    │
    └──────────────┘
    [Đối trọng neo cố định]  ← Tránh quay theo dòng

Cáp cảm biến:
  Loại: 4 lõi × 0.5 mm² chống thấm (IP68)
  Bọc ống PVC 34 mm từ ao lên tủ điều khiển
  Không đi chung ống với cáp điện lực
```

---

## 10.5 Lắp Đặt VL53L0X — Đo Mực Nước

```
       Bờ ao (thành cứng)
          │
     ┌────┴────────────────┐
     │  Hộp nhựa IP65      │  ← Bảo vệ sensor
     │  [VL53L0X ToF]      │
     │  Hướng thẳng đứng   │
     │  xuống mặt nước     │
     └────┬────────────────┘
          │  Lỗ tròn Ø8 mm (cho tia laser xuyên qua)
          │
     ~~~~~~~~~~~~~~~~~~~~  ← Mặt nước (khoảng 50 cm bên dưới)
          │
     [Đáy ao]

Cài đặt:
  height_installed = 100 cm  (khoảng cách từ sensor đến mặt nước bình thường)
  level_cm = height_installed - distance_measured
  Ví dụ: đo được 55 cm → mực nước = 100 - 55 = 45 cm so với đáy

Lưu ý:
  - Tránh bọt nước từ quạt sục khí (lắp ở góc yên tĩnh)
  - Không để côn trùng hay màng nhện che lỗ đo
  - Hiệu chỉnh khi ao đầy nước lần đầu
```

---

## 10.6 Lắp Đặt DS18B20 — Nhiệt Độ Nước

```
3 điểm đo nhiệt độ:

Sensor #1 (địa chỉ sẽ đọc firmware):
  Vị trí: Giữa ao, độ sâu 20–30 cm
  Cố định bằng phao nổi + đối trọng

Sensor #2:
  Vị trí: Đầu vào bơm cấp (trước khi vào ao)
  Đo nhiệt độ nước mới

Sensor #3:
  Vị trí: Đầu ra/mương thoát
  Đo nhiệt độ nước thải

Đầu dò: Loại DS18B20 có vỏ thép không gỉ (waterproof)
  Bọc đầu nối: Heat shrink + keo Epoxy chống thấm
  Kéo cáp qua ống PVC riêng về tủ
```

---

## 10.7 Lắp Đặt Máy Cho Ăn (K20–K23)

```
             Ao nuôi
   ┌──────────────────────────────────┐
   │                                  │
   │  [Feeder K20]      [Feeder K22]  │
   │     ↑ góc NW          ↑ góc NE   │
   │                                  │
   │                                  │
   │  [Feeder K21]      [Feeder K23]  │
   │     ↑ góc SW          ↑ góc SE   │
   └──────────────────────────────────┘

Yêu cầu lắp đặt:
  ✓ 4 góc ao, hướng ra giữa ao
  ✓ Chiều cao: 30–50 cm trên mặt nước
  ✓ Mái che mưa cho motor máy cho ăn
  ✓ Cáp điện 220VAC đi trong ống trên bờ
  ✓ Motor K20–K23: relay board đủ dùng (< 1A)

Hiệu chỉnh thức ăn:
  1. Bật K20 trong 1 giây, cân lượng thức ăn ra
  2. Tính g/giây → cài đặt duration_sec phù hợp
  3. Ví dụ: 50g/giây → 5 giây = 250g/lần/máy
  4. Ghi lại và cập nhật qua MQTT feed/set
```

---

## 10.8 Lắp Đặt UV Trên Đường Ống Cấp (K11)

```
    Ao lắng/nguồn nước
           │
      [Bơm cấp K0]
           │
           ├── [Van kiểm tra (check valve)]
           │
      [Đèn UV K11]  ← Lắp NGANG, sau bơm
           │         Lưu lượng qua UV: 1–5 m³/giờ
           │         Công suất UV: 25–55W
           │
      [Flow meter YF-B10]
           │
           ▼
          Ao nuôi

Lưu ý lắp UV:
  - Lắp NGANG (không lắp đứng — bọt khí tích tụ giảm hiệu quả)
  - Trước UV: tách cặn lớn (túi lọc 100 micron)
  - UV chỉ diệt khuẩn hiệu quả khi nước trong (NTU < 10)
  - Thay bóng UV: mỗi 8,000 giờ hoặc 1 năm
  - Bóng UV không tiếp xúc trực tiếp tay (dầu da làm giảm tuổi thọ)
```

---

## 10.9 Upload Firmware CB2S

### Phương Pháp Upload (UART)

```
Cần có: USB-UART adapter (CH340, CP2102)

Kết nối:
  USB-UART TX  → CB2S RX (GPIO4)
  USB-UART RX  → CB2S TX (GPIO5)
  USB-UART GND → CB2S GND
  USB-UART 3.3V → CB2S 3.3V (hoặc cấp riêng)

Vào chế độ Flash:
  1. Giữ nút BOOT (GPIO11 hoặc theo board)
  2. Nhấn RESET
  3. Thả RESET, sau đó thả BOOT
  4. LED không chớp → đang ở chế độ flash

Upload firmware:
  Dùng bk7231_ota_tools hoặc Tuya Cloudcutter
  baud rate: 115200 (đầu tiên), 921600 (sau khi kết nối)

Cấu hình sau khi flash:
  1. WiFi SSID + Password
  2. MQTT broker: broker.hosco.com.vn
  3. MQTT port: 1883
  4. Farm ID: (gán duy nhất cho mỗi ao)
  5. Species: 0=tôm thẻ, 1=cá tra
  6. Farming day start: ngày bắt đầu nuôi
```

### Cấu Hình MQTT Qua WiFi Provisioning

```cpp
// config.h — cấu hình cơ bản
#define MQTT_BROKER    "broker.hosco.com.vn"
#define MQTT_PORT      1883
#define MQTT_USER      "nextfarm"
#define MQTT_PASS      "your_password"
#define FARM_ID        "aqua_001"    // Thay theo ao

// Topics
#define TOPIC_STATUS   "aqua/" FARM_ID "/status"
#define TOPIC_SENSOR   "aqua/" FARM_ID "/sensor"
#define TOPIC_ALERT    "aqua/" FARM_ID "/alert"
#define TOPIC_RELAY    "aqua/" FARM_ID "/relay"
#define TOPIC_FEED     "aqua/" FARM_ID "/feeding"
#define TOPIC_WATER    "aqua/" FARM_ID "/water"
#define TOPIC_THRESH   "aqua/" FARM_ID "/threshold"
#define TOPIC_LCD      "aqua/" FARM_ID "/lcd"
```

---

## 10.10 Checklist Kiểm Tra Trước Vận Hành

### A — Kiểm Tra Điện

```
□ ELCB 30mA hoạt động: Nhấn nút test → aptomat nhảy
□ Điện trở tiếp đất: Đo ≤ 10 Ω
□ Điện áp nguồn 5V: 4.8–5.2 V (đo tại relay board)
□ Điện áp AMS1117: 3.28–3.32 V (đo tại VCC CB2S)
□ Điện áp 24VAC: 22–26 VAC (đo tại van)
□ Không có ngắn mạch: Đo điện trở trước khi cấp nguồn
```

### B — Kiểm Tra Relay và Tải

```
□ K0  Bơm cấp: Bật thủ công → bơm chạy → nước chảy vào ao
□ K1  Quạt #1: Bật → quạt quay, kiểm tra dòng điện
□ K2  Quạt #2: Bật → quạt quay
□ K3  Quạt #3: Bật → quạt quay
□ K4  Quạt #4: Bật → quạt quay
□ K5  Blower #1: Bật → khí đẩy vào ao
□ K6  Blower #2: Bật → khí đẩy vào ao
□ K7  Bơm thải: Bật → nước thoát đúng hướng
□ K8  Bơm tuần hoàn: Bật → nước lưu thông
□ K9  Bơm vôi: Bật → kiểm tra van, vôi chạy
□ K10 Bơm khoáng: Bật → kiểm tra van, khoáng chạy
□ K11 UV: Bật → đèn UV sáng (không nhìn trực tiếp)
□ K12 Sưởi: Bật → phát nhiệt (đo nhiệt độ tăng)
□ K16–K19 Van ao lắng: Bật từng van → van mở (24VAC)
□ K20–K23 Máy cho ăn: Bật 2 giây → thức ăn ra đúng lượng
□ K24 Van xả đáy #1: Bật → nước xả ra đúng hướng
□ K25 Van xả đáy #2: Bật → nước xả ra đúng hướng
□ K29 Van O₂ khẩn: Bật → khí O₂ phun vào ao
```

### C — Kiểm Tra Cảm Biến

```
□ EZO-DO:   Đọc giá trị 7–9 mg/L (trong không khí)
             Hiệu chỉnh 2 điểm: khí và 0 mg/L (Na₂SO₃)
□ EZO-pH:   Đọc xấp xỉ 7.0 ± 0.5 (mặc định)
             Hiệu chỉnh 2 điểm: pH 4.0 và pH 7.0
□ EZO-EC:   Đọc thấp (không khí, bình thường)
             Hiệu chỉnh bằng dung dịch chuẩn
□ EZO-ORP:  Đọc giá trị trong khoảng 200–400 mV
□ DS18B20:  3 sensors hiện đúng nhiệt độ phòng (chênh ≤ 1°C)
□ VL53L0X:  Đo khoảng cách đến vật thể, sai số ≤ 1 cm
□ ADS1115:  Đọc giá trị raw (turbidity ≈ 3.0V trong nước trong)
□ MS5837:   Đọc áp suất ≈ 1013 mbar (áp suất khí quyển khi ngoài nước)
□ Flow #1:  Cho nước chảy, đếm xung, tính lít
□ Flow #2:  Tương tự
□ OLED:     Hiển thị đúng 4 dòng, không nhòe
```

### D — Kiểm Tra MQTT và WiFi

```
□ WiFi kết nối ổn định: RSSI > -70 dBm
□ MQTT kết nối: broker.hosco.com.vn:1883 → Connected
□ Subscribe test: Gửi lệnh relay từ app → relay hoạt động
□ Publish test: Nhận sensor data trên MQTT Explorer
□ Alert test: Giả lập DO thấp → nhận push notification
□ Lịch cho ăn: Kiểm tra thời gian đúng, NTP đồng bộ
□ Flash: Tắt nguồn → bật lại → trạng thái phục hồi đúng
```

### E — Kiểm Tra Hiệu Chỉnh Atlas EZO

```
Hiệu chỉnh EZO-DO (bắt buộc trước khi nuôi):
  B1: Cắm đầu dò vào không khí 5 phút
  B2: Gửi lệnh MQTT: {"cmd": "cal_do_air"}
  B3: Đặt đầu dò vào Na₂SO₃ 2g/L (0 mg/L DO)
  B4: Gửi lệnh: {"cmd": "cal_do_zero"}
  B5: Kiểm tra đo lại với dung dịch chuẩn

Hiệu chỉnh EZO-pH:
  B1: Lau sạch đầu dò
  B2: Cho vào pH 7.0 buffer, chờ ổn định 60 giây
  B3: Gửi MQTT: {"cmd": "cal_ph_mid", "ph": 7.0}
  B4: Rửa sạch, cho vào pH 4.0 buffer
  B5: Gửi MQTT: {"cmd": "cal_ph_low", "ph": 4.0}
  B6: Tùy chọn: pH 10.0 cho cal 3 điểm
```
