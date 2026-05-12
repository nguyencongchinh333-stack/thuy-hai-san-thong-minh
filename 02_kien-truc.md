# 02 — Kiến Trúc Hệ Thống

---

## 2.1 Sơ Đồ Tổng Quan

```
┌──────────────────────────────────────────────────────────────────┐
│                      NEXTFARM PLATFORM                            │
│                   IoT → Aquaculture Module                        │
└───────────────────────────┬──────────────────────────────────────┘
                            │ MQTT / WiFi (broker.hosco.com.vn:1883)
               ┌────────────┴────────────┐
               │     CB2S (BK7231N)      │
               │   Tuya SDK compatible   │
               │  Tín hiệu I/O: 3.3V    │
               └────────────┬────────────┘
                            │ I2C Bus (SDA GPIO6 / SCL GPIO7)
            ┌───────────────┼──────────────────────┐
            │               │                      │
     ┌──────┴──────┐  ┌─────┴──────┐    ┌──────────┴─────────┐
     │ MCP23017 #1 │  │ MCP23017 #2│    │  Cảm biến I2C Bus  │
     │   (0x20)    │  │   (0x21)   │    │  Atlas EZO ×4       │
     │  Kênh 0–15  │  │  Kênh 16–30│    │  VL53L0X, MS5837   │
     └──────┬──────┘  └─────┬──────┘    │  ADS1115, OLED     │
            │               │           └────────────────────┘
     ┌──────┴──────────┐  ┌─┴────────────────────┐
     │  Relay Board    │  │    Relay Board         │
     │  1+2 (ch 0–15) │  │    3+4 (ch 16–30)     │
     └────────┬────────┘  └───────────┬────────────┘
              │                       │
   ┌──────────┴──────────┐  ┌─────────┴───────────┐
   │   220VAC Tải        │  │  24VAC + 220VAC      │
   │ Quạt sục khí (K1-4) │  │  Van ao lắng (K16-19)│
   │ Blower (K5-6)       │  │  Máy cho ăn (K20-23) │
   │ Bơm cấp (K0)        │  │  Van xả đáy (K24-25) │
   │ UV + Sưởi (K11-12)  │  │  Van thải/O₂ (K27-29)│
   └─────────────────────┘  └─────────────────────┘
```

---

## 2.2 Sơ Đồ Chi Tiết Phần Cứng

```
                  ┌─────────────────────────────────────────────┐
                  │              CB2S (BK7231N)                  │
                  │                                             │
  SDA (GPIO6) ───┤                                   GPIO8  ├─── DS18B20 ×3 (1-Wire)
  SCL (GPIO7) ───┤  I2C Master                       GPIO14 ├─── YF-B10 Flow #1
  3.3V ──────────┤  (4.7kΩ pull-up)                  GPIO26 ├─── YF-B10 Flow #2
  GND ───────────┤                                   GPIO5  ├─── UART TX
                  │                                   GPIO4  ├─── UART RX (debug)
                  └─────────────────────────────────────────────┘
                         │ I2C
          ┌──────────────┼──────────────────────────────┐
          │              │              │                │
   ┌──────┴──┐    ┌──────┴──┐    ┌─────┴──┐    ┌───────┴──┐
   │MCP23017 │    │MCP23017 │    │ADS1115 │    │  OLED    │
   │ #1 0x20 │    │ #2 0x21 │    │ 0x48   │    │SH1106    │
   └──────┬──┘    └──────┬──┘    │AIN0:   │    │ 0x3C     │
          │              │       │Turbidity│    └──────────┘
    CH0–CH15        CH16–CH30    └────────┘
          │              │
   ┌──────┴──┐    ┌──────┴──┐
   │ Atlas   │    │EZO-DO   │    EZO-DO  → 0x61
   │ EZO I2C │    │EZO-ORP  │    EZO-ORP → 0x62
   │ Sensors │    │EZO-pH   │    EZO-pH  → 0x63
   └─────────┘    │EZO-EC   │    EZO-EC  → 0x64
                  └─────────┘
```

---

## 2.3 Luồng Dữ Liệu MQTT

```
NextFarm App / Platform
  │ Subscribe: aqua/{farm_id}/status      ← Trạng thái tổng
  │ Subscribe: aqua/{farm_id}/sensor      ← DO, pH, EC, ORP, T°, mực nước
  │ Subscribe: aqua/{farm_id}/alert       ← CẢNH BÁO DO thấp, pH bất thường
  │ Subscribe: aqua/{farm_id}/lcd         ← Nội dung màn hình OLED
  │ Subscribe: aqua/{farm_id}/feeding     ← Trạng thái máy cho ăn
  │
  │ Publish: aqua/{farm_id}/relay/{k}/set ← Điều khiển relay đơn lẻ
  │ Publish: aqua/{farm_id}/mode/set      ← AUTO | MANUAL | EMERGENCY
  │ Publish: aqua/{farm_id}/threshold/set ← Cài đặt ngưỡng DO, pH, EC
  │ Publish: aqua/{farm_id}/feed/set      ← Lịch cho ăn, khẩu phần
  │ Publish: aqua/{farm_id}/water/set     ← Lịch thay nước
  ▼
CB2S Firmware → MCP23017 → Relay Board → Thiết bị ao
```

Broker: **broker.hosco.com.vn:1883** — QoS 1 cho alert, QoS 0 cho sensor.

---

## 2.4 Logic Điều Khiển DO 6 Cấp

### Ngưỡng và Hành Động

| Cấp | DO (mg/L) | Quạt sục khí | Blower | Bơm cấp | Mức độ |
|:---:|:---------:|:------------:|:------:|:--------:|:------:|
| 5 | > 6.0 | Tắt hết | Tắt | Bình thường | Tốt |
| 4 | 5.0 – 6.0 | K1 (1 quạt) | Tắt | Bình thường | Ổn |
| 3 | 4.0 – 5.0 | K1 + K2 (2 quạt) | K5 | Bình thường | Cần theo dõi |
| 2 | 3.0 – 4.0 | K1+K2+K3+K4 (4 quạt) | K5+K6 | Tăng lưu lượng | CẢNH BÁO |
| 1 | 2.0 – 3.0 | TẤT CẢ K1–K4 + Blower | K5+K6 | Tối đa | NGUY HIỂM |
| 0 | < 2.0 | TẤT CẢ + Khẩn cấp | K5+K6 | Tối đa + K29 O₂ | KHẨN CẤP |

### Sơ Đồ Luồng Logic DO

```
Đọc EZO-DO (mỗi 30 giây)
           │
    DO > 6.0 mg/L? ──YES──► Cấp 5: Tắt quạt nếu đang chạy
           │ NO
    DO > 5.0 mg/L? ──YES──► Cấp 4: Bật K1 (1 quạt)
           │ NO
    DO > 4.0 mg/L? ──YES──► Cấp 3: Bật K1+K2, K5
           │ NO                       MQTT alert: "DO_THEO_DOI"
    DO > 3.0 mg/L? ──YES──► Cấp 2: Bật K1–K4, K5+K6
           │ NO                       MQTT alert: "DO_CANH_BAO"
    DO > 2.0 mg/L? ──YES──► Cấp 1: TẤT CẢ quạt + blower
           │ NO                       MQTT alert: "DO_NGUY_HIEM"
           ▼                          SMS + App notification
    Cấp 0: KHẨN CẤP
           │     → Bật K1–K4, K5–K6, K29 (van O₂)
           │     → MQTT alert: "DO_KHAN_CAP"
           │     → OLED nhấp nháy đỏ
           └──── → Còi cảnh báo (nếu có)
```

### Hysteresis Chống Dao Động

```
Ví dụ ngưỡng cấp 3↔4 (DO = 5.0 mg/L):
  - Chuyển xuống cấp 3 khi DO < 5.0 - 0.2 = 4.8 mg/L
  - Chuyển lên cấp 4 khi DO > 5.0 + 0.2 = 5.2 mg/L
  - Tránh relay đóng/mở liên tục gây hỏng thiết bị
```

---

## 2.5 Logic pH — Bơm Vôi Tự Động

```
Đọc EZO-pH (mỗi 60 giây)
           │
    pH < 6.8? ──YES──► Bật K9 (bơm vôi) trong T giây
           │           T = (7.0 - pH_hiện_tại) × 30 giây / 0.1 pH
           │           MQTT: "PH_THAP_BOM_VOI"
           │ NO
    pH > 8.5? ──YES──► Tắt K9 + MQTT alert: "PH_CAO"
           │           (Không bơm vôi — cần xử lý thủ công)
           │ NO
    pH trong 7.0–8.2 ──► Tắt K9, trạng thái bình thường
```

**Giới hạn bơm vôi an toàn:**
- Không bơm quá 5 phút liên tục / lần
- Chờ 30 phút sau mỗi lần bơm trước khi đọc lại
- Tổng không quá 20 phút / ngày (tránh pH tăng quá nhanh)
- Nếu pH không tăng sau 2 lần bơm → MQTT alert "PH_KHONG_TANG"

---

## 2.6 Lịch Cho Ăn Theo DO

```
Kiểm tra DO trước mỗi lần cho ăn (10 phút trước ca):
  │
  DO > 4.0 mg/L? ──YES──► Cho ăn bình thường (K20–K23 theo lịch)
  │
  DO = 3.0–4.0? ──YES──► Giảm 50% khẩu phần
  │                       MQTT: "CHOUAN_GIAM_DO_THAP"
  │
  DO < 3.0 mg/L? ──YES──► BỎ CA CHO ĂN
                           MQTT: "CHOUAN_BO_CA_DO_THAP"
                           Ghi log ca bị bỏ
```

**Lịch cho ăn tôm thẻ chân trắng (6 lần/ngày):**

| Ca | Giờ | Ghi chú |
|:--:|:---:|---------|
| 1 | 06:00 | Kiểm tra DO sáng sớm |
| 2 | 09:00 | |
| 3 | 12:00 | Kiểm tra DO giữa ngày |
| 4 | 15:00 | |
| 5 | 18:00 | Kiểm tra DO chiều |
| 6 | 21:00 | Ca tối |

**Lịch cho ăn cá tra (3 lần/ngày):**

| Ca | Giờ | Ghi chú |
|:--:|:---:|---------|
| 1 | 07:00 | |
| 2 | 12:00 | |
| 3 | 17:00 | |

---

## 2.7 Lịch Thay Nước Tự Động

```
Chu kỳ thay nước mặc định (cấu hình qua MQTT):
  ┌─────────────────────────────────────────────────────┐
  │ GIAI ĐOẠN 1 (ngày 1–30): Thay 10% mỗi 3 ngày      │
  │ GIAI ĐOẠN 2 (ngày 31–60): Thay 15% mỗi 2 ngày     │
  │ GIAI ĐOẠN 3 (ngày 61–100): Thay 20% mỗi ngày      │
  └─────────────────────────────────────────────────────┘

Thứ tự thực hiện thay nước:
  1. Bật K8 (tuần hoàn) → chờ 5 phút
  2. Mở K24/K25 (van xả đáy) → xả đến mực nước đích
     (VL53L0X đo liên tục → đóng van khi đạt mức)
  3. Đóng K24/K25 → Bật K0 (bơm cấp) đến mực cũ
  4. Kiểm tra pH và EC sau khi cấp nước
  5. Điều chỉnh nếu cần

Điều kiện dừng thay nước khẩn cấp:
  - DO đo được < 4.0 mg/L trong lúc xả → dừng ngay
  - Mực nước < 50% ao → dừng, cảnh báo
  - Thời gian xả > 60 phút → dừng, cảnh báo
```

---

## 2.8 Sơ Đồ Nguồn Điện

```
220VAC (Lưới điện / Máy phát)
          │
   [ELCB 2P 30A 30mA]  ← Bắt buộc môi trường ao ẩm ướt
          │
   ┌──────┼──────────────────────────────────┐
   │      │                                  │
   ▼      ▼                                  ▼
[CB 2P  [Mean Well         [Biến áp        [CB 1P ×nhiều]
 20A]    HDR-60-5]          24VAC 100VA]    Quạt/Bơm/UV
Bơm     5VDC/12A           24VAC            Phân vùng
chính         │              │
              ▼              ▼
   [AMS1117 3.3V]      Van ao lắng
         │ 3.3V         K16–K19 (24VAC)
         │
   ┌─────┴───────────────────┐
   │ CB2S + MCP23017 ×2      │
   │ Atlas EZO ×4            │
   │ ADS1115, VL53L0X        │
   │ MS5837, OLED SH1106     │
   └─────────────────────────┘

Dòng tải AMS1117 3.3V (tối đa 800mA):
  CB2S:          ~150 mA
  MCP23017 ×2:   ~20 mA × 2 = 40 mA
  Atlas EZO ×4:  ~50 mA × 4 = 200 mA
  ADS1115:       ~1 mA
  VL53L0X:       ~20 mA
  MS5837:        ~1 mA
  OLED SH1106:   ~20 mA
  DS18B20 ×3:    ~5 mA × 3 = 15 mA
  Dự phòng:      ~150 mA
  TỔNG:          ~597 mA ✓ (< 800mA)

Lưu ý: DS18B20 cần điện trở pull-up 4.7kΩ lên 3.3V
```

---

## 2.9 Màn Hình OLED SH1106 128×64

### Bố Cục Hiển Thị Bình Thường

```
┌────────────────────────┐
│ DO:6.8 pH:7.5 T:28.3C │  ← Dòng 1: Thông số chính
│ EC:15ms ORP:320mV     │  ← Dòng 2: Độ mặn + ORP
│ Quat:2 Bom:ON UV:ON   │  ← Dòng 3: Thiết bị đang chạy
│ ChoAn:09:00 Nc:72cm   │  ← Dòng 4: Ca ăn tiếp theo + mực nước
└────────────────────────┘
```

### Hiển Thị Khi Cảnh Báo

```
┌────────────────────────┐
│ !!! CANH BAO !!!      │
│ DO THAP: 3.2 mg/L     │
│ Bat 4 quat suc khi    │
│ Kiem tra: 14:25:33    │
└────────────────────────┘
```

### Cảnh Báo 3 Mức Qua MQTT

| Mức | Điều kiện | MQTT Topic | Hành động |
|:---:|-----------|-----------|-----------|
| INFO | pH 6.8–7.0 hoặc DO 4.5–5.0 | `.../alert/info` | Ghi log |
| WARNING | DO 3.0–4.0 hoặc pH < 6.8 | `.../alert/warning` | Push notification |
| CRITICAL | DO < 2.0 hoặc pH < 6.5 | `.../alert/critical` | SMS + còi + tất cả quạt |

---

## 2.10 Cấu Trúc Firmware (Tóm Tắt)

```cpp
// Vòng lặp chính (loop period = 1 giây)
void loop() {
  // === ĐỌC CẢM BIẾN (chu kỳ khác nhau) ===
  if (millis() - lastDO   >= 30000) readEZO_DO();    // 30 giây
  if (millis() - lastPH   >= 60000) readEZO_pH();    // 60 giây
  if (millis() - lastEC   >= 60000) readEZO_EC();    // 60 giây
  if (millis() - lastORP  >= 60000) readEZO_ORP();   // 60 giây
  if (millis() - lastTemp >= 15000) readDS18B20();   // 15 giây
  if (millis() - lastFlow >= 1000)  readFlowMeters();// 1 giây
  if (millis() - lastLevel>= 5000)  readVL53L0X();   // 5 giây
  if (millis() - lastTurb >= 10000) readTurbidity(); // 10 giây
  if (millis() - lastPres >= 30000) readMS5837();    // 30 giây

  // === LOGIC ĐIỀU KHIỂN ===
  controlDO_6level();    // Logic 6 cấp DO
  controlPH_lime();      // Bơm vôi tự động
  controlEC_salinity();  // Cảnh báo độ mặn
  controlORP();          // Cảnh báo ORP
  controlFeeding();      // Lịch cho ăn
  controlWaterChange();  // Lịch thay nước
  controlUV();           // UV theo lịch

  // === XUẤT MQTT + OLED ===
  if (millis() - lastMQTT >= 10000) publishSensorData();
  if (millis() - lastOLED >= 2000)  updateOLED();
  if (millis() - lastAlert > 0)     checkAndSendAlerts();
}
```
