# Truy cập archive CTU-SME-11 — các phương án và bằng chứng

Ngày: 2026-10-24 · Chỉ kiểm tra khả năng truy xuất; **không** phải Gate E2b.

## 1. Xác minh đầu ra Gate E2
- `gate_e2_outputs/`: 9 tệp được git theo dõi và đã push lên `origin/claude/sleepy-cray-jhn01z`.
- `sha256sum -c` trên 8 báo cáo liệt kê trong `gate_e2_outputs/manifest_sha256.md`: **8/8 OK**. (`manifest_sha256.md` không băm chính nó; băm của nó ghi ở `gate_e2b_preflight/manifest_sha256.md`.)

## 2. Có nơi nào công bố sẵn bảng nhãn / thống kê nhãn / Zeek logs tách riêng không?
| Nguồn kiểm tra | Kết quả |
|---|---|
| Zenodo record 7958259 (API) | 14 tệp: 11 archive `.tar.bz2` + `README.md` + `README.html` + `CTU-SME-11_summary_table.csv`. **Không** có bản Zeek-only, không có bảng nhãn. Chỉ 1 phiên bản (`versions`: 1 hit, `1.0.0`). |
| `README.md`, `summary_table.csv` | Chỉ tổng Benign/Malicious/Unknown theo thiết bị và flows theo ngày. Không có thống kê `detailedlabel`. |
| Luận văn Bendl 2023 (đã có) | Có số Benign/Malicious/Unknown tổng; **không có** bảng `detailedlabel`; có khuyến nghị device-day kèm tỷ lệ benign:malicious (Sec 11). Phụ lục A chỉ là poster. |
| Mirror Stratosphere `mcfp.felk.cvut.cz/publicDatasets/CTU-SME-11/` | Thư mục tồn tại nhưng **trống** (index không có tệp). |
| Trang `stratosphereips.org/datasets-ctu-sme-11` | 404. |
| Tìm kiếm web "label statistics / Data in Brief" | Không thấy bài Data in Brief công bố; luận văn nói chỉ là "draft ... to be sent". |
| Bài ontology nhãn (arXiv 2305.01337, được luận văn trích là [26]) | Có ontology 7 mức, **không** có bảng nhãn của CTU-SME-11 (xem `taxonomy_evidence_gaps.md`). |
| NetflowLabeler README (GitHub) | Công cụ; không chứa nhãn CTU-SME-11. |

→ **Không có bảng nhãn đầy đủ hay thống kê nhãn theo thiết bị/ngày được công bố riêng.** Cách duy nhất để lấy là từ các tệp trong archive (hoặc hỏi tác giả).

## 3. Cấu trúc archive — đã xác minh bằng đo trực tiếp
| Câu hỏi | Bằng chứng | Kết luận |
|---|---|---|
| Hỗ trợ HTTP Range? | `HTTP 206` với `Range: bytes=0-10485759` (2 archive) và `0-499999` (9 archive); 1 request `500000-1499999` cũng 206. Phản hồi HEAD không có `accept-ranges` nhưng Range vẫn hoạt động. | **Có** |
| Định dạng | `BZh9`, `.tar.bz2`, tar có header thư mục dạng `CTU-SME-11/<device>/<day>/…` | Theo luồng |
| Có chỉ mục (index) member hoặc bz2 block? | Không có trường/tệp chỉ mục nào trong record; tar không có chỉ mục; bz2 không có. | **Không** |
| Ngày đứng đầu trong tar | **11/11 archive bắt đầu bằng ngày `2023-02-22`** (đọc từ header tar trong 500 KB–10 MiB đầu của từng archive). | Ngày đầu xác định được, ngày sau thì chưa (thứ tự các ngày còn lại chưa quan sát) |
| Thứ tự trong một ngày | `artifacts/labels.config` → `README.html` → `zeek/*` → `raw/*.pcap` (quan sát đầy đủ ở Windows7AD-1 và Echo; ở 9 archive còn lại thấy `artifacts/` → `README.html` → `zeek/…`). `labels.config` nằm **trước** mọi log Zeek của ngày đó. | `labels.config` rất rẻ để lấy cho ngày đầu |
| Thứ tự các log trong `zeek/` | Không cố định giữa thiết bị (dns trước ở nhiều nơi; `conn.log.labeled` ở vị trí khác nhau; với Win7full-1, `dns.log.labeled` = 22.3 MB đứng trước và `conn.log.labeled` chưa thấy trong 6.3 MB giải nén đầu). | Phải đọc tuần tự tới `conn.log.labeled` |
| Truy cập ngẫu nhiên giữa archive | bz2 có block độc lập (~900 kB giải nén). Đã **xác minh** giải mã một block từ offset bất kỳ trên dữ liệu cục bộ (khớp tham chiếu) bằng cách dò magic 48-bit `0x314159265359` ở mọi bit-offset. | Khả thi về kỹ thuật |
| …nhưng chi phí? | Probe 400 KB tại 9 offset trong Win7AD-1 (1.58 GB) cho **0 block hoàn chỉnh** ở cả 9 (đã tốn 3.6 MB không thu được gì). Block nén của pcap đo được: trung vị 156 kB (Win7AD), 292 kB (Echo), tối đa 632 kB; vùng Zeek text 79–306 kB. Cần cửa sổ ≳ 1.3 MB cho mỗi probe để chắc chắn có block hoàn chỉnh. | **Không đủ ngân sách để chứng minh tìm biên ngày; chưa chứng minh được** |
| Tìm biên ngày bằng cách nào? | Không có index ⇒ phải dò theo nội dung (timestamp gói trong pcap, tên header tar). Chưa thực hiện thành công. | Chi phí là **ước tính**, xem §4 |
| Tốc độ | 1.0 MB trong 1.8 s (≈ 0.56 MB/s) cho 1 request đơn. Một mẫu, không đại diện. | Dùng cho ước tính thô |

Tổng kích thước 11 archive (Zenodo) = **96.6 GB** (Zenodo ghi "compressed 80 GB" trong mô tả — lệch, không giải thích được).

## 4. Các phương án truy xuất
| Phương án | Mô tả | Truyền (ước tính) | Trạng thái bằng chứng |
|---|---|---|---|
| **O0 — Hỏi tác giả** | Xin bảng `label × detailedlabel` theo thiết bị-ngày và bản mô tả quy tắc đặt tên | 0 | Không thực hiện được trong phiên này |
| **O1 — Ngày đầu mỗi archive** (`2023-02-22`) | Range tiền tố tới hết `conn.log.labeled` của ngày đầu cho 8–9 archive chưa có; `labels.config` ngày 02-22 của 11 archive **đã có** | ≈ 7–8 MB (8 archive biết vị trí conn.log) + ≈ 3–5 MB (Win7full-1, vị trí conn.log chưa biết) ⇒ **~10–15 MB** | Cấu trúc đã xác minh; ước lượng theo tỷ lệ nén đo ở đầu archive |
| **O2 — Thêm ngày khác bằng dò block bz2** | Tìm biên ngày bằng probe ≳1.3 MB + đọc tiếp `labels.config` (và tuỳ chọn zeek) | Ước tính **~8–13 MB mỗi biên** cho probe + 0.2 MB (`labels.config`) hoặc ~3–4 MB (zeek ngày); 66 device-day còn lại ⇒ ~0.5–1 GB | **Chưa chứng minh**; chi phí chỉ là ngoại suy từ kích thước block đo được |
| **O3 — Stream toàn bộ 11 archive** | Giải nén luồng, chỉ giữ `labels.config` + đếm nhãn | **96.6 GB** (≈ 48 giờ ở 0.56 MB/s một luồng; chưa đo song song) | Chắc chắn đầy đủ nhưng không tương xứng |

## 5. Hạn chế truy cập ngẫu nhiên (tóm tắt)
1. Không có index tar/bz2; vị trí byte của từng member chỉ biết khi đã đọc tuần tự tới đó.
2. Chỉ **ngày đầu** của mỗi archive định vị được miễn phí; ngày còn lại phải dò.
3. pcap nằm giữa các ngày ⇒ không thể "nhảy" qua mà không dò.
4. Thứ tự file trong `zeek/` không cố định ⇒ cần đọc tới `conn.log.labeled`.
5. Dữ liệu tải về là untrusted; xử lý trong thư mục riêng, không chạy mã từ nó (đã tuân thủ).
