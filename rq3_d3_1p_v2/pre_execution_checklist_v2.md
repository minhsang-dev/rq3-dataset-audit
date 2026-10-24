# Pre-execution checklist v2 — D3-1E (CPU; chỉ tính metric từ dự đoán đã lưu)

D3-1E **không được bắt đầu** cho đến khi mọi mục A–C đạt. `[ ]` = chưa thực hiện/xác nhận.

## A. Quyết định và khóa
- [ ] A1. Người nghiên cứu cung cấp/đối chiếu 4 báo cáo local; xác định **ba quyết định REVISE** và cập nhật `O1_O10_resolution.md`.
- [ ] A2. Điền `arm_registry_mapping.md` (6 arm), chỉ định cặp primary hoặc xác nhận "15 cặp mô tả".
- [ ] A3. Xác nhận/thay các tham số PROPOSED: `P_min=N_min=20`, `b`, `K`, 10 bin, `n_valid ≥ 8`, chính sách low-support (D-L1).
- [ ] A4. Xác nhận O7 (false alert theo thời gian = DROPPED) và O8 (không đặt quy tắc alert flood).
- [ ] A5. Xác nhận O9 (nhãn family; nếu không có → `NO_FAMILY_LABEL`).
- [ ] A6. Người nghiên cứu duyệt v2 và ghi lại SHA-256 của `manifest_sha256.md`. **(Chưa thực hiện; tôi không tuyên bố đã duyệt.)**

## B. Xác minh dữ liệu (local; đã báo PASS, cần ghi lại vào hồ sơ)
- [ ] B1. 12 transition; 15 seed; 6 arm; 1.080 unit; 180 `eval_set_hash`; `y_hash` đồng nhất giữa arm.
- [ ] B2. 1.080 ngưỡng khớp unit, `provenance_sha256` khớp; xác nhận không chỉnh bằng nhãn ngày đích.
- [ ] B3. Điểm của 6 arm: kiểu số, [0,1], NaN, bản chất xác suất (TabFM đã xác nhận); số hàng dự đoán = `6 × Σ n_(t,s)`.
- [ ] B4. Kiểm kê (không sửa): `n_(t,s), P_(t,s), N_(t,s)`; số seed có low-support theo từng metric **trước khi chốt A3 nếu có thể**; chồng lấn `eval_row_id` giữa seed (J13).
- [ ] B5. Mọi SHA-256 đầu vào ghi vào `artifact_registry`; không sửa artifact Study 1/2.

## C. Mã và môi trường
- [ ] C1. Mã metric đóng băng (commit hash), CPU-only, phiên bản thư viện.
- [ ] C2. Unit test G1–G7 + 20 test nhân tạo hiện có PASS trước khi đọc dự đoán thật.
- [ ] C3. Không đường mã nào huấn luyện/suy luận, hiệu chuẩn, conformal, selective prediction, context retrieval hay tìm ngưỡng.
- [ ] C4. Đầu ra ghi vào thư mục mới, không ghi đè artifact đã khóa.

## D. Điều kiện dừng
J-check BLOCK hỏng → dừng, báo, không sửa dữ liệu. Muốn đổi quy tắc → `POST_HOC_EXPLORATORY` hoặc phiên bản giao thức mới.
