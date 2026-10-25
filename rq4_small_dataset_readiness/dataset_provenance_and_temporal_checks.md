# CIC-IDS2017 Improved — nguồn gốc và kiểm tra thời gian

**Phương pháp:** đọc các trang công khai ngày 2026-10-25 bằng WebFetch/WebSearch. **Không tải** bất kỳ tệp dữ liệu nào. Lệnh `curl` lấy danh sách thư mục bị reset kết nối; danh sách được lấy lại qua WebFetch.

**Mức xác minh:**
- **PAGE** = đọc trực tiếp trên trang của tác giả.
- **SECONDARY** = lấy từ nguồn thứ ba.
- **MISSING** = chưa xác minh được.

## 1. Bản phát hành
| Mục | Giá trị | Mức |
|---|---|---|
| Tên / nguồn | "CIC-IDS-2017 improved", bản tái tạo từ PCAP gốc bằng CICFlowMeter đã sửa, nhãn gán lại bằng logic công khai. Liu, Engelen, Lynar, Essam, Joosen, "Error Prevalence in NIDS datasets: A Case Study on CIC-IDS-2017 and CSE-CIC-IDS-2018", IEEE CNS 2022, tr. 254–262 | PAGE |
| Tệp phát hành | `CICIDS2017_improved.zip`, **328M**, last-modified **2023-04-27 16:41** (thư mục `/CNS2022/Datasets/`). Không thấy số phiên bản hay changelog | PAGE |
| Checksum do tác giả công bố | Không thấy | MISSING |
| Danh sách CSV theo ngày bên trong zip, số flow theo ngày, schema cột | Trang không liệt kê | **MISSING** (chỉ biết được khi mở zip) |
| Mã gán nhãn | `github.com/GintsEngelen/CNS2022_Code` (`CICIDS2017_labelling_fixed_CICFlowMeter.ipynb`); README tự gọi là "research code" | PAGE |
| CICFlowMeter đã sửa | `github.com/GintsEngelen/CICFlowMeter`. Ghi chú: "Changed the timestamp to output in UTC and with microsecond precision"; sửa FIN/RST termination, Bulk features, TCP segmentation offload; thêm ICMP | PAGE |
| Nhãn "Attempted" | Flow thuộc khung tấn công nhưng không có hành vi độc hại. Tác giả: "When in doubt, we recommend to relabel all 'Attempted' flows as Benign"; "Under no circumstance should the 'Attempted' flows be treated as a separate label"; nhận diện qua `Attempted Category != -1` | PAGE |
| Giấy phép | Không thấy trên trang Improved. Trang UNB: "publicly available for researchers", yêu cầu trích dẫn | MISSING / PAGE |
| Mirror | Không dùng; chưa so mirror nào với bản của tác giả | — |

## 2. Bản gốc (UNB) — lịch capture
Thứ Hai 3/7/2017 9:00 đến thứ Sáu 7/7/2017 17:00, tổng **5 ngày** (PAGE):

| Ngày | Tấn công (khung giờ theo UNB) |
|---|---|
| Thứ Hai 3/7 | **Chỉ benign** |
| Thứ Ba 4/7 | FTP-Patator 9:20–10:20; SSH-Patator 14:00–15:00 |
| Thứ Tư 5/7 | slowloris 9:47–10:10; Slowhttptest 10:14–10:35; Hulk 10:43–11:00; GoldenEye 11:10–11:23; Heartbleed 15:12–15:32 |
| Thứ Năm 6/7 | Web attack (brute force, XSS, SQLi) 9:20–10:42; Infiltration 14:19–15:45 |
| Thứ Sáu 7/7 | Botnet ARES 10:02–11:02; PortScan 13:55–15:29; DDoS LOIT 15:56–16:16 |

PCAP mỗi ngày nặng 7,8–13 GB.

## 3. Đánh giá cho thí nghiệm kiểm soát FAR với phản hồi trễ
| Tiêu chí | Đánh giá | Trạng thái |
|---|---|---|
| Benign có thứ tự thời gian, đủ cho batch streaming | Có khả năng đủ: cả 5 ngày đều có benign. Số lượng theo bản Improved chưa xác minh | **MISSING** (số flow) |
| Attack có thứ tự thời gian | Tấn công là **các đợt có kịch bản ngắn** (13–90 phút), mỗi ngày một họ khác nhau, và **không lặp lại giữa các ngày**. Thứ Hai không có attack | Rủi ro cao |
| Timestamp dùng được để sắp xếp | Bản Improved xuất UTC với độ chính xác micro giây theo ghi chú công cụ. Bản gốc có vấn đề lệch giờ, đã biết trong văn liệu | PAGE (công cụ); MISSING (chưa kiểm trên CSV) |
| Toàn vẹn nhãn | Tốt hơn bản gốc (logic công khai, có Attempted). Phải xử lý Attempted → Benign theo khuyến cáo, quyết định trước khi xem dữ liệu | PAGE |
| Rò rỉ | Các cột Flow ID, IP, port, timestamp phải loại khỏi đặc trưng. Đợt tấn công gắn với IP/thời gian cố định nên dễ thành shortcut | Rủi ro, xử lý được bằng giao thức |
| Phản hồi trễ có ý nghĩa | Vì đợt tấn công ngắn, nếu độ trễ nhãn ≥ độ dài đợt thì phản hồi về sau khi đợt đã kết thúc. Mọi điều chỉnh ngưỡng chỉ còn tác động lên benign, tức là lên FAR, không lên recall của đợt đó | Giới hạn cấu trúc |
| Tách phát triển / xác nhận | Chỉ có 5 ngày (4 ngày có attack, mỗi ngày một họ). Tách theo ngày thì giai đoạn xác nhận chỉ gồm 1–2 ngày với họ tấn công **khác** giai đoạn phát triển, nên không có lặp lại để ước lượng biến thiên | Rủi ro cao |
| Benign là lưu lượng giả lập (B-Profile agents) | Trôi dạt benign có thể nhỏ và không thực tế; phép thử FAR dưới drift có ít nội dung | Rủi ro |
| Quyết định trước đó của dự án | Đề cương v9.16, Bảng 2.1: CIC-IDS2017 là "Bộ dự phòng; **tránh dùng cho trôi dạt thời gian vì lịch tấn công theo ngày**" | Mâu thuẫn với mục đích đề xuất |

**Kết luận dữ liệu:** Bản Improved có nguồn gốc rõ và nhãn tốt hơn bản gốc. Tuy nhiên, để dùng cho kiểm soát FAR với phản hồi trễ thì chưa đạt:
- Còn MISSING số flow theo ngày, schema và kiểm tra thứ tự timestamp trên CSV thật.
- Cấu trúc 5 ngày, đợt tấn công ngắn và không lặp lại giới hạn mạnh mọi kết luận.
- Đề cương hiện hành đã loại bộ này cho phân tích trôi dạt thời gian.
