# Giới hạn và điều chưa thể xác minh

## 1. Phạm vi bằng chứng
- Mọi kết luận về CTU-SME-11 đến từ: metadata Zenodo, README, summary table, luận văn Bendl 2023, và **2 device-day mẫu** (Echo 2023-02-22; Windows7AD 2023-02-22) lấy bằng HTTP Range 10 MiB đầu của hai archive.
- 2/77 device-day (≈2.6%) và 0 trong số các ngày có malware đa dạng (chỉ Trickbot C2 và quét Internet được thấy). **Không khẳng định đã kiểm tra toàn bộ flow.**
- CIDDS-001/002: chỉ trang chính thức và 2 technical report; không mở dữ liệu, không xem GitHub scripts, không đọc 2 paper.
- Không xác thực hash file lớn (không tải). Zenodo chỉ công bố MD5; md5 của README.md và summary_table.csv đã tải khớp Zenodo.

## 2. UNKNOWN trọng yếu (cần xử lý trước khi chuyển sang kiểm toán dữ liệu)
| # | UNKNOWN | Vì sao quan trọng | Cách xử lý |
|---|---|---|---|
| U1 | Tập đầy đủ giá trị `detailedlabel` và số flow theo giá trị × thiết bị × ngày | Quyết định coarse attack type, Mode A/B, số lượng cho k=100 | Kiểm kê nhãn (không mô hình) trên toàn bộ conn.log.labeled hoặc ít nhất trên labels.config |
| U2 | `Unknown` có phải `background` (Zenodo) hay không | Xử lý nhãn, định nghĩa negative class | Đối chiếu trên dữ liệu thật + hỏi tác giả nếu cần |
| U3 | Mức flow-level của nhãn (độ chính xác, bao phủ ngoài danh sách IP) | Nhãn là rule IP; không có xác minh độc lập | Chỉ ghi nhận hạn chế; không sửa nhãn |
| U4 | Danh sách 46 đặc trưng RQ3 | Không biết giao/khác với conn.log | Cần hồ sơ RQ3 đã khóa (read-only) – ngoài phạm vi repo |
| U5 | Lịch sử sử dụng dataset trong toàn bộ luận án (G) | Repo trống (không commit/tệp). Không có inventory máy nghiên cứu | Cần inventory từ máy nghiên cứu; ghi UNKNOWN |
| U6 | iOS: 1,031 flow Malicious nhưng luận văn nói không có malicious | Nhiễu nhãn có thể có ở thiết bị "sạch" | Xem flow cụ thể ở bước sau |
| U7 | Lệch 6,901 flow giữa thesis (2,223,630) và summary table (2,216,729) khi loại Ubuntu ngày 4–5 | Mâu thuẫn nhỏ giữa tài liệu | Ghi nhận; không trọng yếu |

## 3. Giới hạn đã biết của dataset (từ tài liệu)
1. Mất cân bằng cực đoan: 98.3% Malicious; một thiết bị/ngày (Ubuntu ngày 4) = 95.8% tổng flow.
2. Số lượng "họ" nhỏ: 14 malware, mỗi cái thường trên 1 thiết bị 1 lần; 1 tấn công người (Metasploit) và 1 kịch bản exfiltration DNS; còn lại là scan Internet.
3. Lịch sử 7 ngày, left-censoring (Android đã chạy trước; IP công khai bị scan ngay từ ngày 1).
4. Khoảng thiếu capture: VirtualBox capture bật muộn cho VM1/VM2; inter-VM chỉ gộp ngày 5–6 trên 3 máy; IPv4 only; lệch thời gian ms–giây ở pcap gộp. 21 thiết bị ngoài thí nghiệm có mặt trong log.
5. Nhãn rule-based theo IP; IP ≈ nhãn.
6. Không có timestamp xác nhận nhãn ⇒ Mode B chỉ offline/oracle.
7. Múi giờ: tài liệu dùng CET, `ts` là UTC.
8. Summary table: header có khoảng trắng cuối (`Day7TotalFlows `) — lưu ý khi đọc bằng parser.

## 4. Điều audit này KHÔNG làm
Không train/inference/GPU, không metric hiệu năng, không phát triển thuật toán, không chỉnh hồ sơ RQ3, không dùng dữ liệu để chỉnh phương pháp, không tuyên bố independent confirmation. Gate E2 chỉ nói về sự phù hợp metadata.

## 5. Rủi ro phương pháp của chính audit
- Hai mẫu được chọn vì là hai archive nhỏ nhất/thuận tiện, **không phải mẫu ngẫu nhiên** → không đại diện toàn bộ nhãn.
- Chọn ngày 2023-02-22 là ngày đầu tiên có trong tar của cả hai archive (không chọn theo kết quả).
- WebFetch tóm tắt trang CIDDS bằng mô hình nhỏ; các trích dẫn CIDDS quan trọng đã đối chiếu với PDF technical report (PDF là nguồn chính).
