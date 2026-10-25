# Kiểm toán số liệu và nguồn cho mục M7

**Phương pháp.** Mỗi con số trong `m7_thesis_ready_section_vi.md` được đối chiếu như sau:

- Đếm lại trực tiếp từ `m7_tabfm_failure_specificity/failure_pair_matrix.csv` (180 hàng) bằng thao tác đếm và trung vị mô tả. Không có kiểm định thống kê, không tính lại metric gốc, không dùng vector điểm.
- So với các báo cáo M7.
- So với `10_h1_h2_verdict.md` và `14_matched_lightgbm_tabfm_comparisons.csv` của gói handoff. SHA-256 của tệp 14 kiểm lại là `5078e321…bf6c0`, khớp với hồ sơ.

## 1. Số liệu khớp (PASS)
| Số liệu trong mục | Giá trị viết | Đếm lại từ ma trận | Nguồn đối chiếu | Trạng thái |
|---|---|---|---|---|
| Số cặp; số (lần chuyển, seed) khác nhau; số lần chuyển | 180; 180; 12 | 180; 180; 12 | M7-R, M7-T | PASS |
| Thất bại ngưỡng: cả hai / chỉ TabFM / chỉ LGBM / không | 20 / 1 / 9 / 150 | 20 / 1 / 9 / 150 | M7-T, M7-V | PASS |
| Thất bại xếp hạng: cả hai / chỉ TabFM / chỉ LGBM / không | 14 / 12 / 16 / 138 | 14 / 12 / 16 / 138 | M7-T, M7-V | PASS |
| Tổng biên thất bại ngưỡng S2 / S1-ctx | 21 / 29 | 21 / 29 | `10` (S2 21/180, S1-ctx 29/180) | PASS |
| Tổng biên thất bại xếp hạng TabFM / LGBM | 26 / 30 | 26 / 30 (tổng theo lần chuyển 12+10+4; 13+4+9+4) | M7-T | PASS (số suy từ bảng 2×2) |
| Cờ recall = 0 tính lại khớp cờ nguồn | 180/180 | 180/180 (`src_*_zero` so với `*_threshold_failure`) | M7-R | PASS |
| Nhân chứng H1 TabFM / LGBM | 18 / 12 | 18 / 12 | `10` (S2 18, S1-ctx 12) | PASS |
| Lần chuyển có thất bại ngưỡng | 4/12 | 4 | M7-T | PASS |
| Cặp có thất bại ngưỡng nằm ở hai lần chuyển CIC (02-16→02-20: 15; 03-01→03-02: 13) | 28/30 | 28/30 | M7-T | PASS |
| Lần chuyển có thất bại xếp hạng | 4/12 | 4 | M7-T | PASS |
| Thất bại xếp hạng TabFM/LGBM ở 02-16→02-20; 03-01→03-02; ToN 04-24→04-25; ToN 04-27→04-28 | 0/13; 12/4; 10/9; 4/4 | giống hệt | M7-T | PASS |
| Lần chuyển không có thất bại nào ở cả hai loại | 7 | 7 (hợp của hai tập 4 lần chuyển là 5) | suy từ M7-T | PASS (số suy) |
| CIC 02-16→02-20: AUC trung vị TabFM; khoảng theo seed | 0,998; 0,9973–0,9994 | 0,998; 0,9973–0,9994 | M7-T, M7-S | PASS |
| CIC 02-16→02-20: recall@τ TabFM bằng 0 | 15/15 | 15/15 (max = 0,0) | M7-S | PASS |
| CIC 02-16→02-20: ngưỡng S2 bằng 0,5 | 14/15 | 14/15 (một seed 0,93) | ma trận | PASS (chi tiết mới, chỉ đếm) |
| CIC 02-16→02-20: AUC trung vị LGBM; AP trung vị LGBM / TabFM | 0,372; 0,210 / 0,972 | 0,372; như M7-T | M7-T | PASS |
| CIC 02-16→02-20: LGBM thất bại xếp hạng; nhân chứng H1 của LGBM | 13/15; 2 | 13/15; 2 (AUC 0,873 và 0,974) | ma trận | PASS |
| 12 cặp chỉ TabFM thất bại xếp hạng, 8 cặp ở CIC 03-01→03-02 | 8/12 | 8 (còn 2 ở ToN 04-24→04-25, 2 ở ToN 04-27→04-28) | ma trận | PASS |
| Các trung vị ở Bảng [x.y]-2 | chép từ M7-T | đã kiểm AUC trung vị bằng đếm lại | M7-T | PASS |
| P tối thiểu–tối đa; N tối thiểu | 437–9.106; 894 | 437–9.106; 894 (tệp 14 cũng cho 437–9.106) | ma trận, tệp 14 | PASS (xem D1) |
| Trạng thái VALID của AUC, AP, Recall; QA dấu | 180/180; ROBUST | QA dấu đều ROBUST_* | M7-R | PASS |
| Phán quyết M7 | SHARED_OR_MIXED_FAILURE | — | M7-V | PASS |

## 2. Điểm lệch phát hiện (báo cáo, KHÔNG tự hòa giải trong nguồn)
| Mã | Điểm lệch | Xử lý |
|---|---|---|
| **D1** | `input_and_pair_readiness.md` ghi "P = 453–9.054". Cả ma trận M7 và tệp 14 đều cho **P = 437–9.106**. Giá trị 453 trùng với cận dưới support của các đơn vị recall bằng 0 trong báo cáo H2. Có thể M7-R đã chép nhầm khoảng này. | Mục luận án dùng 437–9.106 (từ ma trận và tệp 14, có trích nguồn). **Không sửa** M7-R. Điểm lệch không ảnh hưởng phân loại, vì P ≥ 20 và N ≥ 20 ở mọi cặp theo cả hai khoảng. |
| **D2** | Đề bài viết "LightGBM tương ứng thất bại cả xếp hạng lẫn ngưỡng" ở CIC 02-16→02-20. Theo ma trận, điều này đúng ở **13/15** seed. Hai seed còn lại có AUC > 0,5 và là nhân chứng H1 của LightGBM. | Mục luận án ghi chính xác 13/15 cùng hai nhân chứng H1. |
| **D3** | `final_verdict.md` của M7 viết thất bại xếp hạng riêng TabFM "tập trung ở một transition (CIC 03-01→03-02)". Thực tế 8/12 cặp nằm ở đó, 4 cặp còn lại nằm ở hai lần chuyển ToN. | Mục luận án ghi 8/12, không viết "chỉ ở một lần chuyển". Không sửa M7-V. |
| **D4** | `score_distribution_diagnostics.md` viết "điểm của các flow tấn công … nằm dưới ngưỡng lịch sử". Đây là suy ra trực tiếp từ recall@τ = 0, không phải quan sát phân phối điểm. | Mục luận án nói rõ đây là suy ra từ định nghĩa, không phải phân tích phân phối. |

Không có điểm lệch nào thay đổi các bảng 2×2 hay phán quyết M7.

## 3. Ranh giới đã giữ
- Không có giá trị p, khoảng tin cậy, cỡ hiệu ứng hay hình vẽ.
- Mọi trung vị đều chép từ M7-T; riêng các khoảng AUC theo seed và các đếm phụ được đọc trực tiếp từ ma trận.
- Không sửa artifact LOCKED hay tệp M7 nào.
