# M7 — Sẵn sàng đầu vào và cặp S2–S1-ctx

| Mục | Nguồn | Trạng thái |
|---|---|---|
| So sánh đã lưu S2 (TabFM, recent context) vs S1-ctx (LightGBM, cùng context) | `14_matched_lightgbm_tabfm_comparisons.csv` trong gói handoff (SHA-256 5078e321…62030, khớp manifest gói) | **Có**: 180 hàng = 12 transition × 15 seed, mỗi (transition, seed) đúng 1 hàng |
| Cùng context | Cột `same_context` = True cả 180 hàng; file 08: S2 và S1-ctx cùng hash context XY và eval_X ở 180/180 cặp | VERIFIED (theo hồ sơ khóa) |
| Comparability key / row binding | `comparability_key` (transition, seed, raw ZIP SHA, CSV member, membership, ordered rowid, ordered label hash) có ở mọi hàng. File 10: "mọi comparability_key, ordered binding và support được assert khớp" | VERIFIED theo hồ sơ; Cloud **không** kiểm lại được vì không có vector |
| Ngưỡng | Ngưỡng lịch sử riêng của mỗi arm (`threshold_policy`), giữ nguyên | Có |
| Trạng thái metric | AUC, AP, Recall@τ: VALID 180/180 cho cả hai arm. Precision@τ: LightGBM VALID 134 / LOW_SUPPORT 38 / NULL 8; TabFM 152 / 23 / 5 (precision không dùng cho phân loại) | Có |
| QA dấu | AUC/AP/Recall sign QA đều ROBUST_* trong 180 cặp này; không có dấu NUMERICALLY_UNRESOLVED (42 dấu chưa chứng nhận không nằm ở cặp/metric này) | Có |
| Đối chiếu nguồn | Cờ recall = 0 do M7 tính khớp 180/180 với `LightGBM_zero`/`TabFM_zero` đã lưu; tổng recall = 0 khớp file 10 (S2 21/180, S1-ctx 29/180) | Khớp |
| Vector điểm theo flow | Không có trong gói (cố ý loại) | **MISSING** → không có chẩn đoán phân phối điểm |
| Support | P = 453–9.054 (P ≥ 20 và N ≥ 20 ở mọi cặp) | Đủ |

Kết luận: đủ điều kiện phân tích **read-only trên metric đã lưu**. Không đủ cho phân tích phân phối điểm.

Định nghĩa dùng (áp lại tham chiếu đã khóa của D3, không đặt cutoff mới):
- **Threshold failure** = Recall@τ = 0 (R2).
- **Ranking failure** = AUC ≤ 0,5 hoặc AP ≤ π của chính tập eval (R1a/R1b).
- **H1 witness** = không ranking failure nhưng có threshold failure.
