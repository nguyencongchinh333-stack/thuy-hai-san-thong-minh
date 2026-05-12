# 03 — Phần Cứng & Thông Số Kỹ Thuật

---

# PHẦN A — BỘ ĐIỀU KHIỂN

## A.1 CB2S — BK7231N (Tuya)

| Thông Số | Giá Trị |
|---------|--------|
| Chip | BK7231N |
| WiFi | 802.11 b/g/n 2.4GHz (không hỗ trợ 5GHz) |
| Flash / RAM | 2MB / 256KB |
| GPIO | 3.3V (Max 3.6V tuyệt đối) |
| I2C | GPIO0 (SDA) + GPIO1 (SCL) |
| UART | GPIO4 (TX) + GPIO5 (RX) |
| MQTT Broker | broker.hosco.com.vn |
| Giá | ~45.000đ |

### GPIO Phân Công

| GPIO | Chức Năng | Loại | Nguồn Tín Hiệu |
|------|-----------|------|---------------|
| GPIO0 | I2C SDA | Bidirectional | Pull-up 4.7kΩ → 3.3V |
| GPIO1 | I2C SCL | Output | Pull-up 4.7kΩ → 3.3V |
| GPIO4 | UART TX → MH-Z19B | Output | 3.3V |
| GPIO5 | UART RX ← MH-Z19B | Input | 3.3V |
| GPIO8 | DS18B20 (1-Wire) | Input | Pull-up 4.7kΩ → 3.3V |
| GPIO9 | Nút chế độ | INPUT_PULLUP | GND khi nhấn |
| GPIO10 | LED xanh AUTO | Output | 3.3V qua R220Ω |
| GPIO11 | LED đỏ THỦ CÔNG | Output | 3.3V qua R220Ω |
| GPIO14 | YF-B10 flow meter cấp | INPUT_PULLUP | 3.3V pulse ✅ |
| GPIO26 | YF-B10 flow meter thải | INPUT_PULLUP | 3.3V pulse ✅ |

### I2C Bus Map (Tất Cả 3.3V)

| Địa Chỉ | Thiết Bị | Tín Hiệu |
|:-------:|---------|---------|
| 0x20 | MCP23017 #1 (relay K0–K15) | I2C 3.3V ✅ |
| 0x21 | MCP23017 #2 (relay K16–K31) | I2C 3.3V ✅ |
| 0x29 | VL53L0X mực nước | I2C 3.3V ✅ |
| 0x3C | OLED SH1106 1.3" | I2C 3.3V ✅ |
| 0x48 | ADS1115 (turbidity, pH analog) | I2C 3.3V ✅ |
| 0x61 | Atlas EZO-DO | I2C 3.3V ✅ |
| 0x62 | Atlas EZO-ORP | I2C 3.3V ✅ |
| 0x63 | Atlas EZO-pH | I2C 3.3V ✅ |
| 0x64 | Atlas EZO-EC | I2C 3.3V ✅ |
| 0x76 | MS5837-30BA (áp suất) | I2C 3.3V ✅ |

---

## A.2 MCP23017 + Relay Board

| Thông Số | Giá Trị |
|---------|--------|
| MCP23017 | 16 GPIO/chip, I2C 400kHz, 3.3V |
| Relay Board | 5VDC coil, 10A/250VAC, opto-isolated, Active LOW |
| Số board | 4 × 8 kênh = 32 kênh |
| Giá | MCP23017: 32k/chip · Relay board: 65k/board |

## A.3 Bộ Nguồn

| Nguồn | Model | Output | Cấp Cho |
|-------|-------|--------|---------|
| Mean Well | HDR-60-5 | 5VDC/12A | CB2S, relay coil, AMS1117 input |
| AMS1117-3.3 | SOT-223 | 3.3V/800mA | **Tất cả cảm biến** (từ 5V Mean Well) |
| Biến áp | Liên Hoa 50VA | 24VAC/2A | Van ao lắng ×4 |

---

# PHẦN B — CẢM BIẾN CHẤT LƯỢNG NƯỚC

> **Tất cả cảm biến dưới đây tín hiệu ≤ 3.3V — đấu thẳng GPIO CB2S**
> Nguồn cấp: 3.3V từ AMS1117 (lấy từ Mean Well 5V)

## B.1 DO — Oxy Hòa Tan ⭐⭐⭐ BẮT BUỘC

| | Gói 1 Tiết Kiệm | Gói 2 Khuyến Nghị ✅ | Gói 3 Cao Cấp |
|--|:-:|:-:|:-:|
| Model | DFRobot SEN0237 | **Atlas EZO-DO** | Atlas EZO-DO (Optical) |
| Giao tiếp | Analog → ADS1115 | **I2C 0x61 ← 3.3V** ✅ | I2C 0x61 ← 3.3V ✅ |
| VCC | 3.3V | **3.3V** | 3.3V |
| Tín hiệu CB2S | 0–3.3V → ADS1115 ✅ | **I2C 3.3V trực tiếp** ✅ | I2C 3.3V ✅ |
| Độ chính xác | ±0.3 mg/L | **±0.05 mg/L** | ±0.01 mg/L |
| Hiệu chuẩn | Thường xuyên | 2 tuần/lần | 6 tháng/lần |
| Tuổi thọ đầu dò | 1–2 năm | 1–2 năm | **5+ năm** |
| Giá TL / TT | 380k / 520k | **850k / 1.200k** | 2.500k / 3.500k |

**Kết nối Atlas EZO-DO:**
```
EZO-DO VCC → 3.3V (AMS1117 rail)
EZO-DO GND → GND
EZO-DO SDA → GPIO0
EZO-DO SCL → GPIO1
Địa chỉ: 0x61
Lệnh đọc: Gửi 'R' → delay 600ms → đọc 7 bytes
```

---

## B.2 pH — Độ Axit / Kiềm ⭐⭐⭐ BẮT BUỘC

| | Gói 1 | Gói 2 ✅ | Gói 3 |
|--|:-:|:-:|:-:|
| Model | DFRobot SEN0161-V2 | **Atlas EZO-pH** | Atlas EZO-pH PRO |
| Giao tiếp | Analog → ADS1115 | **I2C 0x63 ← 3.3V** ✅ | I2C 3.3V ✅ |
| VCC | 3.3V | **3.3V** | 3.3V |
| Tín hiệu | 0–3.3V ✅ | **I2C 3.3V** ✅ | I2C 3.3V ✅ |
| Độ chính xác | ±0.1 pH | **±0.002 pH** | ±0.001 pH |
| Giá TL / TT | 150k / 200k | **385k / 595k** | 850k / 1.500k |

**Phụ kiện hiệu chuẩn pH:**
- Buffer pH 4.0: 45k · Buffer pH 7.0: 45k · Buffer pH 10.0: 45k
- Dung dịch KCl 3M ngâm đầu dò: 35k

---

## B.3 EC / Độ Mặn ⭐⭐⭐ (Ao Tôm)

| | Gói 1 | Gói 2 ✅ | Gói 3 |
|--|:-:|:-:|:-:|
| Model | DFRobot DFR0300 | **Atlas EZO-EC** | Atlas EZO-EC K0.1 |
| Giao tiếp | Analog → ADS1115 | **I2C 0x64 ← 3.3V** ✅ | I2C 3.3V ✅ |
| VCC | 3.3V | **3.3V** | 3.3V |
| Tín hiệu | 0–3.3V ✅ | **I2C 3.3V** ✅ | I2C 3.3V ✅ |
| Dải đo | 0–20 mS/cm | 0.07–500.000 μS/cm | Nước ngọt thấp |
| Giá TL / TT | 200k / 280k | **385k / 580k** | 450k / 650k |

**Phụ kiện:** Dung dịch chuẩn 1413 μS/cm: 35k · Khúc xạ kế đối chiếu: 180k

---

## B.4 ORP — Thế Oxy Hóa Khử ⭐⭐

| | Gói 1 | Gói 2 ✅ |
|--|:-:|:-:|
| Model | DFRobot SEN0165 | **Atlas EZO-ORP** |
| Giao tiếp | Analog → ADS1115 | **I2C 0x62 ← 3.3V** ✅ |
| VCC | 3.3V | **3.3V** |
| Tín hiệu | 0–3.3V ✅ | **I2C 3.3V** ✅ |
| Dải đo | ±2000 mV | ±2000 mV |
| Giá TL / TT | 180k / 250k | **480k / 700k** |

---

## B.5 Nhiệt Độ Nước ⭐⭐⭐ — 3 Điểm Đo

| | Gói 1 | Gói 2 ✅ |
|--|:-:|:-:|
| Model | DS18B20 Waterproof | **DS18B20 Titanium** |
| Giao tiếp | 1-Wire GPIO8 | **1-Wire GPIO8** |
| VCC | 3.3V | **3.3V** |
| Tín hiệu | 3.3V ✅ | **3.3V** ✅ |
| Vỏ | Nhựa/INOX | **Titan — chịu muối/axit** |
| Giá TL / TT | 25k / 30k (×3 = 75k/90k) | **45k / 60k (×3 = 135k/180k)** |

> 3 cảm biến trên cùng 1 bus 1-Wire: Đầu ao / Giữa ao / Cuối ao (gần quạt)

---

## B.6 Mực Nước Ao ⭐⭐

| | Gói 1 | Gói 2 ✅ | Gói 3 |
|--|:-:|:-:|:-:|
| Model | HC-SR04 (VCC=3.3V) | **VL53L0X** | VL53L1X |
| Giao tiếp | GPIO TRIG+ECHO | **I2C 0x29 ← 3.3V** ✅ | I2C 3.3V ✅ |
| VCC | 3.3V (ECHO 3.3V ✅) | **3.3V** | 3.3V |
| Tín hiệu | ECHO 3.3V ✅ | **I2C 3.3V** ✅ | I2C 3.3V ✅ |
| Dải đo | 2–200cm | **0–200cm** | 0–400cm |
| Loại | Siêu âm | **Laser ToF** | Laser ToF |
| Giá TL / TT | 25k / 28k | **35k / 45k** | 65k / 85k |

> **VL53L0X khuyến nghị**: Không cần ECHO pin, I2C thuần túy, chính xác hơn, nhỏ hơn

---

## B.7 Áp Suất Nước / Mực Nước Chìm ⭐⭐

| | Gói 1 | Gói 2 ✅ |
|--|:-:|:-:|
| Model | BMP280 (không ngâm) | **MS5837-30BA** |
| Giao tiếp | I2C 0x76 | **I2C 0x76 ← 3.3V** ✅ |
| VCC | 3.3V | **3.3V** |
| Tín hiệu | I2C 3.3V ✅ | **I2C 3.3V** ✅ |
| Chống nước | Không ngâm | **30 bar — ngâm trực tiếp** |
| Dải đo | 0–1000 hPa | 0–30 bar |
| Giá TL / TT | 25k / 30k | **350k / 480k** |

---

## B.8 Độ Đục / Turbidity ⭐⭐

| | Gói 1 ✅ | Gói 2 |
|--|:-:|:-:|
| Model | **SEN0189 (VCC=3.3V)** | SEN0554 |
| Giao tiếp | **Analog → ADS1115** | I2C 3.3V ✅ |
| VCC | **3.3V** (bắt buộc) | 3.3V |
| Tín hiệu | **0–3.3V → ADS1115** ✅ | I2C 3.3V ✅ |
| Dải đo | 0–3.000 NTU | 0–4.000 NTU + nhiệt độ |
| Giá TL / TT | **150k / 200k** | 350k / 480k |

---

## B.9 Lưu Lượng Nước ⭐⭐

| | Gói 1 | Gói 2 ✅ |
|--|:-:|:-:|
| Model | YF-B7 SS304 DN20 | **YF-B10 SS304 DN25** |
| Giao tiếp | GPIO pulse | **GPIO pulse** |
| VCC | 3.3V | **3.3V** |
| Tín hiệu | 3.3V pulse ✅ | **3.3V pulse** ✅ |
| Lưu lượng | 1–30 L/min | **1–60 L/min** |
| Đường kính | DN20 | **DN25** |
| Giá TL / TT | 185k / 215k | **220k / 250k** |

> Dùng 2 cái: 1 đo nước cấp (K14), 1 đo nước thải (K26) → Tính lượng nước thay mỗi ngày

---

## B.10 ADC — ADS1115

| Thông Số | Giá Trị |
|---------|--------|
| Giao tiếp | I2C 0x48 |
| VCC | **3.3V** (Input max ±3.3V) |
| Kênh | 4 kênh single-ended |
| AIN0 | SEN0189 Turbidity (0–3.3V) |
| AIN1 | SEN0165 ORP analog / pH analog |
| AIN2 | Dự phòng |
| AIN3 | Dự phòng |
| Giá TL / TT | 35k / 42k |

---

# PHẦN C — ACTUATOR CHÍNH

## C.1 Quạt Sục Khí Cánh Quạt (K1–K4)

| | Gói 1 | Gói 2 ✅ | Gói 3 | Gói 4 |
|--|:-:|:-:|:-:|:-:|
| Model | Nhựa 0.75kW | **INOX 1.1kW** | INOX 1.5kW | INOX 2.2kW |
| Điện | 220VAC | **220VAC** | 220V/380V | 380V 3P |
| Lưu lượng O₂ | ~1.5 kgO₂/h | **~2.2 kgO₂/h** | ~3.0 kgO₂/h | ~4.5 kgO₂/h |
| Relay CB2S | Relay + Contactor | **Relay + Contactor** | Relay + Contactor | Contactor 3P |
| Diện tích ao | ~500m² | **~1.000m²** | ~1.500m² | ~2.000m² |
| Giá TL / TT | 3.200k / 3.800k | **4.500k / 5.200k** | 6.500k / 7.500k | 9.500k / 11.000k |

> ⚠️ Quạt ≥ 1.1kW: **BẮT BUỘC có Contactor riêng** (không qua relay board trực tiếp)
> Contactor Chint 9A: 95.000đ / contactor

---

## C.2 Máy Thổi Khí Root Blower (K5–K6)

| | Gói 1 ✅ | Gói 2 |
|--|:-:|:-:|
| Công suất | **0.75kW** | 1.5kW |
| Áp suất | 30–50 kPa | 50–80 kPa |
| Phù hợp | Ao <1.000m² | Ao >1.500m² |
| Ưu điểm | Sục khí đều đáy ao | Sục khí mạnh hơn |
| Giá TL / TT | **5.500k / 6.500k** | 9.500k / 11.500k |

---

## C.3 Bơm Cấp/Thải Nước (K0, K7)

| | Bơm Cấp (K0) | Bơm Thải (K7) |
|--|:-:|:-:|
| Model | Pentax CM-80 0.75HP | Bơm chìm 0.5HP |
| Điện | 220VAC | 220VAC |
| Lưu lượng | 60–80 L/min | 40–60 L/min |
| Đầu nối | DN25 (1") | DN25 (1") |
| Giá TL / TT | 2.100k / 2.300k | 850k / 1.000k |

---

## C.4 Bơm Châm Vôi / Hóa Chất (K9, K10)

| | Bơm Vôi (K9) | Bơm Khoáng (K10) |
|--|:-:|:-:|
| Loại | Bơm định lượng màng | Bơm định lượng màng |
| Điện | 220VAC | 220VAC |
| Lưu lượng | 1–5 L/h (điều chỉnh) | 0.5–2 L/h |
| Vật liệu đầu bơm | Ceramic/PVDF (chịu kiềm) | PVDF |
| Giá TL / TT | 850k / 1.100k | 750k / 950k |

---

## C.5 Đèn UV Khử Trùng (K11)

| | Gói 1 ✅ | Gói 2 |
|--|:-:|:-:|
| Công suất | **40W** | 80W |
| Lưu lượng xử lý | ~3 m³/h | ~6 m³/h |
| Phù hợp | Nước cấp ao <1.000m² | Nước cấp ao >1.000m² |
| Thay bóng | 8.000–10.000 giờ | 8.000–10.000 giờ |
| Giá TL / TT | **850k / 1.000k** | 1.500k / 1.800k |

---

## C.6 Máy Cho Ăn Tự Động (K20–K23) — 4 Góc Ao

| | Gói 1 | Gói 2 ✅ | Gói 3 |
|--|:-:|:-:|:-:|
| Loại | Đĩa quay đơn giản | **Đĩa quay có hẹn giờ** | Máy thổi khí nén |
| Bán kính ném | 3–4m | **4–6m** | 8–15m |
| Motor | DC 12V | **220VAC** | Khí nén |
| Điều khiển | Relay | **Relay K20–K23** | Relay |
| Phù hợp | Ao <1.000m² | **Ao 1.000–3.000m²** | Ao >3.000m² |
| Giá TL / TT | 350k / 450k | **650k / 800k** | 2.500k / 3.000k |
| **4 máy TL / TT** | 1.400k / 1.800k | **2.600k / 3.200k** | 10.000k / 12.000k |

---

## C.7 Van Ao Lắng 24VAC (K16–K19)

| | Gói 1 | Gói 2 ✅ |
|--|:-:|:-:|
| Model | Van đồng Viton | **Van SS304 Viton Airtac** |
| Điện | 24VAC NC | **24VAC NC** |
| VCC tín hiệu | 24VAC từ biến áp | 24VAC |
| Chất liệu | Đồng | **SS304** |
| Giá TL / TT | 200k / 240k (×4=800k/960k) | **380k / 520k (×4=1.520k/2.080k)** |

---

## C.8 Bơm Dự Phòng + Van O₂ Khẩn Cấp (K28, K29)

| Thiết Bị | Thông Số | Giá TL | Giá TT |
|---------|---------|-------:|-------:|
| Bơm sục khí dự phòng (K28) | Motor 1.1kW 220VAC + Contactor | 4.800.000đ | 5.500.000đ |
| Van solenoid O₂ (K29) | 24VAC NC DN15 INOX 316 | 350.000đ | 500.000đ |
| Bình O₂ 40L (dự phòng) | Bình công nghiệp + van giảm áp | 1.500.000đ | 1.800.000đ |
| Dây, phụ kiện | — | 200.000đ | 200.000đ |
| **Tổng hệ thống khẩn cấp** | | **6.850.000đ** | **8.000.000đ** |

---

*[← Relay Map](04_relay-channel-map.md) | [Đấu Nối →](05_dau-noi-dien.md)*
