# [x.y] Phân tích đối chứng thất bại theo mô hình trong điều kiện ngữ cảnh tương đồng

> **Trạng thái:** BẢN NHÁP CHỜ NGƯỜI NGHIÊN CỨU DUYỆT. Số mục `[x.y]` là chỗ giữ chỗ; số chính thức lấy theo chương RQ3 khi chèn (xem `rq3_insertion_map.md`). Các mã nguồn [M7-…], [H12], [D3-…] được giải nghĩa ở cuối mục.

## [x.y].1 Động cơ và phạm vi

Phân tích H1/H2 ở mục trước ghi nhận hai hiện tượng [H12]. Thứ nhất, khả năng xếp hạng vượt mức tham chiếu không bảo đảm recall dương tại ngưỡng lịch sử (H1). Thứ hai, recall bằng 0 tại ngưỡng lịch sử xuất hiện lặp lại ở nhiều lần chuyển ngày (H2). Cả hai đều được đánh giá SUPPORTED_DESCRIPTIVELY.

Báo cáo H1/H2 cũng cho thấy recall bằng 0 có ở cả các arm LightGBM lẫn các arm mô hình nền dạng bảng [H12]. Từ đó nảy sinh một câu hỏi hẹp hơn: khi hai mô hình nhận *cùng* ngữ cảnh và được đánh giá trên *cùng* tập dữ liệu, các thất bại này có tập trung một cách nhất quán ở TabFM hay không?

Mục này trả lời câu hỏi đó bằng một phân tích mô tả hậu nghiệm (gọi là M7). Phân tích chỉ đọc các chỉ số đã lưu, không chạy suy luận mô hình, không huấn luyện và không điều chỉnh ngưỡng [M7-R]. M7 không phải kiểm định được đăng ký trước. Trong giao thức D3, cặp S2/S1-ctx chỉ là một trong 15 cặp mô tả, không phải cặp chính [D3-A]. Kết quả M7 vì vậy không được dùng làm bằng chứng về cải tiến phương pháp.

## [x.y].2 Thiết kế đối chứng S2 – S1-ctx

Hai arm của Study 1 được so sánh như sau [D3-A]:

- **S2:** TabFM với ngữ cảnh gần (recent source-day context, 10.000 hàng).
- **S1-ctx:** LightGBM được huấn luyện trên đúng ngữ cảnh gần đó.

Theo hồ sơ đã khóa, hai arm có cùng hash ngữ cảnh (X, y) và cùng hash `eval_X` ở cả 180 cặp [D3-A]. Mỗi đơn vị phân tích là một cặp (lần chuyển, seed), với 12 lần chuyển (6 NF-ToN-IoT-v3, 6 NF-CICIDS2018-v3) × 15 seed = 180 cặp quan sát.

Trong mỗi cặp, hai arm được đánh giá trên cùng tập đánh giá E_(t,s) gồm 10.000 hàng. Số hàng tấn công P dao động từ 437 đến 9.106 và số hàng bình thường N tối thiểu là 894 [M7-M]. Ở cả 180 cặp, các chỉ số AUC, AP và recall@τ của hai arm đều ở trạng thái VALID [M7-R].

Mỗi arm giữ ngưỡng lịch sử riêng của mình. Đó là ngưỡng chỉ dùng dữ liệu nguồn, được phục hồi theo quy tắc đã đăng ký: macro-F1 cực đại trên mẫu ngưỡng của ngày nguồn [D3-I]. M7 không ép hai arm dùng chung một ngưỡng và không khớp lại ngưỡng. Vì vậy, khác biệt tại điểm vận hành phản ánh đồng thời mô hình và ngưỡng lịch sử của chính nó.

## [x.y].3 Định nghĩa thất bại

M7 dùng lại nguyên các tham chiếu R1/R2 đã khóa của D3, không đặt ngưỡng cắt mới [M7-R][H12]:

- **Thất bại ngưỡng** (threshold failure): recall@τ = 0 tại ngưỡng lịch sử của arm (R2).
- **Thất bại xếp hạng** (ranking failure): ROC-AUC ≤ 0,5 (R1a) hoặc AP ≤ π (R1b), trong đó π là tỉ lệ tấn công của chính tập đánh giá.
- **Nhân chứng H1** (H1 witness): một arm không thất bại xếp hạng nhưng thất bại ngưỡng trên cùng đơn vị.

Mỗi cặp được xếp vào đúng một trong bốn nhóm, riêng cho từng loại thất bại: cả hai thất bại, chỉ TabFM, chỉ LightGBM, hoặc không mô hình nào thất bại.

## [x.y].4 Kết quả phân loại theo cặp

**Bảng [x.y]-1.** Phân loại thất bại theo cặp S2 (TabFM) – S1-ctx (LightGBM), 180 cặp (lần chuyển, seed). Nguồn: [M7-M], [M7-T].

| (a) Thất bại ngưỡng: recall@τ = 0 | LightGBM thất bại | LightGBM không thất bại | Tổng |
|---|---:|---:|---:|
| **TabFM thất bại** | 20 | 1 | 21 |
| **TabFM không thất bại** | 9 | 150 | 159 |
| **Tổng** | 29 | 151 | 180 |

| (b) Thất bại xếp hạng: AUC ≤ 0,5 hoặc AP ≤ π | LightGBM thất bại | LightGBM không thất bại | Tổng |
|---|---:|---:|---:|
| **TabFM thất bại** | 14 | 12 | 26 |
| **TabFM không thất bại** | 16 | 138 | 154 |
| **Tổng** | 30 | 150 | 180 |

Không có cặp nào thuộc nhóm "không kết luận được" [M7-T].

Ở cả 180 cặp, cờ recall bằng 0 tính lại trong M7 khớp với cờ đã lưu trong bảng so sánh nguồn [M7-M]. Tổng số đơn vị recall bằng 0 (S2: 21/180; S1-ctx: 29/180) cũng trùng với số đã báo trong phân tích H2 [H12].

Trong phạm vi đối chứng này, có 18 nhân chứng H1 ở TabFM và 12 ở LightGBM. Hai con số này khớp với bảng H1/H2 cho S2 và S1-ctx [H12].

Bảng [x.y]-1 chỉ là các tần số mô tả. Các cặp chia sẻ tập đánh giá chồng lấn giữa các seed và không phải các lần lặp độc lập (xem [x.y].8). Vì vậy mục này không báo khoảng tin cậy, giá trị p hay cỡ hiệu ứng.

## [x.y].5 Tính không đồng nhất giữa các lần chuyển

Thất bại phân bố rất không đều giữa các lần chuyển [M7-T]:

- **Thất bại ngưỡng** chỉ xuất hiện ở 4/12 lần chuyển. Có 28/30 cặp mang ít nhất một thất bại ngưỡng nằm ở hai lần chuyển của NF-CICIDS2018-v3:
  - 2018-02-16→2018-02-20: cả hai arm thất bại ở 15/15 seed.
  - 2018-03-01→2018-03-02: cả hai thất bại ở 5 seed; chỉ LightGBM thất bại ở 8 seed.
  - Hai trường hợp còn lại là đơn lẻ: chỉ LightGBM ở CIC 2018-02-20→2018-02-21 (1 seed) và chỉ TabFM ở ToN 2019-04-27→2019-04-28 (1 seed).
- **Thất bại xếp hạng** cũng chỉ xuất hiện ở 4/12 lần chuyển, và chiều khác biệt giữa hai mô hình đảo ngược theo lần chuyển:
  - CIC 2018-02-16→2018-02-20: LightGBM thất bại ở 13 seed, TabFM ở 0 seed.
  - CIC 2018-03-01→2018-03-02: TabFM thất bại ở 12 seed, LightGBM ở 4 seed.
  - Ở hai lần chuyển ToN, số seed thất bại tương đương: 2019-04-24→2019-04-25 là 10 (TabFM) và 9 (LightGBM); 2019-04-27→2019-04-28 là 4 và 4.
- Bảy lần chuyển còn lại không ghi nhận thất bại ngưỡng hay thất bại xếp hạng ở arm nào.

**Bảng [x.y]-2.** Các lần chuyển có ít nhất một thất bại. Giá trị là trung vị theo seed, chép từ [M7-T]; số seed thất bại tính trên 15.

| Lần chuyển | Thất bại ngưỡng: cả hai / chỉ TabFM / chỉ LGBM | Thất bại xếp hạng: TabFM / LGBM | AUC trung vị LGBM / TabFM | Recall@τ trung vị LGBM / TabFM |
|---|---|---|---|---|
| CIC 2018-02-16→2018-02-20 | 15 / 0 / 0 | 0 / 13 | 0,372 / 0,998 | 0,000 / 0,000 |
| CIC 2018-02-20→2018-02-21 | 0 / 0 / 1 | 0 / 0 | 0,995 / 1,000 | 0,004 / 0,275 |
| CIC 2018-03-01→2018-03-02 | 5 / 0 / 8 | 12 / 4 | 0,564 / 0,479 | 0,000 / 0,003 |
| ToN 2019-04-24→2019-04-25 | 0 / 0 / 0 | 10 / 9 | 0,433 / 0,396 | 0,123 / 0,084 |
| ToN 2019-04-27→2019-04-28 | 0 / 1 / 0 | 4 / 4 | 0,605 / 0,587 | 0,072 / 0,008 |

Tóm lại, kiểu thất bại và mô hình thất bại phụ thuộc vào lần chuyển, không phụ thuộc nhất quán vào loại mô hình. Đơn vị diễn giải là lần chuyển; 15 seed trong mỗi lần chuyển là các lần đo lặp trên những tập đánh giá chồng lấn.

## [x.y].6 Nghiên cứu tình huống: xếp hạng cao nhưng recall bằng 0 tại ngưỡng lịch sử

Lần chuyển NF-CICIDS2018-v3 2018-02-16→2018-02-20 cho thấy rõ nhất sự tách biệt giữa xếp hạng và quyết định tại ngưỡng [M7-T][M7-S][M7-M]:

- **TabFM (S2):** AUC trung vị 0,998 (khoảng theo seed 0,9973–0,9994) và AP trung vị 0,972, nhưng recall@τ = 0 ở cả 15/15 seed. Ngưỡng lịch sử của S2 bằng 0,5 ở 14/15 seed. Như vậy cả 15 seed đều là nhân chứng H1: mô hình phân tách tốt hai lớp trên tập đích, nhưng không flow tấn công nào vượt ngưỡng lịch sử. Đây là thất bại tại điểm quyết định, không phải thất bại xếp hạng.
- **LightGBM (S1-ctx), cùng ngữ cảnh và cùng tập đánh giá:** AUC trung vị 0,372, AP trung vị 0,210, thất bại xếp hạng ở 13/15 seed, và recall@τ = 0 ở cả 15/15 seed. Hai seed còn lại có AUC vượt 0,5 nhưng recall vẫn bằng 0, nên cũng là nhân chứng H1.

Như vậy cùng một lần chuyển tạo ra recall bằng 0 cho cả hai mô hình, nhưng qua hai con đường khác nhau. Với TabFM, xếp hạng còn tốt nhưng ngưỡng lịch sử không còn phù hợp. Với LightGBM, phần lớn seed đã thất bại ngay ở xếp hạng.

Báo cáo H1/H2 ghi nhận recall bằng 0 ở lần chuyển này cho cả sáu arm × 15 seed, nên trường hợp này không thể quy cho riêng TabFM [H12]. Vì không có vector điểm theo flow, M7 không xác định được phân phối điểm của các flow tấn công nằm ở đâu so với ngưỡng. Nhận xét "điểm của flow tấn công nằm dưới ngưỡng" chỉ là suy ra từ định nghĩa recall@τ = 0, không phải quan sát trực tiếp phân phối điểm [M7-S].

## [x.y].7 Diễn giải

Ba quan sát dẫn tới phán quyết **SHARED_OR_MIXED_FAILURE** của M7 [M7-V]:

1. **Thất bại ngưỡng chủ yếu là chung hoặc thuộc LightGBM.** Thất bại ngưỡng chỉ riêng TabFM xảy ra ở 1/180 cặp. Phần lớn các cặp thất bại là cả hai mô hình cùng thất bại (20) hoặc chỉ LightGBM thất bại (9).
2. **Thất bại xếp hạng không có chiều nhất quán.** Số cặp chỉ TabFM thất bại (12) và chỉ LightGBM thất bại (16) cùng bậc, và chiều chênh lệch đảo ngược giữa hai lần chuyển CIC có khác biệt lớn. Trong 12 cặp chỉ TabFM thất bại xếp hạng, 8 cặp nằm ở CIC 2018-03-01→2018-03-02 [M7-M].
3. **Thất bại tập trung theo lần chuyển, không theo mô hình.** Thất bại tập trung vào một số ít lần chuyển, và ở trường hợp nổi bật nhất (mục [x.y].6) cả hai mô hình cùng có recall bằng 0 qua hai con đường khác nhau.

Từ đó, phát biểu kết luận cho mục này như sau:

> Trong phạm vi 180 cặp đánh giá có ngữ cảnh tương đồng, M7 không ghi nhận một mẫu thất bại nhất quán đặc thù cho TabFM so với LightGBM. Các thất bại phụ thuộc vào lần chuyển (transition) và điểm vận hành lịch sử; bằng chứng hiện có chưa cho phép quy nguyên nhân cho riêng cơ chế học trong ngữ cảnh (ICL).

Phát biểu này **không** có nghĩa TabFM không thể có thất bại đặc thù theo mô hình. Ở CIC 2018-03-01→2018-03-02, TabFM thất bại xếp hạng nhiều hơn LightGBM (12 so với 4 seed). Ở ToN 2019-04-27→2019-04-28 có một cặp chỉ TabFM thất bại ngưỡng. M7 chỉ cho thấy rằng, với các cặp đã lưu này, không có mẫu *nhất quán* nào như vậy.

Việc ghép ngữ cảnh cũng không cô lập mọi khác biệt giữa hai mô hình, chẳng hạn họ hàm, cách tạo xác suất hay ngưỡng lịch sử riêng. Do đó không thể quy khác biệt quan sát được cho cơ chế ICL.

Kết quả M7 phù hợp với cách đọc hẹp của H1/H2: xếp hạng vượt tham chiếu không bảo đảm recall dương tại ngưỡng lịch sử, và recall bằng 0 không phải hiện tượng riêng của TabFM [H12]. M7 bổ sung một điểm cho cách đọc đó: ngay cả khi ghép ngữ cảnh, sự đồng xuất hiện thất bại vẫn mang tính chung hoặc hỗn hợp.

## [x.y].8 Giới hạn và hàm ý cho RQ3

**Giới hạn:**

- **Bản chất bằng chứng.** M7 là phân tích khám phá, mô tả hậu nghiệm, chưa được xác nhận độc lập. NF-ToN-IoT-v3 và NF-CICIDS2018-v3 đã được dùng trong quá trình phát triển phương pháp, nên kết quả trên chúng không phải xác nhận độc lập.
- **Tính độc lập.** Seed và các hàng đánh giá chồng lấn không phải các lần lặp khoa học độc lập (1.260 cặp seed chồng lấn theo J13 [D3-A]). Không gộp flow qua các lô đánh giá. Đơn vị diễn giải là lần chuyển, và chỉ có 12 lần chuyển trên hai bộ dữ liệu.
- **Phân phối điểm.** Không có vector điểm theo flow, nên M7 *không* phân tích trực tiếp phân phối điểm [M7-S]. Các nhận xét về vị trí điểm so với ngưỡng chỉ gián tiếp.
- **Gắn hàng.** Danh tính hàng giữa hai arm dựa trên bằng chứng nguồn đã khóa (comparability key, hash ngữ cảnh và `eval_X`). M7 không kiểm lại độc lập ở cấp hàng [M7-R].
- **Ngưỡng.** Ngưỡng lịch sử khác nhau giữa hai arm và không được khớp lại. Đây là các ngưỡng phục hồi chỉ dùng dữ liệu nguồn, không phải giá trị đã tuần tự hóa nguyên bản [D3-I].
- **Ràng buộc số học.** D3-1E giữ trạng thái PARTIAL với historical BLOCK không đổi [D3-I]. Các ràng buộc QA số học vẫn áp dụng, gồm 487 nhãn nhạy làm tròn và 42 dấu cấp seed chưa chứng nhận. Ở 180 cặp của M7, QA dấu cho AUC, AP và recall đều ở trạng thái ROBUST, và M7 không dùng các nhãn bị hạn chế [M7-R].
- **Phạm vi suy rộng.** M7 không nói gì về khả năng triển khai, hiệu năng cảnh báo trong vận hành thực hay khả năng tổng quát hóa ra ngoài hai bộ dữ liệu này.

**Hàm ý cho RQ3:**

- M7 thu hẹp cách diễn giải H1/H2. Không có căn cứ mô tả thất bại tại ngưỡng lịch sử trong Study 1 như một điểm yếu riêng của TabFM hay của ICL. Bằng chứng hiện có cho thấy thất bại gắn với lần chuyển và với điểm vận hành lịch sử, và xuất hiện ở cả mô hình đối chứng LightGBM có cùng ngữ cảnh.
- Vì vậy, M7 không mở ra một đóng góp phương pháp mới. Kết quả này chỉ được dùng để giới hạn phạm vi phát biểu về H1/H2 trong chương RQ3.

---

### Nguồn trích dẫn (truy vết)

| Mã | Tệp (repo `rq3-dataset-audit`, nhánh `claude/sleepy-cray-jhn01z`, hoặc gói handoff) |
|---|---|
| [M7-V] | `m7_tabfm_failure_specificity/final_verdict.md` |
| [M7-M] | `m7_tabfm_failure_specificity/failure_pair_matrix.csv` (180 hàng) |
| [M7-T] | `m7_tabfm_failure_specificity/transition_level_summary.md` |
| [M7-R] | `m7_tabfm_failure_specificity/input_and_pair_readiness.md` |
| [M7-S] | `m7_tabfm_failure_specificity/score_distribution_diagnostics.md` |
| [H12] | Gói handoff `10_h1_h2_verdict.md` (H1/H2 SUPPORTED_DESCRIPTIVELY) |
| [D3-A] | Gói handoff `08_rq3_arms_and_score_join_EXTRACT.md` (arm registry, ghép ngữ cảnh, J13) |
| [D3-I] | Gói handoff `11_d3_interpretation_limits.md` (D3-1E PARTIAL, 487/42), `07_rq3_study1_protocol_and_results_EXTRACT.md` (giao thức Study 1) |
| Nguồn số | Gói handoff `14_matched_lightgbm_tabfm_comparisons.csv`, SHA-256 `5078e32146e1e0d6294395c1aade1f554a5f8ee085217ca52da207c6ac7bf6c0` |
