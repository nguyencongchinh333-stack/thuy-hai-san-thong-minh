# 07 — MQTT API

---

## 7.1 Cấu Hình Kết Nối

| Thông Số | Giá Trị |
|---------|--------|
| Broker | **broker.hosco.com.vn** |
| Port | 1883 |
| Topic gốc | `aqua/{deviceID}/` |
| Device ID | Mã khách hàng (cài qua WiFiManager portal) |
| LWT | `aqua/{id}/status` → `"offline"` |

---

## 7.2 Sơ Đồ Topics

```
aqua/{deviceID}/
├── status              ← online / offline (LWT)
├── info                ← {"ma_khach":"...", "mac":"...", "fw":"1.0.0"}
├── trang_thai          ← Trạng thái tổng hợp (mỗi 15s)
├── sensor              ← DO, pH, EC, ORP, nhiệt độ, turbidity, mực nước
├── che_do              ← tu_dong / thu_cong
│
├── alert/
│   ├── DO              ← DO thấp / khẩn cấp
│   ├── pH              ← pH bất thường
│   ├── EC              ← Độ mặn sai
│   ├── ORP             ← ORP thấp (H₂S nguy hiểm)
│   ├── nhiet           ← Nhiệt độ bất thường
│   ├── muc_nuoc        ← Mực nước thấp
│   ├── thuc_an         ← Sắp hết / Hết thức ăn
│   └── dien            ← Mất điện / Phục hồi
│
└── [Nhận lệnh]:
    ├── che_do/set          ← Đổi AUTO/THỦ CÔNG
    ├── quat/set            ← Cài ngưỡng quạt DO 6 cấp
    ├── cho_an/lich/set     ← Cài lịch cho ăn
    ├── cho_an/lenh         ← Cho ăn thủ công ngay
    ├── thay_nuoc/set       ← Cài lịch thay nước
    ├── nguong/set          ← Cài tất cả ngưỡng cảnh báo
    ├── bom_voi/lenh        ← Bơm vôi thủ công X giây
    ├── dung                ← Dừng khẩn cấp tất cả
    └── get                 ← Lấy toàn bộ config
```

---

## 7.3 Dữ Liệu Cảm Biến

**Topic:** `aqua/{id}/sensor` — publish mỗi 15 giây

```json
{
  "do": 5.82,
  "ph": 8.12,
  "ec_uS": 28450,
  "do_man_ppt": 15.6,
  "orp_mV": 285,
  "nhiet_do": [27.5, 27.8, 27.3],
  "nhiet_do_tb": 27.53,
  "turbidity_ntu": 85,
  "muc_nuoc_cm": 180,
  "muc_nuoc_pct": 90,
  "ap_suat_bar": 0.18,
  "flow_cap_L": 1250.5,
  "flow_thai_L": 850.2,
  "quat_dang_chay": 4,
  "blower_dang_chay": 2,
  "timestamp": 1747123456
}
```

---

## 7.4 Cảnh Báo → Điện Thoại

**Topic:** `aqua/{id}/alert/{loai}` — gửi ngay khi vượt ngưỡng

```json
{
  "loai": "DO",
  "muc": "khan_cap",
  "gia_tri": 2.8,
  "don_vi": "mg/L",
  "nguong": 3.0,
  "hanh_dong": "bat_tat_ca_quat_blower_bom_dp",
  "thoi_gian": "2026-05-12T03:30:00"
}
```

| Topic Alert | Mức | Điều Kiện |
|------------|:---:|-----------|
| `alert/DO` | khan_cap | DO < 3.0 mg/L |
| `alert/DO` | nguy_hiem | DO < 4.0 mg/L |
| `alert/DO` | canh_bao | DO < 5.0 mg/L |
| `alert/pH` | khan_cap | pH < 7.0 hoặc > 9.0 |
| `alert/EC` | canh_bao | Độ mặn < 12 hoặc > 28 ppt |
| `alert/ORP` | nguy_hiem | ORP < 150 mV |
| `alert/muc_nuoc` | canh_bao | Mực nước < 70% |
| `alert/thuc_an` | khan_cap | Mức cám < 5% |

---

## 7.5 Cài Đặt Ngưỡng DO 6 Cấp

**Topic:** `aqua/{id}/nguong/set`

```json
{
  "do_tiet_kiem": 6.5,
  "do_duy_tri": 5.5,
  "do_canh_bao": 5.0,
  "do_nguy_hiem": 4.0,
  "do_bat_het": 3.5,
  "do_khan_cap": 3.0,
  "do_khan_cap_O2": 2.5,
  "ph_min_cb": 7.5, "ph_max_cb": 8.5,
  "ph_min_kc": 7.0, "ph_max_kc": 9.0,
  "ec_min_ppt": 12.0, "ec_max_ppt": 28.0,
  "orp_canh_bao": 200, "orp_nguy_hiem": 150,
  "nhiet_min_cb": 23.0, "nhiet_max_cb": 32.0,
  "muc_nuoc_cb": 70, "thuc_an_cb": 20
}
```

---

## 7.6 Cài Đặt Lịch Cho Ăn

**Topic:** `aqua/{id}/cho_an/lich/set`

```json
{
  "loai": "tom_the",
  "lich": [
    {"gio": 6,  "phut": 0,  "giay_chay": 45, "may": [true,true,true,true]},
    {"gio": 9,  "phut": 0,  "giay_chay": 45, "may": [true,true,true,true]},
    {"gio": 12, "phut": 0,  "giay_chay": 45, "may": [true,true,true,true]},
    {"gio": 15, "phut": 0,  "giay_chay": 45, "may": [true,true,true,true]},
    {"gio": 18, "phut": 0,  "giay_chay": 45, "may": [true,true,true,true]},
    {"gio": 21, "phut": 0,  "giay_chay": 45, "may": [true,true,true,true]}
  ],
  "dieu_chinh_do": true,
  "do_ngung_an": 4.0
}
```

---

## 7.7 Ví Dụ Lệnh MQTT

```bash
# Bật AUTO
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/TOM_001/che_do/set" -m '{"che_do":"tu_dong"}'

# Cho ăn thủ công ngay (máy 1+2, 30 giây)
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/TOM_001/cho_an/lenh" \
  -m '{"may":[1,2],"giay":30}'

# Bơm vôi 60 giây (pH thấp)
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/TOM_001/bom_voi/lenh" -m '{"giay":60}'

# Xem dữ liệu cảm biến
mosquitto_sub -h broker.hosco.com.vn \
  -t "aqua/TOM_001/sensor"

# Dừng khẩn cấp
mosquitto_pub -h broker.hosco.com.vn \
  -t "aqua/TOM_001/dung" -m "1"
```

---

*[← Firmware](06_firmware.md) | [Tự Động Hóa →](08_tu-dong-hoa.md)*
