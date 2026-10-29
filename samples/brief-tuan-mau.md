# Brief tuần 4 — 2026-10-22 → 2026-10-28

## 1. Số tuần này

| Chỉ số | Tuần này | Tuần trước | Δ WoW | So tuần 1 |
|---|---|---|---|---|
| Impressions | 480,000 | 455,000 | +5.5% | +14.3% |
| CTR | 1.54% | — | — | — |
| Clicks | 7,400 | 6,800 | +8.8% | +25.9% |
| Đơn | 140 | 120 | +16.7% | +59.1% |
| CVR | 1.89% | — | — | — |
| GMV | 37,800,000 | 32,400,000 | +16.7% | +59.1% |
| AOV | 270,000 | — | — | — |
| ROAS | 5.53 | — | — | — |
| CPO | 54,286 | — | — | — |
| Phụ thuộc ads | 50.00% | — | — | — |
| Huỷ | 1.80% | 2.40% | -25.0% | -56.1% |
| Giao trễ | 2.90% | 3.80% | -23.7% | -56.7% |
| NFRR | 1.50% | 2.00% | -25.0% | -53.1% |
| Phản hồi chat | 94.00% | 88.00% | +6.8% | +49.2% |
| Rating | 4.70 | 4.62 | +1.7% | +3.3% |
| Số đánh giá | 47 | 33 | +42.4% | +235.7% |
| SKU hết hàng | 2 | 3 | -33.3% | -71.4% |
| Biên góp | 15.00% | 13.00% | +15.4% | +66.7% |

## 2. Chẩn đoán (từ shop_diagnose engine)

**1. [75] CTR-LOW · L2**
- Phát hiện: CTR 1.54% < 2% — thẻ sản phẩm chưa đủ sức bấm.
- Việc: So ảnh chính với 3 đối thủ top cùng từ khoá: khác biệt ở đâu
- Việc: Ảnh chính: sản phẩm chiếm ≥ 60% khung, nền sạch, ≤ 20% diện tích có chữ
- Việc: Kiểm tra giá hiển thị so với top: cao hơn 10–15% thì phải có lý do rõ (combo, quà, ship)
- Skill: listing-seo + promo-pricing · Script: listing_audit.py
- Kỳ vọng: CTR 2,5–4% sau 14 ngày

**2. [74] ADS-ROAS · L6**
- Phát hiện: ROAS 5.53 thấp hơn ROAS hoà vốn 6.67.
- Việc: Tạm dừng nhóm quảng cáo có ROAS thấp nhất 20% trong 7 ngày
- Việc: Kiểm tra tỷ lệ đơn ads bị huỷ — doanh thu ads ghi nhận có thể ảo
- Việc: Chuyển ngân sách sang SKU có biên góp cao thay vì SKU bán chạy
- Skill: ads-engine · Script: ads_planner.py + unit_econ.py
- Kỳ vọng: ROAS > hoà vốn sau 14 ngày

## 3. Ba việc tuần này (điền trước thứ 2, chốt chủ + hạn)

1. Việc: ............ · Agent: ............ · Chỉ số đo: ............ · Hạn: ....
2. Việc: ............ · Agent: ............ · Chỉ số đo: ............ · Hạn: ....
3. Việc: ............ · Agent: ............ · Chỉ số đo: ............ · Hạn: ....

## 4. Ghi chú ngoài số (mùa vụ, đối thủ, thay đổi listing, sự cố)

Ổnđịnhvậnhành.tăngngânsáchads15/tuầntheoluật
