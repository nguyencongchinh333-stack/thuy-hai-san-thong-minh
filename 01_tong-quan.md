# 01 — Tổng Quan & Định Vị

---

## 1.1 Phân Loại Mô Hình Nuôi

| Mô Hình | Diện Tích | DO Cần | Đặc Thù |
|---------|----------|:------:|---------|
| **Tôm thẻ chân trắng** | 500–3.000m² | >5.0 mg/L | Nhạy cảm nhất, cần DO + pH + EC + ORP |
| **Tôm sú** | 1.000–5.000m² | >5.0 mg/L | Mật độ thấp hơn, pH chặt hơn |
| **Cá tra / Cá basa** | 1.000–10.000m² | >3.5 mg/L | Chịu được DO thấp hơn, ăn nhiều |
| **Cá rô phi / Điêu hồng** | 500–3.000m² | >3.0 mg/L | Dễ nuôi nhất trong nhóm |
| **Cá chẽm / Cá mú** | 500–2.000m² | >5.0 mg/L | Nước lợ/mặn, giá trị cao |

---

## 1.2 Ngưỡng Thông Số Nước

### DO — Oxy Hòa Tan (Quan Trọng Nhất)

| DO (mg/L) | Tình Trạng | Hành Động Tự Động |
|:---------:|-----------|-----------------|
| > 6.0 | Lý tưởng — Có thể tiết kiệm điện | Tắt bớt quạt |
| 5.0–6.0 | Bình thường | Duy trì |
| 4.0–5.0 | ⚠️ Cảnh báo | Bật 2 quạt |
| 3.0–4.0 | 🔴 Nguy hiểm | Bật tất cả quạt + blower |
| < 3.0 | 🚨 **Khẩn cấp** | Tất cả + bơm dự phòng + van O₂ + báo điện thoại |
| < 2.0 | 💀 **Chết hàng loạt** | Mất trắng trong 1–3 giờ |

> DO xuống thấp nhất lúc **2:00–5:00 sáng** — tảo ngừng quang hợp + vi sinh tiêu thụ oxy

### pH

| Loại | Tối Ưu | Cảnh Báo | Khẩn Cấp |
|------|:------:|:--------:|:--------:|
| Tôm thẻ | 7.8–8.2 | <7.5 hoặc >8.5 | <7.0 hoặc >9.0 |
| Cá tra | 7.0–8.0 | <6.5 hoặc >8.5 | <6.0 hoặc >9.0 |

### Nhiệt Độ

| Loại | Tối Ưu | Nguy Hiểm |
|------|:------:|:---------:|
| Tôm thẻ | 25–30°C | <20°C hoặc >34°C |
| Cá tra | 26–31°C | <20°C hoặc >35°C |

### Độ Mặn (Tôm)

| Loại | Tối Ưu | Chấp Nhận |
|------|:------:|:---------:|
| Tôm thẻ | 15–25 ppt | 5–35 ppt |
| Tôm sú | 15–30 ppt | 10–35 ppt |

### ORP (Thế Oxy Hóa Khử)

| ORP (mV) | Ý Nghĩa |
|:--------:|---------|
| 250–350 | Tốt — Vi khuẩn hiếu khí hoạt động tốt |
| 200–250 | Cảnh báo |
| < 150 | Nguy hiểm — H₂S (khí trứng thối) tích tụ đáy ao |

---

## 1.3 So Sánh Chi Phí

| | **DIY Controller** | Nextfarm Pro |
|--|--:|--:|
| Bộ điều khiển | **~2.680.000đ** | 27–49.000.000đ |
| Tổng hệ thống ao 1000m² | **~75.000.000đ** | >120.000.000đ |
| Tự lắp đặt | ✅ | ❌ Cần kỹ thuật viên |
| Bảo hành | Tự bảo trì | 3 năm |

---

## 1.4 Yêu Cầu Đặc Biệt Thủy Sản

```
1. MÁY PHÁT ĐIỆN DỰ PHÒNG — BẮT BUỘC
   Mất điện → quạt sục khí tắt → DO giảm → mất trắng
   Không UPS nào chạy được quạt 1.1kW × 4 = 4.4kW

2. HIỆU CHUẨN CẢM BIẾN ĐỊNH KỲ
   DO: 2 tuần/lần | pH: 1 tháng/lần | EC: 3 tháng/lần

3. ĐỌC DO LÚC 4:00 SÁNG HÀNG NGÀY
   Đây là thời điểm DO thấp nhất trong ngày

4. KHÔNG CẮT ĐIỆN ĐỘT NGỘT
   Phải chuyển sang máy phát trước khi cắt lưới
```

---

*[← README](README.md) | [Kiến Trúc →](02_kien-truc.md)*
