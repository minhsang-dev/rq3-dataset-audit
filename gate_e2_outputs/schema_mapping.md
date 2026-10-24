# Ánh xạ schema — CTU-SME-11 (Zeek conn.log) và CIDDS (NetFlow)

Nguồn: `conn.log.labeled` thật của 2 device-day mẫu (header `#fields`/`#types`), luận văn Sec 8–10, technical report CIDDS. Chưa chạy bất kỳ pipeline chuẩn hóa nào.

## 1. CTU-SME-11 — 23 trường (nguyên văn)
`ts uid id.orig_h id.orig_p id.resp_h id.resp_p proto service duration orig_bytes resp_bytes conn_state local_orig local_resp missed_bytes history orig_pkts orig_ip_bytes resp_pkts resp_ip_bytes tunnel_parents label detailedlabel`

Quan sát giá trị trong mẫu: `-` = unset; với Echo, `service`, `duration`, `orig_bytes`, `resp_bytes` thường `-` (các luồng S0 không phản hồi); `local_orig/local_resp` là `-` ở các dòng xem được (không khẳng định cho toàn dataset); `tunnel_parents` rỗng.

| Trường | Phân loại | Dùng cho mô hình tabular? | Ghi chú |
|---|---|---|---|
| `ts` | thời gian thô | **Loại** (chỉ dùng để chia split/sắp thứ tự) | Epoch UTC; leakage theo ngày kịch bản |
| `uid` | ID flow | **Loại** | Định danh duy nhất; khóa nối nhãn |
| `id.orig_h`, `id.resp_h` | IP | **Loại** | Nhãn được gán bằng danh sách IP ⇒ IP ≈ nhãn |
| `id.orig_p` | cổng nguồn | **Loại** (hoặc bỏ) | Ephemeral |
| `id.resp_p` | cổng đích | Có điều kiện | Dùng như categorical/bucket; có thể mã hóa dịch vụ; kiểm leakage theo thiết bị |
| `proto` | categorical | **Dùng** | tcp/udp/icmp |
| `service` | categorical | **Dùng** (nhiều `-`) | Do Zeek DPD suy ra |
| `duration` | số | **Dùng** | `-` khi không có → missing |
| `orig_bytes`, `resp_bytes` | số | **Dùng** | payload bytes; `-` → missing |
| `conn_state` | categorical | **Dùng** | S0, SF, … |
| `local_orig`, `local_resp` | bool | Có điều kiện | Có thể gần như không có thông tin; kiểm trên dữ liệu thật |
| `missed_bytes` | số | **Dùng** | liên quan mất gói (chất lượng capture) – cân nhắc |
| `history` | chuỗi cờ | **Dùng** (mã hóa) | Tóm tắt cờ TCP |
| `orig_pkts`, `orig_ip_bytes`, `resp_pkts`, `resp_ip_bytes` | số | **Dùng** | |
| `tunnel_parents` | set | **Loại** | Rỗng trong mẫu |
| `label`, `detailedlabel` | nhãn | **Chỉ làm target/phân tầng, không làm feature** | `detailedlabel` chứa vai trò nguồn/đích ⇒ leakage tuyệt đối |

**Tối đa 14–16 đặc trưng** từ conn.log (proto, service, resp_p, duration, orig/resp_bytes, conn_state, missed_bytes, history, 4 trường pkts/ip_bytes, ± local_*). Đây không phải bộ 46 đặc trưng; không yêu cầu 46.

## 2. Nguy cơ leakage cần loại / kiểm soát (tổng hợp)
- **IP / uid / mã kịch bản** (thiết bị, ngày): nhãn suy ra từ IP ⇒ loại. Tên thư mục device/ngày cũng là mã kịch bản.
- **Timestamp thô**: ngày ↔ giai đoạn kịch bản (day 1/7 sạch). Chỉ dùng để chia thời gian.
- **Thông tin phát sinh sau nhãn**: `detailedlabel`, mọi cột `*.log.labeled` khác (đã chứa nhãn truyền từ conn.log), `labels.config`.
- **Thiết bị**: mỗi thiết bị có hồ sơ lưu lượng riêng ⇒ split theo thiết bị/ngày để tránh nhớ thiết bị; quan sát mất cân bằng cực đoan (Ubuntu = 97.8% tổng flows).
- **Near-duplicate**: C2 beaconing và scan sinh hàng nghìn flow gần giống ⇒ cần khử trùng lặp trước khi tạo support/eval (xem `temporal_feasibility.md`).

## 3. Đặc trưng RQ3 không thể tái dựng
Repo hiện **trống** và hồ sơ RQ3 đã khóa không nằm trong phạm vi được cung cấp ⇒ **không thể liệt kê chính xác** 46 đặc trưng RQ3. Chỉ có thể nêu theo loại:
- Từ conn.log **không thể** tái dựng: thống kê theo gói (kích thước/IAT/cửa sổ TCP/TTL/retransmission), cờ TCP riêng lẻ (chỉ có chuỗi `history`), đặc trưng payload/DPI, đặc trưng theo cửa sổ cần dữ liệu đa luồng (làm được từ conn.log nếu định nghĩa cửa sổ rõ).
- Có thể tái dựng **chỉ bằng cách tái trích xuất từ PCAP** (160 GB; tool và phiên bản phải trùng RQ3) – chưa kiểm khả thi, chưa thử.
- Khác biệt giao thức: Zeek flow hai chiều (orig/resp), trong khi CIDDS là NetFlow một chiều ⇒ không cùng định nghĩa "flow".

**Kết luận:** so sánh TabFM–LightGBM công bằng là khả thi **trên một bộ đặc trưng conn.log rút gọn, cố định trước, cho cả hai mô hình**; không phải tái hiện nguyên vẹn đặc trưng RQ3. Cần xác nhận danh sách 46 đặc trưng RQ3 từ hồ sơ khóa để biết giao/khác chính xác (UNKNOWN).

## 4. CIDDS-001 / CIDDS-002 (chỉ theo technical report)
Unidirectional NetFlow, 10 trường mặc định (Src IP, Src Port, Dest IP, Dest Port, Proto, Date first seen, Duration, Bytes, Packets, Flags) + `Class`, `AttackType`, `AttackID`, `AttackDescription`. Chỉ 6–7 đặc trưng dùng được sau khi loại IP/AttackID/AttackDescription/timestamp (Proto, Dest Port, Duration, Bytes, Packets, Flags, ±Src Port). `AttackID` và `AttackDescription` là rò rỉ nhãn. Sơ đồ nghèo hơn CTU rất nhiều; ánh xạ sang RQ3 sẽ mất gần như toàn bộ đặc trưng. Chưa mở dữ liệu thật.
