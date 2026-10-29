---
name: shopee-data-diagnosis
description: Đọc số liệu Shopee Seller Center đúng cách và chẩn đoán nghẽn phễu trước khi tăng traffic. Dùng khi seller hỏi "vì sao đơn giảm", "số liệu này đọc thế nào", "nên xử lý gì trước", hoặc cần lập báo cáo tuần/tháng và biết chỉ số nào đang nói dối. Kỹ năng gốc của mọi agent trong Shopee Seller OS.
metadata:
  domain: shopee-seller
  version: "1.0"
  grounded_in: "TUKI Group corpus + knowledge/01-operating-model.md"
  pairs_with_agents: [shopee-chief, shopee-shop-doctor, shopee-data-analyst]
  pairs_with_scripts: [shop_diagnose.py, unit_econ.py, ads_planner.py]
---

# Đọc số & chẩn đoán phễu — nền của mọi quyết định

## 0. Khi nào dùng

- "Đơn tháng này giảm 30%, xử lý sao?" — cần biết nghẽn ở đâu trước khi hành động.
- "ROAS 5 mà vẫn không có lãi" — cần tách doanh thu ads vs doanh thu thật.
- "Nên xem báo cáo nào mỗi tuần?" — cần nhịp đọc số và mẫu báo cáo.
- "Số liệu nào đáng tin?" — cần biết chỗ nào Seller Center ghi nhận lệch.
- "Chuẩn bị số liệu cho agent chẩn đoán" — cần schema đầu vào.
- Trước khi chạy bất kỳ kế hoạch ads / khuyến mãi / mở SKU (bắt buộc).
- Không dùng khi: seller chưa có ít nhất 100 click và 10 đơn — lúc đó chưa có
  mẫu để chẩn đoán, hãy dùng `shopee-launch-bootstrap` trước.

## 1. Cơ chế — vì sao số liệu Shopee khó đọc

1. **Ba hệ báo cáo khác nhau, ba múi giờ cập nhật khác nhau.** Hiệu quả kinh
   doanh, Trung tâm quảng cáo và Vận hành cập nhật lệch nhau (thường vài giờ,
   có chỉ số lệch tới hôm sau). Cùng một ngày, cùng một chỉ số có thể ra 2 số.
   → Luôn so sánh **cùng một nguồn** với chính nó ở kỳ trước.
2. **GMV ≠ tiền về tay.** GMV tính cả đơn chưa giao, đơn huỷ, đơn hoàn. Doanh
   thu ads cũng ghi nhận đơn phát sinh, không phải đơn giao thành công.
   → Báo cáo lãi phải dùng doanh thu sau huỷ/hoàn, không dùng GMV.
3. **Chỉ số hiển thị có độ trễ.** Sản phẩm mới đăng cần thời gian được phân
   phối; đổi ảnh/tên có thể reset tạm thời. Đọc số 3–7 ngày sau thay đổi,
   không phải 24 giờ.
4. **Phân phối phụ thuộc hiệu suất quá khứ.** Cancel/late/NFRR xấu ở kỳ trước
   làm giảm hiển thị kỳ sau. Vì vậy "đơn giảm hôm nay" có thể do lỗi vận hành
   từ 2–4 tuần trước.
5. **Attribution chồng lấn.** Một đơn có thể được ghi cho cả ads, live và
   affiliate. Cộng các nguồn lại sẽ ra tổng > 100% đơn. → Chọn một nguồn
   "sự thật" (thường là Tổng quan) và dùng các nguồn khác để so sánh tương đối.

Ba điều seller kiểm soát được: **hiệu suất vận hành, chất lượng listing, cơ
cấu chi phí**. Ba điều không kiểm soát: thuật toán, đối thủ, mùa vụ. Chẩn đoán
là tách hai nhóm này ra.

## 2. Mô hình ra quyết định

### 2.1 Cây KPI (đọc từ trên xuống, sửa từ dưới lên)

```
GMV
├── Impressions ──► CTR ──► Clicks ──► CVR ──► Đơn ──► AOV
│        ▲            ▲                  ▲
│        │            │                  │
│   phân phối     thẻ sản phẩm     trang sản phẩm
│   (vận hành,    (ảnh, giá,       (review, ship, chat,
│    ads, tồn kho) tag, tên)        mô tả, voucher)
└── × (1 − cancel%) × (1 − return%) ──► Doanh thu thực
                                          └── − chi phí ──► Lợi nhuận
```

**Luật thứ tự chẩn đoán:** L7 (vận hành) → L1 (hiển thị) → L2 (CTR) → L3 (CVR)
→ L4 (AOV) → L5 (mua lại) → L6 (chi phí). Xem
`knowledge/01-operating-model.md#2-cây-chẩn-đoán`.

### 2.2 Đủ mẫu chưa? (tránh kết luận trên nhiễu)

| Chỉ số | Mẫu tối thiểu để kết luận | Nếu chưa đủ |
|---|---|---|
| CTR | ≥ 1.000 impressions | So sánh tương đối, đừng kết luận |
| CVR | ≥ 100 clicks | Xem thêm chat/đánh giá định tính |
| ROAS | ≥ 5.000đ chi ads và ≥ 10 đơn ads | Chờ đủ, đừng tắt campaign |
| Cancel/late/NFRR | ≥ 20 đơn trong kỳ | Xem từng đơn thay vì % |
| AOV | ≥ 20 đơn | Xem cơ cấu SKU |

Quy tắc thực dụng: **một thay đổi = một lần đo = một khoảng thời gian**. Đổi 3
thứ cùng lúc thì không học được gì.

### 2.3 Trước/sau một thay đổi

1. Ghi baseline: 7 ngày trước (cùng thứ, cùng khung giờ nếu được).
2. Chỉ đổi **một** biến (ảnh chính, hoặc giá, hoặc voucher).
3. Ghi ngày đổi + mọi sự kiện ngoài (sàn sale, thời tiết, đối thủ).
4. Đo tại ngày 3, 7, 14. Kết luận ở ngày 14, không sớm hơn.
5. Nếu chỉ số không đổi: giữ nguyên nếu không xấu; revert nếu tệ hơn baseline.

## 3. Playbook theo tình huống

### P1 — Đơn giảm, không biết vì sao (chẩn đoán 20 phút)

**Triệu chứng:** GMV/đơn kỳ này giảm > 20% so với kỳ trước.
**Chẩn đoán (xếp theo khả năng):**
1. Mất hiển thị do vận hành kỳ trước (cancel/late tăng).
2. Hết hàng SKU chủ lực (kiểm tra tồn kho trước tiên).
3. Ads bị tắt/hết ngân sách/bid tụt.
4. Đổi listing hàng loạt (ảnh/tên) làm reset phân phối.
5. Mùa vụ/đối thủ giảm giá.

**Hành động:**
1. Tách đơn theo nguồn: tự nhiên / ads / live / affiliate — nguồn nào rơi?
2. Nếu rơi ở tự nhiên: kiểm tra impressions của top 5 SKU, tồn kho, vi phạm.
3. Nếu rơi ở ads: xem ngân sách 7 ngày, CPO, số campaign bật/tắt.
4. Nếu tổng impressions giữ nguyên mà clicks rơi: xem ảnh/giá có bị sửa không.
5. Chạy `python3 scripts/shop_diagnose.py <metrics.json>` để xếp hạng giả thuyết.

**Kỳ vọng:** xác định được 1 nguyên nhân chính trong 20 phút, không phải "sửa hết".
**Đo lường:** đúng chỉ số của nguyên nhân đó, tại ngày 7 và 14.

### P2 — ROAS đẹp nhưng không có lãi

**Triệu chứng:** ROAS 4–6 nhưng tiền về ít hơn mong đợi.
**Chẩn đoán:** (1) GMV ads gồm đơn huỷ/hoàn; (2) biên góp mỏng; (3) cộng dồn
voucher + freeship + phí sàn chưa tính vào P&L.
**Hành động:** chạy `unit_econ.py` với đúng giá vốn và % phí; tách doanh thu
ads thành "đơn phát sinh" và "đơn giao thành công"; tính lại ROAS trên doanh
thu thực. So ROAS thực với ROAS hoà vốn.
**Kỳ vọng:** thấy rõ ROAS thực thấp hơn 15–30% so với báo cáo ads.
**Đo lường:** ROAS thực/ngày sau khi trừ huỷ/hoàn.

### P3 — Báo cáo tuần 90 phút

**Hành động:** theo mẫu ở mục 6.1 → điền số → chạy `shop_diagnose.py --json` →
chọn 3 hành động → giao cho 3 agent tương ứng trong `agents/`.
**Kỳ vọng:** 3 hành động có chủ, có hạn, có chỉ số đo.
**Đo lường:** tuần sau đánh giá đúng 3 chỉ số đó.

### P4 — Trước khi mở SKU mới hoặc tăng ngân sách

**Hành động:** (1) `unit_econ.py` cho SKU/lô mới; (2) xác định trần CPO;
(3) `ads_planner.py` cho ngân sách dự kiến; (4) chỉ tăng nếu ROAS hiện tại
> ROAS mục tiêu trong 14 ngày.
**Kỳ vọng:** không mở SKU có biên góp < 8% trừ khi là SKU mồi có chiến lược.
**Đo lường:** biên góp/đơn sau 30 ngày.

### P5 — Chuẩn bị dữ liệu cho agent

**Hành động:** điền `samples/shop_metrics.json` theo schema, đảm bảo đủ:
impressions, clicks, orders, gmv, ad_spend, ad_orders, ads_revenue, cancel_rate,
late_rate, nfrr, chat_response_rate, rating, review_count, stockout_skus,
total_skus, repeat_rate, margin_pct (từ `unit_econ.py`), impressions_prev,
orders_prev.
**Kỳ vọng:** agent trả về chẩn đoán + 3 hành động, không hỏi lại.
**Đo lường:** mức độ khớp giữa chẩn đoán và thực tế sau 14 ngày.

## 4. Checklist vận hành

### Hằng ngày (10 phút)
- [ ] Đơn mới & đơn chờ xác nhận (không để quá giờ cắt đơn)
- [ ] Chat chưa trả lời (SLA < 15 phút trong giờ làm)
- [ ] SKU sắp hết hàng / đã hết hàng
- [ ] Ads: chi phí & CPO hôm qua có bất thường
- [ ] Đánh giá mới 1–3★ và đơn huỷ mới

### Hằng tuần (60–90 phút)
- [ ] Điền bảng số tuần (mục 6.1), so với tuần trước và 4 tuần trước
- [ ] Chạy `shop_diagnose.py`, đọc 3 chẩn đoán ưu tiên
- [ ] Cập nhật bảng ads: cắt/tăng nhóm theo luật 3/7/14 ngày
- [ ] Rà top 5 SKU theo doanh thu: tồn kho, giá, review mới
- [ ] Chốt lịch khuyến mãi tuần sau (voucher/flash sale)
- [ ] Ghi 1 dòng "tuần này học được gì"

### Hằng tháng (3 giờ)
- [ ] P&L theo SKU (`unit_econ.py` cho top 10)
- [ ] Cắt SKU lỗ, quyết định SKU mới
- [ ] So sánh 3 tháng: traffic, CVR, AOV, biên góp
- [ ] Rà vi phạm/chính sách, hạn giấy tờ
- [ ] Kế hoạch tháng sau với 3 mục tiêu số

## 5. Chỉ số & ngưỡng

| Chỉ số | Công thức | Lấy ở đâu | Ngưỡng tham chiếu | Độ tin cậy |
|---|---|---|---|---|
| Impressions | — | Hiệu quả kinh doanh | Tùy ngành; theo dõi xu hướng | cao |
| CTR | clicks / impressions | Hiệu quả kinh doanh | 2–4% (thời trang có thể thấp hơn) | trung bình |
| CVR | đơn / clicks | Hiệu quả kinh doanh | 1,5–3% | trung bình |
| AOV | GMV / đơn | Hiệu quả kinh doanh | theo ngành | cao |
| Ad dependency | đơn ads / tổng đơn | Quảng cáo + Tổng quan | < 60% | trung bình [ước lượng ngành] |
| ROAS | doanh thu ads / chi ads | Trung tâm quảng cáo | > ROAS hoà vốn (từ unit_econ) | cao |
| CPO | chi ads / đơn ads | Trung tâm quảng cáo | < trần CPO | cao |
| Cancel rate | đơn huỷ / đơn phát sinh | Vận hành | < 2–3% `[xác minh]` | cao |
| Late shipment | đơn giao trễ / đơn giao | Vận hành | < 2–5% `[xác minh]` | cao |
| NFRR | đơn không giao được / đơn | Vận hành | < 2% `[xác minh]` | cao |
| Chat response | phản hồi / tin nhắn | Chat | > 95% | cao |
| Repeat rate | khách mua ≥2 lần / tổng khách | Hiệu quả kinh doanh | > 15% (nếu có mua lại) | trung bình |
| Biên góp/đơn | xem unit_econ.py | Tự tính | > 15% | cao |

## 6. Template

### 6.1 Bảng số tuần (copy vào Sheet)

```
Tuần | Impressions | Clicks | CTR | Đơn | CVR | GMV | AOV | Đơn ads | ROAS | CPO
     | Cancel%     | Late%  | NFRR| Chat%| Rating| Review mới | Tồn <10 SKU | Ghi chú
So sánh: tuần trước | 4 tuần trước | cùng kỳ năm trước (nếu có)
```

### 6.2 Mẫu báo cáo tuần (gửi chủ shop/đội)

```
TUẦN {W} — {ngày}
1. Kết quả: GMV {x} ({±y}% WoW) · đơn {n} · AOV {a}
2. Chẩn đoán: nghẽn chính là {L?} vì {1 câu số liệu}
3. Ba hành động tuần này: {A} / {B} / {C} — chủ: {...} — hạn: {...}
4. Chỉ số sẽ đổi: {chỉ số} từ {x} → {y} trong 14 ngày
5. Rủi ro: {1–2 dòng}
6. Cần quyết định: {1 câu}
```

### 6.3 Worksheet chẩn đoán (điền nhanh)

```
Nghẽn ở tầng nào? (L7/L1/L2/L3/L4/L5/L6): ___
Bằng chứng (số + kỳ): ___
Đã loại trừ nguyên nhân nào: ___
Giả thuyết số 1: ___  → kiểm chứng bằng: ___
Hành động nhỏ nhất có thể làm hôm nay: ___
Đo lại vào ngày: ___
```

## 7. Anti-pattern

| Sai | Hậu quả | Đúng |
|---|---|---|
| Báo cáo bằng GMV | Tự hào doanh thu ảo, lỗ thật | Dùng doanh thu sau huỷ/hoàn + biên góp |
| Tin ROAS của nền tảng | Bỏ qua đơn huỷ, hoàn | Tính ROAS thực, so ROAS hoà vốn |
| Đổi 5 thứ cùng lúc | Không biết cái nào có tác dụng | 1 biến / 1 lần đo / 14 ngày |
| Kết luận sau 1 ngày | Nhiễu, ra quyết định sai | Đợi đủ mẫu (mục 2.2) |
| So sánh kỳ sale với kỳ thường | Tưởng mình giỏi/tệ hơn thực tế | So cùng loại kỳ, ghi chú mùa vụ |
| Chỉ nhìn ads | Bỏ qua listing/vận hành | Chẩn đoán theo thứ tự L7→L1→… |
| Báo cáo không có hành động | Họp xong không ai làm gì | Mỗi báo cáo phải có 3 hành động có chủ |
| Giấu số xấu | Không sửa được gốc | Ghi rõ, kèm kế hoạch xử lý |

## 8. Bàn giao cho agent/script

```bash
# 1) Chẩn đoán tổng thể
python3 scripts/shop_diagnose.py samples/shop_metrics.json --json > /tmp/diag.json

# 2) P&L SKU trước khi quyết định giá/ads
python3 scripts/unit_econ.py samples/sku_input.json --json

# 3) Ngưỡng & kế hoạch ads
python3 scripts/ads_planner.py samples/ads_input.json --json

# 4) Điểm listing
python3 scripts/listing_audit.py samples/listing_input.json --json
```

- `shopee-shop-doctor`: nhận `diag.json` → chọn 3 hành động → route sang agent khác.
- `shopee-data-analyst`: nhận bảng số tuần → viết báo cáo mục 6.2.
- `shopee-ads-operator`: nhận `ads_planner` output → cắt/tăng campaign.
- Trước khi giao việc: luôn kèm 3 dòng "dữ liệu này lấy từ đâu, kỳ nào, ai nhập".

## 9. Nguồn & ranh giới

Corpus: 681 bài của một đơn vị đào tạo Shopee có chứng nhận (tukigroup.vn).
Các bài đóng góp trực tiếp cho skill này:

- [Những chỉ số shop cần lưu ý và 5 lý do quảng cáo shop nhưng không hiệu quả](https://tukigroup.vn/nhung-chi-so-shop-can-luu-y-va-5-ly-do-quang-cao-shop-nhung-khong-hieu-qua/) — bộ chỉ số nên xem và cách đọc.
- [Những chỉ số đo lường trong quảng cáo Shopee cần quan tâm](https://tukigroup.vn/20-chi-so-quang-cao-shopee-quan-trong/) — định nghĩa chỉ số ads, tránh nhầm.
- [Khi nào nên tắt quảng cáo Shopee — 5 chỉ số cảnh báo đỏ](https://tukigroup.vn/khi-nao-nen-tat-quang-cao-shopee-goc-nhin-phan-tich-tu-5-chi-so-canh-bao-do/) — ngưỡng cắt theo số.
- [Checklist 7 bước phục hồi traffic & doanh số sau khi tụt đơn](https://tukigroup.vn/7-buoc-phuc-hoi-traffic-doanh-so-sau-khi-tut-don/) — quy trình chẩn đoán khi đơn rơi.
- [Top 7 lý do khiến shop tăng tỷ lệ hủy đơn (phần 1)](https://tukigroup.vn/tim-hieu-ngay-top-7-ly-do-khien-shop-tang-ty-le-huy-don-can-phai-tranh-ngay-phan-1/) — nguyên nhân gốc của cancel.
- [Xử lý đơn giao không thành công trên Shopee](https://tukigroup.vn/xu-ly-don-giao-khong-thanh-cong-tren-shopee/) — NFRR và cách xử lý.
- [Thời hạn xử lý đơn hàng siêu tốc 4 giờ và mẹo vận hành](https://tukigroup.vn/thoi-han-xu-ly-don-hang-sieu-toc-4-gio-va-meo-van-hanh-hieu-qua-tren-shopee/) — mốc thời gian xử lý đơn.
- [Cập nhật hiển thị số lượng tồn kho tại trang tất cả sản phẩm](https://tukigroup.vn/cap-nhat-hien-thi-so-luong-ton-kho-tai-trang-tat-ca-san-pham-tren-shopee/) — tồn kho ảnh hưởng hiển thị.
- [Tỷ lệ đánh giá sản phẩm tiêu cực ảnh hưởng đến shop ra sao](https://tukigroup.vn/ty-le-danh-gia-san-pham-tieu-cuc-anh-huong-den-cac-shop-ra-sao/) — rating và hệ quả.
- [13 nguồn tăng traffic giúp x5 lượt truy cập cho shop (phần 1)](https://tukigroup.vn/13-nguon-tang-traffic-giup-x5-luot-truy-cap-cho-shop-phan-1/) — mix nguồn traffic và attribution.
- [Hướng dẫn dùng AI phân tích dữ liệu quảng cáo Shopee](https://tukigroup.vn/huong-dan-dung-ai-phan-tich-du-lieu-quang-cao-shopee-va-dieu-chinh-chien-dich/) — cách đọc dữ liệu ads theo nhóm.
- [Shopee Mall có giúp tăng CR hay chỉ tăng view](https://tukigroup.vn/shopee-mall-co-giup-tang-cr-ty-le-chuyen-doi-hay-chi-tang-view/) — phân biệt traffic và chuyển đổi.

**Ranh giới:** ngưỡng CTR/CVR/ROAS trong skill là **tham chiếu ngành, cần hiệu
chỉnh theo ngành hàng và giá trị đơn của từng shop**. Mọi ngưỡng phí/phạt cụ
thể phải xác minh tại Seller Center — dự án này cố ý không khẳng định con số
chính sách vì chúng thay đổi theo thời gian.
