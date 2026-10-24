# Final verdict — Gate E2 (chỉ về metadata suitability)

> Đây **không** phải independent confirmation và không phải kết quả hiệu năng. Kết luận dựa trên tài liệu công khai, schema, và 2 device-day mẫu.

## Phán quyết: **PARTIAL** cho CTU-SME-11

Không có FAIL ở tiêu chí bắt buộc (`dataset_decision_matrix.csv`: 9 PASS, 7 CONDITIONAL, 3 UNKNOWN trên 19 tiêu chí bắt buộc). Ba UNKNOWN trọng yếu: **B2/B4 (từ vựng nhãn và coarse attack type), G1 (lịch sử sử dụng)**. Được phép chuyển sang kiểm toán dữ liệu đầy đủ **với điều kiện** các UNKNOWN này được xử lý và Mode B được mô tả là offline/oracle.

## Trả lời bắt buộc

1. **CTU-SME-11 có đủ điều kiện để tiếp tục kiểm tra dữ liệu thật không?** Có, có điều kiện (PARTIAL). Độc lập về nguồn, CC-BY-4.0, DOI/version, Zeek logs + nhãn + cấu hình nhãn công khai. Điều kiện: kiểm kê nhãn, quy tắc lấy mẫu chống thống trị bởi Ubuntu ngày 4, và xử lý G1.
2. **Descriptive labels có đủ chi tiết cho Mode A/B không?** **Chưa chứng minh được.** Thấy `Discovery` (hành vi, gồm scan Internet nền) và `Malware_C2-Trickbot` (tên họ nhưng chỉ cho C2). Chưa thấy nhãn cho 13 malware còn lại, exfiltration, Metasploit. Không thể xác định coarse attack type mà không tự suy diễn taxonomy. Mode B chỉ là offline retrospective/oracle vì không có timestamp xác nhận nhãn.
3. **Schema có đủ cho so sánh TabFM–LightGBM công bằng không?** Có, **trên bộ đặc trưng conn.log rút gọn (14–16 trường) cố định trước cho cả hai mô hình**; không tái hiện được 46 đặc trưng RQ3 (không có danh sách; thiếu thống kê theo gói). Phải loại IP, uid, ts, `detailedlabel`, mã thiết bị/ngày.
4. **Vấn đề chưa giải quyết:** U1–U7 trong `limitations_and_unknowns.md` — đặc biệt từ vựng `detailedlabel` đầy đủ và số lượng theo loại; `Unknown` vs `background`; nhãn rule-based theo IP; lịch sử sử dụng (repo trống); danh sách đặc trưng RQ3; iOS có Malicious bất ngờ; mất cân bằng cực đoan.
5. **CIDDS-001/002 có cần kiểm tra tiếp không?** Giữ CIDDS-001 làm dự phòng **chưa kiểm tra tiếp ngay** (chỉ xét nếu CTU bị loại sau kiểm kê nhãn): có ground-truth tấn công (4 loại hành vi thô, 92 tấn công, 4 tuần) nhưng schema nghèo (≤7 đặc trưng), license không rõ, không có DOI. **CIDDS-002 không đáng kiểm tra tiếp** cho mục đích này: chỉ port scan nên Mode A không khả thi, báo cáo kỹ thuật không nhất quán.

## Một bước tiếp theo đề xuất (cần phê duyệt; **chưa thực hiện**)

**Gate E2b — Kiểm kê nhãn CTU-SME-11 (không mô hình).** Phê duyệt ngoại lệ vượt giới hạn 100 MB để **stream** (không lưu pcap) các archive CTU-SME-11 nhằm chỉ giữ `labels.config` và đếm `label × detailedlabel` theo thiết bị × ngày từ `conn.log.labeled`. Vì trong tar mỗi ngày có pcap nằm giữa các ngày, việc stream phải đọc qua cả pcap: tổng truyền ≈ 97 GB cho cả 11 thiết bị (có thể giới hạn cho các archive có malware ≈ 31 GB: Win7full-1/2/3, Win7AD, Android, RPi — nhưng khi đó sẽ không kiểm kê được Ubuntu/macOS/Echo/Chromecast/iPhone). Đầu ra chỉ là bảng kiểm kê nhãn + ánh xạ mốc thời gian CET↔UTC; không AUC/F1, không huấn luyện. Phê duyệt cần chọn phạm vi (cả 11 hoặc 6 archive) và cho phép hạn mức dữ liệu.
