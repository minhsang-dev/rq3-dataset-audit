# Schema join specification — D3-1

Đây là **schema logic yêu cầu** của D3-1E, không phải mô tả artifact thực. Tên cột/tệp thật: `NEEDS_LOCAL_VERIFICATION`. Không tạo dữ liệu thay thế.

## 1. Bảng logic
| Bảng | Khóa chính | Cột bắt buộc | Cột tùy chọn | Nguồn (cần xác minh cục bộ) |
|---|---|---|---|---|
| `transitions` | `transition_id` | `dataset_id`, `source_day`, `target_day`, `is_valid` | `source_split_id` | danh sách 12 transition hợp lệ |
| `eval_rows` | `(transition_id, eval_row_id)` | `y_binary` (0/1), `eval_set_hash` | `attack_family` (nullable), `ts` (epoch UTC), `flow_group_id` | tập đánh giá cố định theo transition |
| `arms` | `arm_id` | `arm_family` ∈ {TABFM, LGBM_MATCHED, LGBM_FULL}, `train_source_def` | `context_id`, `n_train_rows` | đăng ký arm |
| `predictions` | `(transition_id, arm_id, seed_id, eval_row_id)` | `score` (float) | `context_id` | dự đoán đã lưu |
| `thresholds_hist` | `(dataset_id, source_day, arm_id)` (có thể thô hơn) | `tau`, `provenance_path`, `provenance_sha256` | `threshold_rule` | ngưỡng lịch sử đã phục hồi |
| `coverage_gaps` | `(dataset_id, target_day, gap_start, gap_end)` | `gap_type` | `source` | metadata khoảng thiếu dữ liệu |
| `artifact_registry` | `path` | `sha256`, `size_bytes`, `role`, `verified_on` | `source_url` | kiểm kê checksum |
| `seeds` | `seed_id` | — | — | 15 seed |

## 2. Quan hệ và quy tắc join
1. `predictions ⋈ eval_rows` trên `(transition_id, eval_row_id)`: với mỗi `(transition_id, arm_id, seed_id)` quan hệ phải **1:1 trên toàn bộ `eval_rows` của transition**. `y` luôn lấy từ `eval_rows`, **không bao giờ** từ `predictions`.
2. `eval_set_hash` = SHA-256 của danh sách `eval_row_id` đã sắp xếp; **bằng nhau** cho mọi arm, mọi seed của cùng transition. Khác → `EVAL_SET_MISMATCH`.
3. `thresholds_hist` nối với `predictions` bằng `(dataset_id, source_day, arm_id)` (dataset/source_day lấy từ `transitions`). Không nối theo `target_day`.
4. `coverage_gaps` nối bằng `(dataset_id, target_day)`; chỉ dùng cho `H_valid`.
5. `eval_row_id` là khóa **chính của dòng đánh giá, không phải khóa flow thô**; không dùng IP, flow ID thô, mã kịch bản, `ts` thô hay `attack_family` làm đặc trưng ở D3-1E (D3-1E không huấn luyện).

## 3. Kiểm tra toàn vẹn bắt buộc (BLOCK nếu hỏng)
| Mã | Kiểm tra | Hậu quả nếu hỏng |
|---|---|---|
| J1 | `transitions` chứa đúng 12 hàng `is_valid=1`, không trùng `(dataset_id, source_day, target_day)` | BLOCK |
| J2 | `source_day ≠ target_day` và mọi hàng eval thuộc `target_day` | BLOCK |
| J3 | Mỗi `(transition, arm)` có đúng 15 `seed_id` khác nhau (đúng tập `seeds`) | BLOCK (hoặc ghi arm-seed thiếu là `MISSING_PREDICTIONS`) |
| J4 | Khóa `(transition_id, arm_id, seed_id, eval_row_id)` không trùng | BLOCK |
| J5 | Số hàng dự đoán = `Σ_t n_eval(t) × n_arm × 15` (trừ thiếu đã ghi) | liệt kê thiếu; không điền |
| J6 | `eval_set_hash` đồng nhất giữa arm/seed trong transition | `EVAL_SET_MISMATCH` |
| J7 | Tập hàng đánh giá **không giao** với hàng huấn luyện/ngữ cảnh của bất kỳ arm nào (theo `eval_row_id`/nguồn gốc) | BLOCK (rò rỉ) |
| J8 | `score` không NaN, trong `[0,1]` | arm-seed `INVALID_SCORES` |
| J9 | `y_binary ∈ {0,1}`, null bị loại trước metric; ghi số loại theo transition | báo cáo |
| J10 | `thresholds_hist` có `provenance_sha256` khớp `artifact_registry` | `THRESHOLD_MISSING` |
| J11 | Ba arm có cùng `transition_id`/`eval_set_hash` khi so sánh | nếu không, không so sánh |
| J12 | Mọi tệp đầu vào có `sha256` trong `artifact_registry` và khớp giá trị đã khóa | BLOCK |

## 4. Đầu ra trung gian (định dạng, không có giá trị)
| Bảng đầu ra | Khóa | Nội dung |
|---|---|---|
| `T_seed` | `(transition_id, arm_id, seed_id, metric_id, subset)` | `value`, `reason_code`, `flags` |
| `T_transition` | `(transition_id, arm_id, metric_id, subset)` | mean/sd/min/max/median qua seed, `n_valid_seeds` |
| `T_contrast` | `(transition_id, arm_pair, metric_id)` | hiệu seed-mean, nhãn tách khoảng seed |
| `T_global` | `(arm_id|arm_pair, metric_id, subset)` | thống kê mô tả qua transition, danh sách giá trị từng transition |
| `T_evalset` | `(transition_id)` | `n, P, N, π, n_dropped_null, eval_set_hash` |
