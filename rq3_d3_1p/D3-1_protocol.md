# D3-1P — Giao thức D3-1 (bản dự thảo để duyệt và khóa)

**Trạng thái tài liệu:** DRAFT chờ người nghiên cứu duyệt · **Bước:** Protocol Review and Freeze Preparation · **Chưa thực thi D3-1E.**
Bước này **không** chạy metric, không mở kết quả hiệu năng, không vẽ biểu đồ từ dự đoán, không kết luận failure.

## 0. Nguồn của bản dự thảo và điều chưa kiểm
- Repo Cloud (`minhsang-dev/rq3-dataset-audit`, nhánh `claude/sleepy-cray-jhn01z`) chỉ có các báo cáo Gate E2, E2b-PREFLIGHT, E2b-LIMITED. **Không có** tài liệu D3-1 gốc, danh sách transition, bảng dự đoán, ngưỡng lịch sử, hay checksum artifact RQ3. Bản này được soạn từ 12 ràng buộc trong yêu cầu, nên mọi dữ kiện gắn với artifact đều là `NEEDS_LOCAL_VERIFICATION` (xem `artifact_availability_check.md`).
- Các giá trị tham số do tôi **đề xuất** (đánh dấu `PROPOSED`) cần người nghiên cứu xác nhận hoặc thay trước khi khóa. Không giá trị nào được chọn dựa trên kết quả.
- Các gate CTU-SME-11 trước đó không thuộc D3-1; không dùng chung dữ liệu hay kết luận.

## 1. Đơn vị nghiên cứu (ràng buộc 1)
| Cấp | Định nghĩa | Vai trò thống kê |
|---|---|---|
| **Transition** `t` | `dataset × source-day → target-day`; **12 transition hợp lệ**, giữ nguyên | **Đơn vị phân tích độc lập (cố gắng)**; mọi tổng hợp suy luận ở cấp này |
| Arm `a` | TabFM · LightGBM matched-context · LightGBM full-source-day (§2) | Điều kiện so sánh bên trong transition |
| Seed `s` | **15 seed**, lặp bên trong mỗi (transition, arm) | Lặp đo; **không** phải mẫu độc lập |
| Eval row | `eval_row_id` trong tập đánh giá của transition | Quan sát phụ thuộc (flow lặp, cùng host, cùng ngày); **không** phải mẫu độc lập |

Quy tắc cứng:
1. Không nhân số seed hay số flow để tăng cỡ mẫu (ví dụ không tuyên bố n = 12×15 hay n = số flow).
2. Các transition cùng dataset, hoặc dùng chung một ngày làm nguồn/đích, **không độc lập đầy đủ**; báo cáo kèm nhóm dataset (xem `aggregation_specification.md`) và không dùng p-value cho n = 12.
3. Không gộp (pool) flow của nhiều transition để tính một metric duy nhất.
4. `NEEDS_LOCAL_VERIFICATION`: danh sách 12 transition (định danh, ngày) và 15 seed id cụ thể.

## 2. Các arm và tính so sánh được (ràng buộc 2)
| arm_id | Nguồn huấn luyện / ngữ cảnh | Ghi chú |
|---|---|---|
| `TABFM` | Ngữ cảnh (support/context rows) lấy từ source-day theo định nghĩa D3-1 | Định nghĩa chính xác của context: `NEEDS_LOCAL_VERIFICATION` |
| `LGBM_MATCHED` | LightGBM huấn luyện **trên đúng tập context** mà TabFM nhận (cùng hàng, cùng cỡ) | Đối chứng **cùng lượng thông tin** với TabFM |
| `LGBM_FULL` | LightGBM huấn luyện trên **toàn bộ source-day** | Đối chứng **khác ngân sách thông tin**; không đồng nhất với hai arm kia |

Khóa so sánh (`comparability_key`) = `(transition_id, eval_set_hash, train_source_def)`.
- Chỉ so sánh hai arm khi **cùng `transition_id` và cùng `eval_set_hash`** (hash SHA-256 của danh sách `eval_row_id` đã sắp xếp), và `train_source_def` của từng arm được ghi rõ.
- So sánh **chính (primary):** `TABFM` vs `LGBM_MATCHED`. **Thứ cấp (descriptive):** `TABFM` vs `LGBM_FULL`, và `LGBM_MATCHED` vs `LGBM_FULL` (hiệu ứng kích thước nguồn). Không xếp hạng ba arm trong một bảng duy nhất mà không nêu `train_source_def`.
- Nếu một arm có tập đánh giá khác (hàng bị loại, thiếu dự đoán) → mọi so sánh với arm đó dừng, ghi `EVAL_SET_MISMATCH`; không tính lại trên giao nhau thầm lặng.
- Nếu artifact có thêm arm ngoài ba arm trên → liệt kê, mặc định **loại khỏi D3-1E** cho đến khi người nghiên cứu duyệt.

## 3. Tập đánh giá và nhãn (ràng buộc 5)
- Tập đánh giá `E_t` của transition `t` là cố định, **không phụ thuộc arm hay seed**.
- **Binary toàn bộ attack:** `y = 1` nếu flow thuộc bất kỳ lớp attack nào, `y = 0` nếu benign. Đây là bộ metric chính.
- **Family-vs-benign (phân tích riêng, mô tả):** với mỗi family `f` có mặt trong `E_t`: `E_{t,f}` = benign ∪ attack có family `f`; **attack của family khác bị loại** (không là âm, không là dương); `y = 1` cho `f`. Điểm số vẫn là điểm của mô hình nhị phân (không huấn luyện mô hình family).
- Tập chỉ gồm positive (không có benign trong `E_{t,f}`) → **không tính ROC-AUC** và không tính AP (prevalence = 1); chỉ báo recall tại ngưỡng lịch sử/ngân sách, kèm mã `POSITIVES_ONLY`.
- Family với `P_f < P_min` → `LOW_SUPPORT`, chỉ mô tả.
- Bộ binary và bộ family được báo cáo ở **bảng tách biệt**; không trộn số liệu.
- Nhãn null/NA ở bước join: loại **trước** mọi metric và áp dụng giống nhau cho mọi arm; số hàng bị loại được ghi (§schema).

## 4. Metric (ràng buộc 3, 4, 6)
Định nghĩa đầy đủ ở `metric_dictionary.md` / `.csv`. Tóm tắt:
| Nhóm | Metric | Baseline/tham chiếu |
|---|---|---|
| Xếp hạng | ROC-AUC, **Average Precision (AP)** | AUC: 0.5; **AP: prevalence của chính `E_t` (hoặc `E_{t,f}`)** — **không dùng 0.5 chung**; báo thêm `AP−π` và `AP/π` |
| Xác suất | Brier, ECE (10 bin đều) | Brier: `π(1−π)` (oracle-constant, mô tả); ECE: không có chuẩn "chance" |
| Ngưỡng lịch sử `τ_hist` | precision, recall, alert count, **FAR = FPR theo flow = FP/N** | — |
| Ngân sách cảnh báo | precision@K, recall@K với K cố định trước (rate và tuyệt đối), tie xử lý bằng kỳ vọng | Cận trên `min(P,K)/K`, `min(P,K)/P` |
| Thời gian | **False alerts per valid observed hour/day** (khác FAR theo flow) | Chỉ khi có `ts` và metadata phủ |

## 5. Ngưỡng (ràng buộc 7)
- Chỉ dùng `τ_hist` **đã phục hồi** từ artifact đã khóa. Mỗi `τ_hist` được tham chiếu theo khóa `(dataset_id, source_day, arm_id)`; độ chi tiết thật của khóa là `NEEDS_LOCAL_VERIFICATION`.
- Không tối ưu ngưỡng mới, không tính lại ngưỡng từ nhãn target, không hiệu chuẩn, **không** conformal, selective prediction, context retrieval hay lựa chọn context mới. Không biến đổi điểm số (không Platt/isotonic/temperature).
- Điểm số ngoài `[0,1]` hoặc NaN → arm-seed-transition đó `INVALID_SCORES`, mọi metric null; **không** chuẩn hóa/cắt.
- Quy tắc cảnh báo: `alert ⇔ score ≥ τ_hist` (bao gồm bằng).

## 6. Tổng hợp (ràng buộc 8) — xem `aggregation_specification.md`
Seed → trung bình/độ lệch/khoảng trong (transition, arm); sau đó mỗi transition một trọng số bằng nhau; **không bắt buộc bootstrap CI**; báo giá trị từng transition.

## 7. Failure (ràng buộc 9, 10) — xem `failure_decision_rules.md`
Nhãn failure chỉ gán bằng quy tắc định lượng khóa trước; quy tắc không có tham chiếu khách quan → `INDETERMINATE` + bằng chứng mô tả. Không nêu nguyên nhân nhân quả.

## 8. Phát biểu được phép và không được phép
Được: mô tả theo transition, số transition đáp ứng quy tắc / số transition đánh giá được, chênh lệch cặp trong cùng transition với cùng tập đánh giá.
Không được: nhân quả ("vì sao"), tính mới thuật toán, "độc lập confirmation" cho dữ liệu Study 1 (Study 1 là dữ liệu của nghiên cứu hiện hữu), p-value/khoảng tin cậy coi 15 seed hoặc flow là mẫu độc lập, tuyên bố vượt phạm vi 12 transition.

## 9. Kiểm tra artifact (ràng buộc 11)
Cloud không truy cập được artifact thực → tất cả `NEEDS_LOCAL_VERIFICATION`. Không dựng dữ liệu thay thế. Chi tiết: `artifact_availability_check.md`.

## 10. Giả định và quyết định mở cần người nghiên cứu xác nhận
| Mã | Nội dung | Đề xuất |
|---|---|---|
| O1 | D3-1E **chỉ tính metric từ dự đoán đã lưu trên CPU**, không chạy lại mô hình/inference | Giả định; nếu cần chạy lại mô hình → thay đổi phạm vi, cần duyệt riêng |
| O2 | `P_min = N_min` (ngưỡng low-support) | `PROPOSED` 20 |
| O3 | Ngân sách rate `b` | `PROPOSED` {0.001, 0.005, 0.01, 0.05} |
| O4 | Ngân sách tuyệt đối `K` | `PROPOSED` {100, 1000} |
| O5 | Số bin ECE | `PROPOSED` 10 bin đều rộng |
| O6 | Granularity của `τ_hist` (theo arm? theo nguồn-ngày?) | Cần đọc từ artifact |
| O7 | Có `ts` cấp flow và metadata khoảng thiếu dữ liệu ở bảng eval không | Cần đọc từ artifact; nếu không → FA theo giờ là `NO_TIMESTAMP` |
| O8 | Ngưỡng "alert flood" so với lịch sử | Không đặt → `INDETERMINATE` (xem failure rules) |
| O9 | Có nhãn family ở mức eval row không | Nếu không → bỏ phần family, ghi `NO_FAMILY_LABEL` |
| O10 | Dung sai tie giữa hai arm | Không dùng dung sai; dùng quy tắc tách theo khoảng seed |

## 11. Tệp của gói D3-1P
`D3-1_protocol.md` · `metric_dictionary.md` · `metric_dictionary.csv` · `schema_join_spec.md` · `aggregation_specification.md` · `failure_decision_rules.md` · `limitations.md` · `pre_execution_checklist.md` · `artifact_availability_check.md` · `manifest_sha256.md`
