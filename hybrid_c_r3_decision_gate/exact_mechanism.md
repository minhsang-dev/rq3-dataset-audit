# C-R3 — cơ chế nguyên văn (khôi phục từ `hybrid_routing_spec/03_candidate_specifications.md`, commit e6fed4d)

Không thêm, không sửa. Các phần nguồn để mở (TBD) được ghi là TBD.

## Các bước như đã đặc tả
1. Mỗi batch: edge (LightGBM) chấm điểm mọi flow.
2. Router chọn tập `R`, |R| ≤ b_c·n. **Quy tắc router: không được chỉ định** trong C-R3; trong đặc tả chỉ là "router chọn tập R".
3. Cloud (TFM) chấm `R` với context hiện tại `C_t`.
4. Tập `A ⊆ R`, |A| ≤ b_l·n, gửi analyst theo "quy tắc khóa trước". **Quy tắc acquisition: TBD.**
5. Nhãn về sau trễ `d`, được chèn vào `C_{t+1}` theo "quy tắc cập nhật khóa trước (ví dụ recency có giữ cân bằng lớp)". **Quy tắc cập nhật: TBD; chỉ có ví dụ.**
6. Một phần cố định `ε·b_l` nhãn lấy **ngẫu nhiên đều trong batch** để ước lượng prevalence và hạn chế thiên lệch chọn mẫu.
7. LightGBM edge được huấn luyện lại trên **đúng** tập nhãn đó (cùng wrapper).

## Điểm khác biệt mà C-R3 tự nêu
- Thu nhãn bị ràng buộc vào tập đã escalate.
- Có trễ nhãn.
- Cập nhật qua context ICL, không cần huấn luyện.

## Bất nhất nội bộ (ghi nhận, không sửa)
- Bước 4 nói `A ⊆ R`, nhưng bước 6 lấy `ε·b_l` nhãn đều trong **toàn batch**. Vậy tập nhãn thực là `A_R ∪ A_ε` với `A_R ⊆ R`, còn `A_ε` không nhất thiết ⊆ R. Ràng buộc "chỉ từ tập đã escalate" chỉ đúng cho phần `(1−ε)`.
- Ba thành phần mang tính quyết định (router, acquisition, update) đều TBD. Mọi khác biệt kỹ thuật phải nằm trong ba thành phần này, nên hiện **không có cơ chế cụ thể nào để kiểm**.

## Phần cố định khả dĩ duy nhất
Cố định duy nhất là cấu trúc **ghép nối**: nhãn lấy từ pool = R (trừ phần ε), dùng điểm cloud có sẵn trên R, đưa vào context TFM, có trễ d.
