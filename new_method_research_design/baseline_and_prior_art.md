# Baseline và kiểm tra prior art/tương đương

## Baseline được chọn: CURE
**CURE** — Lee, Choi, Choi, Yoo. "Bounded Context Management for Tabular Foundation Models on Stream Learning". arXiv:**2606.18677v1** (17/6/2026), spotlight FMSD@ICML 2026; có link mã nguồn và dataset. Đọc toàn văn một phần (§3–4).
Cơ chế (theo §4):
- Ngân sách context chia thành short bank `S_t` (FIFO recent) và long bank `L_t`.
- Mẫu rời `S_t` được nhận vào `L_t` nếu **entropy dự đoán đã lưu > τ** (sau khi `L_t` đầy).
- Khi `L_t` vượt ngân sách: chọn lớp `c_max` có nhiều mẫu nhất **theo y**; tìm cặp cùng lớp gần nhất trong biểu diễn chuẩn hóa `ϕ(x)`; loại điểm cuối **xa centroid lớp `c_max` của short bank** hơn.
- Backbone dùng TabICL-v2; trùng với arm S2-ICL của luận án.

Lý do chọn: chi tiết đủ để tái hiện, có mã, là biến thể mạnh nhất hiện biết (xếp hạng 1 trong các policy, theo tác giả). Thành phần sẽ sửa: **bước chọn nạn nhân và phạm vi tìm cặp khi loại bỏ**.

## So sánh với biến thể mạnh nhất
| Phương pháp (định danh, mức xác minh) | Khóa cân bằng | Quy tắc loại bỏ | Khác với đề xuất |
|---|---|---|---|
| FIFO / recency (2610.05352v1, đọc một phần: FIFO là mặc định mạnh) | — | Cũ nhất | Không có bộ nhớ dài hạn |
| DualFIFO (Lourenço et al., KDD '26, DOI 10.1145/3770854.3780305; arXiv:2502.16840v2, đọc §4/Alg. 1) | y | Mẫu cũ nhất của lớp dư nhất | Không thấy họ; không có redundancy |
| CURE (2606.18677v1) | y | Cặp gần nhất cùng y trong lớp dư nhất; loại điểm xa centroid | Không thấy họ; cặp có thể gồm hai họ khác nhau |
| CBRS (Chrysakis & Moens, ICML 2020, PMLR 119:1952–1961; chỉ snippet) | lớp | Cân bằng reservoir cho replay huấn luyện | Bộ nhớ replay cho mô hình học bằng gradient, không phải context ICL |
| CBRS mở rộng cho NIDS (Amalapuram et al., NeurIPS 2023; chỉ snippet) | lớp tấn công | CBRS dưới mất cân bằng nặng | Huấn luyện mô hình; khóa = lớp dự đoán |
| Replay cân bằng lớp neo benign cho IDS (arXiv:2608.04602v1; abstract) | lớp | Replay benign ở mọi lần cập nhật | Huấn luyện transformer; khóa = lớp dự đoán |
| S3 mixed-history của luận án (O1, đã NO-GO ≡ DualFIFO) | nhị phân | Lấy đều lịch sử | Không phải đề xuất này |
| Tab-AICL, LUCoS (chọn dòng để gán nhãn) | — | — | Bài toán khác (thu nhãn), không phải eviction với cùng luồng nhãn |

## Kiểm tương đương
- **Không phải đổi tên:** quyết định loại bỏ thay đổi trên một witness cụ thể. Với nhãn nhị phân, trạng thái và quyết định của CURE **bất biến theo danh tính họ**; đề xuất phụ thuộc họ. Cấu hình witness: cặp gần nhất trong lớp attack gồm một dòng họ hiếm `r` và một dòng họ lũ `f`; `r` xa centroid attack hơn; họ của `r` còn đúng `m_floor` dòng. CURE loại `r`, đề xuất không loại `r`. Không tham số nào của CURE/DualFIFO với nhãn nhị phân tái tạo được quyết định này.
- **Không phải chỉ tinh chỉnh:** đề xuất thêm một biến quyết định (khóa họ `κ`), tách khỏi nhãn dự đoán, cùng một ràng buộc bảo vệ (floor). `m_floor` là tham số, nhưng cơ chế khóa tách rời không tồn tại trong CURE.
- **Rủi ro tương đương còn lại:**
  1. "CURE chạy với y = họ (ICL đa lớp) rồi gộp xác suất attack". Phải đưa vào làm comparator ở nơi khả thi; TabFM/TabPFN v1 chỉ nhận ≤ 10 lớp, CIC có 14 họ.
  2. CBRS với khóa họ chuyển sang context ICL: nếu ablation "floor + chọn ngẫu nhiên/cũ nhất trong họ" ngang đề xuất, đóng góp thu hẹp thành "CBRS-family cho context TFM".
  3. Cụm không giám sát thay cho nhãn họ: nếu ngang, nhãn họ không cần thiết và đóng góp phải đặc tả lại **trước** khi xác nhận.
- **Không phải comparator yếu:** baseline chính là CURE, không phải S2 hay FIFO.

## Đánh giá tính mới (trung thực)
Mức đóng góp là **cải tiến có điều kiện miền** cho một phương pháp đã có (CURE): đưa nguyên lý cân bằng theo lớp con của CBRS-NIDS vào eviction context của TFM nhị phân. Đây không phải thuật toán mới. Giá trị phụ thuộc hoàn toàn vào việc thắng CURE ở cùng ngân sách trên dữ liệu xác nhận sạch.
