# Báo Cáo Tương Thích Cảm Biến — CB2S (BK7231N) 3.3V
## Kiểm Tra Toàn Bộ 3 Hệ Thống: Nhà Kính / Chăn Nuôi / Thủy Hải Sản

> **Nguyên tắc của người dùng**: Điện cấp bao nhiêu không quan trọng.
> Chỉ cần **tín hiệu trả về ≤ 3.3V** để đấu thẳng vào GPIO CB2S.
> Tất cả cảm biến **dùng chung nguồn** từ Mean Well 5VDC.

---

# PHẦN 1 — SƠ ĐỒ NGUỒN CHUNG

```
Mean Well HDR-60-5 (5VDC / 12A)
│
├── 5V Rail ─────────────────────────────────────────────────
│   ├── CB2S (qua internal 3.3V reg)
│   ├── MCP23017 × 2 (qua 3.3V CB2S)
│   ├── Relay Board × 4 (coil 5V)
│   ├── AJ-SR04M (VCC=5V, ECHO=3.3V qua mod R19) ✅
│   └── MH-Z19B CO₂ (VCC=5V, UART signal=3.3V) ✅
│
└── AMS1117-3.3 LDO ──→ 3.3V Rail ──────────────────────────
    (5V → 3.3V, 800mA, giá ~8.000đ)
    ├── SEN0321 NH₃ (VCC=3.3V → AOUT=0–3.3V) ✅
    ├── MS5837-30BA áp suất nước (VCC=3.3V, I2C) ✅
    ├── OLED SH1106 1.3" (VCC=3.3V, I2C) ✅
    ├── SHT30 nhiệt độ+độ ẩm (VCC=3.3V, I2C) ✅
    ├── ADS1115 ADC (VCC=3.3V, I2C) ✅
    ├── BH1750 ánh sáng (VCC=3.3V, I2C) ✅
    ├── Atlas EZO-DO/pH/EC/ORP (VCC=3.3V, I2C) ✅
    ├── DS18B20 (VCC=3.3V, 1-Wire) ✅
    └── Capacitive soil moisture (VCC=3.3V, Analog→ADS1115) ✅
```

---

# PHẦN 2 — LDO AMS1117-3.3 (Thêm Vào Bộ Điều Khiển)

## Mạch Lắp Trên Perfboard (Cạnh CB2S)

```
Mean Well 5V ──── AMS1117-3.3 VIN
                  AMS1117-3.3 GND ──── GND
                  AMS1117-3.3 VOUT ─── Busbar 3.3V cảm biến
                                │
                          [10μF] + [100nF]  (tụ lọc)
                                │
                               GND

Chi phí:
  AMS1117-3.3 SOT-223:  6.000đ
  Tụ 10μF ×2:           2.000đ
  Tụ 100nF ×1:          500đ
  ─────────────────────────────
  Tổng thêm vào BOM:    ~8.500đ
```

> AMS1117-3.3 cấp tối đa 800mA ở 3.3V — đủ cho tất cả cảm biến (tổng ~120mA).

---

# PHẦN 3 — 4 CẢM BIẾN THAY THẾ & CÁCH KẾT NỐI

## 3.1 HC-SR04 → AJ-SR04M (Mực Nước / Mức Cám)

**Vấn đề cũ**: HC-SR04 ECHO pin = 5V → hỏng GPIO CB2S
**Giải pháp**: AJ-SR04M có jumper/resistor R19 để chọn 3.3V output mode

```
AJ-SR04M                CB2S
  VCC  ──── 5V (Mean Well)
  GND  ──── GND
  TRIG ──── GPIO13 (OUTPUT 3.3V — OK)
  ECHO ──── GPIO12 (INPUT 3.3V — OK sau khi bridge R19)

Cách bridge R19 để ECHO = 3.3V:
  Trên board AJ-SR04M có resistor R19 (gần chân ECHO)
  → Hàn nối tắt (short) R19 → ECHO output = 3.3V ✅
  → Không cần bất kỳ mạch thêm nào

Hoặc đơn giản hơn: Cấp VCC = 3.3V (từ AMS1117 rail)
  → ECHO tự động = 3.3V ✅ (không cần mod gì cả)
```

| | HC-SR04 (cũ ❌) | AJ-SR04M (mới ✅) |
|--|:-:|:-:|
| VCC | 5V | 5V hoặc 3.3V |
| ECHO signal | **5V** ❌ | **3.3V** ✅ |
| Chống nước | Không | **IP65** |
| Giá TL | 25.000đ | **55.000đ** |
| Giá TT | 28.000đ | **65.000đ** |

---

## 3.2 MQ-135 → SEN0321 DFRobot (NH₃ Amoniac — Chăn Nuôi)

**Vấn đề cũ**: MQ-135 heater cần 5V, AOUT lên đến 5V → hỏng ADS1115
**Giải pháp**: SEN0321 cấp 3.3V từ AMS1117 → AOUT = 0–3.3V → thẳng ADS1115

```
SEN0321                  CB2S / ADS1115
  VCC  ──── 3.3V (AMS1117 rail)
  GND  ──── GND
  AOUT ──── ADS1115 AIN0 (I2C 0x48)

ADS1115 đọc AOUT → quy đổi ra ppm NH₃
ADS1115 VCC cũng nối 3.3V → Input max = 3.3V → AOUT 0–3.3V ✅
```

| | MQ-135 (cũ ❌) | SEN0321 (mới ✅) |
|--|:-:|:-:|
| VCC | 5V (bắt buộc) | **3.3V** ✅ |
| AOUT signal | **0–5V** ❌ | **0–3.3V** ✅ |
| Đo NH₃ | Chung (nhiều khí) | **Chuyên NH₃** |
| Preheat | 24–48h | **15 phút** |
| Giá TL | 35.000đ | **85.000đ** |
| Giá TT | 40.000đ | **100.000đ** |

---

## 3.3 HK-0901 → MS5837-30BA (Áp Suất Nước — Thủy Sản)

**Vấn đề cũ**: HK-0901 output 0.5–4.5V (power 5V) → vượt ngưỡng ADS1115 3.3V
**Giải pháp**: MS5837-30BA dùng I2C 3.3V, ngâm nước trực tiếp, chính xác cao

```
MS5837-30BA              CB2S
  VCC  ──── 3.3V (AMS1117 rail)
  GND  ──── GND
  SDA  ──── GPIO0 (I2C SDA)
  SCL  ──── GPIO1 (I2C SCL)
  Địa chỉ I2C: 0x76

Ngâm trực tiếp dưới đáy ao — chịu 30 bar (300m nước)
Không cần vỏ bảo vệ thêm
```

| | HK-0901 (cũ ❌) | MS5837-30BA (mới ✅) |
|--|:-:|:-:|
| VCC | 5V | **3.3V** ✅ |
| Output signal | **0.5–4.5V analog** ❌ | **I2C 3.3V** ✅ |
| Chịu nước | IP65 (không ngâm) | **30 bar (ngâm trực tiếp)** |
| Độ chính xác | ±0.5% | **±0.2 mbar** |
| Giao tiếp | Analog → ADS1115 | **I2C trực tiếp CB2S** |
| Giá TL | 120.000đ | **350.000đ** |
| Giá TT | 150.000đ | **480.000đ** |

---

## 3.4 LCD I2C 5V → OLED SH1106 1.3" (Tất Cả Hệ Thống)

**Vấn đề cũ**: LCD PCF8574 pull-up SDA/SCL lên 5V → hỏng GPIO CB2S
**Giải pháp**: OLED SH1106 native 3.3V, I2C signal 3.3V, nhỏ hơn, rẻ hơn

```
OLED SH1106              CB2S
  VCC  ──── 3.3V (AMS1117 rail)
  GND  ──── GND
  SDA  ──── GPIO0 (I2C SDA)
  SCL  ──── GPIO1 (I2C SCL)
  Địa chỉ I2C: 0x3C (mặc định)

Hiển thị: 128×64 pixel trắng đen
Góc nhìn: Rộng hơn LCD thường
```

| | LCD 16×2/20×4 I2C (cũ ❌) | OLED SH1106 1.3" (mới ✅) |
|--|:-:|:-:|
| VCC | 5V | **3.3V** ✅ |
| SDA/SCL signal | **5V** ❌ | **3.3V** ✅ |
| Kích thước | Lớn | Nhỏ gọn |
| Độ tương phản | Bình thường | **Cao, góc nhìn rộng** |
| Giá TL | 50.000–70.000đ | **55.000đ** (rẻ hơn) |
| Giá TT | 60.000–80.000đ | **65.000đ** (rẻ hơn) |

---

# PHẦN 4 — KẾT QUẢ CUỐI CÙNG: TẤT CẢ TƯƠNG THÍCH

## Nhà Kính — Cảm Biến Sau Cập Nhật

| Cảm Biến | Model | Nguồn | Tín Hiệu → CB2S | Rail |
|---------|-------|-------|----------------|------|
| Nhiệt độ | DS18B20 | 3.3V | 1-Wire 3.3V ✅ | AMS1117 |
| Nhiệt độ + Độ ẩm | DHT22 | 3.3V | DATA 3.3V ✅ | AMS1117 |
| Ánh sáng | BH1750 | 3.3V | I2C 3.3V ✅ | AMS1117 |
| Độ ẩm đất | Capacitive | 3.3V | Analog 0–3.3V → ADS1115 ✅ | AMS1117 |
| ADC | ADS1115 | 3.3V | I2C 3.3V ✅ | AMS1117 |
| Flow meter | YF-B7 | 3.3V | Pulse 3.3V ✅ | AMS1117 |
| **Mực nước** | **AJ-SR04M** | **5V/3.3V** | **ECHO 3.3V** ✅ | **5V hoặc AMS1117** |
| **Màn hình** | **OLED SH1106** | **3.3V** | **I2C 3.3V** ✅ | **AMS1117** |

---

## Chăn Nuôi — Cảm Biến Sau Cập Nhật

| Cảm Biến | Model | Nguồn | Tín Hiệu → CB2S | Rail |
|---------|-------|-------|----------------|------|
| Nhiệt độ + Độ ẩm ×3 | SHT30 | 3.3V | I2C 3.3V ✅ | AMS1117 |
| **NH₃ Amoniac** | **SEN0321** | **3.3V** | **Analog 0–3.3V → ADS1115** ✅ | **AMS1117** |
| CO₂ | MH-Z19B | **5V** | **UART 3.3V** ✅ | **Mean Well 5V** |
| ADC | ADS1115 | 3.3V | I2C 3.3V ✅ | AMS1117 |
| Cân | HX711 | 3.3V | GPIO 3.3V ✅ | AMS1117 |
| **Mức cám** | **AJ-SR04M** | **5V/3.3V** | **ECHO 3.3V** ✅ | **5V hoặc AMS1117** |
| Flow meter | YF-B7 | 3.3V | Pulse 3.3V ✅ | AMS1117 |
| **Màn hình** | **OLED SH1106** | **3.3V** | **I2C 3.3V** ✅ | **AMS1117** |

---

## Thủy Hải Sản — Cảm Biến Sau Cập Nhật

| Cảm Biến | Model | Nguồn | Tín Hiệu → CB2S | Rail |
|---------|-------|-------|----------------|------|
| DO Oxy | Atlas EZO-DO | 3.3V | I2C 3.3V ✅ | AMS1117 |
| pH | Atlas EZO-pH | 3.3V | I2C 3.3V ✅ | AMS1117 |
| EC / Độ mặn | Atlas EZO-EC | 3.3V | I2C 3.3V ✅ | AMS1117 |
| ORP | Atlas EZO-ORP | 3.3V | I2C 3.3V ✅ | AMS1117 |
| Nhiệt độ nước ×3 | DS18B20 | 3.3V | 1-Wire 3.3V ✅ | AMS1117 |
| Độ đục | SEN0189 | 3.3V | Analog 0–3.3V → ADS1115 ✅ | AMS1117 |
| **Mực nước** | **AJ-SR04M** | **5V/3.3V** | **ECHO 3.3V** ✅ | **5V hoặc AMS1117** |
| **Áp suất nước** | **MS5837-30BA** | **3.3V** | **I2C 3.3V** ✅ | **AMS1117** |
| Flow meter ×2 | YF-B10 | 3.3V | Pulse 3.3V ✅ | AMS1117 |
| Mức thức ăn | AJ-SR04M | 5V/3.3V | ECHO 3.3V ✅ | AMS1117 |
| ADC | ADS1115 | 3.3V | I2C 3.3V ✅ | AMS1117 |
| **Màn hình** | **OLED SH1106** | **3.3V** | **I2C 3.3V** ✅ | **AMS1117** |

---

# PHẦN 5 — CẬP NHẬT BOM BỘ ĐIỀU KHIỂN

## Thêm Vào Mạch Điều Khiển (Mục 2.1)

| STT | Linh Kiện | Model | Giá TL | Giá TT | SL | TL Tổng | TT Tổng |
|:---:|----------|-------|-------:|-------:|:--:|--------:|--------:|
| + | LDO 3.3V cho cảm biến | AMS1117-3.3 SOT-223 | 6.000đ | 8.000đ | 1 | 6.000đ | 8.000đ |
| + | Tụ lọc 10μF | — | 1.000đ | 1.000đ | 2 | 2.000đ | 2.000đ |
| + | Tụ bypass 100nF | — | 500đ | 500đ | 1 | 500đ | 500đ |
| | | | | | | **+8.500đ** | **+10.500đ** |

> Chỉ thêm **8.500đ** cho toàn bộ rail 3.3V cảm biến — không đáng kể.

---

# PHẦN 6 — SƠ ĐỒ KẾT NỐI NGUỒN CUỐI CÙNG

```
┌─────────────────────────────────────────────────────────────┐
│                  NGUỒN CHUNG: MEAN WELL 5VDC               │
└──────────┬──────────────────────────────────────────────────┘
           │
    ┌──────┴──────────────────────────────────────────────┐
    │  5V Rail                                            │
    │  ├── CB2S (→ internal 3.3V reg → MCP23017, logi)   │
    │  ├── Relay Board × 4 (coil 5V)                     │
    │  ├── MH-Z19B CO₂ (VCC 5V, UART signal 3.3V → CB2S)│
    │  ├── AJ-SR04M × n (VCC 5V, ECHO 3.3V via R19 mod) │
    │  └── AMS1117-3.3 LDO Input                         │
    │             │                                       │
    │  3.3V Rail  ▼                                      │
    │  ├── SEN0321 NH₃ (AOUT 0–3.3V → ADS1115)           │
    │  ├── SEN0189 Turbidity (AOUT 0–3.3V → ADS1115)     │
    │  ├── MS5837 áp suất (I2C → CB2S)                   │
    │  ├── OLED SH1106 (I2C → CB2S)                      │
    │  ├── SHT30 ×3 (I2C → CB2S)                         │
    │  ├── Atlas EZO ×4 (I2C → CB2S)                     │
    │  ├── ADS1115 (I2C → CB2S)                          │
    │  ├── BH1750 (I2C → CB2S)                           │
    │  ├── DS18B20 (1-Wire → CB2S)                       │
    │  ├── HX711 cân (GPIO → CB2S)                       │
    │  └── Capacitive moisture (Analog → ADS1115)        │
    └─────────────────────────────────────────────────────┘

Tất cả đấu tín hiệu thẳng GPIO CB2S — KHÔNG qua bộ trung gian.
```

---

*Cập nhật: 2026-05-12 | Áp dụng cho: Nhà Kính + Chăn Nuôi + Thủy Hải Sản*
