# CTU-SME-11 — Taxonomy nhãn (nguyên văn) và giới hạn

Không suy diễn taxonomy. Chỉ liệt kê nhãn **thực sự quan sát/được trích** và nói rõ phạm vi quan sát.

## 1. Nhãn cấp cao (`label`) — nguyên văn
| Nguồn | Giá trị |
|---|---|
| Luận văn Sec 9 (Bendl 2023) | `Malicious`, `Unknown`, `Benign` — "Malicious was assigned to all the confirmed attacks … It is about the intention of the action. Benign … verified coming from the operating systems or … the human user. Unknown … not confirmed to come from benign actions, but … confirmed not to be attacks." |
| Zenodo description | "benign, malicious or background" |
| Dữ liệu mẫu (2 device-day) và summary table | `Malicious`, `Benign`, `Unknown` (summary: cột `TotalUnknownFlows`) |

**Xung đột tài liệu:** Zenodo ghi `background`, còn luận văn, dữ liệu mẫu và summary table dùng `Unknown`. Chưa chứng minh `Unknown` ≡ `background`; định nghĩa của luận văn ("confirmed not to be attacks", vd. traffic switch) khác với nghĩa thông thường của background. → **UNKNOWN** cho đến khi kiểm tra toàn bộ nhãn.

## 2. Nhãn mô tả (`detailedlabel`) — nguyên văn, chỉ phần đã quan sát
Mẫu 1 — `Honeypot-Assistant-Amazon-Echo1stGen-1`, ngày 2023-02-22, 9,180 dòng conn.log.labeled:

| label | detailedlabel (nguyên văn) | số dòng |
|---|---|---|
| Malicious | `From_malicious-To_benign-Discovery` | 3,915 |
| Benign | `From_benign-To_benign-DNS` | 2,208 |
| Benign | `From_benign-To_benign-Amazon` | 1,799 |
| Benign | `From_benign-To_benign-Cloudfront` | 392 |
| Benign | `From_benign-To_benign-DHCP` | 287 |
| Benign | `From_benign-To_benign-ICMP` | 282 |
| Benign | `From_benign-To_benign-Uptimerobot` | 144 |
| Benign | `From_benign` | 127 |
| Benign | `From_benign-To_benign-SSDP` | 26 |

Mẫu 2 — `Experiment-VM-Microsoft-Windows7AD-1`, ngày 2023-02-22, 35,672 dòng:

| label | detailedlabel (nguyên văn) | số dòng |
|---|---|---|
| Malicious | `From_malicious-To_malicious-Malware_C2-Trickbot` | 221 |
| Unknown | `(empty)` (trường rỗng của Zeek) | 853 |
| Benign | `From_benign-To_benign-DNS` (28,194), `…-Device_chromecast_tv_assistant` (1,895), `From_benign-To_benign` (1,357), `…-1e100` (995), `…-Multicast` (612), `…-Worldstream.net` (392), `…-ICMP` (236), `…-Cloudflare` (235), `…-Microsoft` (166), `…-Akamai` (93), `…-Amazon` (87), `…-Device_edimax_camera` (75), `…-Application_twitter` (53), `…-Fastly` (49), `…-Google` (47), `…-Application_instagram` (32), và 13 giá trị ≤17 dòng (29 giá trị Benign khác nhau) | 34,598 Benign |

(Đây là kiểm kê từ vựng nhãn mô tả, không phải chỉ số mô hình. Số đếm chỉ áp dụng cho 2 device-day này.)

Từ vựng quy tắc trong `labels.config` của mẫu 2 có thêm nhiều tên tổ chức/ứng dụng/thiết bị Benign (Spotify, Zoom, Discord, Dropbox, …) nhưng **chỉ một** quy tắc Malicious (`Malware_C2-Trickbot`) — vì ngày đó chỉ có Trickbot trên máy này.

## 3. Nhãn mô tả có phải attack family không?
Kết luận: **chỉ một phần, không đồng nhất**.
- `…-Discovery`: mô tả **hành vi** (quét/dò từ Internet tới thiết bị có IP công khai). Luận văn không nói đây là họ tấn công; quy tắc là danh sách `srcIp=` của các nguồn quét. Theo luận văn Sec 9.1, mọi kết nối do IP công khai khởi tạo tới thiết bị honeypot-like đều bị coi Malicious → `Discovery` bao gồm cả nhiễu quét Internet nền (không phải tấn công có chủ đích nhắm vào lab).
- `…-Malware_C2-Trickbot`: có **tên họ malware**, nhưng chỉ cho **lưu lượng C2** xác định theo IP; các luồng khác của cùng malware (nếu có) có thể mang nhãn khác hoặc Unknown (chưa kiểm).
- Định dạng `From_<src>-To_<dst>-<mô tả>` mã hóa vai trò nguồn/đích (benign/malicious) + một hậu tố tự do. Hậu tố cho Benign là tên dịch vụ/tổ chức, không phải loại hành vi.
- Các nhãn khác (exfiltration DNS, Metasploit insider attack, ransomware SMB, miner, scan của Kinsing/Lupper, …) **chưa quan sát được** nhãn thực tế; luận văn mô tả scenario nhưng không liệt kê giá trị `detailedlabel`.

## 4. Nhãn nào đủ điều kiện làm "coarse attack type"?
**UNKNOWN / chưa quyết định được.** Với bằng chứng hiện có không thể khẳng định nhãn nào là coarse attack type. Chỉ có thể nói:
- Có thể là ứng viên (cần kiểm toán đầy đủ): `Discovery` (hành vi), `Malware_C2-<tên>` (family-bearing, phạm vi hẹp).
- Không được gom thủ công `Malware_C2-X` thành "C2" hay `Discovery` thành "Reconnaissance" cho tới khi có kiểm kê toàn bộ giá trị và quy tắc ánh xạ được ghi rõ từ nguồn. Đó là suy diễn taxonomy (bị cấm).

## 5. Nguồn gốc nhãn (ground truth / expert / detector / pseudo-label?)
Bằng chứng (luận văn Sec 4.4, 9, 9.3; `labels.config`):
1. Kịch bản có kiểm soát: biết thiết bị nào bị nhiễm malware nào, thời điểm thực thi (ghi theo CET trong Sec 7).
2. Nhãn gán bằng **bộ quy tắc theo IP** (NetflowLabeler, `labels.config`) do một nhà nghiên cứu bảo mật tạo/duyệt; IP độc hại xác nhận bằng VirusTotal/Shodan và chạy lại mẫu malware trước thí nghiệm; lặp "cho đến khi không còn IP đáng ngờ".
3. Không phải detector/IDS tự động quyết định; nhưng cũng không phải nhãn từng flow do chuyên gia xem tay.

→ Phân loại trung thực: **rule-based labels do chuyên gia tạo, dựa trên scenario ground truth + IP threat intel**; có yếu tố pseudo-label ở chỗ nhãn suy ra từ IP chứ không từ nội dung flow. Hệ quả: IP là một proxy gần hoàn hảo cho nhãn → rủi ro leakage nếu giữ IP (xem `schema_mapping.md`).
Điều chưa kiểm: độ chính xác của nhãn ở mức flow (không có đối chiếu độc lập), nên **không** khẳng định nhãn "đúng tuyệt đối".

## 6. Nhãn Zeek khác
`*.log.labeled` cho dns/http/ssl/files/... được truyền nhãn từ conn.log qua `uid` (zeek-files-labeler.py). Audit này chỉ xem conn.log.
