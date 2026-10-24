# Final go / no-go — Gate E2b-PREFLIGHT

> Preflight về **chi phí và phương pháp** thu thập metadata. Không phải Gate E2b, không phải independent confirmation, không có mô hình hay metric hiệu năng.

## Quyết định: **GO_LIMITED**

Cần **một lần tải dữ liệu nhỏ có mục tiêu, trần cứng 20 MB**, kèm câu hỏi gửi tác giả chạy song song (không chặn). Tải toàn phần (96.6 GB) và dò block nhiều ngày (chưa chứng minh) **không** được đề xuất ở giai đoạn này.

## Lý do (bằng chứng)
1. Không có bảng nhãn hay thống kê nhãn công bố riêng (Zenodo 14 tệp, mirror trống, không có Data in Brief) ⇒ phải lấy từ archive hoặc từ tác giả.
2. Cả 11 archive mở đầu bằng ngày 2023-02-22 và `labels.config` nằm ngay đầu; vì thế **từ vựng quy tắc ngày đó của 11 thiết bị đã lấy được với chi phí 0 thêm** và `conn.log.labeled` ngày đó của các thiết bị còn lại ước tính ≈ 10–15 MB.
3. Từ vựng ngày 3 đã cho thấy nhãn malicious **không đồng nhất** (6 cấu trúc, tên khác thesis, 1 quy tắc cho 2 malware). Điều này đủ để biết rằng taxonomy cần giải thích của tác giả trước khi dùng cho Mode A.
4. Truy cập ngẫu nhiên các ngày sau chưa chứng minh được trong ngân sách (9/9 probe 400 KB không có block hoàn chỉnh); không nên chi hàng trăm MB hay hàng chục GB cho tới khi có kết quả từ bước giới hạn.

## Trả lời các câu hỏi của preflight
| # | Câu hỏi | Trả lời |
|---|---|---|
| 1 | Xuất/xác minh báo cáo Gate E2 | 9 tệp trong `gate_e2_outputs/`, đã push; SHA-256 8/8 OK |
| 2 | Có bảng nhãn đầy đủ hoặc bản Zeek riêng? | **Không** (`archive_access_options.md` §2) |
| 3 | Cấu trúc archive, Range, truy cập ngẫu nhiên | Range **có**; không index; ngày đầu định vị miễn phí; ngày sau phải dò (chưa chứng minh) |
| 4 | Phân biệt loại nhãn | Làm xong ở mức quan sát (`taxonomy_evidence_gaps.md` §2); **không** ánh xạ |
| 5–6 | Hai lựa chọn và bảng dung lượng | `minimal_data_acquisition_plan.md` §2 |
| 7 | Bất thường | 98.3% = Ubuntu ngày 4–5 (có bằng chứng thesis); Ubuntu ngày 4 = quét Kinsing (thesis), nhãn thực chưa biết; iPhone 1,031: **nguyên nhân chưa xác định**; bất thường mới A1–A7 |
| 8 | Taxonomy chưa xác minh có chặn trước feature mapping/Mode A/B? | Feature mapping: **không**. Mode A: **có**. Mode B: **một phần** (cần số flow theo loại; vẫn chỉ offline/oracle). |

## Bước cần phê duyệt (một bước)
**Gate E2b-LIMITED** — bắt buộc tuân thủ:
- Range tiền tố tới hết `conn.log.labeled` của ngày 2023-02-22 cho tối đa 9 archive; **tổng ≤ 20 MB**; không tải archive đầy đủ; nếu vượt cap thì dừng và báo cáo.
- Chỉ kiểm kê `label × detailedlabel` và đối chiếu số dòng với `Day3TotalFlows`; chỉ chấp nhận member có độ dài khớp header tar.
- Gửi tác giả danh sách câu hỏi (a)–(f) ở `minimal_data_acquisition_plan.md` §3.
- Không mô hình, không metric hiệu năng, không chỉnh RQ3; dữ liệu confirmation không dùng để điều chỉnh phương pháp.
- Mở rộng (ngày 4–5 / toàn phần) cần phê duyệt riêng sau khi có kết quả.

## Điều kiện chuyển sang HOLD hoặc NO-GO
- **HOLD** nếu bước giới hạn cho thấy nhãn ngày 3 không thể đối chiếu với `Day3TotalFlows` hoặc tác giả không trả lời về ý nghĩa nhãn mà Mode A cần.
- **NO-GO** nếu việc định nghĩa sự kiện Mode A hợp lệ cần ≥ vài chục GB hoặc tác giả xác nhận nhãn không có tên họ cho phần lớn malware; khi đó CTU-SME-11 chỉ còn dùng được như dataset benign/malicious thô (không đủ cho nghiên cứu first-appearance) và chuyển sang xét CIDDS-001.

## Không tuyên bố
Không nói CTU-SME-11 đã được xác nhận độc lập; không dùng số lượng flow lớn làm bằng chứng cho Mode A/B.
