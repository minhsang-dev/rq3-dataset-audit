# Taxonomy consistency audit — CTU-SME-11, ngày 2023-02-22, 11 thiết bị

**Phạm vi:** 11/11 thiết bị, đúng 1 ngày (`2023-02-22`) = 11/77 device-day (14.3%), 306,172 / 99,993,509 flows (0.31%), 133,076 / 98,319,252 flow Malicious (0.14%). Không suy ra gì về 6 ngày còn lại.

**Ký hiệu bằng chứng:** **[D]** = đếm/đọc trực tiếp từ `conn.log.labeled` hoặc `labels.config` đã tải · **[DOC]** = tài liệu (luận văn, Zenodo README, README công cụ NetflowLabeler, bài arXiv 2305.01337) · **[INF]** = suy luận của người kiểm toán, chưa được tác giả xác nhận.

## 1. Tập nhãn quan sát được (nguyên văn) [D]
67 tổ hợp `(label, detailedlabel)` duy nhất trên 222 dòng (thiết bị × tổ hợp). Chi tiết: `raw_label_inventory.csv`, `device_day_label_counts.csv`.

| label | số tổ hợp | flows |
|---|---|---|
| `Benign` | 57 | 170,839 |
| `Malicious` | 9 | 133,076 |
| `Unknown` | 1 (`detailedlabel` = `(empty)` — chuỗi rỗng của Zeek) | 2,257 |
| `background` | **0** (không xuất hiện ở bất kỳ tệp nào) | 0 |

Tổ hợp Malicious và Unknown, nguyên văn:

| detailedlabel | thiết bị (flows) |
|---|---|
| `From_malicious-To_benign-Malware_C2_connection_attempt-DNS` | Win7full-1 (42,397) |
| `From_malicious-To_benign-Discovery` | Echo (3,915), RaspberryPi (20,277), Chromecast (16,912) |
| `From_malicious-To_malicious-Malware_data_exfiltration-RemcosRAT` | Win7full-3 (33,739) |
| `From_malicious` | RaspberryPi (7,705) |
| `From_malicious-To_malicious-Malware_C2-Redline_stealer` | Win7full-2 (5,637) |
| `From_malicious-To_malicious-Malware-Octo-HTTP` | Android (2,269) |
| `From_malicious-To_malicious-Malware_C2-Trickbot` | Win7AD (221) |
| `From_malicious-To_malicious-Malware_C2-Ingress_tool_access-RemcosRAT-HTTP` | Win7full-3 (3) |
| `From_malicious-To_malicious-Malware-PwnRing_miner-HTTP` | Ubuntu (1) |
| `Unknown` / `(empty)` | Ubuntu 17, Win7AD 853, Win7full-1 656, -2 542, -3 6, Android 176, iPhone 7 |
| *(quy tắc có trong cấu hình nhưng **0 flow**)* `From_malicious-To_benign-Malware-Nukesped_miner` | MacBook |

Mọi tổ hợp quan sát được đều có quy tắc tương ứng trong `labels.config` cùng ngày (222/222) [D].

## 2. Bốn loại nhãn — phân biệt trên bằng chứng
| Loại | Quan sát [D] | Ghi chú |
|---|---|---|
| **Nhãn hành vi** | `…-Discovery` | Xem §3: là quy tắc mặc định (`srcIp=all`) của 3 honeypot, nên **chưa chứng minh** mô tả hành vi quét đã được kiểm chứng từng flow |
| **Nhãn có tên malware** | `Malware_C2-Trickbot`, `Malware_C2-Redline_stealer`, `Malware-Octo-HTTP`, `Malware_C2-Ingress_tool_access-RemcosRAT-HTTP`, `Malware_data_exfiltration-RemcosRAT`, `Malware-PwnRing_miner-HTTP` (+ `Malware-Nukesped_miner` ở cấu hình, 0 flow) | Gắn với danh sách IP đích (xem §5); không phải nhãn "họ" theo nghĩa phân loại |
| **Malicious chung, không tên** | `Malware_C2_connection_attempt-DNS` (Win7full-1), `From_malicious` (RPi) | Win7full-1 có 2 malware theo luận văn [DOC] nhưng chỉ 1 quy tắc malicious, 2 `dstIp` |
| **Cấp cao** | `Benign` / `Malicious` / `Unknown` | Không có `background` |

Cấu trúc chuỗi [D]: `From_<malicious|benign>[-To_<malicious|benign>][-<hậu tố>]`. Có ít nhất 6 dạng hậu tố malicious khác nhau (`Malware-X-HTTP`, `Malware-X_miner`, `Malware_C2-X`, `Malware_C2-<sub>-X-HTTP`, `Malware_data_exfiltration-X`, `Malware_C2_connection_attempt-DNS`) và 2 chuỗi không có `To_…` (`From_malicious`, `From_benign`).
Bài ontology (arXiv 2305.01337, Sec 4) định nghĩa 7 mức [DOC]; **không có tài liệu CTU-SME-11 nào nêu cách ánh xạ các đoạn của chuỗi lên các mức đó** ⇒ không áp dụng, không tách chuỗi thành "technique/process".

## 3. `Unknown`, `background`, `Benign`, `Malicious`
1. **Cơ chế gán [D + DOC]:** README NetflowLabeler nêu "Labels are assigned from top to bottom. Each new label supersedes and overwrites the previous match" và ví dụ dùng `Background: - srcIP=all` làm quy tắc đầu [DOC]. Trong 8 thiết bị không phải honeypot, **khối đầu tiên của `labels.config` là `Unknown:` với một dòng `srcIP=all`** (Malicious ở khối 1) [D]. iPhone và MacBook cũng có khối này, nhưng 0 flow Unknown ở MacBook (mọi flow đã bị quy tắc sau ghi đè).
2. **[INF]** `Unknown` trong dữ liệu là nhãn **mặc định cho flow không khớp quy tắc nào** (phần dư), đóng đúng vai trò của `Background` trong ví dụ README công cụ. Điều này **không** tương đương với định nghĩa luận văn Sec 9 ("flows that were not confirmed to come from benign actions, but they were confirmed not to be attacks") [DOC] — định nghĩa đó nói "confirmed", còn cơ chế cấu hình là mặc định cho phần chưa khớp. Hai cách hiểu chưa được tác giả hòa giải.
3. **Hồ sơ Unknown [D]:** 2,257 flow, 100% nguồn là IP riêng (thiết bị lab), chủ yếu `tcp` cổng đích 443/80 (Ubuntu, Win7AD, Win7full-1/2/3, Android) và, ở iPhone, 6/7 flow `tcp/445` trạng thái `REJ`. Nhất quán với "kết nối đi ra tới đích chưa nằm trong danh sách nào" [INF].
4. **`background` (Zenodo):** không tồn tại trong 11 tệp [D]. Việc `Unknown` ≡ `background` là **UNKNOWN** (không có xác nhận của tác giả; chỉ có suy đoán dựa trên vai trò mặc định ở mục 2).
5. **Ba honeypot** (Echo, RPi, Chromecast) không có quy tắc `Unknown`. Khối đầu tiên là **Malicious** `…-Discovery` chứa dòng `srcIp=all` cùng hàng nghìn `srcIp=` [D]. Như vậy trên honeypot, flow không khớp quy tắc Benign nào sẽ là Malicious `Discovery` **theo cấu hình** — khớp với lập luận thesis Sec 9.1 "we consider all the connections initiated by a public IP address malicious" [DOC].
6. **Hệ quả cho `Discovery` [D+INF]:**
   - Echo: 136/3,915 flow `Discovery` có nguồn là IP riêng (không phải IP công khai); Chromecast: 2/16,912; RPi: 0/20,277.
   - Flow `Discovery` gồm `icmp` (RPi 9,822; Chromecast 12,898), `tcp` cổng 22/23/445… và `udp`.
   - Nên `Discovery` là nhãn mặc định của honeypot có hậu tố mô tả hành vi, chưa phải bằng chứng từng flow là quét. Chưa thể kết luận ngược lại; cần tác giả.
7. **`Benign` [D]:** hậu tố là tên dịch vụ/tổ chức/ứng dụng/thiết bị/giao thức (57 giá trị, trong đó 8 bắt đầu bằng `…Application_`, 3 bằng `…Device_`, 2 chuỗi ngắn `From_benign-To_benign` và `From_benign`). Không phải nhãn hành vi.

## 4. Đối chiếu nhãn với kịch bản trong luận văn (ngày 3 = 2023-02-22)
Mapping ngày: số dòng của cả 11 tệp bằng đúng `Day3TotalFlows` và không bằng cột ngày nào khác — xem `summary_reconciliation.csv` [D].

| Thiết bị | Malware đang hoạt động ngày này theo luận văn [DOC] | Nhãn Malicious ghi nhận [D] | Quan sát |
|---|---|---|---|
| Win7AD | TrickBot, thực thi 16:15:51 CET (= 15:15:51 UTC) | `Malware_C2-Trickbot` 221 flow | Flow Malicious đầu tiên **15:13:09 UTC**, tức **sớm hơn 2 phút 42 giây** so với giờ thực thi ghi trong luận văn. README thiết bị và `ts` cùng múi giờ UTC+1 (đã kiểm: `Capture started` − 1h = `ts` đầu tiên, 11/11). Nguyên nhân: UNKNOWN |
| Ubuntu | PwnRig, thực thi lại 16:26:03 CET (= 15:26:03 UTC) | `Malware-PwnRing_miner-HTTP` **1** flow | Flow Malicious đầu tiên 15:26:04 UTC (+1 s). Chỉ 1 flow malicious |
| Win7full-1 | NanoCore (từ ngày 2), RHADAMANTHYS (16:06:19 CET) | `Malware_C2_connection_attempt-DNS` 42,397 flow, 100% `udp/53` | 2 malware, 1 nhãn không tên |
| Win7full-2 | RedLineStealer | `Malware_C2-Redline_stealer` 5,637 flow, 100% `tcp/2567` | Phù hợp tên |
| Win7full-3 | RemcosRAT (exfiltration DNS là ngày 4) | `Malware_data_exfiltration-RemcosRAT` 33,739 flow, 100% `tcp/5888`, 99.6% `REJ`; `…Ingress_tool_access-RemcosRAT-HTTP` 3 flow | Nhãn ghi "data_exfiltration" cho ngày mà luận văn mô tả exfiltration DNS ở ngày 4; flow là kết nối bị từ chối tới cổng 5888 |
| MacBook | Nukesped, đang chạy (Sec 7.7) | **0** flow Malicious (quy tắc `Nukesped_miner` tồn tại, 0 khớp) | Mâu thuẫn tài liệu ↔ dữ liệu; nguyên nhân UNKNOWN |
| Android | Octo (từ ngày 2) | `Malware-Octo-HTTP` 2,269 flow (`tcp/443`) | Phù hợp |
| iPhone | Không có | 0 Malicious, 7 Unknown | Phù hợp ngày 02-22; **1,031 malicious của iPhone nằm ở 6 ngày khác** [D+arith] |
| Echo / RPi / Chromecast | Chỉ tấn công từ Internet | `Discovery` (+ `From_malicious` ở RPi) | Xem §3 |

Tên chuỗi khác với luận văn (giữ nguyên, không sửa) [D vs DOC]: nhãn `PwnRing_miner` vs luận văn "PwnRig Miner"; nhãn `Nukesped_miner` vs luận văn Table 5.1 "Nukesped" loại **RAT** (và Sec 7.7 còn viết "Nukepsed"); nhãn `Malware-Octo-HTTP` vs "Octo" RAT.

## 5. Nguồn gán nhãn [D + DOC]
Mỗi quy tắc Malicious có tên malware là **danh sách IP đích** (`dstIp`/`dstIP`): Trickbot 14, Redline 1, Remcos 1+1, Nukesped 3, Octo 2, Win7full-1 2, Ubuntu 1 `dstIP` + 1 `srcIP`; Discovery là danh sách `srcIp` (Echo 1,666; RPi 2,741; Chromecast 3,426) + `srcIp=all`. ⇒ nhãn theo **IP + thứ tự quy tắc** do chuyên gia soạn (Sec 9.3 luận văn), không có nhãn theo nội dung flow hay detector [DOC]. Hệ quả: một flow của malware tới IP chưa có trong danh sách sẽ không nhận nhãn malicious.

## 6. Tính đủ điều kiện làm "coarse attack type"
Không có bằng chứng nào cho phép nâng bất kỳ chuỗi nào lên "coarse attack type" mà không tự suy diễn:
- Không có ánh xạ chính thức từ chuỗi sang loại.
- 6 dạng hậu tố không đồng nhất; hai nhãn malicious chung không tên.
- Phủ chỉ 1 ngày. Vì vậy `Discovery`, `Malware_C2-*`, `Malware_data_exfiltration-*` **không** được gộp thành "reconnaissance", "C2", "exfiltration".
