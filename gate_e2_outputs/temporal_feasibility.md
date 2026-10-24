# Khả thi thời gian — Mode A / Mode B

Bằng chứng: summary table (Zenodo), luận văn Sec 4.2–4.3, 7, 8, 10, và timestamp của 2 device-day mẫu. Chưa quét toàn bộ flow.

## 1. Timestamp, độ dài, phạm vi
- Cấp flow: trường `ts` (epoch float, UTC) ở mọi dòng conn.log → **PASS**.
- 7 ngày capture (20–26/02/2023), 11 thiết bị, 77 device-day. Mỗi pcap cắt theo ngày UTC (thesis Sec 6.3). README/summary dùng giờ địa phương (CET, UTC+1): Start 01:0x ngày 20 → End 01:0x ngày 27 (summary table) vs README ngày "Capture stopped on: 2023-02-26". Không mâu thuẫn thực chất nhưng cần chuẩn hóa múi giờ khi dùng.
- Lịch sử quan sát cục bộ = **tối đa 7 ngày trên mỗi thiết bị**, rất ngắn so với khái niệm "first appearance trong lịch sử".

## 2. Điểm cần thận trọng về thời gian
| Vấn đề | Bằng chứng | Hệ quả |
|---|---|---|
| Ngày 1 và 7 thiết kế là sạch, nhưng thiết bị có IP công khai (RPi, Echo, Chromecast) nhận scan/tấn công Internet **ngay từ ngày 1** | Thesis Sec 4.3, 7.10; mẫu Echo ngày 3: 3,915/9,180 flow Malicious `Discovery` | "Ngày sạch" không đảm bảo không có Malicious |
| Thiết bị có trạng thái trước capture (Android đã chạy) | Sec 7.9 | Left-censoring: không biết hành vi trước 20/02 |
| VirtualBox capture không bật cho VM1/VM2 đến ngày 3 09:14 CET; inter-VM chỉ gộp cho ngày 5–6 trên 3 máy | Sec 7.2, 7.3, 8.1 | **Khoảng thiếu dữ liệu ≠ không có tấn công**; traffic nội bộ VM ngày 2–4 không có |
| Mất gói nhỏ khi tải cao; IPv4 only (không ARP/IPv6) | Sec 4.2, 6.3 | Một phần hành vi không quan sát được |
| Lệch thời gian ms–giây ở pcap VirtualBox gộp | Sec 8.1 | Không dùng đối sánh thời gian mịn hơn vài giây cho 3 máy đó |
| Cắt theo ngày làm flow dài bị chia | Sec 4.2 | Cần quy ước gán nhãn flow vắt ngày |
| Ubuntu: 95.8M/99.99M flow ở một ngày (ngày 4) | summary table | Pooling flow-level theo thời gian bị chi phối bởi một sự kiện |
| Số flow "không tính Ubuntu ngày 4–5": thesis 2,223,630; tính từ summary table 2,216,729 | Thesis Sec 10.3 vs summary | Lệch 6,901 chưa giải thích (UNKNOWN, không trọng yếu) |
| iOS: thesis "no malicious traffic" vs summary 1,031 flow Malicious | Sec 7.8 vs summary | Chưa rõ nguồn nhãn (UNKNOWN) |

## 3. Mode A — local first appearance của họ/loại tấn công
Định nghĩa dùng ở đây: lần xuất hiện đầu tiên **trong lịch sử quan sát của chính thiết bị đó** của một nhãn mô tả malicious.

- **Ground truth sự kiện có sẵn:** thesis ghi giờ thực thi từng malware theo CET (vd NanoCore trên Win VM1 7:42:35 CET ngày 2; RHADAMANTHYS 16:06:19 ngày 3; Kinsing 9:19:34 ngày 4) → có mốc đối chiếu độc lập với `ts` của nhãn.
- **Hạn chế:** (i) nhãn chỉ có tên họ cho C2 (đã thấy `Malware_C2-Trickbot`); (ii) giá trị nhãn cho các họ còn lại chưa quan sát; (iii) 14 malware rải trên 7 thiết bị (3 Win client, AD, Ubuntu, macOS, Android), mỗi malware thường chỉ trên một thiết bị một lần → "first appearance" gần như trùng sự kiện lây nhiễm thủ công, không phải sự xuất hiện tự nhiên; (iv) lịch sử cục bộ 7 ngày, ngày 1 là baseline.
- **Điều kiện cần để chạy Mode A (chưa thỏa):** kiểm kê đầy đủ `detailedlabel` per device-day, ánh xạ coarse type có nguồn, và đối chiếu `ts` đầu tiên với mốc thực thi.
- **Trạng thái:** CONDITIONAL (thiết kế được nhưng chưa chứng minh; số lượng "họ" nhỏ).

## 4. Mode B — retrospective few-shot, k ∈ {1, 5, 10, 30, 100}
**Không có timestamp xác nhận nhãn thực tế** (nhãn gán sau khi toàn bộ capture hoàn tất; cấu hình `labels.config` không có thời điểm). Do đó:

> **Mode B trên CTU-SME-11 chỉ là offline retrospective / oracle.** Không được tuyên bố thích nghi online, không mô phỏng độ trễ xác nhận, không suy ra "analyst đã biết lúc t".

Điều kiện tạo support/evaluation tách biệt:
1. Tách theo **thời gian** (support xảy ra trước evaluation theo `ts`) và theo **flow/uid** (không trùng); tốt hơn là tách theo **thiết bị-ngày** để tránh near-duplicate giữa support và evaluation (beaconing/scan lặp).
2. Cần ≥ k + (kích thước evaluation tối thiểu) flow cho **mỗi loại cần kiểm** → số flow theo loại **chưa biết** (UNKNOWN); với k=100 và loại hiếm (exfiltration DNS, Metasploit insider) có thể không đủ.
3. Khử trùng lặp / giới hạn mỗi (nguồn-IP, dịch vụ) trước khi chọn k.
4. Cố định seed và quy tắc chọn support **trước** khi xem evaluation; không dùng dữ liệu confirmation để chỉnh phương pháp.
5. Tách `label` benign: ngày 1 và 7 chứa traffic sạch nhưng không nên là bằng chứng "benign hoàn toàn" (xem §2).
- **Trạng thái:** CONDITIONAL (khả thi như oracle offline; số lượng theo loại UNKNOWN).

## 5. CIDDS (theo technical report)
- CIDDS-001: 4 tuần, ~32M flow, 92 tấn công (70 trong OpenStack, 22 tại external server), tuần 3–4 chỉ benign; `AttackType` ∈ {pingScan, portScan, bruteForce, dos}; có `Date first seen` và thời điểm tấn công trong `attack_logs` → Mode A khả thi hơn về mặt thời gian nhưng chỉ 3–4 loại thô; Mode B cũng chỉ offline oracle (nhãn gán sau, không có confirmation time).
- CIDDS-002: chỉ port scan (technical report ghi là "port scan data set", nhưng đoạn mô tả bị sao chép từ CIDDS-001 và liệt kê dos/bruteForce) → Mode A **không khả thi** (một loại); báo cáo ghi khoảng mất traffic 03:52–09:08 ngày 04/08 do lỗi OpenStack (ví dụ điển hình: khoảng thiếu không được coi là không có tấn công).
