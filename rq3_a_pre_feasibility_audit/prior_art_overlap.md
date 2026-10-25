# Chồng lấn với prior art và với các thiết kế đã bị bác (G2, task 3–4)

## 1. Vấn đề bảo toàn thứ hạng (task 3)
Gọi `s(x)` là điểm gốc của một arm trên lô đích. Hiệu chỉnh posterior theo prior (Saerens et al., 2002; trọng số BBSE `w(y)` của Lipton et al., 2018) cho trường hợp nhị phân là:

`p'(x) = w₁·s(x) / (w₁·s(x) + w₀·(1 − s(x)))`, với `w₁, w₀ > 0` là hằng số trong cả lô.

Hàm `g(s) = w₁s / (w₁s + w₀(1−s))` có đạo hàm `w₀w₁ / (w₁s + w₀(1−s))² > 0` trên (0, 1). Đây là hàm **tăng nghiêm ngặt** theo `s`. Từ đó:

- **Top-K với K cố định.** Thứ tự xếp hạng theo `p'` trùng thứ tự theo `s`, nên tập K flow được chọn **giống hệt** top-K thuần. Recall@K, precision@K và FAR@K không đổi.
- **Hòa điểm.**
  - Cặp hòa trên `s` vẫn hòa trên `p'`, nên quy tắc tie-break quyết định *cả hai* phương án như nhau.
  - Ngoại lệ duy nhất là sai số làm tròn. Nếu `p'` được tính rồi ép về float32, hai giá trị `s` khác nhau rất gần nhau có thể bị gộp thành một (tạo hòa mới). Hiếm hơn, nếu tính bằng float64 trên đầu vào float32 thì không phát sinh hòa mới. Mọi khác biệt kiểu này là **artefact số học, không phải cơ chế**, và tôi không ước lượng được nó vì vector MISSING.
  - TabFM có nhiều ngưỡng lịch sử bằng 0,5 (M7). Điều đó liên quan đến lưới ngưỡng (0,01–0,99, bước 0,01; tie-break gần 0,5 rồi thấp hơn, theo `07` dòng 51), không liên quan đến hòa điểm theo flow.
- **AUC và AP** bất biến dưới mọi biến đổi tăng nghiêm ngặt. Vậy A **không thể** sửa các trường hợp thất bại xếp hạng (AUC ≤ 0,5 hoặc AP ≤ π). Trong 180 cặp M7 có 26 cặp như vậy ở S2.
- **Ngưỡng cố định τ trên `p'`** tương đương với ngưỡng `g⁻¹(τ)` trên `s`. Như vậy A chỉ là **một quy tắc chọn điểm vận hành khác**: tức là chọn số cảnh báo K̂ = #{s ≥ g⁻¹(τ)}.
  - Ở endpoint **cùng ngân sách cảnh báo**, K là cố định, nên A ≡ top-K thuần.
  - Ở endpoint **cùng FAR mục tiêu**, ngưỡng đạt FAR α trên nguồn đã giữ FAR α trên đích *dưới label shift*, vì P(s | Y = 0) không đổi. Hiệu chỉnh posterior không thêm gì cho việc kiểm soát FAR. Khi P(X | Y) thay đổi, cả hai đều mất bảo đảm.

**Kết luận task 3.** "Posterior correction + top-K" **không chọn khác** top-K thuần, ngoại trừ artefact hòa điểm do làm tròn. Ở cả hai endpoint mà nhiệm vụ yêu cầu, phần còn lại của A quy về một quy tắc chọn K hoặc chọn ngưỡng.

## 2. Biến thể "cân lại lớp trong context của TabFM"
- Biến thể này **thay chính hàm chấm điểm**, nên về nguyên tắc *có thể* đổi thứ hạng. Đây là phần duy nhất của A không bị lập luận ở mục 1 loại trừ.
- **Ánh xạ prior art.** Đây là dạng ICL của bước sửa chuẩn trong BBSE: huấn luyện lại có trọng số theo `w(y)` (importance-weighted ERM, Lipton et al., 2018), thực hiện bằng cách lấy mẫu lại thay vì gán trọng số loss. Lấy mẫu lại tập huấn luyện cho khớp prior đích cũng là cách xử lý prior shift cổ điển.
- **Dưới chính giả định label shift**, posterior Bayes của mô hình được cân lại là `p'(y|x) ∝ w(y)·p(y|x)`. Đó là một biến đổi đơn điệu của posterior gốc, nên thứ hạng *lý tưởng* không đổi. Mọi khác biệt xếp hạng quan sát được sẽ đến từ hành vi không lý tưởng của ICL (lỗi xấp xỉ, nhạy với thành phần context), chứ không đến từ cơ chế có căn cứ. Như vậy hoặc nó tương đương mục 1, hoặc lợi ích (nếu có) không có lời giải thích đúng dưới giả định của chính nó.
- **Đối chứng công bằng.** Phải là LightGBM S1-ctx huấn luyện lại với trọng số lớp `w(y)` trên **cùng** context (B13-kiểu). Khi đó phần "riêng TabFM" phải vượt cả LightGBM có trọng số.
- **Khả thi.** Biến thể cần điểm phản thực cho context mới, tức là inference mới. File `05_saved_prediction_reuse.md` (C-R3/B11–B13) ghi: "artifact hiện có không cung cấp". Đây cũng là lý do `12_d3_method_feasibility.md` đánh **C3 (label-budgeted ICL context refresh) NO-GO**.

## 3. So với các thiết kế đã bị bác
| Thiết kế trước | Phán quyết | Quan hệ với A |
|---|---|---|
| **D3-C2**: recalibration/label-shift correction rồi budget selection (`12_d3_method_feasibility.md`) | **NO-GO** như đóng góp đứng riêng: "biến đổi tăng nghiêm ngặt không cải thiện ranking ở fixedK"; chỉ giữ làm baseline/thành phần | **Phần lõi của A trùng D3-C2.** Đề xuất A trong lượt trả lời trước của tôi đã không đối chiếu điểm này. Đó là một thiếu sót của tôi, không phải thông tin mới |
| **C-R1**: rank-budget/quantile routing (`hybrid_routing_spec/03_candidate_specifications.md`) | NO-GO cho tuyên bố cải tiến; **baseline bắt buộc B4** | Top-K/quantile trong A chính là baseline này |
| **D3-C3**: ICL context refresh có ngân sách nhãn | NO-GO (thiếu timestamp và điểm phản thực) | Biến thể cân lại lớp trong context thuộc cùng họ, gặp cùng chặn |
| **O2** (`thesis_method_recovery_gate/closed_directions_registry.md`): quy tắc chọn/tie-break ngưỡng lịch sử | NO-GO, REJECTED_MECHANISM ("tham số của C2/C-R1") | Bất kỳ "tinh chỉnh tie-break" nào để cứu A đều rơi vào đây |
| **D3-C1** admission contract | NO-GO (≡ joint-risk LTT) | Không liên quan trực tiếp; loại trừ hướng "thêm chứng nhận rủi ro" |

## 4. Prior art đã có (mức xác minh)
| Thành phần A | Prior art | Mức xác minh tại đây |
|---|---|---|
| Ước lượng prevalence đích từ dữ liệu chưa nhãn | BBSE (Lipton, Wang & Smola, ICML 2018; [35] trong đề cương); EM (Saerens, Latinne & Decaestecker, Neural Computation 2002); họ phương pháp quantification | Theo trích dẫn đã có trong đề cương và do người nghiên cứu cung cấp; không đọc lại toàn văn trong gate này |
| Hiệu chỉnh posterior theo prior | Saerens et al. (2002) | Như trên |
| Ngưỡng theo ngân sách cảnh báo, top-K/quantile | Alert-budget và quantile threshold; C-R1/B4 trong repo | Repo `hybrid_routing_spec/02`, `03` |
| Ngưỡng thích nghi dưới dịch chuyển | ACI (Gibbs & Candès, 2021; [23]) | Theo đề cương |
| Huấn luyện lại có trọng số theo label shift | BBSE importance-weighted ERM (Lipton et al., 2018) | Như trên |

## 5. Còn lại thay đổi kỹ thuật nào phân biệt được?
**Không.** Lý do:
- (i) Hiệu chỉnh posterior cộng top-K tương đương top-K thuần.
- (ii) Hiệu chỉnh posterior cộng ngưỡng cố định chỉ là một quy tắc chọn K hoặc chọn ngưỡng, thuộc họ C2/C-R1/O2 đã bác. Dưới label shift nó không thêm gì cho FAR, và khi label shift sai thì nó mất căn cứ.
- (iii) Cân lại lớp trong context là bản ICL của importance-weighted ERM trong BBSE. Dưới chính giả định của nó, nó tương đương về thứ hạng lý tưởng. Nó cần inference mới, và thuộc họ C3 đã bác.

Ghép các mô-đun đã biết này lại không tạo thành cải tiến có thể tuyên bố.
