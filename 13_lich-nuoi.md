# 13 — Lịch Nuôi Trồng

---

## 13.1 Tôm Thẻ Chân Trắng (Litopenaeus vannamei)

### Tổng Quan Chu Kỳ Nuôi

```
Ngày 0      Ngày 30     Ngày 60     Ngày 90     Ngày 100–110
  │           │           │           │              │
  ▼           ▼           ▼           ▼              ▼
[Thả giống]──[Gđ 1]────[Gđ 2]──────[Gđ 3]───────[Thu hoạch]
  PL10–12   Ương–nuôi   Phát triển  Tăng trọng    Size 40–50
  mật độ    thưa        trung bình   nhanh         con/kg
  100–120   10–15g/m²   15–20g/m²   20–25g/m²
  con/m²
```

---

### GIAI ĐOẠN 1: Ngày 1–30 (Ương và Nuôi Nhỏ)

#### Thông Số Môi Trường

| Thông số | Giá trị mục tiêu | Ngưỡng can thiệp |
|----------|:----------------:|:----------------:|
| Nhiệt độ (°C) | 28–30 | < 25: bật sưởi; > 32: tăng sục khí |
| Độ mặn (‰) | 15–20 | < 12 hoặc > 25 |
| DO (mg/L) | 6–8 | < 5: tăng quạt |
| pH (sáng) | 7.5–8.0 | < 7.2 hoặc > 8.8 |
| pH (chiều) | 7.8–8.5 | < 7.5 hoặc > 9.0 |
| NH₃-N (mg/L) | < 0.1 | > 0.3: thay nước |
| Turbidity (NTU) | 20–40 | > 60: ao lắng |
| Mực nước | 80–100 cm | < 70: bơm cấp ngay |

#### Lịch Cho Ăn Giai Đoạn 1

| Ca | Giờ | Lượng (g/100m²) | Loại thức ăn | Ghi chú |
|:--:|:---:|:---------------:|:------------:|---------|
| 1 | 06:00 | 30 | Starter 0 (mịn) | Kiểm tra DO trước |
| 2 | 09:00 | 30 | Starter 0 | |
| 3 | 12:00 | 25 | Starter 0 | Giảm 17% trưa nắng |
| 4 | 15:00 | 30 | Starter 0 | |
| 5 | 18:00 | 30 | Starter 0 | |
| 6 | 21:00 | 35 | Starter 0 | Ca tối +17% |
| **Tổng** | | **180 g/100m²** | | FCR mục tiêu: 1.3 |

**Cài đặt MQTT giai đoạn 1:**
```json
{
  "species": "shrimp",
  "stage": 1,
  "feed_schedule": [
    {"hour":6,  "min":0,  "duration_sec":2, "feeders":15},
    {"hour":9,  "min":0,  "duration_sec":2, "feeders":15},
    {"hour":12, "min":0,  "duration_sec":2, "feeders":15},
    {"hour":15, "min":0,  "duration_sec":2, "feeders":15},
    {"hour":18, "min":0,  "duration_sec":2, "feeders":15},
    {"hour":21, "min":0,  "duration_sec":3, "feeders":15}
  ],
  "do_min_feed": 4.0,
  "do_half_feed": 3.0
}
```

#### Lịch Thay Nước Giai Đoạn 1

```
Chu kỳ: Mỗi 3 ngày, thay 10% thể tích ao
Thực hiện: 8:00 sáng (sau khi DO đã đủ cao)
Điều kiện dừng: DO < 4.5 mg/L lúc xả

Cài đặt MQTT:
{
  "water_change": {
    "interval_days": 3,
    "percent": 10,
    "hour": 8,
    "do_min": 4.5
  }
}
```

#### Lịch Xử Lý Nước Giai Đoạn 1

| Ngày | Xử lý | Liều lượng | Ghi chú |
|:----:|-------|-----------|---------|
| 1 | Vôi CaCO₃ | 100 kg/1000m³ | Ổn định pH |
| 3 | Men vi sinh | Theo nhãn | Cân bằng vi sinh |
| 7 | Khoáng tổng hợp | 5 kg/1000m³ | Bù khoáng |
| 10 | Kiểm tra NH₃, NO₂ | — | Điều chỉnh vi sinh |
| 14 | Men vi sinh | Theo nhãn | Lặp lại 2 tuần/lần |
| 21 | Khoáng + vitamin | 5 kg/1000m³ | Tăng đề kháng |

---

### GIAI ĐOẠN 2: Ngày 31–60 (Tăng Trưởng Chính)

#### Thông Số Môi Trường

| Thông số | Giá trị mục tiêu | Ghi chú |
|----------|:----------------:|---------|
| Nhiệt độ (°C) | 27–30 | Tốt nhất 28–29 |
| Độ mặn (‰) | 12–18 | Có thể giảm dần |
| DO (mg/L) | 5–8 | Ngưỡng can thiệp: < 4.5 |
| pH | 7.5–8.5 | Biến động trong ngày ≤ 1 đơn vị |
| ORP (mV) | 250–350 | Duy trì bằng sục khí |
| Turbidity (NTU) | 25–50 | Duy trì tảo tốt |

#### Lịch Cho Ăn Giai Đoạn 2

| Ca | Giờ | Lượng (g/100m²) | Loại thức ăn |
|:--:|:---:|:---------------:|:------------:|
| 1 | 06:00 | 50 | Grower 1 (1.8 mm) |
| 2 | 09:00 | 55 | Grower 1 |
| 3 | 12:00 | 45 | Grower 1 |
| 4 | 15:00 | 55 | Grower 1 |
| 5 | 18:00 | 55 | Grower 1 |
| 6 | 21:00 | 65 | Grower 1 |
| **Tổng** | | **325 g/100m²** | FCR mục tiêu: 1.4 |

**Cài đặt MQTT giai đoạn 2:**
```json
{
  "species": "shrimp",
  "stage": 2,
  "feed_schedule": [
    {"hour":6,  "min":0,  "duration_sec":3, "feeders":15},
    {"hour":9,  "min":0,  "duration_sec":4, "feeders":15},
    {"hour":12, "min":0,  "duration_sec":3, "feeders":15},
    {"hour":15, "min":0,  "duration_sec":4, "feeders":15},
    {"hour":18, "min":0,  "duration_sec":4, "feeders":15},
    {"hour":21, "min":0,  "duration_sec":5, "feeders":15}
  ],
  "do_min_feed": 4.0,
  "do_half_feed": 3.5
}
```

#### Lịch Thay Nước Giai Đoạn 2

```
Chu kỳ: Mỗi 2 ngày, thay 15% thể tích
Thực hiện: 7:00 sáng
Lý do tăng tần suất: Chất thải tăng theo khối lượng tôm
```

---

### GIAI ĐOẠN 3: Ngày 61–100 (Tăng Trọng Nhanh và Thu Hoạch)

#### Thông Số Môi Trường

| Thông số | Giá trị mục tiêu | Chú ý |
|----------|:----------------:|-------|
| Nhiệt độ (°C) | 27–30 | Không để > 32 trong giai đoạn này |
| DO (mg/L) | 5–8 | Mật độ cao → DO xuống nhanh hơn |
| pH sáng | 7.3–8.0 | |
| pH chiều | 7.8–8.8 | Tảo mạnh → pH chiều cao |
| NH₃ (mg/L) | < 0.05 | Giai đoạn cuối rất dễ NH₃ tăng |
| EC (mS/cm) | 20–35 | Giữ ổn định |

#### Lịch Cho Ăn Giai Đoạn 3

| Ca | Giờ | Lượng (g/100m²) | Loại thức ăn |
|:--:|:---:|:---------------:|:------------:|
| 1 | 06:00 | 80 | Finisher (2.5 mm) |
| 2 | 09:00 | 90 | Finisher |
| 3 | 12:00 | 70 | Finisher |
| 4 | 15:00 | 90 | Finisher |
| 5 | 18:00 | 90 | Finisher |
| 6 | 21:00 | 100 | Finisher |
| **Tổng** | | **520 g/100m²** | FCR mục tiêu: 1.5 |

#### Lịch Thay Nước Giai Đoạn 3

```
Chu kỳ: Mỗi ngày, thay 20% thể tích
Thực hiện: 6:00 sáng (trước ca ăn đầu)
Bơm bùn K26: Chạy 30 phút sau mỗi lần thay nước

Ngày 90–100 (chuẩn bị thu hoạch):
  - Ngừng thay nước 3 ngày trước thu hoạch
  - Giảm lượng ăn 30% (ngày -3, -2, -1 trước thu)
  - Ngày thu hoạch: Ngừng ăn hoàn toàn
```

#### Lịch Cài Đặt Ngưỡng Tự Điều Chỉnh Theo Giai Đoạn

```
Firmware tự điều chỉnh dựa vào farming_day:

MQTT để cài ngày nuôi hiện tại:
{
  "farming_day": 45,
  "species": 0,
  "auto_adjust_thresholds": true
}

Khi auto_adjust_thresholds = true:
  Ngày 1–30:   DO cảnh báo < 5.0
  Ngày 31–60:  DO cảnh báo < 4.5
  Ngày 61–100: DO cảnh báo < 4.0  (tôm lớn hơn chịu tốt hơn)
```

---

## 13.2 Cá Tra (Pangasianodon hypophthalmus)

### Tổng Quan Chu Kỳ Nuôi

```
Tuần 0      Tuần 4      Tuần 10     Tuần 16     Tuần 20
  │           │           │           │              │
  ▼           ▼           ▼           ▼              ▼
[Thả cá]──[Cá nhỏ]────[Phát triển]──[Vỗ béo]───[Thu hoạch]
  5–10g     50–100g      200–400g     600–800g    800–1200g
  50con/m²  40con/m²     30con/m²     20con/m²    con/m²
  nước ngọt thức ăn       thức ăn      năng suất   ~800 g/con
  hoặc lợ   nhỏ          viên lớn     cao
```

---

### TUẦN 1–4: Cá Giống (5–100g/con)

#### Thông Số Môi Trường

| Thông số | Giá trị mục tiêu | Ngưỡng can thiệp |
|----------|:----------------:|:----------------:|
| Nhiệt độ (°C) | 27–30 | < 24: bật sưởi; > 33: tăng sục khí |
| DO (mg/L) | 5–7 | < 4: tăng quạt ngay |
| pH | 6.5–7.8 | < 6.0 hoặc > 8.5 |
| Độ mặn (‰) | 0–3 | Nước ngọt tốt nhất |
| NH₃-N (mg/L) | < 0.2 | > 0.5: thay nước khẩn |
| Turbidity (NTU) | 30–60 | > 100: thay nước |

#### Lịch Cho Ăn Tuần 1–4

| Ca | Giờ | Lượng (g/100m²) | Loại thức ăn | Ghi chú |
|:--:|:---:|:---------------:|:------------:|---------|
| 1 | 07:00 | 40 | Cám cá giống 30% đạm | |
| 2 | 12:00 | 35 | Cám cá giống | Giảm trưa |
| 3 | 17:00 | 40 | Cám cá giống | |
| **Tổng** | | **115 g/100m²/ngày** | | FCR: 1.5–1.7 |

```json
{
  "species": "catfish",
  "stage": 1,
  "feed_schedule": [
    {"hour":7,  "min":0,  "duration_sec":4, "feeders":15},
    {"hour":12, "min":0,  "duration_sec":3, "feeders":15},
    {"hour":17, "min":0,  "duration_sec":4, "feeders":15}
  ],
  "do_min_feed": 3.5,
  "do_half_feed": 2.5
}
```

#### Lịch Thay Nước Tuần 1–4

```
Tuần 1–2: Thay 15% mỗi 3 ngày
Tuần 3–4: Thay 20% mỗi 2 ngày
Thời điểm: 7:30 sáng (sau đọc DO, trước ca ăn)
```

---

### TUẦN 5–10: Tăng Trưởng (100–400g/con)

#### Thông Số Môi Trường

| Thông số | Giá trị mục tiêu | Ghi chú |
|----------|:----------------:|---------|
| Nhiệt độ (°C) | 26–30 | Mật độ tăng → nhiệt độ tăng |
| DO (mg/L) | 4–7 | Cá tra chịu DO thấp hơn tôm |
| pH | 6.5–8.0 | |
| EC (mS/cm) | < 5 | Nước ngọt / lợ nhẹ |
| Turbidity (NTU) | 40–80 | Chấp nhận cao hơn tôm |

#### Lịch Cho Ăn Tuần 5–10

| Ca | Giờ | Lượng (g/100m²) | Loại thức ăn |
|:--:|:---:|:---------------:|:------------:|
| 1 | 07:00 | 80 | Cám cá thịt 26% đạm, 3mm |
| 2 | 12:00 | 70 | Cám cá thịt |
| 3 | 17:00 | 80 | Cám cá thịt |
| **Tổng** | | **230 g/100m²/ngày** | FCR: 1.6–1.8 |

#### Lịch Thay Nước Tuần 5–10

```
Thay 20% mỗi 2 ngày → Tuần 9–10: 25% mỗi ngày
Bơm bùn K26: Chạy 45 phút sau mỗi lần thay nước
UV K11: Bật toàn thời gian có bơm cấp hoạt động
```

---

### TUẦN 11–16: Vỗ Béo (400–800g/con)

#### Thông Số Môi Trường Giai Đoạn Quan Trọng

| Thông số | Giá trị mục tiêu | Nguy hiểm |
|----------|:----------------:|:---------:|
| DO (mg/L) | 4–7 | < 2 → chết nhanh do mật độ cao |
| pH | 6.5–8.0 | > 9 → hỏng mang cá |
| NH₃ (mg/L) | < 0.1 | > 0.3 → cá stress, bệnh |
| Nhiệt độ (°C) | 26–30 | > 33 → giảm ăn, dịch bệnh |

#### Lịch Cho Ăn Tuần 11–16

| Ca | Giờ | Lượng (g/100m²) | Loại thức ăn |
|:--:|:---:|:---------------:|:------------:|
| 1 | 07:00 | 130 | Cám vỗ béo 22% đạm, 4mm |
| 2 | 12:00 | 120 | Cám vỗ béo |
| 3 | 17:00 | 130 | Cám vỗ béo |
| **Tổng** | | **380 g/100m²/ngày** | FCR: 1.7–2.0 |

#### Lịch Thay Nước Tuần 11–16

```
Thay 25–30% mỗi ngày (chất thải rất nhiều)
Bơm bùn K26: 60 phút/ngày
Ao lắng (K16–K19): Hoạt động liên tục khi bơm thải chạy
```

---

### TUẦN 17–20: Chuẩn Bị Thu Hoạch (800–1200g/con)

#### Điều Chỉnh Cuối Vụ

| Thời điểm | Hành động | Ghi chú |
|-----------|----------|---------|
| Tuần 17–18 | Duy trì cho ăn bình thường | Tăng trọng lượng tối đa |
| Tuần 19 | Giảm lượng ăn 20% | Chuẩn bị |
| Ngày -7 trước thu | Giảm lượng ăn 50% | Sạch đường ruột |
| Ngày -3 trước thu | Giảm lượng ăn 70% | |
| Ngày -1 trước thu | Ngừng cho ăn | Thịt đẹp, ít mỡ |
| Ngày thu hoạch | Tắt hoàn toàn máy cho ăn | |

---

## 13.3 Bảng Tổng Hợp Cài Đặt Firmware Theo Giai Đoạn

### Tôm Thẻ — Cài Đặt Tự Động Theo Ngày Nuôi

| Ngày nuôi | Duration ăn | DO cảnh báo | Thay nước | Blower |
|:---------:|:-----------:|:-----------:|:---------:|:------:|
| 1–30 | 2 giây | 5.0 mg/L | 10%/3 ngày | Khi DO < 4.0 |
| 31–60 | 3–4 giây | 4.5 mg/L | 15%/2 ngày | Khi DO < 3.5 |
| 61–100 | 4–5 giây | 4.0 mg/L | 20%/1 ngày | Khi DO < 3.0 |

### Cá Tra — Cài Đặt Tự Động Theo Tuần Nuôi

| Tuần nuôi | Duration ăn | DO cảnh báo | Thay nước | Bơm bùn |
|:---------:|:-----------:|:-----------:|:---------:|:-------:|
| 1–4 | 3–4 giây | 4.0 mg/L | 15–20%/2–3 ngày | Không |
| 5–10 | 5–6 giây | 3.5 mg/L | 20%/2 ngày | 45 ph/ngày |
| 11–16 | 7–8 giây | 3.0 mg/L | 25–30%/ngày | 60 ph/ngày |
| 17–20 | 8 giây → 0 | 3.0 mg/L | 30%/ngày | 60 ph/ngày |

---

## 13.4 MQTT Lệnh Chuyển Giai Đoạn

```bash
# Chuyển sang giai đoạn 2 tôm thẻ (ngày nuôi 31)
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/config/set" \
  -m '{
    "farming_day": 31,
    "species": "shrimp",
    "stage": 2,
    "auto_adjust": true,
    "feed_duration_sec": 4,
    "water_change_percent": 15,
    "water_change_interval_days": 2,
    "do_warn_threshold": 4.5,
    "do_alert_threshold": 3.5,
    "do_critical_threshold": 2.0
  }'

# Kiểm tra ngày nuôi và giai đoạn hiện tại
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/aqua_001/config/get" \
  -m '{}'
# Response: aqua/aqua_001/config/status
```

---

## 13.5 Ghi Chú Quan Trọng Theo Mùa

### Mùa Hè (Tháng 4–8)

```
  Rủi ro: Nhiệt độ nước > 32°C
  Biện pháp:
    □ Tăng blower từ 14:00–17:00
    □ Giảm mật độ nuôi 20%
    □ Giảm lượng ăn 15–20% trong ngày nắng nóng
    □ Thay nước lúc 6:00 sáng (nước mát hơn)
    □ Cài TEMP_HIGH_WARN = 31°C (thay vì 32°C)
```

### Mùa Mưa (Tháng 9–11)

```
  Rủi ro: Độ mặn giảm, pH biến động, DO giảm do mây che
  Biện pháp:
    □ Theo dõi EC/độ mặn hàng ngày (mưa pha loãng)
    □ Giảm thay nước trong đợt mưa kéo dài
    □ Vôi CaCO₃ bổ sung 20 kg/1000m³ sau mỗi trận mưa lớn
    □ Bật blower 24/7 trong ngày흐m âm u (ít quang hợp)
```

### Mùa Lạnh (Tháng 12–2)

```
  Rủi ro: Nhiệt độ < 25°C, tôm/cá bỏ ăn
  Biện pháp:
    □ Bật sưởi K12 (TEMP_HEAT_ON = 26°C thay vì 25°C)
    □ Giảm lượng ăn theo nhiệt độ:
       27–28°C: 80% khẩu phần bình thường
       25–27°C: 60% khẩu phần
       < 25°C:  40% khẩu phần (tôm ít di chuyển)
    □ Tăng khoảng cách giữa các ca ăn
    □ Cài MQTT:
       {"temp_feed_reduction": [
         {"temp": 27, "factor": 0.8},
         {"temp": 25, "factor": 0.6},
         {"temp": 23, "factor": 0.4}
       ]}
```
