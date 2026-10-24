# Changelog v1 → v2

| # | Thay đổi | Tài liệu bị ảnh hưởng |
|---|---|---|
| 1 | `E_t` → `E_(t,s)` (180 tập); n/P/N/π, AP baseline, Brier ref, support, low-support, K ngân sách **theo từng seed** | protocol, dictionary, schema, aggregation, failure |
| 2 | Khóa join `eval_rows` thêm `seed_id`; `eval_set_hash` theo `(t,s)` + `y_hash`; khóa ngưỡng = unit `(t,s,a)` (1.080); thêm J13 | schema |
| 3 | `comparability_key = (transition, seed, eval_set_hash)`; bỏ quy tắc "khoảng seed không chồng"; dùng hiệu cặp theo seed | protocol, aggregation, failure |
| 4 | Thống nhất valid seed / low-support / `n_valid ≥ 8`; bản chính và bản nhạy | aggregation |
| 5 | 6 arm lịch sử, không gộp/loại; bảng điền `arm_registry_mapping.md` | protocol, arm mapping |
| 6 | Tách `FAM_AP` (benign vs family) khỏi `FAM_RECALL_BUDGET` (ngân sách chung) và `FAM_RECALL_TAU` | dictionary |
| 7 | Bỏ false alerts/giờ-ngày (M14 DROPPED); thêm `FA_count` | dictionary, protocol, limitations |
| 8 | Failure flags đánh giá theo seed, đếm `k/n_valid`, không dominant cause | failure |
| 9 | Giải quyết O1–O10; thêm D-A1, D-V1, D-L1, D-K1 | O1_O10_resolution |
| 10 | Thêm golden vectors G6, G7 | dictionary |
v1 (`rq3_d3_1p/`) không bị sửa.
