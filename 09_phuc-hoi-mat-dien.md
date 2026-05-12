# 09 — Phục Hồi Sau Mất Điện

---

## 9.1 Vấn Đề Đặc Thù Thủy Sản

Mất điện trong ao nuôi nguy hiểm hơn rất nhiều so với nhà kính hay chuồng trại, vì:

| Tình huống | Hậu quả nếu không phục hồi đúng | Thời gian nguy hiểm |
|-----------|--------------------------------|:------------------:|
| Quạt sục khí tắt ban đêm | DO giảm về 0, tôm cá chết hàng loạt | 30–60 phút |
| Quạt tắt sáng sớm (3–6h) | DO vốn thấp nhất ngày, kết hợp mất điện = thảm họa | 15–30 phút |
| Bơm cấp tắt giữa thay nước | Mực nước thấp, tôm stress + dễ bệnh | 2–4 giờ |
| Van xả mở, bơm cấp không bật | Cạn ao → chết toàn bộ | < 1 giờ |
| UV tắt lâu ngày | Vi khuẩn phát triển, dịch bệnh | 24–48 giờ |
| Máy cho ăn bỏ ca | Tôm thiếu ăn, cạnh tranh, ăn nhau | 12–24 giờ |

**Nguyên tắc tối thượng: Quạt sục khí phải bật TRƯỚC KHI có WiFi, TRƯỚC KHI có NTP.**

---

## 9.2 Thiết Bị UPS và Máy Phát Bắt Buộc

### UPS Cho Bộ Điều Khiển

```
Bộ điều khiển NextFarm (CB2S + MCP + relay coil):
  Tiêu thụ: 5VDC × 0.6A ≈ 3W
  Khuyến nghị UPS: 500 VA (lý tưởng) hoặc 300 VA (tối thiểu)
  Thời gian dự phòng: 300 VA → ~2 giờ cho mạch điều khiển
  
  UPS cần bảo vệ:
    - Mạch 5V Mean Well (bộ điều khiển CB2S, relay coil)
    - Router WiFi (không thể kết nối MQTT nếu mất WiFi)
    → KHÔNG cần cấp điện cho quạt, bơm, UV từ UPS
```

### Máy Phát Điện — BẮT BUỘC Cho Quạt Sục Khí

```
Tải tối thiểu máy phát:
  4× Quạt sục khí 250W = 1,000 W
  2× Blower 750W        = 1,500 W
  Bơm cấp 500W          =   500 W
  Tải khởi động × 2.5   = 7,500 W (peak)

  Máy phát tối thiểu: 5 kVA (chạy liên tục)
  Khuyến nghị:        7.5 kVA (có biên an toàn)

Chuyển mạch tự động ATS (Automatic Transfer Switch):
  - ATS chuyển sang máy phát trong < 10 giây
  - CB2S + UPS duy trì trong 10 giây chờ ATS
  - Sau khi ATS chuyển xong → bật lại quạt ngay lập tức
```

---

## 9.3 Dữ Liệu Lưu Flash (Preferences)

```cpp
#include <Preferences.h>

struct AquaStateFlash {
  // === QUẠT & SỤC KHÍ — PHỤC HỒI ĐẦU TIÊN ===
  uint8_t  do_level_last;      // Cấp DO cuối (0–5)
  bool     fan_k1_on;
  bool     fan_k2_on;
  bool     fan_k3_on;
  bool     fan_k4_on;
  bool     blower_k5_on;
  bool     blower_k6_on;
  bool     o2_emergency_on;    // K29

  // === BƠM ===
  bool     pump_in_on;         // K0 bơm cấp
  bool     pump_out_on;        // K7 bơm thải
  bool     pump_circ_on;       // K8 tuần hoàn
  bool     pump_lime_on;       // K9 bơm vôi
  bool     pump_mineral_on;    // K10 bơm khoáng

  // === CHO ĂN — THEO DÕI CA BỊ LỠ ===
  uint32_t power_off_timestamp; // Unix timestamp lúc mất điện
  bool     feed_ca_done[6];     // 6 ca/ngày đã chạy chưa
  uint8_t  feed_skip_count;     // Số ca bị bỏ hôm nay

  // === THAY NƯỚC ===
  bool     water_change_in_progress; // Đang thay nước lúc mất điện?
  float    water_target_level;       // Mực nước đích
  uint8_t  water_change_stage;       // 0=chờ, 1=xả, 2=cấp

  // === NGƯỠNG HIỆN TẠI ===
  float    do_l1, do_l2, do_l3, do_l4, do_l5;
  float    ph_low_lime, ph_target;
  float    ec_min, ec_max;

  // === NGÀY NUÔI ===
  uint16_t farming_day;        // Ngày nuôi hiện tại
  uint8_t  species;            // 0=tôm thẻ, 1=cá tra, 2=cá rô phi

  uint32_t crc32;              // Kiểm tra tính toàn vẹn dữ liệu
};

void saveStateToFlash() {
  AquaStateFlash state;
  // ... điền dữ liệu
  state.crc32 = calcCRC32(&state, sizeof(state) - 4);
  prefs.putBytes("aqua_state", &state, sizeof(state));
}

bool loadStateFromFlash(AquaStateFlash* state) {
  size_t len = prefs.getBytes("aqua_state", state, sizeof(AquaStateFlash));
  if (len != sizeof(AquaStateFlash)) return false;
  uint32_t crc = calcCRC32(state, sizeof(AquaStateFlash) - 4);
  return (crc == state->crc32);
}
```

---

## 9.4 Luồng Phục Hồi Khi Khởi Động

```
CB2S Khởi động
      │
      ▼
Khởi tạo MCP23017 (đặt tất cả relay về OFF an toàn)
      │
      ▼
Đọc trạng thái từ flash — kiểm tra CRC
      │
      ├── CRC LỖI → dùng giá trị mặc định an toàn
      │              (bật 2 quạt mặc định, đợi đọc DO)
      │
      ▼ CRC OK
══════════════════════════════════════
  ƯU TIÊN 1: BẬT NGAY QUẠT SỤC KHÍ
  (KHÔNG CHỜ WIFI, KHÔNG CHỜ NTP)
  Phục hồi do_level từ flash
  → Bật quạt theo cấp DO đã lưu
  → Nếu do_level_last <= 2 → bật TẤT CẢ quạt (an toàn nhất)
══════════════════════════════════════
      │
      ▼ (song song với kết nối WiFi)
Đọc EZO-DO ngay lập tức (30 giây đầu tiên)
  → Cập nhật cấp DO thực tế
  → Điều chỉnh quạt theo đo lường thực
      │
      ▼
Kết nối WiFi → MQTT (broker.hosco.com.vn)
      │
      ├── MQTT OK → Gửi "aqua/{id}/status/boot"
      │             {"reason": "power_restored", "do_level": N}
      │
      ▼
Đồng bộ NTP (chờ tối đa 10 giây)
      │
      ├── NTP OK → Tính toán ca cho ăn bị lỡ
      │
      ├── NTP FAIL → Sử dụng RTC onboard (nếu có)
      │              hoặc bỏ qua logic cho ăn hôm nay
      │
      ▼
Phục hồi các hệ thống còn lại (ưu tiên 2–4)
```

---

## 9.5 Thứ Tự Phục Hồi Ưu Tiên

### Ưu Tiên 1 — Ngay Khi Khởi Động (Không Chờ WiFi)

```
Thời gian: 0–2 giây sau khởi động
  ✓ Bật quạt sục khí theo cấp DO đã lưu
  ✓ Nếu do_level ≤ 2: bật TẤT CẢ quạt + blower
  ✓ Nếu o2_emergency_on = true: bật K29 ngay
  ✓ Hiển thị OLED: "PHUC HOI - DO:??"
```

### Ưu Tiên 2 — Sau Khi Đọc DO Thực Tế (~30 giây)

```
  ✓ Điều chỉnh quạt theo DO đo được thực tế
  ✓ Bật bơm tuần hoàn K8 (an toàn để bật)
  ✓ Hiển thị DO thực trên OLED
```

### Ưu Tiên 3 — Sau Khi Có WiFi + NTP (~60 giây)

```
  ✓ Kiểm tra ca cho ăn bị lỡ (xem mục 9.6)
  ✓ Kiểm tra thay nước bị dở (xem mục 9.7)
  ✓ Bật UV K11 theo lịch (an toàn)
  ✓ Gửi báo cáo khởi động MQTT
```

### Ưu Tiên 4 — Vận Hành Bình Thường (~120 giây)

```
  ✓ Toàn bộ logic điều khiển hoạt động
  ✓ Lịch cho ăn tiếp theo
  ✓ Theo dõi pH, EC, ORP
  ✓ Lịch thay nước theo kế hoạch
```

---

## 9.6 Xử Lý Ca Cho Ăn Bị Lỡ

```cpp
void checkMissedFeedCas() {
  if (!ntp_synced) return;  // Cần biết giờ thực

  AquaStateFlash state;
  if (!loadStateFromFlash(&state)) return;

  uint32_t now = getUnixTimestamp();
  uint32_t power_off = state.power_off_timestamp;
  uint32_t power_off_duration = now - power_off;

  // Không bù ca nếu mất điện > 4 giờ
  // (tôm/cá đã đói lâu, cho ăn đột ngột dễ gây sốc)
  if (power_off_duration > 4 * 3600) {
    char buf[256];
    snprintf(buf, sizeof(buf),
      "{\"event\":\"long_power_off\",\"duration_min\":%d,"
      "\"action\":\"skip_all_missed_feeds\","
      "\"reason\":\"mat_dien_qua_lau_khong_bu_an\"}",
      power_off_duration / 60);
    mqtt.publish("aqua/" FARM_ID "/feeding/missed", buf);
    return;
  }

  // Kiểm tra từng ca bị lỡ
  int current_hour = getHour();
  int current_min  = getMinute();

  for (int i = 0; i < 6; i++) {  // Tôm: 6 ca
    FeedSchedule* sched = &shrimp_schedule[i];
    bool should_have_run = (
      sched->hour < current_hour ||
      (sched->hour == current_hour && sched->minute <= current_min)
    );

    if (should_have_run && !state.feed_ca_done[i]) {
      // Ca này đã qua mà chưa chạy → bị lỡ do mất điện
      float current_do = getLatestDO();

      if (current_do >= sched->do_min_feed) {
        // DO đủ → cho ăn bù (giảm 30% vì bù trễ)
        uint16_t makeup_duration = sched->duration_sec * 0.7;
        runFeeders(sched->feeders_mask, makeup_duration);

        char buf[256];
        snprintf(buf, sizeof(buf),
          "{\"event\":\"makeup_feed\",\"ca\":%d,"
          "\"duration\":%d,\"do\":%.2f}",
          i, makeup_duration, current_do);
        mqtt.publish("aqua/" FARM_ID "/feeding/makeup", buf);
      } else {
        // DO thấp → bỏ qua ca bù
        char buf[256];
        snprintf(buf, sizeof(buf),
          "{\"event\":\"skip_makeup_do_low\",\"ca\":%d,"
          "\"do\":%.2f}",
          i, current_do);
        mqtt.publish("aqua/" FARM_ID "/feeding/missed", buf);
      }
    }
  }
}
```

---

## 9.7 Xử Lý Thay Nước Bị Dở

```cpp
void checkInterruptedWaterChange() {
  AquaStateFlash state;
  if (!loadStateFromFlash(&state)) return;

  if (!state.water_change_in_progress) return;

  // Đang thay nước lúc mất điện → xác định giai đoạn
  float current_level = readVL53L0X_level();

  switch (state.water_change_stage) {
    case 1:  // Đang xả nước
      if (current_level > state.water_target_level) {
        // Chưa xả xong → tiếp tục xả
        publishAlert("THAYNUOC_TIEP_TUC_XA",
                     "Tiếp tục xả nước bị gián đoạn");
        setRelay(K24, ON); setRelay(K25, ON);
        // Hàm chờ và theo dõi...
      } else {
        // Đã xả đủ → chuyển sang cấp nước
        setRelay(K0, ON);
        state.water_change_stage = 2;
        saveStateToFlash();
      }
      break;

    case 2:  // Đang cấp nước
      if (current_level < state.water_target_level + 0.05) {
        // Chưa cấp xong → tiếp tục cấp
        publishAlert("THAYNUOC_TIEP_TUC_CAP",
                     "Tiếp tục cấp nước bị gián đoạn");
        setRelay(K0, ON);
      } else {
        // Xong
        setRelay(K0, OFF);
        state.water_change_in_progress = false;
        saveStateToFlash();
        publishAlert("THAYNUOC_HOAN_THANH",
                     "Thay nước hoàn tất sau gián đoạn");
      }
      break;
  }
}
```

---

## 9.8 Firmware: Lưu Trạng Thái Liên Tục

```cpp
// Gọi mỗi khi thay đổi relay quan trọng
void onRelayChange(uint8_t k, bool state_on) {
  setRelay(k, state_on);
  saveStateToFlash();  // Lưu ngay sau mỗi thay đổi
}

// Lưu timestamp "còn sống" mỗi 30 giây
void heartbeatSave() {
  static uint32_t last_save = 0;
  if (millis() - last_save >= 30000) {
    AquaStateFlash s;
    loadStateFromFlash(&s);
    s.power_off_timestamp = getUnixTimestamp(); // "còn sống đến thời điểm này"
    saveStateToFlash();
    last_save = millis();
  }
}
```

---

## 9.9 Hướng Dẫn Cài Đặt ATS + Máy Phát

### Sơ Đồ Đấu Nối ATS

```
Lưới điện 220VAC ──┐
                    ├──[ATS 40A]──── Tủ phân phối ao
Máy phát ──────────┘
                    │
              Tín hiệu ATS OK
                    │
                  (GPIO dự phòng CB2S)
                  → Firmware biết đang chạy máy phát
                  → Giảm tải không cần thiết

ATS chuyển mạch:
  - Lưới → Máy phát: < 10 giây (UPS bù)
  - Máy phát → Lưới: ngay khi lưới ổn định > 30 giây
```

### Checklist Máy Phát

```
□ Xăng/dầu: Kiểm tra hàng tuần, đủ ≥ 8 giờ vận hành
□ Acquy đề máy: Sạc đủ, thay mỗi 2 năm
□ Bảo dưỡng: Thay dầu mỗi 100 giờ hoạt động
□ Chạy thử: 15 phút/tuần, tải thực tế
□ ATS test: Mô phỏng mất điện mỗi tháng, đo thời gian chuyển
□ CB bảo vệ ngược dòng: Kiểm tra không điện ngược lên lưới
```

---

## 9.10 Checklist Phục Hồi Sau Sự Cố

```
Sau khi điện vào lại hoặc máy phát bật:

□ Kiểm tra OLED: Hiển thị DO thực tế, quạt đang chạy?
□ MQTT: Nhận được status/boot từ CB2S?
□ DO: > 4.0 mg/L? (nếu không → quạt đang chạy đúng không?)
□ Mực nước: Bình thường? (Không xả hết trong khi mất điện?)
□ Van xả: Đã đóng? (K24, K25 = OFF)
□ Ca cho ăn: App báo ca nào bị lỡ?
□ pH: Còn trong ngưỡng? (tảo chết do thiếu sục khí = pH giảm)
□ Ghi nhật ký: Thời gian mất điện, DO thấp nhất, thiệt hại
```
