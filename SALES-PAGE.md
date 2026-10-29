# Shopee Seller OS — bản đầy đủ

> Trang này mô tả bản trả phí. Bản xem trước (miễn phí) nằm ngay trong repo này.

---

## Shop đang có đơn — nhưng anh có biết mình lãi bao nhiêu mỗi đơn?

**Shopee Seller OS là bộ não vận hành cho shop Shopee:** chẩn đoán đúng nút
thắt trong 10 phút, chia việc cho 13 agent chuyên môn, và tính lãi trên từng
đơn trước khi tiêu thêm một đồng ads nào.

Chạy được trong **Claude Code / Codex / Cursor** — không cần biết code, không
cần server, dữ liệu shop ở lại trên máy.

---

## Anh nhận được gì

| Thành phần | Chi tiết |
|---|---|
| **13 agent** | điều phối · chẩn đoán · listing · gian hàng · ads · khuyến mãi · nội dung/live · chuyển đổi · vận hành · chính sách · tăng trưởng · dữ liệu · khởi nghiệp |
| **11 skill** (6.428 dòng) | mỗi skill: cơ chế → framework quyết định → playbook theo tình huống → checklist → bảng chỉ số có ngưỡng → template → anti-pattern → nguồn |
| **6 công cụ CLI** | chẩn đoán phễu · P&L đơn vị (trần CPO, ROAS hoà vốn) · ngưỡng & kế hoạch ads · chấm điểm listing 0–100 + bản viết lại · trần giảm giá + lịch khuyến mãi 12 tháng · nhập số tuần → brief tuần |
| **Runbook 30 ngày** | Day 0 (90 phút), nhịp 20'/ngày + 90'/tuần, 4 tuần có cổng quyết định, mẫu case study |
| **Bộ tài liệu thương mại** | định vị, thang gói/giá, sales page, GTM 90 ngày, SOP giao hàng, pháp lý/license |
| **Cập nhật** | 6 tháng (khi sàn đổi chính sách) |

---

## 3 việc hệ thống làm được ngay trong buổi đầu

```bash
# 1. Chẩn đoán: đơn giảm vì đâu?
python3 scripts/shop_diagnose.py shop-cua-anh.json
# → "Huỷ 4,1% > 3%: xử lý 20 đơn huỷ trước khi tăng traffic"
#   Điểm L7_ops = 0 → mọi khuyến nghị ads bị hạ ưu tiên

# 2. P&L: mỗi đơn còn bao nhiêu?
python3 scripts/unit_econ.py sku-chu-luc.json
# → biên góp 25%, trần ads 95.750đ/đơn, ROAS hoà vốn 2,61x

# 3. Khuyến mãi: được giảm tối đa bao nhiêu?
python3 scripts/promo_calendar.py sku-chu-luc.json
# → trần hoà vốn 41,6%, trần còn biên 8% là 36,2%, ngưỡng freeship, lịch 12 tháng
```

---

## Vì sao đáng tin

- **Không bịa chính sách:** mọi con số phí/hạn mức/ngưỡng phạt đều gắn
  `[xác minh tại Seller Center]` hoặc dẫn nguồn có ngày.
- **Có số trước khi khuyên:** thiếu baseline thì hệ thống yêu cầu đúng 5 trường
  cần lấy, không đoán.
- **Không hứa tuyệt đối:** kết quả kèm biên độ + điều kiện + ngày đo.
- **Có kiểm chứng công khai:** 49 kiểm tra tự động + kiểm tra chống sao chép
  chạy trên CI mỗi lần cập nhật.

---

## Giá tham khảo

| Gói | Nội dung | Giá |
|---|---|---|
| Starter | 11 skill + 13 agent + 6 công cụ, hướng dẫn cài 30 phút | **990.000đ** |
| Pro | Starter + 4 buổi nhóm chẩn đoán shop thật + 30 ngày hỗ trợ | **2.490.000đ** |
| Audit chuyên sâu | 1 báo cáo 15–20 trang cho 1 shop | **1.990.000đ** |
| Done-with-you | 2 buổi/tuần cùng vận hành | **8.900.000đ/tháng** |
| Agency license | 10 ghế khách, white-label, 12 tháng cập nhật | **39.000.000đ/năm** |

Hoàn tiền 7 ngày với Starter/Pro nếu đã cài và chạy thử mà không phù hợp.

---

## Cam kết & giới hạn

**Có:** cài đặt hỗ trợ, cập nhật 6 tháng, hoàn tiền 7 ngày.

**Không:** không hứa tăng X% đơn; không phải tool tự động đăng bài/chạy ads;
không thay anh ra quyết định kinh doanh; không tư vấn pháp lý/kế toán.

---

## Liên hệ

- GitHub: [@tyluudinh](https://github.com/tyluudinh)
- Zalo / email: `{{THÔNG_TIN_LIÊN_HỆ}}`
- Xem trước chất lượng: [`skills/shopee-data-diagnosis/SKILL.md`](skills/shopee-data-diagnosis/SKILL.md)
