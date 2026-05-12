# 04 — Bản Đồ 31 Kênh Relay

---

## 4.1 Bảng Chi Tiết

| Kênh | Define | Thiết Bị | Điện Áp | MCP23017 | GPIO |
|:----:|--------|---------|---------|---------|------|
| **0** | `K_BOM_CAP` | Bơm cấp nước chính | 220VAC | #1 GPA0 | 0x20 |
| **1** | `K_QUAT1` | Quạt sục khí cánh quạt 1 | 220VAC | #1 GPA1 | 0x20 |
| **2** | `K_QUAT2` | Quạt sục khí cánh quạt 2 | 220VAC | #1 GPA2 | 0x20 |
| **3** | `K_QUAT3` | Quạt sục khí cánh quạt 3 | 220VAC | #1 GPA3 | 0x20 |
| **4** | `K_QUAT4` | Quạt sục khí cánh quạt 4 | 220VAC | #1 GPA4 | 0x20 |
| **5** | `K_BLOWER1` | Máy thổi khí (air blower) 1 | 220VAC | #1 GPA5 | 0x20 |
| **6** | `K_BLOWER2` | Máy thổi khí (air blower) 2 | 220VAC | #1 GPA6 | 0x20 |
| **7** | `K_BOM_THAI` | Bơm thải / xả nước | 220VAC | #1 GPA7 | 0x20 |
| **8** | `K_BOM_TUAN` | Bơm tuần hoàn nội bộ | 220VAC | #1 GPB0 | 0x20 |
| **9** | `K_BOM_VOI` | Bơm châm vôi Ca(OH)₂ | 220VAC | #1 GPB1 | 0x20 |
| **10** | `K_BOM_KHOANG` | Bơm châm khoáng / hóa chất | 220VAC | #1 GPB2 | 0x20 |
| **11** | `K_DEN_UV` | Đèn UV khử trùng nước cấp | 220VAC | #1 GPB3 | 0x20 |
| **12** | `K_SUOI_NUOC` | Bộ sưởi nước (mùa lạnh) | 220VAC | #1 GPB4 | 0x20 |
| **13** | `K_DEN_AO_A` | Đèn chiếu sáng ao khu A | 220VAC | #1 GPB5 | 0x20 |
| **14** | `K_DEN_AO_B` | Đèn chiếu sáng ao khu B | 220VAC | #1 GPB6 | 0x20 |
| **15** | `K_SPARE1` | **Dự phòng** | — | #1 GPB7 | 0x20 |
| **16** | `K_VAN_LANG1` | Van cấp từ ao lắng 1 | 24VAC | #2 GPA0 | 0x21 |
| **17** | `K_VAN_LANG2` | Van cấp từ ao lắng 2 | 24VAC | #2 GPA1 | 0x21 |
| **18** | `K_VAN_LANG3` | Van cấp từ ao lắng 3 | 24VAC | #2 GPA2 | 0x21 |
| **19** | `K_VAN_LANG4` | Van cấp từ ao lắng 4 | 24VAC | #2 GPA3 | 0x21 |
| **20** | `K_CHOAN1` | Máy cho ăn tự động góc 1 | 220VAC | #2 GPA4 | 0x21 |
| **21** | `K_CHOAN2` | Máy cho ăn tự động góc 2 | 220VAC | #2 GPA5 | 0x21 |
| **22** | `K_CHOAN3` | Máy cho ăn tự động góc 3 | 220VAC | #2 GPA6 | 0x21 |
| **23** | `K_CHOAN4` | Máy cho ăn tự động góc 4 | 220VAC | #2 GPA7 | 0x21 |
| **24** | `K_VAN_XA1` | Van xả đáy ao 1 | 220VAC | #2 GPB0 | 0x21 |
| **25** | `K_VAN_XA2` | Van xả đáy ao 2 | 220VAC | #2 GPB1 | 0x21 |
| **26** | `K_BOM_BUN` | Bơm hút bùn đáy | 220VAC | #2 GPB2 | 0x21 |
| **27** | `K_VAN_THAI` | Van xả thải ra ngoài | 220VAC | #2 GPB3 | 0x21 |
| **28** | `K_BOM_DU_PHONG` | Bơm sục khí dự phòng khẩn cấp | 220VAC | #2 GPB4 | 0x21 |
| **29** | `K_VAN_O2` | Van cấp O₂ bình khẩn cấp | 220VAC | #2 GPB5 | 0x21 |
| **30** | `K_SPARE2` | **Dự phòng mở rộng** | — | #2 GPB6 | 0x21 |
| **31** | `K_SPARE3` | **Dự phòng mở rộng** | — | #2 GPB7 | 0x21 |

---

## 4.2 Phân Nhóm Chức Năng

```
K0          BƠM CẤP NƯỚC CHÍNH
K1–K4       QUẠT SỤC KHÍ CÁNH QUẠT × 4  (bật theo DO)
K5–K6       MÁY THỔI KHÍ × 2             (bật theo DO)
K7          BƠM THẢI NƯỚC
K8          BƠM TUẦN HOÀN
K9          BƠM CHÂM VÔI                  (bật khi pH thấp)
K10         BƠM CHÂM KHOÁNG/HÓA CHẤT
K11         ĐÈN UV KHỬ TRÙNG
K12         SƯỞi NƯỚC                     (mùa lạnh)
K13–K14     ĐÈN CHIẾU SÁNG AO
K15         DỰ PHÒNG
K16–K19     VAN AO LẮNG × 4               (24VAC NC)
K20–K23     MÁY CHO ĂN × 4               (4 góc ao)
K24–K25     VAN XẢ ĐÁY AO × 2
K26         BƠM HÚT BÙN
K27         VAN XẢ THẢI
K28         BƠM DỰ PHÒNG KHẨN CẤP        (K28 — ưu tiên tuyệt đối)
K29         VAN O₂ BÌNH KHẨN CẤP         (K29 — ưu tiên tuyệt đối)
K30–K31     DỰ PHÒNG MỞ RỘNG
```

---

## 4.3 Ưu Tiên Relay Khi Khởi Động (Boot Priority)

```
Ngay sau khi CB2S boot (TRƯỚC KHI KẾT NỐI WiFi):
  1. Đọc DO từ Atlas EZO-DO qua I2C
  2. Nếu DO < 4.0 → Bật K1+K2+K3+K4 (tất cả quạt)
  3. Nếu DO < 3.0 → Bật thêm K5+K6 (blower) + K28 (bơm DP)
  
Lý do: Tôm/Cá không thể chờ WiFi kết nối (1–2 phút)
        trong khi DO đang ở mức nguy hiểm
```

---

*[← Phần Cứng](03_phan-cung.md) | [Đấu Nối →](05_dau-noi-dien.md)*
