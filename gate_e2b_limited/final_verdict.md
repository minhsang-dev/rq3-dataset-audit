# Final verdict — Gate E2b-LIMITED

> Chỉ về kiểm kê nhãn metadata của **một ngày (2023-02-22)**. Không phải independent confirmation. Không có mô hình, metric hiệu năng, hay thay đổi hồ sơ RQ3.

## Quyết định: **PARTIAL**

Lấy được dữ liệu đầy đủ cho ngày mẫu (11/11 archive, `COMPLETE`, 7.17 MB mạng mới, không request lỗi), số dòng khớp chính xác `Day3TotalFlows` của summary cho cả 11 thiết bị. Nhưng taxonomy, độ phủ và ánh xạ nhãn vẫn chưa đủ rõ để đề xuất bước metadata tiếp theo bằng dữ liệu; chưa rơi vào HOLD vì ngân sách không bị chạm, chưa NO-GO vì các mâu thuẫn còn lại đều có thể giải bằng câu trả lời của tác giả.

## Điều đã chứng minh bằng dữ liệu thật
1. Schema 23 trường conn.log giống hệt ở 11 tệp; 0 hàng lỗi; `label`/`detailedlabel` là hai cột cuối.
2. Mapping ngày: `2023-02-22` ↔ `Day3TotalFlows`, khớp duy nhất ở 11/11; tổng 306,172 flows.
3. Tập nhãn quan sát: 67 tổ hợp (57 Benign, 9 Malicious, 1 Unknown); **không có `background`**.
4. Quy tắc gán nhãn theo IP và thứ tự quy tắc; `Unknown` (8 thiết bị) và `Discovery` (3 honeypot) là quy tắc mặc định đầu tiên `srcIP/srcIp=all` của cấu hình.
5. README thiết bị dùng UTC+1; capture là cửa sổ ~24 h từ gói đầu.

## Điều còn chưa rõ (chi tiết ở `limitations_and_blockers.md`)
- `Unknown` ≟ `background`; nghĩa chuỗi `detailedlabel` và ánh xạ sang loại thô (Mode A bị chặn).
- Nhãn của 6 malware và 2 kịch bản người ở các ngày khác.
- Bất nhất: Nukesped (miner/RAT, 0 flow), PwnRing/PwnRig, VM1 (2 malware, 1 nhãn), VM3 `data_exfiltration` ↔ tcp/5888 `REJ`, Trickbot sớm 2 phút 42 giây, iPhone 1,031 malicious ở ngày khác.
- Lịch sử sử dụng dataset trong luận án vẫn UNKNOWN.

## Một bước tiếp theo cần phê duyệt (chưa thực hiện)
**Phê duyệt gửi sáu câu hỏi trong `author_questions_draft.md` cho tác giả CTU-SME-11 (email), rồi tạm dừng mọi tải dữ liệu thêm cho đến khi có phản hồi hoặc bạn quyết định tiếp.** Không tải thêm dữ liệu trong bước này; nếu tác giả không đáp, bước kế tiếp mới là quyết định có mở rộng ngày 4–5 hay chuyển sang CIDDS-001.
