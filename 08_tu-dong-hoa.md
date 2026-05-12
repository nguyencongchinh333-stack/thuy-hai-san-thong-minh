# 08 — Tự Động Hóa & Logic Điều Khiển Chi Tiết

---

## 8.1 DO — Điều Khiển Oxy Hòa Tan 6 Cấp

### Bảng Ngưỡng Mặc Định

| Cấp | DO (mg/L) | K1 | K2 | K3 | K4 | K5 | K6 | K29 | Cảnh báo |
|:---:|:---------:|:--:|:--:|:--:|:--:|:--:|:--:|:---:|----------|
| 5 | > 6.0 | OFF | OFF | OFF | OFF | OFF | OFF | OFF | Bình thường |
| 4 | 5.0–6.0 | ON  | OFF | OFF | OFF | OFF | OFF | OFF | Theo dõi |
| 3 | 4.0–5.0 | ON  | ON  | OFF | OFF | ON  | OFF | OFF | INFO |
| 2 | 3.0–4.0 | ON  | ON  | ON  | ON  | ON  | ON  | OFF | WARNING |
| 1 | 2.0–3.0 | ON  | ON  | ON  | ON  | ON  | ON  | OFF | CRITICAL |
| 0 | < 2.0   | ON  | ON  | ON  | ON  | ON  | ON  | ON  | EMERGENCY |

### Code Firmware Logic DO 6 Cấp

```cpp
// Ngưỡng DO (mg/L) — cài được qua MQTT
float DO_L5 = 6.0, DO_L4 = 5.0, DO_L3 = 4.0;
float DO_L2 = 3.0, DO_L1 = 2.0;
float DO_HYST = 0.2;   // Hysteresis chống dao động

uint8_t do_level_prev = 5;

void controlDO_6level(float do_val) {
  uint8_t new_level;

  // Xác định cấp mới (với hysteresis)
  if      (do_val > DO_L5 + DO_HYST) new_level = 5;
  else if (do_val > DO_L4 + DO_HYST) new_level = 4;
  else if (do_val > DO_L3 + DO_HYST) new_level = 3;
  else if (do_val > DO_L2 + DO_HYST) new_level = 2;
  else if (do_val > DO_L1 + DO_HYST) new_level = 1;
  else                                new_level = 0;

  if (new_level == do_level_prev) return; // Không thay đổi

  do_level_prev = new_level;
  applyDOLevel(new_level);
  publishDOAlert(new_level, do_val);
}

void applyDOLevel(uint8_t level) {
  // K1=bit0, K2=bit1, K3=bit2, K4=bit3, K5=bit4, K6=bit5, K29=bit6
  const uint8_t relay_mask[6] = {
    0b1111111,  // Cấp 0: K1-K4 + K5-K6 + K29
    0b0111111,  // Cấp 1: K1-K4 + K5-K6
    0b0111111,  // Cấp 2: K1-K4 + K5-K6
    0b0010011,  // Cấp 3: K1+K2 + K5
    0b0000001,  // Cấp 4: K1 only
    0b0000000,  // Cấp 5: tắt hết
  };
  uint8_t mask = relay_mask[level];
  setRelay(K1,  (mask >> 0) & 1);
  setRelay(K2,  (mask >> 1) & 1);
  setRelay(K3,  (mask >> 2) & 1);
  setRelay(K4,  (mask >> 3) & 1);
  setRelay(K5,  (mask >> 4) & 1);
  setRelay(K6,  (mask >> 5) & 1);
  setRelay(K29, (mask >> 6) & 1);
}

void publishDOAlert(uint8_t level, float do_val) {
  char buf[128];
  const char* levels[] = {"KHAN_CAP","NGUY_HIEM","CANH_BAO","THEO_DOI","ON","TOT"};
  snprintf(buf, sizeof(buf),
    "{\"level\":%d,\"do\":%.2f,\"status\":\"%s\"}",
    level, do_val, levels[level]);
  if (level <= 1)
    mqtt.publish("aqua/" FARM_ID "/alert/critical", buf);
  else if (level == 2)
    mqtt.publish("aqua/" FARM_ID "/alert/warning", buf);
  else if (level == 3)
    mqtt.publish("aqua/" FARM_ID "/alert/info", buf);
  else
    mqtt.publish("aqua/" FARM_ID "/status/do", buf);
}
```

### DO Sáng Sớm (Dawn DO Monitoring)

```
Hiện tượng: DO thường thấp nhất lúc 4:00–6:00 sáng
  (tảo/vi khuẩn hô hấp ban đêm, quang hợp chưa bắt đầu)

Logic đặc biệt:
  - Từ 03:00–06:00: Ngưỡng cảnh báo tăng thêm 0.5 mg/L
  - Ví dụ: Cấp 2 bình thường DO < 4.0 → buổi sáng DO < 4.5
  - Blower K5+K6 bật trước (mạnh hơn quạt sục) từ 03:00
  - MQTT gửi thêm topic: aqua/{id}/alert/dawn_do_low
```

---

## 8.2 pH — Điều Khiển Bơm Vôi Tự Động

### Logic Điều Khiển pH

```cpp
// Ngưỡng pH — cài được qua MQTT
float PH_LOW_WARN  = 7.0;   // Cảnh báo pH thấp
float PH_LOW_LIME  = 6.8;   // Bắt đầu bơm vôi
float PH_TARGET    = 7.5;   // Mục tiêu
float PH_HIGH_WARN = 8.5;   // Cảnh báo pH cao
float PH_HIGH_CRIT = 9.0;   // Nguy hiểm pH quá cao

uint32_t lime_pump_total_today = 0;  // Tổng giây bơm hôm nay
uint32_t lime_pump_max_day = 1200;   // Tối đa 20 phút/ngày
uint32_t lime_last_pump_time = 0;    // Lần cuối bơm

void controlPH_lime(float ph_val) {
  uint32_t now = millis();

  if (ph_val < PH_LOW_LIME) {
    // Kiểm tra giới hạn an toàn
    if (lime_pump_total_today >= lime_pump_max_day) {
      publishAlert("PH_VUOT_GIOI_HAN_BOM",
                   "Bơm vôi đã đạt giới hạn ngày");
      return;
    }
    if (now - lime_last_pump_time < 1800000) { // 30 phút
      return; // Chờ đủ 30 phút
    }

    // Tính thời gian bơm theo độ lệch pH
    float delta = PH_TARGET - ph_val;
    uint32_t pump_sec = (uint32_t)(delta / 0.1 * 30);
    pump_sec = min(pump_sec, (uint32_t)300); // Tối đa 5 phút/lần

    setRelay(K9, ON);
    delay(pump_sec * 1000);
    setRelay(K9, OFF);

    lime_pump_total_today += pump_sec;
    lime_last_pump_time = now;

    char buf[128];
    snprintf(buf, sizeof(buf),
      "{\"action\":\"bom_voi\",\"ph\":%.2f,\"sec\":%d,\"total_today\":%d}",
      ph_val, pump_sec, lime_pump_total_today);
    mqtt.publish("aqua/" FARM_ID "/alert/warning", buf);

  } else if (ph_val > PH_HIGH_WARN) {
    setRelay(K9, OFF);
    if (ph_val > PH_HIGH_CRIT)
      publishAlert("PH_NGUY_HIEM_CAO", "pH vượt ngưỡng 9.0");
    else
      publishAlert("PH_CAO", "pH cao > 8.5, kiểm tra tảo");
  } else {
    setRelay(K9, OFF);  // pH bình thường
  }
}
```

### Bảng Ngưỡng pH Theo Đối Tượng Nuôi

| Thông số | Tôm thẻ chân trắng | Cá tra | Cá rô phi |
|----------|:-----------------:|:------:|:---------:|
| Tối ưu | 7.5–8.5 | 6.5–8.0 | 7.0–8.5 |
| Bơm vôi khi < | 7.3 | 6.3 | 7.0 |
| Cảnh báo cao > | 8.8 | 8.5 | 8.8 |
| Nguy hiểm > | 9.2 | 9.0 | 9.2 |

---

## 8.3 EC/Độ Mặn — Cảnh Báo và Theo Dõi

```cpp
// Cài đặt ngưỡng EC (mS/cm) — Tôm thẻ
float EC_MIN = 10.0;   // Độ mặn tối thiểu (≈ 5‰)
float EC_MAX = 35.0;   // Độ mặn tối đa (≈ 18‰)
float EC_OPT_LOW  = 20.0;  // Tối ưu thấp (≈ 10‰)
float EC_OPT_HIGH = 30.0;  // Tối ưu cao (≈ 15‰)

void controlEC_salinity(float ec_val) {
  // Chuyển EC → ‰ (gần đúng): ‰ ≈ EC_mS/cm × 0.5
  float salinity_ppt = ec_val * 0.5;

  if (ec_val < EC_MIN) {
    char buf[128];
    snprintf(buf, sizeof(buf),
      "{\"ec\":%.2f,\"salinity\":%.1f,\"status\":\"QUAT_THAP\"}",
      ec_val, salinity_ppt);
    mqtt.publish("aqua/" FARM_ID "/alert/warning", buf);
    // Thêm khoáng K10 (bơm khoáng) nếu được cấu hình auto
    if (mineral_auto_mode) setRelay(K10, ON);

  } else if (ec_val > EC_MAX) {
    char buf[128];
    snprintf(buf, sizeof(buf),
      "{\"ec\":%.2f,\"salinity\":%.1f,\"status\":\"QUA_MAN\"}",
      ec_val, salinity_ppt);
    mqtt.publish("aqua/" FARM_ID "/alert/critical", buf);
    // Cần pha loãng — cấp thêm nước ngọt
    if (dilute_auto_mode) setRelay(K0, ON);

  } else {
    setRelay(K10, OFF);  // Tắt bơm khoáng
    // Gửi giá trị bình thường
    publishSensorValue("ec", ec_val);
  }
}
```

---

## 8.4 ORP — Cảnh Báo Chất Lượng Nước

```cpp
// Ngưỡng ORP (mV)
int ORP_MIN = 150;   // Thấp: thiếu oxy, vi khuẩn kỵ khí
int ORP_OPT = 300;   // Tối ưu cho tôm cá khỏe mạnh
int ORP_MAX = 450;   // Cao: dư oxy, có thể do hóa chất

void controlORP(int orp_val) {
  if (orp_val < ORP_MIN) {
    publishAlert("ORP_THAP",
      "ORP thấp < 150mV: thiếu oxy, nguy cơ vi khuẩn");
    // Tăng sục khí (bật thêm quạt nếu chưa ở cấp cao nhất)
    if (do_level_prev > 2) forceBlowerOn();

  } else if (orp_val > ORP_MAX) {
    publishAlert("ORP_CAO",
      "ORP cao > 450mV: kiểm tra hóa chất xử lý nước");

  } else if (orp_val < 200) {
    publishAlert("ORP_THEO_DOI",
      "ORP theo dõi 150–200mV");
  }
  publishSensorValue("orp", orp_val);
}
```

---

## 8.5 Lịch Cho Ăn Tự Động

### Cấu Trúc Lịch Cho Ăn

```cpp
struct FeedSchedule {
  uint8_t  hour;          // Giờ (0–23)
  uint8_t  minute;        // Phút
  uint8_t  feeders_mask;  // Bit: K20=bit0, K21=bit1, K22=bit2, K23=bit3
  uint16_t duration_sec;  // Thời gian mỗi máy chạy (giây)
  bool     check_do;      // true = kiểm tra DO trước khi cho ăn
  float    do_min_feed;   // DO tối thiểu để cho ăn
  float    do_half_feed;  // DO cho ăn nửa khẩu phần
};

// Lịch cho ăn TÔM THẺ (6 lần/ngày)
FeedSchedule shrimp_schedule[] = {
  {06, 00, 0b1111, 5, true,  4.0, 3.0},  // Ca 1: Sáng sớm
  {09, 00, 0b1111, 5, true,  4.0, 3.0},  // Ca 2: Buổi sáng
  {12, 00, 0b1111, 5, true,  4.0, 3.0},  // Ca 3: Trưa
  {15, 00, 0b1111, 5, true,  4.0, 3.0},  // Ca 4: Chiều
  {18, 00, 0b1111, 5, true,  4.0, 3.0},  // Ca 5: Chiều tối
  {21, 00, 0b1111, 5, false, 0.0, 0.0},  // Ca 6: Tối (ít quan trọng hơn)
};

// Lịch cho ăn CÁ TRA (3 lần/ngày)
FeedSchedule fish_schedule[] = {
  {07, 00, 0b1111, 8, true,  3.5, 2.5},  // Ca 1: Sáng
  {12, 00, 0b1111, 8, true,  3.5, 2.5},  // Ca 2: Trưa
  {17, 00, 0b1111, 8, true,  3.5, 2.5},  // Ca 3: Chiều
};
```

### Logic Kiểm Tra Trước Khi Cho Ăn

```cpp
void executeFeedCa(FeedSchedule* sched) {
  // Kiểm tra DO nếu yêu cầu
  if (sched->check_do) {
    float current_do = getLatestDO();

    if (current_do < sched->do_min_feed) {
      // BỎ CA CHO ĂN — DO quá thấp
      char buf[256];
      snprintf(buf, sizeof(buf),
        "{\"action\":\"skip_feed\",\"reason\":\"do_low\","
        "\"do\":%.2f,\"threshold\":%.2f,\"ca\":%d}",
        current_do, sched->do_min_feed, current_ca);
      mqtt.publish("aqua/" FARM_ID "/feeding/skip", buf);
      logSkippedFeed(current_ca);
      return;
    }

    uint16_t actual_duration = sched->duration_sec;
    if (current_do < sched->do_half_feed) {
      // GIẢM 50% KHẨU PHẦN
      actual_duration /= 2;
      publishAlert("CHOUAN_GIAM_KHAU_PHAN",
                   "DO thấp, giảm 50% lượng ăn");
    }

    sched->duration_sec = actual_duration;
  }

  // Chạy máy cho ăn theo mask
  runFeeders(sched->feeders_mask, sched->duration_sec);

  // Ghi log MQTT
  char buf[256];
  snprintf(buf, sizeof(buf),
    "{\"action\":\"fed\",\"ca\":%d,\"feeders\":%d,"
    "\"duration\":%d,\"do\":%.2f}",
    current_ca, sched->feeders_mask,
    sched->duration_sec, getLatestDO());
  mqtt.publish("aqua/" FARM_ID "/feeding/log", buf);
}

void runFeeders(uint8_t mask, uint16_t duration_sec) {
  if (mask & 0b0001) setRelay(K20, ON);
  if (mask & 0b0010) setRelay(K21, ON);
  if (mask & 0b0100) setRelay(K22, ON);
  if (mask & 0b1000) setRelay(K23, ON);

  delay(duration_sec * 1000);

  setRelay(K20, OFF); setRelay(K21, OFF);
  setRelay(K22, OFF); setRelay(K23, OFF);
}
```

---

## 8.6 Thay Nước Tự Động

### Lịch Thay Nước Theo Giai Đoạn Nuôi

```cpp
struct WaterChangeSchedule {
  uint8_t  day_from;      // Ngày nuôi bắt đầu
  uint8_t  day_to;        // Ngày nuôi kết thúc
  uint8_t  interval_days; // Chu kỳ thay nước (ngày)
  uint8_t  percent;       // Tỉ lệ thay (%)
  uint8_t  hour;          // Giờ thực hiện
};

WaterChangeSchedule water_schedule[] = {
  {  1,  30,  3, 10, 8},  // Giai đoạn 1: 10% mỗi 3 ngày
  { 31,  60,  2, 15, 7},  // Giai đoạn 2: 15% mỗi 2 ngày
  { 61, 100,  1, 20, 6},  // Giai đoạn 3: 20% mỗi ngày
};
```

### Logic Thực Hiện Thay Nước

```cpp
void executeWaterChange(uint8_t percent) {
  float target_level = current_level * (1.0 - percent / 100.0);
  float original_level = current_level;

  // 1. Bật tuần hoàn trước khi xả
  setRelay(K8, ON);
  delay(5 * 60 * 1000); // 5 phút

  // 2. Mở van xả đáy
  setRelay(K24, ON);
  setRelay(K25, ON);

  // 3. Xả đến mực đích (VL53L0X giám sát)
  uint32_t drain_start = millis();
  while (true) {
    float level = readVL53L0X_level();

    // Kiểm tra DO trong khi xả
    if (getLatestDO() < 4.0) {
      setRelay(K24, OFF); setRelay(K25, OFF);
      publishAlert("THAYNUOC_DUNG_DO_THAP",
                   "Dừng xả do DO giảm thấp");
      return;
    }
    if (level <= target_level) break;
    if (millis() - drain_start > 60 * 60 * 1000) {
      setRelay(K24, OFF); setRelay(K25, OFF);
      publishAlert("THAYNUOC_QUA_THOI_GIAN", "Xả quá 60 phút");
      return;
    }
    delay(5000);
  }

  // 4. Đóng van xả, mở bơm cấp
  setRelay(K24, OFF); setRelay(K25, OFF);
  setRelay(K0, ON);

  // 5. Cấp đến mực ban đầu
  while (readVL53L0X_level() < original_level) {
    delay(5000);
  }
  setRelay(K0, OFF);
  setRelay(K8, OFF);

  // 6. Gửi báo cáo
  char buf[256];
  snprintf(buf, sizeof(buf),
    "{\"action\":\"water_change_done\",\"percent\":%d,"
    "\"ph\":%.2f,\"ec\":%.2f}",
    percent, getLatestPH(), getLatestEC());
  mqtt.publish("aqua/" FARM_ID "/water/report", buf);
}
```

---

## 8.7 UV — Diệt Khuẩn Theo Lịch

```cpp
// Lịch UV (K11) — chạy khi có bơm cấp nước
// Bật K11 cùng lúc K0 (bơm cấp), tắt sau khi bơm xong + 10 phút

void controlUV() {
  static bool uv_override = false;

  if (getRelayState(K0) == ON) {
    setRelay(K11, ON);   // UV bật khi có nước chạy qua
    uv_last_on = millis();
  } else if (uv_override || millis() - uv_last_on < 10 * 60 * 1000) {
    setRelay(K11, ON);   // Giữ thêm 10 phút sau khi bơm tắt
  } else {
    setRelay(K11, OFF);
  }

  // UV cũng chạy lịch cố định: 6:00–7:00 và 18:00–19:00
  int h = getHour(), m = getMinute();
  if ((h == 6) || (h == 18)) setRelay(K11, ON);
  if ((h == 7 && m == 0) || (h == 19 && m == 0)) setRelay(K11, OFF);
}
```

---

## 8.8 Sưởi Nước (K12)

```cpp
// Nhiệt độ tối ưu tôm thẻ: 26–30°C
// Nhiệt độ tối ưu cá tra: 26–30°C
float TEMP_HEAT_ON  = 25.0;  // Bật sưởi khi < 25°C
float TEMP_HEAT_OFF = 27.0;  // Tắt sưởi khi > 27°C

void controlHeater(float temp) {
  if (temp < TEMP_HEAT_ON && !heater_on) {
    setRelay(K12, ON);
    heater_on = true;
    publishAlert("SUOI_BAT", "Nhiệt độ thấp, bật sưởi nước");
  } else if (temp > TEMP_HEAT_OFF && heater_on) {
    setRelay(K12, OFF);
    heater_on = false;
    publishAlert("SUOI_TAT", "Nhiệt độ đủ ấm, tắt sưởi");
  }

  // Cảnh báo nhiệt độ cao
  if (temp > 32.0) {
    publishAlert("NHIET_DO_CAO",
                 "Nhiệt độ > 32°C: giảm cho ăn, tăng sục khí");
    setRelay(K5, ON); setRelay(K6, ON); // Bật blower thêm
  }
}
```

---

## 8.9 Bơm Ao Lắng — Van K16–K19

```cpp
// Ao lắng: dùng để lọc bùn trước khi thải
// K16–K19: van điều hướng dòng chảy

void controlSedimentPond() {
  // Chế độ lọc: Mở K16 (vào ao lắng), đóng K17 (bypass)
  // Chế độ bypass: Đóng K16, Mở K17

  if (turbidity_ntu > TURBIDITY_THRESHOLD) {
    // Nước đục → qua ao lắng
    setRelay(K16, ON);   // Mở van vào ao lắng
    setRelay(K17, OFF);  // Đóng bypass
    setRelay(K18, ON);   // Mở van ra từ ao lắng
    setRelay(K19, OFF);  // Đóng van thải trực tiếp
    publishAlert("TURBIDITY_CAO", "Chuyển qua ao lắng");
  } else {
    // Nước trong → bypass
    setRelay(K16, OFF);
    setRelay(K17, ON);
    setRelay(K18, OFF);
    setRelay(K19, ON);
  }
}
```

---

## 8.10 Cài Đặt Ngưỡng Qua MQTT

### Định Dạng Lệnh MQTT

```
Topic: aqua/{farm_id}/threshold/set
Payload JSON:
{
  "do_l5": 6.0,
  "do_l4": 5.0,
  "do_l3": 4.0,
  "do_l2": 3.0,
  "do_l1": 2.0,
  "do_hyst": 0.2,
  "ph_low_lime": 6.8,
  "ph_target": 7.5,
  "ph_high_warn": 8.5,
  "ec_min": 10.0,
  "ec_max": 35.0,
  "orp_min": 150,
  "temp_heat_on": 25.0,
  "temp_heat_off": 27.0,
  "turbidity_threshold": 50
}
```

```cpp
void onMQTTThreshold(const char* payload) {
  DynamicJsonDocument doc(512);
  deserializeJson(doc, payload);

  if (doc.containsKey("do_l5"))        DO_L5 = doc["do_l5"];
  if (doc.containsKey("do_l4"))        DO_L4 = doc["do_l4"];
  if (doc.containsKey("ph_low_lime"))  PH_LOW_LIME = doc["ph_low_lime"];
  if (doc.containsKey("ph_target"))    PH_TARGET   = doc["ph_target"];
  // ... (tương tự cho các ngưỡng khác)

  saveThresholdsToFlash();  // Lưu ngay vào flash
  publishThresholdAck();    // Xác nhận đã nhận
}
```

### Điều Khiển Thủ Công Qua MQTT

```
Topic: aqua/{farm_id}/relay/{k}/set
Payload: {"state": 1}   → Bật
Payload: {"state": 0}   → Tắt
Payload: {"state": 1, "duration": 60}  → Bật 60 giây rồi tự tắt

Topic: aqua/{farm_id}/mode/set
Payload: {"mode": "AUTO"}     → Tự động
Payload: {"mode": "MANUAL"}   → Thủ công (tắt auto control)
Payload: {"mode": "EMERGENCY"}→ Khẩn cấp: bật tất cả quạt

Topic: aqua/{farm_id}/feed/trigger
Payload: {"ca": 2}  → Cho ăn ngay ca 2 (bất kể lịch)
```

---

## 8.11 Cảnh Báo 3 Mức — Bảng Tổng Hợp

| Mức | Condition | Topic MQTT | OLED | Hành động tự động |
|:---:|-----------|-----------|------|-------------------|
| INFO | DO 4.5–5.0 | `.../alert/info` | Nhấp nháy nhẹ | Ghi log |
| INFO | pH 7.0–7.3 | `.../alert/info` | Nhấp nháy nhẹ | Ghi log |
| INFO | EC ngoài tối ưu | `.../alert/info` | Hiển thị | Ghi log |
| WARNING | DO 3.0–4.0 | `.../alert/warning` | Đảo ngược | Push notification |
| WARNING | pH < 6.8 | `.../alert/warning` | Đảo ngược | Bơm vôi K9 |
| WARNING | Turbidity > 50 NTU | `.../alert/warning` | Đảo ngược | Ao lắng |
| WARNING | Temp > 32°C | `.../alert/warning` | Đảo ngược | Tăng blower |
| CRITICAL | DO < 2.0 | `.../alert/critical` | Nhấp nháy nhanh | TẤT CẢ quạt + K29 |
| CRITICAL | pH < 6.5 | `.../alert/critical` | Nhấp nháy nhanh | Bơm vôi + notification |
| CRITICAL | Mực nước < 50% | `.../alert/critical` | Nhấp nháy nhanh | Dừng xả, bơm cấp |
| CRITICAL | ORP < 100 mV | `.../alert/critical` | Nhấp nháy nhanh | Tăng sục khí |
