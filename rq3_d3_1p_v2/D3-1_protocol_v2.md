# D3-1P v2 — Giao thức D3-1 sau kiểm toán tương thích artifact (DRAFT, chưa được duyệt)

**Trạng thái:** DRAFT chờ người nghiên cứu duyệt. **Không** phải bản đã được phê duyệt hay đã khóa. **Chưa thực thi D3-1E.**
**Thay thế:** `rq3_d3_1p/` (v1, bị BLOCK bởi kiểm toán artifact cục bộ). v1 giữ nguyên, không sửa.
Bước này không chạy metric, không mở/phân tích kết quả hiệu năng, không huấn luyện/suy luận TabFM, không chỉnh ngưỡng bằng nhãn ngày đích, không sửa artifact Study 1/2.

## 0. Nguồn của v2 và điều chưa đọc được
| Nguồn | Trạng thái |
|---|---|
| Giao thức D3-1P v1 (`rq3_d3_1p/`) | Đã đọc |
| Dữ kiện artifact do người nghiên cứu cung cấp trong yêu cầu v2 (xem §1) | Dùng nguyên văn, **không tự kiểm lại được trong Cloud** |
| `protocol_compatibility_findings.md`, `schema_J1_J12_review.csv`, `open_decisions_O1_O10.md`, `final_verdict.md` (local) | **Không có trong repo/phiên Cloud.** Không đọc được; **không suy đoán** nội dung, kể cả việc ba quyết định nào là REVISE |
| Danh sách tên 6 arm lịch sử, tên cột/khóa thật, giá trị n/P/N từng tập eval | Không có → để trống trong `arm_registry_mapping.md` và `schema_join_spec_v2.md` |

## 1. Dữ kiện đã xác minh (do người nghiên cứu cung cấp; Cloud không tự kiểm)
1. Study 1: **12 transition đủ điều kiện × 15 seed × 6 arm = 1.080 primary unit**.
2. **180 tập eval riêng biệt** = 12 transition × 15 seed; **các arm dùng cùng tập eval trong cùng (transition, seed)**; tập eval **thay đổi giữa các seed**.
3. **1.080 ngưỡng phục hồi** đã nối đúng theo full unit/seed.
4. Score TabFM là **xác suất binary attack hợp lệ** (theo mã nguồn).
5. Kiểm thử định nghĩa trên dữ liệu nhân tạo: 20/20 PASS. Artifact verification PASS. Giao thức v1: BLOCK.
Nhãn: các dữ kiện này đến từ báo cáo local, không từ artifact mà Cloud đã mở.

## 2. Đơn vị nghiên cứu (thay đổi so với v1)
| Cấp | Định nghĩa v2 | Số lượng | Vai trò |
|---|---|---|---|
| **Transition** `t` | dataset × source-day → target-day | 12 | Đơn vị phân tích chính; tổng hợp cuối ở cấp này |
| **Seed** `s` | lặp trong transition | 15/transition | Chỉ là lặp đo; **không** phải mẫu thống kê độc lập |
| **Tập eval `E_(t,s)`** | tập đánh giá của (t, s); **chung cho mọi arm**, **đổi theo seed** | 180 | Cơ sở tính prevalence, baseline, support **riêng từng seed** |
| **Arm** `a` | 6 arm lịch sử (§3) | 6 | Điều kiện so sánh |
| **Primary unit** `(t,s,a)` | một dự đoán đầy đủ trên `E_(t,s)` + ngưỡng của chính unit | 1.080 | Đơn vị tính metric |
| Eval row | `eval_row_id` trong `E_(t,s)` | thay đổi | Quan sát phụ thuộc, không độc lập |

**Thay mô hình v1:** `E_t` (một tập eval cho mỗi transition) → **`E_(t,s)`**. Mọi mệnh đề "tập đánh giá của transition" trong v1 được hiểu là "tập đánh giá của (transition, seed)". Cấm: pool hàng của các `E_(t,s)` khác nhau để tính một metric; cấm dùng 15 seed hoặc số flow làm cỡ mẫu.

## 3. Sáu arm lịch sử (ràng buộc 6)
- Giữ **đủ 6 arm** như hiện hữu; mỗi arm có `arm_id` lịch sử riêng; **không gộp, không loại, không đổi tên** khi chưa có quyết định của người nghiên cứu.
- Tên và định nghĩa của 6 arm **không có trong Cloud**; `arm_registry_mapping.md` là bảng cần người nghiên cứu điền (cột `historical_arm_id`, `protocol_class`, `train_source_def`).
- Lớp giao thức (`protocol_class`) dùng để phân biệt: `TABFM`, `LGBM_MATCHED` (LightGBM trên đúng tập context mà TabFM nhận), `LGBM_FULL` (LightGBM trên toàn bộ source-day), và `OTHER` (arm lịch sử khác; không suy đoán nội dung).
- Chỉ so sánh các kết quả có **cùng (transition, seed, eval rows)** và `train_source_def` được khai báo.
- So sánh cặp chính (primary pair) và các cặp thứ cấp do người nghiên cứu chỉ định (mục **D-A1**). Mặc định khi chưa chỉ định: 15 cặp C(6,2) chỉ báo cáo **mô tả**, không cặp nào là "primary".

## 4. Khóa so sánh arm (ràng buộc 3)
`comparability_key = (transition_id, seed_id, eval_set_hash(E_(t,s)))`.
- Hai arm chỉ được so sánh trên cùng `comparability_key`. So sánh giữa seed khác nhau **không** được ghép cặp (eval khác).
- Khác biệt arm mô tả bằng **hiệu cặp theo seed** `Δ_(t,s) = v_(t,s,a) − v_(t,s,b)` (§7, ràng buộc 8), chỉ trên các seed hợp lệ cho cả hai arm.
- Không "khoảng seed không chồng" như v1 (v1 so min/max giữa các tập eval khác nhau — không còn hợp lệ).

## 5. Binary toàn bộ attack và family-vs-benign (ràng buộc 7)
| Phân tích | Tập | Metric | Baseline |
|---|---|---|---|
| **Binary all-attack** | `E_(t,s)` (`y=1` mọi attack) | ROC-AUC, AP, Brier, ECE, precision/recall tại τ, ngân sách | AP: `π_(t,s)`; Brier: `π(1−π)` của chính `E_(t,s)` |
| **Family-vs-benign AP** | `E_(t,s,f)` = benign ∪ attack family `f` của `E_(t,s)` (attack family khác bị loại) | ROC-AUC, AP, `AP_excess` | `π_(t,s,f) = P_f/(P_f+N)` |
| **Family recall tại global alert budget** | xếp hạng và ngân sách `K` đặt trên **toàn bộ** `E_(t,s)` (mọi family + benign) | `Recall_f@K = E[TP_f]/P_f` | cận trên `min(P_f,K)/P_f` |
| **Family recall tại τ** | toàn `E_(t,s)`, cảnh báo `score ≥ τ` | `TP_f/P_f` | — |

**Hai phân tích family khác bản chất, không so sánh trực tiếp:** AP family-vs-benign chỉ xếp hạng benign với family `f`; recall tại global budget bị ảnh hưởng bởi cạnh tranh từ các family khác trong cùng top-K. Không tính ROC-AUC/AP trên tập chỉ gồm positive (`POSITIVES_ONLY`). Brier/ECE không báo cho family-vs-benign.
Family có `P_f < P_min` → `LOW_SUPPORT`. Sự có mặt nhãn family ở mức eval row: xem **O9** (chưa xác minh trong Cloud).

## 6. Ngưỡng
- 1.080 ngưỡng lịch sử đã phục hồi, **khóa theo unit `(transition_id, seed_id, arm_id)`**; đã nối đúng full unit/seed (dữ kiện §1.3). Quy tắc cảnh báo `score ≥ τ`.
- Không chọn lại ngưỡng, không dùng nhãn ngày đích để tạo/chỉnh ngưỡng, không calibration, conformal, selective prediction, context retrieval, biến đổi điểm.
- Điểm TabFM hợp lệ trong `[0,1]` (dữ kiện §1.4); điểm ngoài `[0,1]` hoặc NaN của bất kỳ arm nào → unit đó `INVALID_SCORES`.

## 7. Tổng hợp (ràng buộc 5, 8)
`unit (t,s,a)` → hiệu cặp theo seed → transition-arm / transition-pair → mô tả qua 12 transition. Chi tiết: `aggregation_specification_v2.md`. Không bootstrap CI bắt buộc, không p-value, không coi seed hay flow là mẫu độc lập.

## 8. False alert
- **FAR ≡ FPR = FP/N**, theo flow, **riêng từng `E_(t,s)`**.
- Báo thêm **số cảnh báo sai `FP` trên `E_(t,s)`** (đếm, không chuẩn hóa thời gian).
- **Bỏ** diễn giải "false alerts/giờ" hoặc "false alerts/ngày toàn mạng" (v1 M13/M14). Lý do: tập eval thay đổi theo seed nên `E_(t,s)` không được chứng minh là toàn bộ quần thể một ngày/mạng (đây là suy luận từ dữ kiện §1.2; chưa kiểm). Chỉ phục hồi nếu báo cáo local xác nhận đồng thời: (i) `ts` cấp flow, (ii) metadata khoảng thiếu, (iii) `E_(t,s)` là toàn bộ quần thể quan sát hợp lệ của ngày đích. Mặc định: **DROPPED**.

## 9. Failure (ràng buộc 10)
Cờ mô tả, đánh giá **theo seed** trên chính `E_(t,s)`, không nhân quả, không chọn nguyên nhân "dominant", cho phép nhiều cờ cùng xuất hiện. Chi tiết: `failure_decision_rules_v2.md`.

## 10. Phát biểu được/không được phép
Không khẳng định nguyên nhân nhân quả; không kết luận tính mới thuật toán; Study 1 **không** là confirmation độc lập; không kết luận ngoài 12 transition; không tuyên bố giao thức đã được phê duyệt.

## 11. Giải quyết O1–O10
Xem `O1_O10_resolution.md`. Tóm tắt: O6 được giải quyết bằng dữ kiện đã xác minh; các mục còn lại cần người nghiên cứu phê duyệt hoặc chờ báo cáo local; ba mục REVISE **không được xác định** trong Cloud.

## 12. Trạng thái cuối: **PARTIAL**
Điều kiện còn phải đáp ứng trước khi có thể xếp **READY_FOR_APPROVAL**:
1. Cung cấp (hoặc dán các phần liên quan của) bốn báo cáo local để đối chiếu O1–O10, xác định ba quyết định REVISE và kết quả J1–J12.
2. Điền `arm_registry_mapping.md` (6 arm lịch sử → lớp giao thức) và chỉ định cặp primary (D-A1).
3. Người nghiên cứu xác nhận các tham số `PROPOSED` (P_min/N_min, ngân sách, bin, số seed hợp lệ tối thiểu).
4. Xác nhận khả dụng nhãn family ở eval row (O9) và ý định về false-alert theo thời gian (O7; mặc định DROPPED).
5. Người nghiên cứu duyệt và ghi lại SHA-256 của `manifest_sha256.md`.
Không chuyển sang D3-1E (CPU) khi chưa đủ năm điều kiện.
