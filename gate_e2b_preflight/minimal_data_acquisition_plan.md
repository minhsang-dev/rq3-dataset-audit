# Kế hoạch thu thập dữ liệu tối thiểu — so sánh kiểm toán có mục tiêu và đầy đủ

**Chưa thực hiện bước nào dưới đây ngoài những gì đã nêu ở `archive_access_options.md`.** Mọi số "ước tính" là ngoại suy từ đo đạc ở đầu archive; chưa được chứng minh đầu-cuối.

## 1. Đã tiêu thụ trong preflight (tổng ≈ 12.8 MB < 20 MB)
| Mục | MB |
|---|---|
| bài ontology arXiv 2305.01337 (PDF) | 2.7 |
| probe thất bại 9 × 400 KB trong Win7AD-1 | 3.6 |
| 9 đầu archive × 500 KB (+ tải lại Win7full-2/3 vì ghi đè tệp tạm) | 5.5 |
| 1 request 1 MB đo tốc độ (Win7full-1, offset 500000) | 1.0 |
| metadata nhỏ (versions, README GitHub) | < 0.1 |

Ngân sách còn lại của phiên preflight ≈ 7 MB, **không dùng** thêm.

## 2. Hai lựa chọn được yêu cầu đánh giá

### Lựa chọn T — Kiểm toán có mục tiêu: ngày đầu của mỗi archive (2023-02-22, 11 device-day)
| Thành phần | Truyền | Bằng chứng thu được | Vẫn không xác minh được |
|---|---|---|---|
| `labels.config` ngày 02-22, 11 thiết bị | **0 (đã có)** | Từ vựng quy tắc ngày 3 (bảng ở `taxonomy_evidence_gaps.md`) | Số flow; các ngày khác |
| `conn.log.labeled` ngày 02-22 của 9 thiết bị chưa có (Echo, Win7AD đã có mẫu) — Range tiền tố tới hết `conn.log.labeled` | ≈ 7–8 MB (8 archive, vị trí conn.log biết qua header) + ≈ 3–5 MB (Win7full-1: `dns.log.labeled` 22.3 MB đứng trước, vị trí conn.log chưa biết) ⇒ **≈ 10–15 MB, giới hạn cứng 20 MB** | Đếm `label × detailedlabel` thật cho 11/77 device-day (14%), gồm 8/14 malware đang hoạt động ngày 3 (NanoCore, RHADAMANTHYS, RedLineStealer, Remcos, TrickBot, PwnRig, Nukesped, Octo) và 3 honeypot IP công khai; kiểm tra schema/ts thật cho cả 11 thiết bị | AgentTesla, Lockbit, Kinsing, Lupper, RealTimeSpy, Evilquest (ngày 4–5); exfiltration DNS (VM3 ngày 4); Metasploit (ngày 2); iPhone 1,031 flow; Ubuntu ngày 4; ngày 7 tái ghi |
| Độ rủi ro | Thấp: cấu trúc đã xác minh 11/11; dừng ngay nếu offset conn.log vượt dự kiến (hard cap) | | |

Điều kiện dừng: nếu tổng truyền chạm 20 MB mà chưa đủ, báo cáo thay vì tải tiếp. Dữ liệu chỉ được dùng để kiểm kê nhãn/schema; không mô hình, không điều chỉnh phương pháp.

### Lựa chọn F — Kiểm toán đầy đủ trên toàn bộ nguồn
| Thành phần | Truyền | Bằng chứng | Vẫn không xác minh |
|---|---|---|---|
| Stream 11 archive, giữ `labels.config` + đếm `conn.log.labeled`, bỏ pcap | **96.6 GB** (≈ 48 giờ với 0.56 MB/s một luồng; chưa đo song song) | Kiểm kê đầy đủ 77 device-day, đối chiếu tổng với summary table và thesis | Nhãn có đúng hay không (không có đối chiếu độc lập); ý nghĩa tên nhãn nếu tác giả không trả lời; gap capture ngoài những gì thesis nêu |
| Phương án trung gian — dò block bz2 (O2) cho một số ngày chọn lọc | ước tính 8–13 MB/biên ngày **(chưa chứng minh)** | Mở rộng ra ngày 4–5 | Chi phí thật; có thể kém hiệu quả hơn ước tính |

### Bảng so sánh
| Tiêu chí | T (ngày đầu, 11 device-day) | O2 (chọn lọc nhiều ngày) | F (đầy đủ) |
|---|---|---|---|
| Dung lượng | ≈ 10–15 MB (cap 20) | ≈ 0.1–0.2 GB cho ~10–15 biên (ước tính) | 96.6 GB |
| Đã khả thi đã chứng minh | Một phần (cấu trúc + ước lượng) | Không | Có (stream) nhưng không tương xứng |
| Phủ từ vựng nhãn | ngày 3 (đã biết config) + đếm flow ngày 3 | + ngày 4–5 (malware còn thiếu) | Đầy đủ |
| Giải được bất thường iPhone? | Không (cần ngày 4–5 hoặc ngày chứa 1,031 flow) | Có thể | Có |
| Quyết định được Mode A? | Không đủ (cần ngày 4–5) | Có thể | Có |
| Giá trị khoa học trên chi phí | Cao: xác nhận/bác bỏ nhanh mức đồng nhất của taxonomy | Trung bình, rủi ro kỹ thuật | Thấp trước khi có phản hồi tác giả |

Ghi chú: `conn.log.labeled` ngày 02-22 của Ubuntu (603,449 B) nằm trọn trong 500 KB đầu đã tải (prefix cần 2.3 MB giải nén < 2.7 MB thu được), nên chưa cần tải lại; chưa phân tích để giữ preflight không biến thành E2b. Con số 9 archive ở trên là cận trên.

## 3. Kế hoạch tối thiểu đề xuất (chờ phê duyệt; **chưa thực hiện**)
**Gate E2b-LIMITED = Lựa chọn T + hỏi tác giả song song**
1. Range tiền tố tới hết `conn.log.labeled` cho 9 archive (ngày 2023-02-22); giới hạn cứng tổng 20 MB; không tải archive đầy đủ.
2. Đếm `label × detailedlabel` cho 11 device-day; đối chiếu số dòng với summary table (`Day3TotalFlows`) để kiểm tra tính trọn vẹn của trích xuất.
3. Soạn câu hỏi cho tác giả (stratosphere@aic.fel.cvut.cz): (a) bảng `detailedlabel` đầy đủ và ý nghĩa tiền tố; (b) `Unknown` vs `background`; (c) Nukesped miner/RAT, PwnRing/PwnRig; (d) VM1 hai malware – một quy tắc; (e) nguồn 1,031 malicious flow của iPhone; (f) ngày 7 tái ghi 30/04/2023 và tác động tới nhãn.
4. Không có bước tải lớn hơn khi chưa có phê duyệt mới; quyết định mở rộng (O2/F) chỉ sau khi có kết quả T và phản hồi.

## 4. Giả định cần kiểm khi thực hiện
- Vị trí `conn.log.labeled` ngày đầu của Win7full-1 chưa biết; tỷ lệ nén `conn.log` có thể khác `dns.log`.
- Sau khi giải nén một phần, chỉ chấp nhận member nếu độ dài thu được bằng kích thước trong header tar (tránh dùng dữ liệu cắt cụt).
