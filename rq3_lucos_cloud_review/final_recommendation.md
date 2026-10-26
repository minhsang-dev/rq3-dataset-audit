# RQ3 — LUCoS extension: **READY_FOR_LOCAL_PROTOTYPE**

Trạng thái này **không** có nghĩa là đã chứng minh tính mới hay hiệu quả.

**Đề xuất sửa đổi:** LUCoS-CQ. Giữ LUCoS cho phần lõi `B − ⌊ρB⌋` medoid. Dành `⌊ρB⌋` cho medoid của các cụm chặt nhỏ, phát hiện bằng chênh lệch mật độ kNN trong không gian PIN, có tách khỏi các medoid đã chọn. Quy trình một vòng, không nhãn. Pseudocode ở `candidate_selector_spec.md` §2.

**Phương pháp gần nhất:**
- LUCoS (arXiv:2605.27254, đọc toàn văn) cho phần lõi.
- He & Carbonell (NIPS 2007, NNDB) cho tiêu chí chênh lệch mật độ.
- HaCon (Chen et al.) cho phân tầng theo cụm không nhãn.

Không tìm thấy quy tắc giống hệt trong phạm vi tìm có giới hạn. Tính mới ở mức **gia tăng**, nhắm vào giới hạn mà tác giả LUCoS tự nêu (§5, tr. 10).

## Bốn tiêu chí
| Tiêu chí | Đánh giá |
|---|---|
| 1. Phương pháp gốc đã công bố | **Có:** LUCoS, mã MIT `ari-dasci/S-LUCoS` |
| 2. Sửa đổi kỹ thuật cụ thể | **Có:** pseudocode, tham số khóa trước, quy tắc tách |
| 3. Đo hiệu quả so với baseline mạnh | **Có kế hoạch:** 13 selector cùng tham chiếu oracle; hit rate và AUPRC; ablation quyết định là "anomaly quota" (#9) |
| 4. Dữ liệu và tính toán khả thi | **Có điều kiện:** CPU đủ cho chọn mẫu; nhưng giới hạn đầu vào của TabClustPFN và dữ liệu xác nhận sạch chưa có |

## Phải kiểm ở máy cục bộ trước khi khóa giao thức
1. Clone `ari-dasci/S-LUCoS` và `Tianqi-Zhao/TabClustPFN`; ghi **hash commit**. Tải `step-10000.ckpt` từ Google Drive; ghi **SHA-256**.
2. Giới hạn đầu vào của PIN Encoder (số hàng, số cột, kiểu dữ liệu), lấy từ mã nguồn. Kiểm xem 46 đặc trưng NetFlow có đi qua được mà không bị cắt không.
3. Đường tiền xử lý của LUCoS (one-hot, chuẩn hóa) và cách nó xử lý đặc trưng đuôi nặng.
4. Chi phí K-Medoids trên kích thước pool dự định, và quy tắc lấy mẫu con.
5. Mức phơi nhiễm của ngày và dataset dùng làm pool. Nguồn xác nhận độc lập hiện **không có**. Kết quả trên NF-ToN/NF-CIC chỉ là phát triển.

Không chạy thí nghiệm nào cho đến khi giao thức và script phân tích được khóa.
