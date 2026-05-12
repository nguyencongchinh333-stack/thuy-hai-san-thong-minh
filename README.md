# NextFarm DIY Controller — Thủy Hải Sản Thông Minh v1.0
### Hệ Thống Điều Khiển Ao Nuôi Tự Động — Tích Hợp NextFarm Platform

---

## Dự Án Này Là Gì

Bộ điều khiển phần cứng **tự lắp (DIY)** cho ao nuôi thủy sản sử dụng CB2S (BK7231N) + 2× MCP23017, điều khiển **31 relay** cho toàn bộ hệ thống: quạt sục khí, máy thổi khí, bơm cấp/thải, bơm hóa chất, UV, máy cho ăn, van O₂ dự phòng — kết nối **NextFarm Platform** qua MQTT tại `broker.hosco.com.vn`.

---

## Cảnh Báo Quan Trọng Nhất

```
DO (Oxy hòa tan) < 3.0 mg/L → Tôm/Cá chết trong 1–3 giờ
DO < 2.0 mg/L    → MẤT TRẮNG TOÀN BỘ AO

Hệ thống ưu tiên tuyệt đối: Bật quạt sục khí NGAY khi boot
(trước cả WiFi và MQTT) nếu phát hiện DO thấp
```

---

## Tóm Tắt Hệ Thống

| Thông số | Giá trị |
|----------|---------|
| Vi điều khiển | CB2S (BK7231N) — Tuya, LibreTuya |
| MQTT Broker | broker.hosco.com.vn |
| Relay channels | 31 kênh (32 vật lý) |
| Cảm biến | DO, pH, EC/Độ mặn, ORP, Nhiệt độ ×3, Turbidity, Mực nước, Lưu lượng |
| Tín hiệu cảm biến | ≤ 3.3V — đấu thẳng GPIO CB2S, không qua bộ trung gian |
| Nguồn cảm biến | 3.3V từ AMS1117-3.3 (lấy từ Mean Well 5V) |
| Phù hợp | Ao tôm thẻ / Cá tra / Cá rô phi — 500–3.000m² |

---

## Thiết Bị Điều Khiển (31 Kênh)

| Nhóm | Thiết Bị | Kênh | Điện Áp |
|------|---------|:----:|---------|
| Sục khí | Quạt cánh quạt ×4 + Blower ×2 | 6 | 220VAC |
| Bơm | Cấp nước + Thải + Tuần hoàn | 3 | 220VAC |
| Hóa chất | Bơm vôi + Bơm khoáng | 2 | 220VAC |
| Khử trùng | Đèn UV + Bộ sưởi nước | 2 | 220VAC |
| Chiếu sáng | Đèn ao ban đêm | 2 | 220VAC |
| Van | Van ao lắng ×4 | 4 | 24VAC |
| Cho ăn | Máy cho ăn ×4 | 4 | 220VAC |
| Xả | Van xả đáy ×2 + Bơm bùn + Van thải | 4 | 220VAC |
| Khẩn cấp | Bơm dự phòng + Van O₂ bình | 2 | 220VAC |
| Dự phòng | — | 2 | — |

---

## Mục Lục

| # | File | Nội Dung |
|---|------|---------|
| 01 | [Tổng Quan](01_tong-quan.md) | Phân loại ao nuôi, ngưỡng quan trọng |
| 02 | [Kiến Trúc](02_kien-truc.md) | Sơ đồ khối, logic DO, pH, cho ăn |
| 03 | [Phần Cứng](03_phan-cung.md) | Thông số bộ điều khiển + cảm biến + actuator |
| 04 | [Relay Map](04_relay-channel-map.md) | 31 kênh chi tiết |
| 05 | [Đấu Nối Điện](05_dau-noi-dien.md) | Sơ đồ kết nối từng thiết bị |
| 06 | [Firmware](06_firmware.md) | Code đầy đủ CB2S LibreTuya |
| 07 | [MQTT API](07_mqtt-api.md) | Topics, JSON, cảnh báo |
| 08 | [Tự Động Hóa](08_tu-dong-hoa.md) | Logic DO 6 cấp, pH, cho ăn, thay nước |
| 09 | [Phục Hồi Mất Điện](09_phuc-hoi-mat-dien.md) | UPS, ưu tiên sục khí |
| 10 | [Lắp Đặt](10_lap-dat.md) | Hướng dẫn từng bước + checklist |
| 11 | [Vận Hành](11_van-hanh.md) | Đọc LCD, lệnh MQTT, sự cố |
| 12 | [Báo Giá](12_bang-gia.md) | TL + TT từng kênh, 4 gói |
| 13 | [Lịch Nuôi Trồng](13_lich-nuoi.md) | Tôm thẻ, cá tra theo tuần |

---

*Phiên bản: 1.0 | Cập nhật: 2026-05-12 | MQTT: broker.hosco.com.vn*
