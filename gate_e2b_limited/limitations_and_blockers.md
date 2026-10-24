# Giới hạn và blocker — Gate E2b-LIMITED

## 1. Phạm vi thực sự đạt được
- **Đã làm:** 11/11 archive, ngày `2023-02-22`, `conn.log.labeled` đầy đủ (độ dài thành viên tar = kích thước header; không dùng dữ liệu cắt cụt) + `labels.config` + `README.html` của ngày đó. Tất cả trạng thái `COMPLETE` (`summary_reconciliation.csv`).
- **Chưa làm (không được phép):** 6 ngày còn lại của mỗi thiết bị; mọi log Zeek ngoài conn; pcap; archive đầy đủ.
- **Độ phủ:** 14.3% device-day; 0.31% tổng flows; 0.14% flow Malicious. Ngày 3 là ngày thiết kế có nhiều malware hoạt động, nhưng **không có** AgentTesla, Lockbit, Kinsing, Lupper, RealTimeSpy, Evilquest, exfiltration DNS (ngày 4), tấn công Metasploit (ngày 2) ⇒ từ vựng nhãn của chúng **chưa biết**.
- **Mạng:** 7,168,000 byte mới trong 28 request (không có request lỗi) = 7.17 MB < 20 MB; tái sử dụng 26,471,520 byte đã cache (tính lặp theo request nên lớn hơn kích thước tệp thực). Xem `network_bytes_and_access_log.csv`. Ubuntu không cần tải thêm.

## 2. Blocker và UNKNOWN còn lại
| # | Nội dung | Loại | Ảnh hưởng |
|---|---|---|---|
| X1 | `Unknown` ≡ `background`? Dữ liệu chỉ có `Unknown`, là nhãn mặc định `srcIP=all` [INF] mâu thuẫn nhẹ với định nghĩa "confirmed not attacks" của luận văn | Cần tác giả | Định nghĩa lớp âm / loại trừ |
| X2 | Ánh xạ chuỗi `detailedlabel` → coarse attack type | Cần tác giả | **Chặn Mode A**; ảnh hưởng chọn lớp cho Mode B |
| X3 | Từ vựng nhãn các ngày khác (6 malware + 2 kịch bản người chưa thấy) | Cần dữ liệu thêm (ngoài quyền hiện tại) | Phủ taxonomy |
| X4 | Nukesped: 0 flow malicious dù malware đang chạy; VM1: 2 malware 1 nhãn; VM3 nhãn "exfiltration" cho tcp/5888 `REJ`; PwnRing/PwnRig; Nukesped miner/RAT | Cần tác giả | Độ tin cậy nhãn |
| X5 | Trickbot: flow malicious đầu tiên sớm hơn thời điểm thực thi trong thesis 2 phút 42 giây | Cần tác giả | Độ chính xác thời gian cho Mode A |
| X6 | iPhone: 1,031 malicious nằm ngoài 02-22 | Cần dữ liệu ngày khác hoặc tác giả | Nhiễu nhãn thiết bị "sạch" |
| X7 | Cửa sổ capture là 24 h từ gói đầu, không phải 00:00–23:59 UTC; 0–38 flow mỗi thiết bị có `ts` sau 02-23 00:00 UTC | Dữ liệu đã quan sát | Quy ước gán ngày/chia split theo thời gian phải dùng cửa sổ capture thật |
| X8 | Ngày 7 tái ghi và dịch giờ (thesis Sec 12.4), rerun VM nội bộ ngày 5–6 | Tài liệu | Ảnh hưởng mọi phân tích thời gian ở các ngày đó |
| X9 | `Discovery` là nhãn mặc định của honeypot, gồm cả flow ICMP/22/23/445; 138 flow có nguồn IP riêng ở Echo/Chromecast | Dữ liệu + suy luận | Không coi là nhãn hành vi đã kiểm chứng |
| X10 | Lịch sử sử dụng dataset trong luận án (G) | Không đổi so với Gate E2 | UNKNOWN |

## 3. Ý nghĩa cho Mode A/B (chỉ về điều kiện dữ liệu)
- **Mode A:** trên ngày 02-22 chỉ **3 sự kiện** có thời điểm bắt đầu trong ngày theo luận văn (TrickBot trên AD, PwnRig chạy lại trên Ubuntu, RHADAMANTHYS trên VM1). Trong đó chỉ Trickbot có nhãn mang tên; Ubuntu có 1 flow; RHADAMANTHYS không có nhãn riêng. Số flow lớn của VM1/VM3/RPi không làm thành sự kiện Mode A hợp lệ. Chưa đủ để định nghĩa sự kiện.
- **Mode B:** số flow theo nhãn trong ngày này (ví dụ Trickbot 221, Redline 5,637, Octo 2,269, Remcos-HTTP 3, PwnRing 1) chỉ cho thấy k=100 *chưa bị loại* với vài nhãn; chưa khử trùng lặp, chưa xét tách theo thời gian/thiết bị; không có timestamp xác nhận nên **chỉ offline retrospective/oracle**.
- **Feature mapping:** không bị chặn (schema 23 trường xác minh trên 11 tệp; mọi hàng đủ 23 trường, 0 hàng lỗi).

## 4. Erratum / bổ sung cho báo cáo đã commit (không sửa tệp cũ)
| Báo cáo | Điều cần đính chính / bổ sung |
|---|---|
| Gate E2 `ctu_label_taxonomy.md` §3 | Gọi `Discovery` là "hành vi quét" và nhiễu Internet nền. Dữ liệu cho thấy nó là **quy tắc mặc định (`srcIp=all`)** của honeypot; chưa kiểm chứng hành vi |
| Gate E2 `temporal_feasibility.md` §1 | Nói pcap cắt theo ngày UTC. Dữ liệu: cửa sổ ~24 h bắt đầu từ gói đầu (00:00:02–00:02:07 UTC) |
| Preflight `minimal_data_acquisition_plan.md` | Ước tính 10–15 MB cho 9 archive; thực tế 7.17 MB mạng mới (Ubuntu 0). Vị trí `conn.log` của Win7full-1: nằm sau tiền tố 3,292,000 byte nén |
| Preflight `taxonomy_evidence_gaps.md` §3.1 | Giả định `Unknown` chưa rõ; nay có bằng chứng cấu hình là quy tắc mặc định đầu tiên (trừ honeypot) |
| Preflight `label_anomalies_review.md` | iPhone: xác nhận 0 Malicious ngày 02-22 ⇒ 1,031 ở ngày khác |

## 5. Hạn chế phương pháp của gate này
- Chỉ 1 ngày; nhãn các ngày khác có thể đổi tên/cấu hình (mỗi ngày có `labels.config` riêng) — không ngoại suy.
- Giải thích "Unknown mặc định" và "Discovery mặc định" là **[INF]** dựa trên README công cụ và vị trí khối cấu hình; tác giả chưa xác nhận.
- Kiểm tra mapping ngày dựa vào khớp chính xác số dòng; không phải bằng chứng độc lập về nội dung nhãn.
- Thesis dùng giờ CET; chuyển UTC−1h được xác minh bằng README↔`ts` đầu tiên (11/11), không dùng cho các giờ trong thesis mà không kiểm.
