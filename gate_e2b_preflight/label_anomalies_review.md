# Rà soát bất thường nhãn — chỉ diễn giải khi có bằng chứng

Nguồn số liệu: `CTU-SME-11_summary_table.csv` (Zenodo; tổng khớp luận văn: 99,993,509 / 1,608,273 / 98,319,252 / 65,984), luận văn Sec 7, 9, 10, 12, và `labels.config` ngày 2023-02-22. Không có đếm flow nào được tự tính từ dữ liệu flow trong preflight.

## 1. Tỷ lệ malicious 98.3%
| Số liệu (từ summary table) | Giá trị |
|---|---|
| Malicious / tổng | 98,319,252 / 99,993,509 = **98.33%** |
| Ubuntu: tổng flows / malicious | 97,830,092 (97.8% tổng) / 97,791,657 (99.46% malicious của toàn dataset) |
| Ubuntu ngày 4 / ngày 5 | 95,765,820 / 2,010,960 flows |
| 10 thiết bị còn lại | 2,163,417 flows: 1,571,169 Benign (72.6%), 527,595 Malicious (24.4%), 64,653 Unknown (3.0%) |
| Ubuntu ngoài ngày 4–5 | 53,312 flows |

**Nguyên nhân có bằng chứng:** thesis Sec 7.6 — Kinsing "was scanning a very large number of IP addresses", dừng 16:42 CET ngày 4; Lupper "scanning IP addresses on the internet", 18:46–21:30 CET ngày 5; Sec 10.1: "very large scan done by malware on the Linux Ubuntu device on days 4 and 5". → Tỷ lệ phản ánh một thiết bị quét, **không** phản ánh phân bố lưu lượng của cả thí nghiệm.
**Không thể xác nhận:** số flow Malicious theo ngày của Ubuntu (summary chỉ có tổng theo ngày của mọi nhãn và tổng malicious theo thiết bị); các flow Ubuntu ngày 4 có nhãn gì (`detailedlabel` chưa biết).
**Hệ quả:** không dùng 98.3% hay 95.8M flow như bằng chứng cho Mode A/B; xem như một sự kiện duy nhất (1 thiết bị × 1 ngày × 1 malware), không phải nhiều sự kiện độc lập. Phân tích gộp flow cần giới hạn theo thiết bị-ngày.

Lệch 6,901 flows giữa thesis Sec 10.3 và summary (đã ghi là chưa giải thích ở Gate E2) — **đối soát số học**: thesis "bỏ Ubuntu ngày 4–5" còn 2,223,630 = 1,608,273 Benign + 549,373 Malicious + 65,984 Unknown, tức giữ nguyên toàn bộ Benign và Unknown của dataset và chỉ bớt 98,319,252 − 549,373 = 97,769,879 flow Malicious. Còn flows Ubuntu ngày 4+5 theo summary là 97,776,780, nên 97,776,780 − 97,769,879 = **6,901** flow Benign/Unknown ở hai ngày đó vẫn nằm trong con số của thesis. Cách đọc này khớp chính xác bằng số học nhưng là suy luận về cách tính của tác giả, **chưa được xác nhận**; nếu đúng, Ubuntu ngày 4–5 không hoàn toàn Malicious (ít nhất 6,901 flow không-Malicious).

## 2. Ubuntu ngày thứ tư
| Bằng chứng | Nội dung |
|---|---|
| Summary table | `Day4TotalFlows = 95,765,820` (95.77% của cả dataset) |
| Thesis Sec 7.6 | Day 4: reset snapshot 9:07:39 CET, nhiễm Kinsing 9:19:34 CET, "scanning a very large number of IP addresses", reset lại 16:42 CET. Còn là ngày dự kiến "data exfiltration attack" cho Ubuntu (Sec 4.3.4) nhưng Sec 7.6 không nêu exfiltration trên Ubuntu |
| Thesis Sec 10.3.1 | Tổng kích thước conn.log của Ubuntu ≈ 17.42 GB (ngày 4 hầu hết) |
| `labels.config` ngày 02-22 (ngày 3) của Ubuntu | Chỉ 1 quy tắc Malicious: `From_malicious-To_malicious-Malware-PwnRing_miner-HTTP`; **cấu hình ngày 4 chưa xem** |

Kết luận được hỗ trợ: sự kiện là malware quét diện rộng (theo thesis). **Chưa xác minh**: nhãn thực tế của 95.8M flow, có quy tắc theo IP hay quy tắc tổng quát (ví dụ `srcIp=all`/ gán theo thời gian) — chưa có `labels.config` ngày 4.

## 3. iPhone có 1,031 Malicious flows
| Bằng chứng | Nội dung |
|---|---|
| Summary table | iPhone: 48,671 flows = 47,586 Benign + 1,031 Malicious + 54 Unknown; flows theo ngày 6,723 / 4,427 / 11,105 / 17,594 / 3,809 / 3,086 / 1,927 |
| Thesis Sec 7.8 | "There was no malicious traffic on this device." |
| Thesis Sec 4.3.2 | iOS "remain benign"; iPhone **không** có IP công khai (chỉ Alexa, RPi, Chromecast) |
| `labels.config` ngày 02-22 (iPhone) | **Không có quy tắc Malicious** (50 Benign, 1 Unknown) — nhất quán với ngày 3 không có Malicious; 1,031 flow nằm ở ngày khác hoặc do quy tắc khác |

**Không thể xác định nguyên nhân.** Các giả thuyết (chưa có bằng chứng, **không** dùng làm kết luận): (a) quy tắc trong ngày khác gán Malicious cho traffic nội bộ tới/từ thiết bị đang quét (Ubuntu ngày 4–5); (b) quy tắc IP quá rộng; (c) lỗi hoặc ngoại lệ trong cấu hình. Cần: `labels.config` ngày 4, 5 của iPhone và đếm nhãn; hoặc giải thích từ tác giả.
Hệ quả: nếu 1,031 flow là nhiễu nhãn thì có thể có nhiễu tương tự ở thiết bị "benign" khác; mức độ UNKNOWN.

## 4. Các bất thường khác ghi nhận trong preflight
| # | Quan sát | Bằng chứng | Trạng thái |
|---|---|---|---|
| A1 | Day 1 mô tả "all benign" (thesis Sec 10.1, 11.1) nhưng RPi/Echo/Chromecast có IP công khai bị tấn công/scan từ ngày 1 và quy tắc coi mọi kết nối do IP công khai khởi tạo là Malicious (Sec 7.10, 9.1) | thesis | Mâu thuẫn tài liệu; chưa kiểm số flow Malicious ngày 1 |
| A2 | Ngày 7 **được ghi lại ngày 30/04/2023** cho cả 11 thiết bị và dịch thời gian bằng `editcap` về 26/02/2023 (Sec 12.4) vì capture ngày 7 của 3 Windows client bị hỏng | thesis | `ts` ngày 7 là giả lập thời gian; ảnh hưởng mọi phân tích theo thời gian. **E2 chưa ghi mục này** (xem erratum) |
| A3 | VM2, VM3, AD: lưu lượng nội bộ VM ngày 5–6 chạy lại ~1 tuần sau (Sec 12.2) | thesis | Cùng nhóm với A2 |
| A4 | MacBook ngày 7 = 717,902 / 836,782 flows (85.8%), cao bất thường so với các ngày 1–6 (5,086–43,544) | summary | Chưa giải thích; không suy diễn nguyên nhân |
| A5 | 84.0% flows `Unknown` của dataset nằm ở Win7full-3 (46,042) và Win7AD (9,394) | summary | Nguồn gốc `Unknown` chưa rõ |
| A6 | Tên nhãn khác thesis (miner vs RAT; PwnRing vs PwnRig; 2 malware – 1 quy tắc) | `labels.config` vs Table 5.1 | Xem `taxonomy_evidence_gaps.md` §3 |
| A7 | Zenodo: "compressed 80GB" vs tổng archive 96.6 GB | Zenodo API | Không ảnh hưởng nhãn; ghi nhận |

## 5. Erratum cho Gate E2 (không sửa tệp đã khóa, chỉ ghi nhận ở đây)
1. `temporal_feasibility.md` thiếu A2/A3 (ngày 7 tái ghi, dịch thời gian; lưu lượng nội bộ VM rerun); và mục "lệch 6,901 chưa giải thích" nay có cách đọc khớp số học (§1, chưa xác nhận).
2. `ctu_label_taxonomy.md` §3 gọi `Malware_C2-<tên>` là "family-bearing, phạm vi hẹp" dựa trên 1 mẫu (Trickbot); preflight cho thấy 6 cấu trúc malicious khác nhau ở ngày 3 ⇒ nhận định đó không được tổng quát hoá.
3. `final_verdict.md` Mode A "14 malware rải trên 7 thiết bị" đúng ở mức kịch bản nhưng chỉ 6 malware có tên trong nhãn ngày 3 (Trickbot, Redline_stealer, RemcosRAT, Octo, PwnRing_miner, Nukesped_miner); NanoCore/RHADAMANTHYS không có tên trong nhãn.
