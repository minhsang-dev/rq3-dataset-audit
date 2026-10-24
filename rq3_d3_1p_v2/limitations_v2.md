# Giới hạn v2 — D3-1

1. **Báo cáo local không đọc được trong Cloud** (`protocol_compatibility_findings.md`, `schema_J1_J12_review.csv`, `open_decisions_O1_O10.md`, `final_verdict.md`). Mọi dữ kiện về artifact trong v2 là lời khai của người nghiên cứu, không tự kiểm. Ba quyết định REVISE chưa biết.
2. **Tên 6 arm, tên cột, kiểu dữ liệu, kích thước `n/P/N` từng `E_(t,s)`** không có trong Cloud; mọi tham số `PROPOSED` (P_min, N_min, ngân sách, bin, số seed hợp lệ tối thiểu) chọn không dựa trên phân phối thực của support.
3. **12 transition** không độc lập hoàn toàn (cùng dataset, có thể chung ngày). 15 seed là lặp đo; **tập eval đổi theo seed**, nên biến thiên giữa seed gồm cả eval resampling lẫn biến thiên mô hình/context và không tách được. Không có suy luận thống kê, không CI, không p-value.
4. **Hiệu cặp theo seed** chỉ mô tả trên cùng `E_(t,s)`; không phải bằng chứng về sự khác biệt tổng quát.
5. **Baseline oracle:** `π` và `π(1−π)` dùng nhãn của chính `E_(t,s)` làm tham chiếu mô tả, không phải bộ dự đoán khả thi.
6. **Không hiệu chuẩn, không đặt ngưỡng mới:** ECE/Brier phản ánh điểm gốc; 1.080 ngưỡng dùng nguyên.
7. **False alert theo thời gian bị bỏ:** chỉ có FAR theo flow và `FA_count` trên `E_(t,s)`; không có tuyên bố về tần suất cảnh báo sai theo giờ/ngày hay toàn mạng.
8. **Family:** `FAM_AP` (benign vs family f) và `FAM_RECALL_BUDGET` (cạnh tranh trong ngân sách chung) khác bản chất; family xuất hiện/seed thay đổi; phụ thuộc có nhãn family (O9).
9. **Ngân sách nhỏ** (`b=0.001`, `K<20`) có thể thường xuyên `LOW_SUPPORT`; chưa biết do thiếu `n_(t,s)`.
10. **Study 1** không phải confirmation độc lập; không kết luận nhân quả; không kết luận tính mới thuật toán; không tuyên bố phê duyệt.
11. Các gate CTU-SME-11 (E2/E2b) không cung cấp bằng chứng cho D3-1.
12. Tệp dữ liệu thật không được mở; kiểm thử "20/20 PASS" trên dữ liệu nhân tạo của người nghiên cứu không được kiểm lại; 7 golden vectors G1–G7 trong dictionary là tính tay và chưa chạy.
