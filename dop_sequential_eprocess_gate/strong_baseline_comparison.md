# So sánh với baseline mạnh (task 1, 2, 4)

## 1. Công thức DoP có thẩm quyền đọc được
Nguồn đọc được trong phiên này là **đề cương v9.16, Định nghĩa 8** (tệp đã tải lên). Nội dung:
- **Điểm theo nguồn.** Mỗi ngày d, nguồn k được chấm bởi mô hình học trên K − 1 nguồn còn lại. `T_{d,k}` là trung bình điểm nghi ngờ `1 − p̂(ỹ|x)` trên một mẫu n dòng cố định.
- **P-value ngang hàng.** `p_{d,k} = (1 + #{j ≠ k : T_{d,j} ≥ T_{d,k}}) / K`. Đây là p-value hạng (permutation/conformal) trên K nguồn.
- **Hiệu chỉnh khi có nguồn bị chiếm.** `p̃_{d,k} = min{1; p_{d,k}·K/(K − m_max)}`.
- **Gộp qua D ngày.** Chỉ hợp lệ khi phân công được rút ngẫu nhiên lại mỗi ngày, độc lập với quá khứ. Bảo đảm là theo từng nguồn; tuyên bố trên tập nguồn bị gắn cờ cần thêm kiểm định nhiều giả thuyết. Có trạng thái "chưa xác định" dựa trên kiểm định hai mẫu bằng bộ phân loại.

**Kiểm toán tương đương trước đó** (chỉ có bản tóm tắt). `04_thesis_gap_and_inventory_EXTRACT.md` dòng 29 ghi: "rank-calibrated classical peer score với cùng giả định/correction/aggregation/abstention **trùng quyết định trên giấy**", NO-GO Level 2/3. Các tệp gốc (`dop_go_no_go.md`, `dop_primary_literature_matrix.md`, `prereg_lock_log_dop.md`, `dop_confirm_report.md` dưới `D:\Personal\PhD\IUH\…`, hash ở dòng 144–150) **MISSING** đối với Cloud. Tôi không mở lại và không sửa các kết luận đó.

## 2. Prior art về suy luận tuần tự anytime-valid
| Thành phần | Công trình | Nội dung đã có | Mức xác minh |
|---|---|---|---|
| Test martingale, p-to-e calibrator | Shafer, Shen, Vereshchagin & Vovk (2011), *Statistical Science* 26(1) | Nhân e-value qua thời gian; Ville cho bảo đảm anytime | Theo hiểu biết; chưa đọc lại trong gate |
| E-value: calibrator, kết hợp | Vovk & Wang (2021), *Annals of Statistics* 49(3) | p → e (ví dụ `κ·p^{κ−1}`), tích/trung bình e-value | Như trên |
| E-process, SAVI | Ramdas, Grünwald, Vovk & Shafer (2023), *Statistical Science* 38(4) | Optional stopping/continuation, dữ liệu đến không đều | Như trên |
| Confidence sequence | Howard, Ramdas, McAuliffe & Sekhon (2021), *Annals of Statistics* 49(2) | Khoảng tin cậy đúng mọi thời điểm | Như trên |
| Kiểm định hạng bằng betting | Duan, Ramdas & Wasserman, "Interactive rank testing by betting", CLeaR 2022, PMLR 177:201–235 | Phiên bản martingale của Wilcoxon/Kruskal-Wallis/Friedman; tuần tự; bảo đảm nhờ Ville; thêm đối tượng không cần hiệu chỉnh | Tìm kiếm web xác nhận abstract |
| Martingale từ p-value conformal (hạng) | Vovk et al., "Retrain or not retrain: Conformal test martingales for change-point detection", COPA 2021, PMLR 152 | Biến chuỗi p-value hạng/conformal thành martingale cảnh báo thay đổi; có Ville procedure | Tìm kiếm web xác nhận abstract |
| FDR với e-value | Wang & Ramdas (2022), e-BH, *JRSS-B* 84(3) | Kiểm soát FDR dưới phụ thuộc bất kỳ | Theo hiểu biết |
| Kiểm định nhiều giả thuyết trực tuyến | Xu & Ramdas, "Online multiple testing with e-values" (e-LOND), AISTATS 2024, PMLR 238:3997–4005 | FDR trực tuyến dưới phụ thuộc bất kỳ, chưa biết | Tìm kiếm web xác nhận abstract |
| Kiểm định hai mẫu tuần tự (dùng cho "chưa xác định") | Shekhar & Ramdas (2023), "Nonparametric two-sample testing by betting", *IEEE Trans. Inf. Theory* | Kiểm định hai mẫu anytime-valid | Theo hiểu biết; chưa đọc lại |

## 3. Baseline mạnh bắt buộc: **sequential peer-score**
Để không làm yếu baseline, đối chứng phải có **cùng bằng chứng và cùng cơ hội dừng** với Sequential DoP:
1. P-value theo ngày: dùng p-value hạng của **rank-calibrated peer-score** (comparator của kiểm toán tương đương), với cùng hiệu chỉnh `m_max`.
2. Biến p thành e theo cùng cách. Phương án mạnh nhất là **e-process kiểu betting với cược dự đoán được** (Duan et al. 2022; Ramdas et al. 2023), không phải calibrator cố định.
3. Cùng lịch nhận nhãn: trễ, chọn lọc, không đều; cùng thời điểm được phép dừng.
4. Nhiều nguồn: e-BH (cố định) hoặc e-LOND (trực tuyến), cùng mức α.
5. Trạng thái "chưa xác định": cùng kiểm định hai mẫu tuần tự.

## 4. Kết quả so sánh
Gọi `F(p_1, …, p_t)` là bất kỳ tầng tuần tự nào (calibrator rồi nhân, hoặc betting) áp lên chuỗi p-value theo ngày của một nguồn:
- **Nếu DoP và peer-score cho cùng p-value theo ngày** dưới cùng giả định, thì `F` cho **cùng một e-process, cùng thời điểm dừng, cùng quyết định**. Không có khác biệt.
- **Nếu tương đương trước đây chỉ ở mức quyết định** tại một horizon cố định (bản tóm tắt ghi "trùng quyết định"; chi tiết MISSING), thì mọi khác biệt dưới `F` đến từ **hàm điểm theo ngày**. Đó chính là phần đã được kiểm toán và đã NO-GO, không phải từ tầng tuần tự.

Trong cả hai trường hợp, tầng tuần tự **không** tạo lợi thế cho DoP so với sequential peer-score có cùng bằng chứng.
