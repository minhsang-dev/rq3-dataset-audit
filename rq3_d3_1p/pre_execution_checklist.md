# Pre-execution checklist — D3-1E (CPU, chỉ tính metric từ dự đoán đã lưu)

D3-1E **không được bắt đầu** cho đến khi mọi mục A và B đạt và người nghiên cứu ký khóa. `[ ]` = chưa làm.

## A. Phê duyệt và khóa giao thức
- [ ] A1. Người nghiên cứu đọc và duyệt `D3-1_protocol.md`, `metric_dictionary.*`, `schema_join_spec.md`, `aggregation_specification.md`, `failure_decision_rules.md`, `limitations.md`.
- [ ] A2. Quyết định O1–O10 (`D3-1_protocol.md` §10) được điền; mọi giá trị `PROPOSED` được xác nhận hoặc thay.
- [ ] A3. Bản giao thức phiên bản cuối commit; SHA-256 của `manifest_sha256.md` được người nghiên cứu ghi lại làm khóa.
- [ ] A4. Xác nhận lại: D3-1E chỉ tính từ dự đoán đã lưu (O1); không inference, không GPU.

## B. Xác minh artifact trên máy Windows (NEEDS_LOCAL_VERIFICATION)
- [ ] B1. Liệt kê tệp artifact và SHA-256 (ví dụ PowerShell: `Get-FileHash -Algorithm SHA256 <tệp>`); điền `artifact_registry`; checksum khớp giá trị đã khóa từ trước (nếu có) — nếu không có checksum cũ, ghi `NO_PRIOR_CHECKSUM` và dùng giá trị hiện tại như mốc, không mô tả là xác minh nguồn gốc.
- [ ] B2. Danh sách đúng **12 transition hợp lệ** (id, dataset, source-day, target-day), không trùng; ghi transition dùng chung ngày.
- [ ] B3. Đúng **15 seed** cho mỗi (transition, arm); danh sách `seed_id`.
- [ ] B4. Ba arm có mặt và khớp định nghĩa (§2); liệt kê arm khác nếu có.
- [ ] B5. Cột có sẵn: `eval_row_id`, `y_binary`, `score`, `transition_id`, `arm_id`, `seed_id`; có/không `attack_family`, `ts`; kiểu dữ liệu của `score` (float32/64).
- [ ] B6. J1–J12 (`schema_join_spec.md` §3) đạt; `eval_set_hash` đồng nhất giữa arm/seed; không giao giữa eval và huấn luyện/ngữ cảnh.
- [ ] B7. `τ_hist`: có đủ cho mọi `(dataset_id, source_day, arm_id)` cần; nguồn và `provenance_sha256`; granularity (O6).
- [ ] B8. Metadata khoảng thiếu dữ liệu (O7) hoặc xác nhận không có (khi đó chỉ `FA_day_unadjusted`).
- [ ] B9. Kiểm kê các giá trị `score` NaN/ngoài [0,1]/đúng 0 hoặc 1 (đếm, không sửa).

## C. Mã và môi trường
- [ ] C1. Mã tính metric đóng băng (commit hash ghi vào manifest D3-1E); CPU-only, phiên bản thư viện ghi lại.
- [ ] C2. Kiểm thử đơn vị với golden vectors G1–G5 (`metric_dictionary.md` §7) **trước** khi đọc dự đoán thật; kết quả kỳ vọng khớp.
- [ ] C3. Mã không có tham số nào được đặt sau khi xem kết quả; không có đường dẫn nào chạy mô hình, hiệu chuẩn, conformal, selective prediction, context retrieval hay tìm ngưỡng.
- [ ] C4. Đầu ra ghi vào thư mục mới, không ghi đè artifact RQ3 đã khóa.

## D. Điều kiện dừng trong D3-1E
- Bất kỳ J-check BLOCK hỏng → dừng và báo; không sửa dữ liệu.
- Phát hiện cần thay đổi quy tắc → dừng; ghi `POST_HOC_EXPLORATORY`; xin duyệt phiên bản giao thức mới.
