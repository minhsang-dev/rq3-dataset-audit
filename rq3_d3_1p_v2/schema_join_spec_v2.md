# Schema join specification v2 — D3-1

Đây là schema logic yêu cầu, dựa trên dữ kiện đã xác minh (12 transition × 15 seed × 6 arm = 1.080 unit; 180 tập eval; 1.080 ngưỡng nối theo unit). **Tên cột/tệp thật và kết quả J1–J12 chi tiết nằm trong báo cáo local, không truy cập được từ Cloud**; cột `tên thật` để người nghiên cứu điền. Không tạo dữ liệu thay thế.

## 1. Bảng logic
| Bảng | Khóa chính | Số hàng kỳ vọng | Cột bắt buộc | Tên thật |
|---|---|---|---|---|
| `transitions` | `transition_id` | 12 | `dataset_id, source_day, target_day` | _điền_ |
| `seeds` | `seed_id` | 15 | — | _điền_ |
| `arms` | `arm_id` | 6 | `protocol_class, train_source_def` (xem `arm_registry_mapping.md`) | _điền_ |
| `eval_sets` | `(transition_id, seed_id)` | **180** | `eval_set_hash, n, P, N` | _điền_ |
| `eval_rows` | `(transition_id, seed_id, eval_row_id)` | `Σ_(t,s) n_(t,s)` | `y_binary`, [`attack_family`] | _điền_ |
| `units` | `(transition_id, seed_id, arm_id)` | **1.080** | `threshold_id` hoặc nhúng `tau` | _điền_ |
| `predictions` | `(transition_id, seed_id, arm_id, eval_row_id)` | `6 × Σ n_(t,s)` | `score` | _điền_ |
| `thresholds` | `(transition_id, seed_id, arm_id)` | **1.080** | `tau, provenance_path, provenance_sha256` | _điền_ |
| `artifact_registry` | `path` | — | `sha256, size_bytes, role` | _điền_ |

## 2. Thay đổi so với v1
| v1 | v2 | Lý do |
|---|---|---|
| `eval_rows` khóa `(transition_id, eval_row_id)` | khóa **`(transition_id, seed_id, eval_row_id)`** | eval thay đổi theo seed; một `eval_row_id` có thể xuất hiện ở nhiều seed |
| một `eval_set_hash` mỗi transition | **một hash mỗi `(transition_id, seed_id)`** (180 hash) | `E_(t,s)` |
| khóa ngưỡng `(dataset_id, source_day, arm_id)` | **`(transition_id, seed_id, arm_id)`** (= unit), 1.080 ngưỡng | ngưỡng đã nối theo full unit/seed |
| `n, P, N, π` mỗi transition | mỗi `(t,s)` | baseline/support theo seed |
| `coverage_gaps` bảng bắt buộc | bỏ khỏi bảng bắt buộc | false alert/giờ-ngày DROPPED |
| 2 hoặc 3 arm | **6 arm lịch sử** | dữ kiện xác minh |

## 3. `eval_set_hash`
`eval_set_hash(t,s)` = SHA-256 của chuỗi UTF-8 gồm các `eval_row_id` (dạng chuỗi, đúng như lưu) sắp xếp tăng dần (theo kiểu gốc của khóa: số nếu số, chuỗi từ điển nếu chuỗi), nối bằng `\n`, kết thúc `\n`. Quy ước tuần tự hóa phải ghi lại khi tính (kiểu khóa, bộ sắp xếp). Cần **một** hash mỗi `(t,s)`, **bằng nhau** giữa 6 arm; và `y_hash(t,s)` (SHA-256 của chuỗi `eval_row_id:y` theo cùng thứ tự) để xác nhận nhãn lấy từ một nguồn duy nhất. `comparability_key = (transition_id, seed_id, eval_set_hash)`.

## 4. Quy tắc join
1. `predictions ⋈ eval_rows` trên `(transition_id, seed_id, eval_row_id)`: với mỗi `(t,s,a)` là 1:1 trên toàn bộ `eval_rows` của `(t,s)`. `y` luôn lấy từ `eval_rows`, không từ `predictions`.
2. `units ⋈ thresholds` 1:1 trên `(transition_id, seed_id, arm_id)`.
3. `predictions` nối `units` trên `(transition_id, seed_id, arm_id)`; threshold của unit chỉ dùng cho unit đó.
4. Không dùng `target_day` nhãn để tạo/chỉnh ngưỡng; ngưỡng đọc nguyên từ artifact đã khóa.
5. Không tính metric trên dữ liệu gộp nhiều `(t,s)`.

## 5. Kiểm tra toàn vẹn (J1–J12, đã sửa)
Số thứ tự J giữ nguyên để đối chiếu với `schema_J1_J12_review.csv` (local). **Cloud không thấy kết quả từng J**; người nghiên cứu báo tổng thể "artifact verification PASS".
| Mã | Kiểm tra v2 | Hậu quả nếu hỏng |
|---|---|---|
| J1 | `transitions` = 12 hàng hợp lệ, không trùng `(dataset, source, target)` | BLOCK |
| J2 | `source_day ≠ target_day`; mọi `eval_rows` của `(t,s)` thuộc `target_day` của t | BLOCK |
| J3 | `seeds` = 15; `arms` = 6; `units` = 12×15×6 = 1.080, khóa không trùng | BLOCK |
| J4 | Khóa `predictions` không trùng | BLOCK |
| J5 | `predictions` = `6 × Σ n_(t,s)` (thiếu → `MISSING_PREDICTIONS`, không điền) | liệt kê |
| J6 | Cả 6 arm của `(t,s)` có **cùng** `eval_set_hash` và `y_hash`; `eval_sets` = 180 hash | `EVAL_SET_MISMATCH` |
| J7 | Hàng eval không giao với hàng huấn luyện/context của bất kỳ arm nào trong cùng `(t,s)` (nếu có thông tin) | BLOCK |
| J8 | `score` không NaN, trong [0,1] | unit `INVALID_SCORES` |
| J9 | `y_binary ∈ {0,1}`; ghi số hàng null bị loại theo `(t,s)` | báo cáo |
| J10 | 1.080 ngưỡng đủ, mỗi ngưỡng có `provenance_sha256` khớp `artifact_registry`; ngưỡng không phụ thuộc nhãn ngày đích | `THRESHOLD_MISSING` / BLOCK |
| J11 | Khi so sánh arm: cùng `comparability_key` | không so sánh |
| J12 | Mọi tệp đầu vào có SHA-256 khớp giá trị đã khóa | BLOCK |
| J13 (mới) | `eval_row_id` ghép được duy nhất trong `(t,s)`; ghi mức chồng lấn `eval_row_id` giữa các seed của cùng transition (mô tả, không loại) | báo cáo |

## 6. Bảng đầu ra (định dạng)
`T_evalset(t,s)`: `n,P,N,π,dropped_null,eval_set_hash,y_hash` · `T_unit(t,s,a,metric,subset)`: `value,status,reason_code,flags` · `T_pair(t,s,a,b,metric)`: `Δ,status` · `T_trans(t,a,metric)` · `T_trans_pair(t,a,b,metric)` · `T_global`.
