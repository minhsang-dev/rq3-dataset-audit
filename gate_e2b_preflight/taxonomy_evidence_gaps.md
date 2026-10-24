# Khoảng trống bằng chứng taxonomy nhãn CTU-SME-11

Không tự suy diễn ánh xạ. Chỉ nêu cái gì quan sát được, ở đâu, và cái gì không.

## 1. Bằng chứng mới thu được trong preflight: `labels.config` của ngày 2023-02-22 (11/11 thiết bị)
`labels.config` là tệp quy tắc của NetflowLabeler; mỗi quy tắc có tiêu đề dạng `<label>, <detailedlabel>:`. Đây là **từ vựng nhãn mà cấu hình của ngày đó có thể gán**, **không phải** số đếm flow, và chỉ là 1 ngày trong 7 (ngày 3 theo thiết kế thí nghiệm). Không xem được flow cho 9/11 thiết bị.

Quy tắc **Malicious** và **Unknown** nguyên văn:

| Thiết bị (ngày 2023-02-22) | Quy tắc Malicious (nguyên văn) | Unknown |
|---|---|---|
| Windows7AD-1 | `From_malicious-To_malicious-Malware_C2-Trickbot` | `Unknown` (không có detailedlabel) |
| Windows7full-1 | `From_malicious-To_benign-Malware_C2_connection_attempt-DNS` | `Unknown` |
| Windows7full-2 | `From_malicious-To_malicious-Malware_C2-Redline_stealer` | `Unknown` |
| Windows7full-3 | `From_malicious-To_malicious-Malware_C2-Ingress_tool_access-RemcosRAT-HTTP` ; `From_malicious-To_malicious-Malware_data_exfiltration-RemcosRAT` | `Unknown` |
| Linux-Ubuntu2204-1 | `From_malicious-To_malicious-Malware-PwnRing_miner-HTTP` | `Unknown` |
| MacOS-Macbook-Air | `From_malicious-To_benign-Malware-Nukesped_miner` | `Unknown` |
| Android-Nokia | `From_malicious-To_malicious-Malware-Octo-HTTP` | `Unknown` |
| iOS-iPhone-Xs | *(không có quy tắc Malicious)* | `Unknown` |
| RaspberryPi-Gen3-20 | `From_malicious-To_benign-Discovery` ; `From_malicious` | *(không có)* |
| Chromecast-Gen2 | `From_malicious-To_benign-Discovery` | *(không có)* |
| Echo-1stGen | `From_malicious-To_benign-Discovery` | *(không có)* |

Số quy tắc Benign mỗi thiết bị: 14–77 (tên dịch vụ/tổ chức/ứng dụng/thiết bị/giao thức). Quy tắc `Unknown` không kèm `detailedlabel` (khớp mẫu Win7AD: `Unknown / (empty)`).

## 2. Phân biệt bốn loại nhãn (chỉ mô tả cái quan sát)
| Loại | Ví dụ quan sát | Giới hạn |
|---|---|---|
| **Nhãn hành vi** | `…-Discovery` (honeypot IP công khai) | Là mô tả hành vi (quét). Theo luận văn Sec 9.1 mọi kết nối do IP công khai khởi tạo bị coi là malicious ⇒ gồm cả nhiễu quét Internet nền |
| **Nhãn có tên malware** | `Malware_C2-Trickbot`, `Malware_C2-Redline_stealer`, `…RemcosRAT…`, `Malware-Octo-HTTP`, `Malware-PwnRing_miner-HTTP`, `Malware-Nukesped_miner` | Tên thay đổi cấu trúc giữa thiết bị (xem §3); gắn với *lưu lượng đã gán qua danh sách IP*, không chứng minh là mọi flow của malware đó |
| **Nhãn malicious chung, không có tên** | `Malware_C2_connection_attempt-DNS` (Win7full-1), `From_malicious` (RPi) | Win7full-1 theo luận văn chạy **2 malware** ngày này (NanoCore, và RHADAMANTHYS từ 16:06 CET) nhưng cấu hình chỉ có **1** quy tắc Malicious không tên malware |
| **Nhãn cấp cao** | `Benign` / `Malicious` / `Unknown` | Zenodo mô tả cấp cao là `benign, malicious or background`; luận văn và dữ liệu dùng `Unknown`. `Unknown` của luận văn = "không xác nhận là benign nhưng xác nhận không phải tấn công" (vd. traffic switch); chưa chứng minh đồng nhất với `background` |

## 3. Điểm bất nhất trong từ vựng (quan sát được, chưa giải thích)
1. **Cấu trúc đặt tên không đồng nhất**: `Malware_C2-X` (AD, VM2), `Malware_C2-<sub>-X-HTTP` và `Malware_data_exfiltration-X` (VM3), `Malware-X-HTTP` (Ubuntu, Android), `Malware-X_miner` (Mac), `Malware_C2_connection_attempt-DNS` (VM1), `Discovery` (honeypot).
2. **Tên khác với luận văn**: nhãn Ubuntu `PwnRing_miner` vs luận văn "PwnRig Miner"; nhãn Mac `Nukesped_miner` trong khi luận văn Table 5.1 xếp Nukesped là **RAT** (Sec 7.7: "Nukesped RAT"). Không xác định bên nào đúng; cần tác giả.
3. **Số malware khác số quy tắc**: Win7full-1 (2 malware) – 1 quy tắc.
4. Luận văn Sec 11.2 nói "six malware families: adware, info stealer, remote access trojan, ransomware, crypto miner, and spyware", trong khi Table 5.1 (cột MType) có thêm banking trojan, worm, "RAT + info stealer". "Family" trong luận văn = loại malware, không phải họ theo nghĩa tên họ; đây là cấp **kịch bản**, không phải nhãn flow.
5. Bài ontology (arXiv 2305.01337, Sec 4) định nghĩa 7 mức: `label`, `source`, `destination`, `technique` (ví dụ DoS, Command_and_control, Discovery, Initial_access), `sub-technique`, `process` (vd Windows, Linux, Mirai), `app-protocol`. Chuỗi `Malware_C2-Ingress_tool_access-RemcosRAT-HTTP` *có thể* khớp mức technique/sub-technique/process/app-protocol, nhưng **không có tài liệu nào của CTU-SME-11 nêu ánh xạ này**, và các chuỗi khác (`Malware-PwnRing_miner-HTTP`) không có thành phần technique. → Không dùng ánh xạ.

## 4. Còn thiếu (UNKNOWN sau preflight)
| # | Thông tin chưa có | Cần để |
|---|---|---|
| T1 | `detailedlabel` của ngày 2, 4, 5, 6, 7 (AgentTesla, Lockbit, Kinsing, Lupper, RealTimeSpy, Evilquest, DNS exfil, Metasploit) | Biết nhãn của 6/14 malware + hai kịch bản người |
| T2 | Số flow theo `label × detailedlabel` × thiết bị × ngày | Mode B (k ≤ 100), phân bố |
| T3 | Quy tắc đặt tên chính thức, nghĩa các tiền tố (`Malware`, `Malware_C2`, `Malware_data_exfiltration`) | Quyết định coarse type |
| T4 | Nguồn gốc `Unknown` (84% tổng flow Unknown nằm ở Win7full-3 và Win7AD) và quan hệ với `background` | Định nghĩa lớp âm |
| T5 | Giải thích bất nhất tên (miner/RAT, PwnRing/PwnRig, 1 quy tắc cho 2 malware) | Độ tin cậy nhãn |
| T6 | Cấu hình `labels.config` các ngày khác có đổi tên không (cấu hình mỗi ngày là một tệp riêng) | Hợp nhất từ vựng giữa ngày |

## 5. Taxonomy chưa xác minh có chặn trước feature mapping và Mode A/B không?
| Bước | Có bị chặn không | Lý do |
|---|---|---|
| **Feature mapping** (`schema_mapping.md`) | **Không** | Schema 23 trường conn.log đã xác minh trực tiếp ở 2 ngày và header tar của 9 archive; việc chọn/loại đặc trưng không phụ thuộc vào taxonomy nhãn. Chỉ cần quy tắc: không dùng `detailedlabel`/tên thiết bị/IP làm đặc trưng. |
| **Định nghĩa sự kiện Mode A** | **Có** | Cần xác định "họ/loại tấn công" nào là đối tượng "first appearance". Từ vựng hiện không cho phép quyết định (chỉ 6 nhãn có tên malware ở ngày 3; nhãn hành vi `Discovery` là nhiễu nền; cấu trúc không đồng nhất). Số flow lớn **không** làm một sự kiện hợp lệ: ví dụ Ubuntu ngày 4 có 95.8M flow nhưng là một thiết bị, một malware (Kinsing), một ngày, và nhãn của ngày đó chưa biết. |
| **Định nghĩa support/evaluation Mode B** | **Có (một phần)** | Cần số flow theo loại để biết k=100 có khả thi và để khử trùng lặp; đồng thời Mode B chỉ là offline/oracle (không có confirmation timestamp). |
| **Quyết định Go/No-go cho kiểm toán đầy đủ** | Không | Có thể giải quyết bằng O1 + hỏi tác giả trước khi tốn dữ liệu lớn. |
