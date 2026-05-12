# 05 — Đấu Nối Điện

---

## 5.1 Sơ Đồ Tổng Quan Nguồn

```
220VAC Lưới / Máy phát dự phòng
           │
    [ELCB 2P 30A 30mA]  ← BẮT BUỘC — môi trường ao ẩm ướt nguy hiểm
           │
   ┌───────┼─────────────────────────────────────┐
   │       │                     │               │
   ▼       ▼                     ▼               ▼
[CB 2P  [Mean Well           [Biến áp       [CB 1P × 6]
 20A]    HDR-60-5]            24VAC 100VA]   Phân vùng tải
Bơm     5VDC / 12A           24VAC
chính       │                   │
            ▼                   ▼
   [AMS1117 3.3V]         Van ao lắng
        │ 3.3V             K16–K19
        │                  (24VAC solenoid)
   ┌────┴──────────────────────────────┐
   │  CB2S + MCP23017 ×2              │
   │  Atlas EZO ×4 (DO, ORP, pH, EC)  │
   │  VL53L0X + MS5837 + ADS1115      │
   │  OLED SH1106 + DS18B20 ×3        │
   └───────────────────────────────────┘

Phân vùng CB 1P:
  CB 10A — Quạt sục khí K1–K4 (4× 220VAC 250W)
  CB 10A — Blower K5–K6 (2× 220VAC 750W)
  CB 16A — Bơm cấp K0 + Bơm thải K7 + Bơm bùn K26
  CB  6A — UV K11 + Sưởi K12 + Đèn K13–K14
  CB  6A — Máy cho ăn K20–K23 (220VAC 100W ×4)
  CB  6A — Bơm tuần hoàn K8, vôi K9, khoáng K10, dự phòng
```

---

## 5.2 CB2S ↔ MCP23017

### Sơ Đồ Kết Nối I2C

```
                   3.3V (AMS1117)
                      │
                 4.7kΩ │ 4.7kΩ
                      │ │
CB2S GPIO6 (SDA) ─────┴─┬──── MCP23017 #1 SDA (pin 13)
                         └──── MCP23017 #2 SDA (pin 13)

                   3.3V (AMS1117)
                      │
                 4.7kΩ │
                      │
CB2S GPIO7 (SCL) ─────┴─┬──── MCP23017 #1 SCL (pin 12)
                         └──── MCP23017 #2 SCL (pin 12)

3.3V ──────────────────────── VDD cả 2 chip (pin 9)
GND  ──────────────────────── VSS cả 2 chip (pin 10)

Địa chỉ MCP23017 #1: A0=GND, A1=GND, A2=GND → 0x20
Địa chỉ MCP23017 #2: A0=VCC, A1=GND, A2=GND → 0x21
RESET cả 2 (pin 18):   3.3V qua 10kΩ — BẮT BUỘC
INT A/B: Không dùng trong ứng dụng này
```

---

## 5.3 MCP23017 ↔ Relay Board

### Mapping Chân

```
MCP23017 #1 (0x20):
  GPA0 (pin 21) → Relay Board 1, IN1 → K0  (Bơm cấp)
  GPA1 (pin 22) → Relay Board 1, IN2 → K1  (Quạt sục #1)
  GPA2 (pin 23) → Relay Board 1, IN3 → K2  (Quạt sục #2)
  GPA3 (pin 24) → Relay Board 1, IN4 → K3  (Quạt sục #3)
  GPA4 (pin 25) → Relay Board 1, IN5 → K4  (Quạt sục #4)
  GPA5 (pin 26) → Relay Board 1, IN6 → K5  (Blower #1)
  GPA6 (pin 27) → Relay Board 1, IN7 → K6  (Blower #2)
  GPA7 (pin 28) → Relay Board 1, IN8 → K7  (Bơm thải)
  GPB0 (pin 1)  → Relay Board 2, IN1 → K8  (Bơm tuần hoàn)
  GPB1 (pin 2)  → Relay Board 2, IN2 → K9  (Bơm vôi)
  GPB2 (pin 3)  → Relay Board 2, IN3 → K10 (Bơm khoáng)
  GPB3 (pin 4)  → Relay Board 2, IN4 → K11 (Đèn UV)
  GPB4 (pin 5)  → Relay Board 2, IN5 → K12 (Sưởi)
  GPB5 (pin 6)  → Relay Board 2, IN6 → K13 (Đèn #1)
  GPB6 (pin 7)  → Relay Board 2, IN7 → K14 (Đèn #2)
  GPB7 (pin 8)  → Relay Board 2, IN8 → K15 (Dự phòng)

MCP23017 #2 (0x21):
  GPA0 (pin 21) → Relay Board 3, IN1 → K16 (Van ao lắng #1, 24VAC)
  GPA1 (pin 22) → Relay Board 3, IN2 → K17 (Van ao lắng #2, 24VAC)
  GPA2 (pin 23) → Relay Board 3, IN3 → K18 (Van ao lắng #3, 24VAC)
  GPA3 (pin 24) → Relay Board 3, IN4 → K19 (Van ao lắng #4, 24VAC)
  GPA4 (pin 25) → Relay Board 3, IN5 → K20 (Máy cho ăn #1)
  GPA5 (pin 26) → Relay Board 3, IN6 → K21 (Máy cho ăn #2)
  GPA6 (pin 27) → Relay Board 3, IN7 → K22 (Máy cho ăn #3)
  GPA7 (pin 28) → Relay Board 3, IN8 → K23 (Máy cho ăn #4)
  GPB0 (pin 1)  → Relay Board 4, IN1 → K24 (Van xả đáy #1)
  GPB1 (pin 2)  → Relay Board 4, IN2 → K25 (Van xả đáy #2)
  GPB2 (pin 3)  → Relay Board 4, IN3 → K26 (Bơm bùn)
  GPB3 (pin 4)  → Relay Board 4, IN4 → K27 (Van thải)
  GPB4 (pin 5)  → Relay Board 4, IN5 → K28 (Bơm dự phòng)
  GPB5 (pin 6)  → Relay Board 4, IN6 → K29 (Van O₂ khẩn cấp)
  GPB6 (pin 7)  → Relay Board 4, IN7 → K30 (Dự phòng)
  GPB7 (pin 8)  → Relay Board 4, IN8 → K31 (Dự phòng)

Nguồn Relay Board:
  VCC tất cả 4 board → 5VDC (Mean Well HDR-60-5)
  GND tất cả 4 board → GND chung
  Logic IN: Active LOW (MCP23017 xuất 0 = relay đóng)
```

---

## 5.4 Đấu Nối Atlas EZO (I2C 3.3V)

### EZO-DO — Oxy Hòa Tan (0x61)

```
EZO-DO Circuit Board:
  VCC → 3.3V (AMS1117)   ← KHÔNG dùng 5V
  GND → GND
  SDA → GPIO6 (CB2S)
  SCL → GPIO7 (CB2S)
  TX  → Không nối (chế độ I2C)
  RX  → Không nối (chế độ I2C)
  PRB → Đầu dò DO (cáp riêng)

Vị trí đầu dò:
  - Đặt cách đáy ao 50 cm
  - Cách quạt sục khí tối thiểu 2 m
  - Không đặt gần đường cấp nước
  - Chống sáng trực tiếp (bọc ống đen nếu cần)
  - Kiểm tra màng DO mỗi 4–6 tuần
```

### EZO-pH (0x63), EZO-ORP (0x62), EZO-EC (0x64)

```
Tất cả 4 module Atlas EZO đấu chung I2C bus:

          3.3V (AMS1117)
            │
       4.7kΩ ┤ (pull-up SDA)
            │
GPIO6 (SDA) ──┬──── EZO-DO  SDA (0x61)
              ├──── EZO-ORP SDA (0x62)
              ├──── EZO-pH  SDA (0x63)
              └──── EZO-EC  SDA (0x64)

          3.3V (AMS1117)
            │
       4.7kΩ ┤ (pull-up SCL)
            │
GPIO7 (SCL) ──┬──── EZO-DO  SCL
              ├──── EZO-ORP SCL
              ├──── EZO-pH  SCL
              └──── EZO-EC  SCL

VCC tất cả → 3.3V
GND tất cả → GND

Lưu ý khi đấu EZO-EC:
  K+ (hằng số cell) cần hiệu chỉnh theo cell thực tế
  Probe: đặt ở điểm đại diện nhất của ao
  Không đặt gần van cấp nước (nước mới pha loãng)
```

### ADS1115 — ADC 16-bit cho Turbidity (0x48)

```
ADS1115:
  VDD  → 3.3V (AMS1117)
  GND  → GND
  SDA  → GPIO6 (I2C chung)
  SCL  → GPIO7 (I2C chung)
  ADDR → GND   → Địa chỉ 0x48
  ALRT → Không nối
  AIN0 → SEN0189 Turbidity (analog out)
  AIN1 → Dự phòng
  AIN2 → Dự phòng
  AIN3 → Dự phòng

SEN0189 Turbidity Sensor:
  VCC  → 5V (Mean Well 5V)   ← Cần 5V, nhưng AOUT max ~4.5V
  GND  → GND
  AOUT → Phân áp 10kΩ/20kΩ → 3.3V max → ADS1115 AIN0
         (Bắt buộc phân áp — ADS1115 chịu max 3.3V ở VDD=3.3V)
```

**Mạch phân áp SEN0189 → ADS1115:**

```
SEN0189 AOUT (0–4.5V)
         │
        10kΩ
         │
         ┴──── ADS1115 AIN0 (đọc điện áp tại đây)
         │
        20kΩ
         │
        GND

Điện áp tại AIN0 = AOUT × 20/(10+20) = AOUT × 0.667
  → Khi AOUT = 4.5V: AIN0 = 3.0V ✓ (an toàn cho ADS1115 ở 3.3V)
```

---

## 5.5 DS18B20 — Nhiệt Độ Nước (GPIO8, 3.3V)

```
DS18B20 #1, #2, #3 (1-Wire Bus chung):

GPIO8 ──────┬──── DS18B20 #1 DATA
            ├──── DS18B20 #2 DATA
            └──── DS18B20 #3 DATA

Điện trở pull-up:
  GPIO8 ──[4.7kΩ]── 3.3V (AMS1117)
  Bắt buộc, không bỏ qua

Nguồn DS18B20:
  VDD  → 3.3V (AMS1117)
  GND  → GND
  DATA → GPIO8

Vị trí lắp 3 cảm biến:
  #1: Giữa ao (độ sâu 30 cm)
  #2: Đầu vào nước cấp
  #3: Đầu ra nước thải / mương thoát

Lưu ý vỏ không thấm nước:
  - Dùng đầu dò DS18B20 có vỏ thép không gỉ (waterproof type)
  - Bọc đầu nối với heat shrink + keo chống thấm
  - Không để cáp ngâm dài trong nước — dẫn qua ống nhựa PVC
```

---

## 5.6 VL53L0X — Cảm Biến Mực Nước (I2C 0x29)

```
VL53L0X ToF Sensor:
  VCC  → 3.3V (AMS1117)
  GND  → GND
  SDA  → GPIO6 (I2C chung)
  SCL  → GPIO7 (I2C chung)
  XSHUT→ GPIO (tùy chọn, bỏ trống nếu chỉ 1 sensor)
  GPIO1→ Không nối (polling mode)

Lắp đặt:
  - Gắn trên bờ ao, hướng thẳng đứng xuống mặt nước
  - Chiều cao lắp đặt: 50–100 cm so với mặt nước tối đa
  - Phạm vi đo: 3 cm – 200 cm (khoảng cách tới mặt nước)
  - Tính mực nước: Mực_nước = Chiều_cao_lắp - Khoảng_cách_đo
  - Che nắng trực tiếp và mưa bằng hộp nhựa đục lỗ nhỏ
  - Không để nước bắn trực tiếp vào bề mặt sensor
```

---

## 5.7 MS5837-30BA — Cảm Biến Áp Suất (I2C 0x76)

```
MS5837-30BA:
  VDD  → 3.3V (AMS1117)
  GND  → GND
  SDA  → GPIO6 (I2C chung)
  SCL  → GPIO7 (I2C chung)

Phạm vi: 0–30 bar, độ phân giải 0.2 mbar
Ứng dụng ao: đo độ sâu nước bằng áp suất thủy tĩnh
  Độ sâu (m) = (P_đo - P_khí_quyển) × 100 / (ρ × g)
  ρ nước ngọt ≈ 998 kg/m³; ρ nước mặn ≈ 1025 kg/m³

Lắp đặt:
  - Đặt ở điểm sâu nhất cần đo (đáy ao hoặc tầng giữa)
  - Dùng dây cáp có lớp bảo vệ dưới nước
  - Để ổn định 5 phút sau khi cấp nguồn trước khi đọc
```

---

## 5.8 YF-B10 — Đồng Hồ Lưu Lượng (GPIO14, GPIO26)

```
YF-B10 Flow Meter #1 (đường cấp nước vào):
  VCC    → 5V (Mean Well)
  GND    → GND
  SIGNAL → GPIO14 (CB2S) qua điện trở phân áp 3.3V

YF-B10 Flow Meter #2 (đường thải nước ra):
  VCC    → 5V (Mean Well)
  GND    → GND
  SIGNAL → GPIO26 (CB2S) qua điện trở phân áp 3.3V

Mạch chuyển mức 5V → 3.3V cho tín hiệu xung:
  SIGNAL (5V) ──[10kΩ]──┬── GPIO (3.3V CB2S)
                        │
                      [20kΩ]
                        │
                       GND

  Hoặc dùng BSS138 level shifter chuyên dụng

Cài đặt firmware:
  K_factor = 98 xung/lít  (YF-B10 mặc định)
  Flow_LPM = pulse_count / K_factor × 60  (lít/phút)
  Interrupt-driven (RISING edge), ngắt GPIO

Lắp đặt:
  - Lắp nằm ngang hoặc nghiêng ≤45°
  - Trước sensor: ống thẳng tối thiểu 10× đường kính
  - Sau sensor:  ống thẳng tối thiểu 5× đường kính
  - Không lắp ngay sau khúc cua, van, hay bơm
```

---

## 5.9 Đấu Nối Quạt Sục Khí + Contactor 220VAC

```
Quạt sục khí K1–K4 (220VAC, dòng cao — dùng contactor):

  ┌─────────────────────────────────────────────────────────┐
  │  Mạch điều khiển K1 (tương tự K2, K3, K4):            │
  │                                                         │
  │  MCP23017 GPA1 (3.3V) → Relay Board IN2                │
  │                          Relay NO → Cuộn coil contactor │
  │                          (12VDC coil recommended)       │
  │                          Contactor chính → Motor quạt  │
  │                                                         │
  │  Nguồn cuộn contactor: 12VDC / 24VDC (tùy model)      │
  │  Dòng tiếp điểm contactor: ≥ 16A cho quạt 250W        │
  └─────────────────────────────────────────────────────────┘

Sơ đồ đơn giản:
  220VAC L ──[CB 10A]──[Contactor NC]──[Motor quạt]── N
                         Cuộn coil: 12VDC
                         Cuộn ← Relay K1 → 12VDC

Ghi chú quan trọng:
  - Relay board 5V KHÔNG được đấu trực tiếp với motor 250W+
  - Luôn đi qua contactor trung gian
  - Tụ chống nhiễu (snubber) 0.1μF/400V song song tiếp điểm
  - Cầu chì bảo vệ mỗi quạt riêng biệt
```

---

## 5.10 Van 24VAC — Ao Lắng K16–K19

```
Van điện từ 24VAC (solenoid valve):
  Điện áp: 24VAC (biến áp riêng)
  Công suất: 10–20 VA mỗi van

Relay K16–K19 (5V coil relay trên board):
  COM → Dây chung 24VAC (một đầu biến áp)
  NO  → Đầu cuộn van
  Đầu còn lại cuộn van → Đầu kia biến áp 24VAC

Ký hiệu đấu nối một van:
  Biến áp 24VAC:
    Đầu A ──── Relay K16 COM
    Đầu A ──── Relay K17 COM (chung)
    Đầu B ──── Van K16 Terminal 2
    Đầu B ──── Van K17 Terminal 2 (chung)

  Relay K16 NO ──── Van K16 Terminal 1
  Relay K17 NO ──── Van K17 Terminal 1

Lưu ý:
  Tụ 0.47μF/100V song song mỗi cuộn van để giảm spike
  Cách ly điện 24VAC hoàn toàn với mạch 3.3V/5V logic
```

---

## 5.11 Đấu Nối Blower K5–K6 (220VAC 750W)

```
Blower 750W — dòng khởi động cao → BẮT BUỘC dùng contactor

  Contactor 3P 25A (Schneider LC1-D09 hoặc tương đương):
    Tiếp điểm chính → Motor blower 220VAC
    Cuộn coil 24VDC hoặc 220VAC (tùy model)

  Relay K5/K6 (5V logic):
    NO → Cuộn contactor (24VDC common)
    → Bật contactor → Bật blower

Khởi động mềm (nên dùng):
  Dùng relay K5/K6 đóng sau 3 giây kể từ khi cấp điện contactor
  (giảm dòng khởi động đột ngột)
```

---

## 5.12 Máy Cho Ăn K20–K23 (220VAC Motor)

```
Máy cho ăn tự động (feeder):
  Motor: 220VAC 60–100W, dòng < 1A
  Relay board 10A đủ dùng trực tiếp (không cần contactor)

  MCP23017 GPA4 → Relay Board 3 IN5 → K20
  COM → 220VAC L (qua CB 6A)
  NO  → Motor máy cho ăn #1 → N (chung)

  Tương tự K21, K22, K23 cho máy cho ăn #2, #3, #4

Điều khiển khẩu phần:
  Relay đóng T_giây = số vòng quay × thời gian/vòng
  Cài đặt qua MQTT: aqua/{id}/feed/set {"portion_sec": 5}
  Mặc định: 5 giây / lần cho tôm, 8 giây / lần cho cá

Lắp đặt máy cho ăn:
  - 4 góc ao, hướng vào trung tâm ao
  - Chiều cao: 30–50 cm trên mặt nước
  - Che mưa bằng tấm nhựa góc nghiêng
```

---

## 5.13 Sơ Đồ Tổng Hợp Kết Nối CB2S

```
                      CB2S (BK7231N)
               ┌────────────────────────┐
GPIO6 (SDA)   ─┤ I2C SDA               ├─ 3.3V từ AMS1117
GPIO7 (SCL)   ─┤ I2C SCL               ├─ GND
GPIO8          ─┤ 1-Wire DS18B20 ×3     │
GPIO14         ─┤ Flow Meter #1 (INTR)  │
GPIO26         ─┤ Flow Meter #2 (INTR)  │
GPIO4          ─┤ UART RX (debug)       │
GPIO5          ─┤ UART TX (debug)       │
               └────────────────────────┘

I2C Bus (tất cả ở 3.3V từ AMS1117):
  0x20 → MCP23017 #1 (relay K0–K15)
  0x21 → MCP23017 #2 (relay K16–K30)
  0x29 → VL53L0X (mực nước)
  0x3C → OLED SH1106
  0x48 → ADS1115 (turbidity)
  0x61 → Atlas EZO-DO
  0x62 → Atlas EZO-ORP
  0x63 → Atlas EZO-pH
  0x64 → Atlas EZO-EC
  0x76 → MS5837-30BA (áp suất)
```

---

## 5.14 Lưu Ý An Toàn Môi Trường Ao

### Chống Ẩm và Chống Nước

| Hạng mục | Yêu cầu | Ghi chú |
|----------|---------|---------|
| Tủ điều khiển | IP55 tối thiểu | Ao ẩm, hơi nước nhiều |
| Cáp cảm biến ngâm nước | Vỏ PVC hoặc PE chịu hóa chất | Không dùng cáp thường |
| Đầu nối ngoài trời | IP67 | Kẹp cáp chống thấm |
| Relay board trong tủ | Cần thông thoáng, không đọng nước | Tủ có lỗ thoát nước đáy |
| ELCB 30mA | Bắt buộc | Người vận hành thường tiếp xúc nước |

### Khoảng Cách An Toàn Điện – Nước

```
  Tủ điện ──── tối thiểu 3 m ──── Mép ao
  Cáp điện ──── đi trong ống PVC/thép ──── dưới đất 30 cm
  Cáp cảm biến ──── cách ly hoàn toàn với cáp điện lực
  Contactor ──── cách mặt đất tối thiểu 30 cm (tránh ngập lụt)
```

### Tiếp Đất (Earthing)

```
  Cột tiếp đất: thép mạ đồng 1.5 m đóng xuống đất
  Dây tiếp đất: 6 mm² từ cột đến thanh đồng trong tủ
  Tất cả vỏ kim loại (tủ, cáp giáp) nối về thanh tiếp đất
  Điện trở đất: đo kiểm ≤ 10 Ω (ao ẩm thường đạt dễ)
  KHÔNG bỏ qua tiếp đất trong môi trường ao
```

### Hóa Chất Xử Lý Nước

```
  Vôi CaO/Ca(OH)₂ → ăn mòn cáp và đầu nối
  → Bọc đầu nối bằng băng tự hàn (self-amalgamating tape)
  → Kiểm tra đầu dò pH, DO, EC hàng tuần

  Chlorine xử lý ao → hỏng màng đầu dò DO nhanh
  → Rút đầu dò DO trước khi xử lý chlorine
  → Chờ ít nhất 24h sau xử lý mới đặt lại
```
