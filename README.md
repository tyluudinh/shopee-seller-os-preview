# Shopee Seller OS — bản xem trước

**Bộ agent + skill vận hành bán hàng Shopee dành cho AI agent** (Claude Code,
Codex, Cursor). Mục tiêu không phải "viết content hay", mà là: **chẩn đoán đúng
nút thắt, làm 3 việc/tuần, đo bằng số, và không đốt tiền vào ads sai.**

> Đây là **bản xem trước công khai**. Bản đầy đủ (13 agent, 11 skill, 6 công cụ,
> runbook 30 ngày) là sản phẩm trả phí — xem mục *Bản đầy đủ có gì*.

---

## 3 triệu chứng quen thuộc

1. **Đơn tăng, tiền không tăng.** ROAS 5 nhưng cuối tháng không dư.
2. **Đơn tụt, không biết vì sao.** Tăng ads thì đốt tiền; giảm giá thì mất lãi.
3. **Huỷ 4%, giao trễ 7%.** Không ai nói rằng đây là lý do hiển thị tụt.

Cả ba cùng một nguyên nhân: **ra quyết định mà không có số đúng.**

---

## Cách hệ thống hoạt động

```
Số từ Seller Center (7–30 ngày)
        │
        ▼
Chẩn đoán phễu  ──►  nút thắt + điểm sức khoẻ 7 tầng
        │
        ▼
3 hành động/tuần  ──►  agent chuyên môn thực thi (13 vai trò)
        │
        ▼
Đo lại sau 7–14 ngày  ──►  giữ / sửa / dừng
```

Mọi khuyến nghị đi theo chuỗi: **số hiện tại → giả thuyết → hành động → chỉ số
sẽ đổi → ngày đo**. Không có "hãy tối ưu shop chung chung".

---

## Trong bản xem trước này có gì

| File | Nội dung |
|---|---|
| [`skills/shopee-data-diagnosis/SKILL.md`](skills/shopee-data-diagnosis/SKILL.md) | **Toàn bộ 1 skill thật** (277 dòng): đọc số Seller Center đúng cách, cây chẩn đoán phễu L7→L1, ngưỡng tham chiếu, mẫu báo cáo tuần |
| [`samples/brief-tuan-mau.md`](samples/brief-tuan-mau.md) | Brief tuần mẫu do công cụ sinh ra: bảng WoW, chẩn đoán xếp hạng, khung 3 việc |
| [`samples/shop_metrics.json`](samples/shop_metrics.json) | Schema số liệu đầu vào (mô phỏng) để bạn thử điền số shop mình |

Đọc skill demo là biết chất lượng phần còn lại: mỗi skill 350–650 dòng gồm cơ
chế → framework quyết định → playbook theo tình huống → checklist → bảng chỉ
số có ngưỡng → template copy-paste → anti-pattern → nguồn.

---

## Bản đầy đủ có gì

| Thành phần | Chi tiết |
|---|---|
| **13 agent** | điều phối, chẩn đoán, listing, gian hàng, ads, khuyến mãi, nội dung/live, chuyển đổi, vận hành, chính sách, tăng trưởng, dữ liệu, khởi nghiệp |
| **11 skill** | 6.428 dòng: listing-seo, storefront, ads-engine, promo-pricing, content-live-affiliate, conversion-cro, ops-fulfillment, policy-compliance, data-diagnosis, growth-strategy, launch-bootstrap |
| **6 công cụ chạy được** | chẩn đoán phễu · P&L đơn vị (trần CPO, ROAS hoà vốn) · ngưỡng & kế hoạch ads · chấm điểm listing 0–100 · trần giảm giá + lịch khuyến mãi 12 tháng · nhập số tuần → brief |
| **Runbook 30 ngày** | Day 0 chuẩn bị, nhịp 20'/ngày + 90'/tuần, 4 tuần có cổng chặn, mẫu case study |
| **Corpus 681 bài** | nền tri thức có dẫn nguồn, kiểm tra không sao chép |
| **Lớp thương mại hoá** | định vị, thang gói/giá, sales page, GTM 90 ngày, SOP giao hàng, legal/license |

**Nguyên tắc thiết kế:** mọi con số phí/hạn mức/chính sách đều gắn
`[xác minh tại Seller Center]` — hệ thống không bịa chính sách, không hứa
"tăng X% đơn", không dạy lách luật.

---

## Ai phù hợp

- **Seller 50–500 đơn/tháng:** biết SKU nào có lãi, ngừng đốt ads sai.
- **Shop mới:** lộ trình 0→100 đơn/28 ngày, không nhập tồn sai.
- **Chủ shop nhiều nhân viên:** chuẩn hoá quy trình, không phụ thuộc 1 người.
- **Agency / đơn vị đào tạo:** quy trình chuẩn giao cho nhân viên và khách.

## Không phù hợp nếu

- Bạn cần tool tự động đăng bài/chạy ads (bộ này là lớp **quyết định**, không
  phải tool thao tác).
- Bạn muốn cam kết "tăng đơn bằng X lần" — hệ thống chỉ đưa quy trình + số đo.
- Bạn chưa sẵn sàng dành 20 phút/ngày cho vận hành.

---

## Liên hệ & mua bản đầy đủ

- GitHub: [@tyluudinh](https://github.com/tyluudinh)
- Nội dung bán hàng chi tiết: xem [`SALES-PAGE.md`](SALES-PAGE.md)
- Zalo / email: `{{THÔNG_TIN_LIÊN_HỆ}}`

---

## Giấy phép & giới hạn

- Bản xem trước: được đọc và dùng nội bộ để đánh giá. **Không** sao chép, bán
  lại, hoặc dùng thương mại nếu chưa có văn bản đồng ý. Xem [`LICENSE`](LICENSE).
- Không liên kết, không được bảo trợ bởi Shopee. Xem [`NOTICE.md`](NOTICE.md).
- Không có nội dung nào từ bên thứ ba trong repo này (bản đầy đủ có corpus tham
  khảo, giữ riêng tư).
