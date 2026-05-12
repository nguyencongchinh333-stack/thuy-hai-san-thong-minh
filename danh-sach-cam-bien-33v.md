# Danh Sách Cảm Biến Tín Hiệu 3.3V — Tương Thích Trực Tiếp CB2S (Tuya)

> **Tất cả cảm biến trong danh sách này**:
> - Tín hiệu trả về ≤ 3.3V → đấu thẳng GPIO CB2S ✅
> - Điện cấp: 3.3V (từ AMS1117) hoặc 5V (Mean Well) — không quan trọng
> - Không cần bộ trung gian, không cần chia áp

---

# NHÓM 1 — NHIỆT ĐỘ & ĐỘ ẨM KHÔNG KHÍ

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **DHT22** | GPIO 1 dây | 3.3V | 3.3V ✅ | Nhiệt độ ±0.5°C + Độ ẩm ±2% | 45.000đ | 55.000đ |
| Trung bình | **AHT21** | I2C 0x38 | 3.3V | I2C 3.3V ✅ | Nhiệt độ ±0.3°C + Độ ẩm ±2% | 35.000đ | 45.000đ |
| **Khuyến nghị ✅** | **SHT30** | I2C 0x44 | 3.3V | I2C 3.3V ✅ | Nhiệt độ ±0.3°C + Độ ẩm ±2% | 75.000đ | 90.000đ |
| Cao cấp | **SHT40** | I2C 0x44 | 3.3V | I2C 3.3V ✅ | Nhiệt độ ±0.2°C + Độ ẩm ±1.8% | 120.000đ | 150.000đ |

---

# NHÓM 2 — NHIỆT ĐỘ ĐƠN (Chống Nước — Ao / Đất)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **DS18B20 Waterproof** | 1-Wire GPIO | 3.3V | 3.3V ✅ | -55~+125°C / ±0.5°C | 25.000đ | 30.000đ |
| **Khuyến nghị ✅** | **DS18B20 Titanium** | 1-Wire GPIO | 3.3V | 3.3V ✅ | Vỏ titan, chịu axit/muối | 45.000đ | 60.000đ |
| Cao cấp | **PT100 + MAX31865** | SPI | 3.3V | SPI 3.3V ✅ | ±0.1°C, công nghiệp | 185.000đ | 240.000đ |

> DS18B20: Tối đa 8 cảm biến trên 1 bus 1-Wire (mỗi cái có địa chỉ ROM riêng)

---

# NHÓM 3 — ÁNH SÁNG (Nhà Kính)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **BH1750** | I2C 0x23 | 3.3V | I2C 3.3V ✅ | 1–65.535 lux | 25.000đ | 30.000đ |
| **Khuyến nghị ✅** | **VEML7700** | I2C 0x10 | 3.3V | I2C 3.3V ✅ | 0–120.000 lux, tự động gain | 35.000đ | 45.000đ |
| Cao cấp | **TSL2591** | I2C 0x29 | 3.3V | I2C 3.3V ✅ | Dải động rất rộng, IR + visible | 55.000đ | 70.000đ |

---

# NHÓM 4 — ĐỘ ẨM ĐẤT (Nhà Kính)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **Capacitive v1.2** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | 0–100% (tương đối) | 35.000đ | 40.000đ |
| **Khuyến nghị ✅** | **SEN0193 Capacitive** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | Bền hơn, INOX 304 | 55.000đ | 70.000đ |
| Cao cấp | **TEROS-12** | UART 3.3V | 3.3V | UART 3.3V ✅ | EC + Nhiệt độ + Độ ẩm đất | 1.800.000đ | 2.500.000đ |

> ⚠️ Analog sensor: **Bắt buộc cấp VCC = 3.3V** → AOUT = 0–3.3V → ADS1115 an toàn

---

# NHÓM 5 — CO₂ (Chăn Nuôi)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **CM1106** | UART | 5V | UART 3.3V ✅ | 400–5000 ppm | 180.000đ | 220.000đ |
| **Khuyến nghị ✅** | **MH-Z19B** | UART | 5V | UART 3.3V ✅ | 400–5000 ppm / tự hiệu chuẩn | 250.000đ | 280.000đ |
| Cao cấp | **SCD41** | I2C 0x62 | **3.3V** | I2C 3.3V ✅ | CO₂ + Nhiệt độ + Độ ẩm / ±40ppm | 850.000đ | 1.100.000đ |

> MH-Z19B và CM1106: VCC 5V nhưng **UART TX/RX = 3.3V logic** → đấu thẳng CB2S ✅

---

# NHÓM 6 — NH₃ AMONIAC (Chăn Nuôi / Thủy Sản)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **SEN0321 DFRobot** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | NH₃ chuyên dụng | 85.000đ | 100.000đ |
| **Khuyến nghị ✅** | **SEN0321 × 2** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | 2 điểm đo trong chuồng | 170.000đ | 200.000đ |
| Cao cấp | **Sensirion SFA30** | I2C 0x5D | **3.3V** | I2C 3.3V ✅ | NH₃ + HCHO + NOx / ±10% | 850.000đ | 1.200.000đ |

---

# NHÓM 7 — DO OXY HÒA TAN (Thủy Sản)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **DFRobot SEN0237** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | 0–20 mg/L / ±0.3 | 380.000đ | 520.000đ |
| **Khuyến nghị ✅** | **Atlas EZO-DO** | I2C 0x61 | **3.3V** | I2C 3.3V ✅ | 0–20 mg/L / ±0.05 | 850.000đ | 1.200.000đ |
| Cao cấp | **Atlas EZO-DO (optical)** | I2C 0x61 | **3.3V** | I2C 3.3V ✅ | Quang học, 5+ năm không thay | 2.500.000đ | 3.500.000đ |

---

# NHÓM 8 — pH NƯỚC (Thủy Sản)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **DFRobot SEN0161-V2** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | 0–14 pH / ±0.1 | 150.000đ | 200.000đ |
| **Khuyến nghị ✅** | **Atlas EZO-pH** | I2C 0x63 | **3.3V** | I2C 3.3V ✅ | 0–14 pH / ±0.002 | 385.000đ | 595.000đ |
| Cao cấp | **Atlas EZO-pH (PRO)** | I2C 0x63 | **3.3V** | I2C 3.3V ✅ | Đầu dò công nghiệp | 850.000đ | 1.500.000đ |

---

# NHÓM 9 — EC / ĐỘ MẶN (Thủy Sản)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **DFRobot DFR0300** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | 0–20 mS/cm | 200.000đ | 280.000đ |
| **Khuyến nghị ✅** | **Atlas EZO-EC** | I2C 0x64 | **3.3V** | I2C 3.3V ✅ | 0.07–500.000 μS/cm | 385.000đ | 580.000đ |
| Cao cấp | **Atlas EZO-EC (K0.1)** | I2C 0x64 | **3.3V** | I2C 3.3V ✅ | Nước ngọt độ dẫn thấp | 450.000đ | 650.000đ |

---

# NHÓM 10 — ORP (Thủy Sản)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **DFRobot SEN0165** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | -2000 đến +2000 mV | 180.000đ | 250.000đ |
| **Khuyến nghị ✅** | **Atlas EZO-ORP** | I2C 0x62 | **3.3V** | I2C 3.3V ✅ | -2000 đến +2000 mV / ±1mV | 480.000đ | 700.000đ |

---

# NHÓM 11 — ĐO KHOẢNG CÁCH / MỰC NƯỚC / MỨC CÁM

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Dải Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|--------|-------:|-------:|
| Tiết kiệm | **HC-SR04 (VCC=3.3V)** | GPIO TRIG+ECHO | **3.3V** | ECHO 3.3V ✅ | 2cm–200cm (giảm tầm so với 5V) | 25.000đ | 28.000đ |
| Trung bình | **AJ-SR04M (VCC=3.3V)** | GPIO TRIG+ECHO | **3.3V** | ECHO 3.3V ✅ | 20cm–600cm / IP65 chống nước | 55.000đ | 65.000đ |
| **Khuyến nghị ✅** | **AJ-SR04M (VCC=5V, R19 mod)** | GPIO TRIG+ECHO | 5V | ECHO 3.3V ✅ | 20cm–600cm / IP65 / tầm đầy đủ | 55.000đ | 65.000đ |
| Cao cấp | **VL53L0X** | I2C 0x29 | **3.3V** | I2C 3.3V ✅ | 0–2m / Laser ToF / ±3% | 35.000đ | 45.000đ |
| Cao cấp + | **VL53L1X** | I2C 0x29 | **3.3V** | I2C 3.3V ✅ | 0–4m / Laser ToF / ±3% | 65.000đ | 85.000đ |

> **HC-SR04 ở 3.3V**: Tầm đo giảm xuống ~2m (đủ cho đo mức cám hoặc mực nước ao nhỏ)
> **VL53L0X/L1X**: Laser ToF — không cần ECHO pin, I2C thuần túy, chính xác hơn siêu âm

---

# NHÓM 12 — ÁP SUẤT NƯỚC (Thủy Sản)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **BMP280** | I2C 0x76 | **3.3V** | I2C 3.3V ✅ | Áp suất không khí (không ngâm nước) | 25.000đ | 30.000đ |
| **Khuyến nghị ✅** | **MS5837-30BA** | I2C 0x76 | **3.3V** | I2C 3.3V ✅ | 0–30 bar / ngâm nước hoàn toàn | 350.000đ | 480.000đ |
| Cao cấp | **MS5803-14BA** | I2C | **3.3V** | I2C 3.3V ✅ | 0–14 bar / độ chính xác cao hơn | 450.000đ | 620.000đ |

---

# NHÓM 13 — ĐỘ ĐỤC NƯỚC / TURBIDITY (Thủy Sản)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| **Khuyến nghị ✅** | **SEN0189 (VCC=3.3V)** | Analog → ADS1115 | **3.3V** | 0–3.3V ✅ | 0–3000 NTU | 150.000đ | 200.000đ |
| Cao cấp | **DFROBOT SEN0554** | I2C | **3.3V** | I2C 3.3V ✅ | Turbidity + nhiệt độ | 350.000đ | 480.000đ |

> SEN0189: **Bắt buộc cấp VCC = 3.3V** → AOUT = 0–3.3V → ADS1115 an toàn

---

# NHÓM 14 — LƯU LƯỢNG NƯỚC

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **YF-S201 (nhựa)** | GPIO pulse | **3.3V** | 3.3V ✅ | 1–30 L/min / DN15 | 35.000đ | 42.000đ |
| **Khuyến nghị ✅** | **YF-B7 SS304** | GPIO pulse | **3.3V** | 3.3V ✅ | 1–30 L/min / DN20 / SS304 | 185.000đ | 215.000đ |
| Cao cấp | **YF-B10 SS304** | GPIO pulse | **3.3V** | 3.3V ✅ | 1–60 L/min / DN25 / SS304 | 220.000đ | 250.000đ |

> Hall effect flow meter: Tín hiệu open-collector, pull-up 4.7kΩ → 3.3V → đấu thẳng GPIO ✅

---

# NHÓM 15 — CÂN TRỌNG LƯỢNG

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Đo | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---|-------:|-------:|
| Tiết kiệm | **HX711 + Load cell 10kg** | GPIO 2 dây | **3.3V** | GPIO 3.3V ✅ | 0–10kg / ±1g | 60.000đ | 75.000đ |
| **Khuyến nghị ✅** | **HX711 + Load cell 50kg** | GPIO 2 dây | **3.3V** | GPIO 3.3V ✅ | 0–50kg / ±5g | 110.000đ | 130.000đ |
| Cao cấp | **HX711 + Load cell 200kg** | GPIO 2 dây | **3.3V** | GPIO 3.3V ✅ | 0–200kg / ±20g | 185.000đ | 220.000đ |

> HX711: VCC 2.6–5.5V → dùng 3.3V từ AMS1117. DT và SCK đấu thẳng GPIO CB2S ✅

---

# NHÓM 16 — HIỂN THỊ (Màn Hình)

| Gói | Model | Giao Tiếp | VCC | Tín Hiệu → CB2S | Hiển Thị | Giá TL | Giá TT |
|-----|-------|----------|-----|----------------|---------|-------:|-------:|
| Tiết kiệm | **OLED 0.96" SSD1306** | I2C 0x3C | **3.3V** | I2C 3.3V ✅ | 128×64 pixel | 35.000đ | 42.000đ |
| **Khuyến nghị ✅** | **OLED 1.3" SH1106** | I2C 0x3C | **3.3V** | I2C 3.3V ✅ | 128×64 pixel / to hơn | 55.000đ | 65.000đ |
| Trung bình | **LCD 20×4 I2C (3.3V version)** | I2C 0x27 | **3.3V** | I2C 3.3V ✅ | 20 ký tự × 4 dòng | 70.000đ | 85.000đ |
| Cao cấp | **TFT 2.4" ILI9341 SPI** | SPI | **3.3V** | SPI 3.3V ✅ | 240×320 màu | 95.000đ | 115.000đ |

> LCD 20×4 3.3V version: Phải mua loại PCF8574 có pull-up lên **3.3V** (không phải loại thông thường pull-up 5V)

---

# NHÓM 17 — ADC CHO CẢM BIẾN ANALOG

| Model | Giao Tiếp | VCC | Input Max | Kênh | Giá TL | Giá TT |
|-------|----------|-----|----------|------|-------:|-------:|
| **ADS1115** | I2C 0x48 | **3.3V** | **±3.3V** (khi VCC=3.3V) | 4 | 35.000đ | 42.000đ |
| ADS1015 | I2C 0x48 | **3.3V** | ±3.3V | 4 | 25.000đ | 32.000đ |

> ⚠️ ADS1115 khi VCC=3.3V: Input analog tối đa = **3.3V**. Mọi cảm biến analog **phải cấp 3.3V** để AOUT ≤ 3.3V.

---

# BẢNG TỔNG HỢP THEO HỆ THỐNG

## Nhà Kính — Danh Sách Cảm Biến Cuối

| Chức năng | Model Chọn | VCC | Tín Hiệu | Giá TL | Giá TT |
|----------|-----------|-----|---------|-------:|-------:|
| Nhiệt độ + Độ ẩm | SHT30 | 3.3V | I2C ✅ | 75.000đ | 90.000đ |
| Ánh sáng | VEML7700 | 3.3V | I2C ✅ | 35.000đ | 45.000đ |
| Độ ẩm đất | SEN0193 Capacitive | 3.3V | Analog→ADS1115 ✅ | 55.000đ | 70.000đ |
| ADC | ADS1115 | 3.3V | I2C ✅ | 35.000đ | 42.000đ |
| Flow meter | YF-B7 SS304 | 3.3V | GPIO pulse ✅ | 185.000đ | 215.000đ |
| Khoảng cách/mực | VL53L0X (laser ToF) | 3.3V | I2C ✅ | 35.000đ | 45.000đ |
| Màn hình | OLED 1.3" SH1106 | 3.3V | I2C ✅ | 55.000đ | 65.000đ |
| **Tổng cảm biến** | | | | **475.000đ** | **572.000đ** |

---

## Chăn Nuôi — Danh Sách Cảm Biến Cuối

| Chức năng | Model Chọn | VCC | Tín Hiệu | Giá TL | Giá TT |
|----------|-----------|-----|---------|-------:|-------:|
| Nhiệt độ + Độ ẩm ×3 | SHT30 | 3.3V | I2C ✅ | 225.000đ | 270.000đ |
| NH₃ Amoniac ×2 | SEN0321 | 3.3V | Analog→ADS1115 ✅ | 170.000đ | 200.000đ |
| CO₂ | MH-Z19B | 5V (UART 3.3V) | UART 3.3V ✅ | 250.000đ | 280.000đ |
| ADC | ADS1115 | 3.3V | I2C ✅ | 35.000đ | 42.000đ |
| Cân | HX711 + Load cell 50kg | 3.3V | GPIO ✅ | 110.000đ | 130.000đ |
| Mức cám ×4 | VL53L0X (laser ToF) | 3.3V | I2C ✅ | 140.000đ | 180.000đ |
| Flow meter nước | YF-B7 SS304 | 3.3V | GPIO pulse ✅ | 185.000đ | 215.000đ |
| Màn hình | OLED 1.3" SH1106 | 3.3V | I2C ✅ | 55.000đ | 65.000đ |
| **Tổng cảm biến** | | | | **1.170.000đ** | **1.382.000đ** |

> ⭐ Dùng **VL53L0X** thay AJ-SR04M cho mức cám: I2C thuần túy, không cần TRIG/ECHO, chính xác hơn, 35k/cái

---

## Thủy Hải Sản — Danh Sách Cảm Biến Cuối

| Chức năng | Model Chọn | VCC | Tín Hiệu | Giá TL | Giá TT |
|----------|-----------|-----|---------|-------:|-------:|
| DO Oxy hòa tan | Atlas EZO-DO | 3.3V | I2C ✅ | 850.000đ | 1.200.000đ |
| pH nước | Atlas EZO-pH | 3.3V | I2C ✅ | 385.000đ | 595.000đ |
| EC / Độ mặn | Atlas EZO-EC | 3.3V | I2C ✅ | 385.000đ | 580.000đ |
| ORP | Atlas EZO-ORP | 3.3V | I2C ✅ | 480.000đ | 700.000đ |
| Nhiệt độ nước ×3 | DS18B20 Titanium | 3.3V | 1-Wire ✅ | 135.000đ | 180.000đ |
| Độ đục | SEN0189 (3.3V) | 3.3V | Analog→ADS1115 ✅ | 150.000đ | 200.000đ |
| Mực nước | VL53L0X | 3.3V | I2C ✅ | 35.000đ | 45.000đ |
| Áp suất nước | MS5837-30BA | 3.3V | I2C ✅ | 350.000đ | 480.000đ |
| Flow meter ×2 | YF-B10 SS304 | 3.3V | GPIO pulse ✅ | 440.000đ | 500.000đ |
| Mức thức ăn ×4 | VL53L0X | 3.3V | I2C ✅ | 140.000đ | 180.000đ |
| ADC | ADS1115 | 3.3V | I2C ✅ | 35.000đ | 42.000đ |
| Màn hình | OLED 1.3" SH1106 | 3.3V | I2C ✅ | 55.000đ | 65.000đ |
| **Tổng cảm biến** | | | | **3.440.000đ** | **4.767.000đ** |

---

# I2C ADDRESS MAP (Để Tránh Trùng Địa Chỉ)

```
0x10 — VEML7700 (ánh sáng)
0x20 — MCP23017 #1 (relay 0–15)
0x21 — MCP23017 #2 (relay 16–31)
0x23 — BH1750 (ánh sáng — địa chỉ thay thế)
0x29 — VL53L0X / VL53L1X (laser ToF)
0x38 — AHT21 (nhiệt độ + độ ẩm)
0x3C — OLED SSD1306 / SH1106
0x44 — SHT30 (điểm 1)
0x45 — SHT30 (điểm 2) — ADDR pin = VCC
0x46 — SHT30 (điểm 3) — cần I2C multiplexer TCA9548A
0x48 — ADS1115
0x5D — Sensirion SFA30 (NH₃/NOx)
0x61 — Atlas EZO-DO
0x62 — Atlas EZO-ORP
0x63 — Atlas EZO-pH
0x64 — Atlas EZO-EC
0x76 — MS5837-30BA / BMP280
0x77 — BMP280 (địa chỉ thay thế)
```

> **Vấn đề SHT30 × 3**: SHT30 chỉ có 2 địa chỉ (0x44 và 0x45).
> Nếu cần 3 điểm → Dùng **TCA9548A I2C Multiplexer** (0x70) để có 8 kênh I2C riêng.
> Giá TCA9548A: ~25.000đ

---

*Cập nhật: 2026-05-12 | Tất cả tín hiệu ≤ 3.3V — đấu thẳng CB2S*
