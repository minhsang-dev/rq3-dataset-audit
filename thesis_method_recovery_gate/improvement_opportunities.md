# Cơ hội cải tiến được xét (tối đa hai) — cả hai bị loại

Chỉ xét cơ hội gắn với một hạn chế đã **quan sát** và một baseline **xác định được**. Không mở lại các hướng đã đóng.

## O1 — Context "mixed history" (S3) thay recent-only refresh (S2) cho TabFM
| Mục | Nội dung |
|---|---|
| Hạn chế quan sát | Recent-only context (S2) chỉ thấy ngày t; nhiều transition `new_class` (Study 1 §5, file 07) |
| Bằng chứng hiện có | Phân tích phụ đăng ký trước S3 − S2 macro-F1 +0,050. CI theo seed [+0,036; +0,065]. CI bootstrap hai chiều theo transition [+0,003; +0,118]. 7/5 transition dương/âm. D3: S2 − S3 cặp seed AUC và AP đều **MIXED ở 12/12 transition**. H2 không cải thiện: S3 có 22/180 unit recall = 0, S2 có 21/180 (files 07, 09, 10) |
| Baseline | S2: TabFM, 10.000 hàng recent ngày t, phân tầng theo Label |
| Thay đổi kỹ thuật | 5.000 hàng ngày t cộng 5.000 hàng lấy đều từ các ngày 1…t−1, mỗi nửa phân tầng; transition đầu dùng lại recent (prereg §3) |
| Giả định | Nhãn của mọi ngày trước được lưu và có sẵn (oracle như S2). Cùng kích thước context. Cùng chi phí cập nhật (S2 338,7 s, S3 338,8 s; 1,84 MB) |
| Prior art gần nhất | **DualFIFO**: Lourenço, Gama, Xing, Marreiros, "In-context Learning of Evolving Data Streams with Tabular Foundational Models", KDD '26, tr. 973–982, DOI 10.1145/3770854.3780305 (Crossref); arXiv:2502.16840v2. Đọc toàn văn một phần (§4 và Algorithm 1). Cơ chế: bộ nhớ ngắn hạn FIFO cộng bộ nhớ dài hạn cân bằng lớp từ các mẫu cũ, dùng làm context TabPFN. Ablation của chính bài cho thấy bộ nhớ kép tốt hơn bộ nhớ ngắn hạn đơn thuần. Ngoài ra Verma et al. arXiv:2610.05352v1 (đọc một phần) thấy FIFO nói chung ngang DualFIFO/CURE |
| Có phải chỉ tinh chỉnh/đổi tên? | **Có.** S3 thuộc họ bộ nhớ kép ngắn hạn + dài hạn của DualFIFO. Khác biệt chỉ là tham số và quy tắc: tỉ lệ 50/50, lấy mẫu đều thay vì loại lớp dư, độ hạt theo ngày, phân tầng nhị phân. Đây là áp dụng một phương pháp đã có, không phải cải tiến nó |
| Đối chứng công bằng | Không phải S2 mà là DualFIFO với cùng kích thước bộ nhớ; ngoài ra LightGBM trên cùng mixed context. Cả hai **không có** trong artifact |
| Kiểm được bằng artifact hiện có? | Chỉ S3 vs S2 trên dữ liệu development đã phơi nhiễm. S3 vs DualFIFO và LightGBM-mixed cần REQUIRES_NEW_INFERENCE/huấn luyện mới |
| Quyết định | **NO-GO** như cải tiến phương pháp. Giữ làm **đóng góp thực nghiệm**: bằng chứng đăng ký trước trên NIDS theo ngày, hướng giống DualFIFO và trái với kết luận chung của 2610.05352. Bằng chứng yếu ở cấp transition (7/5; MIXED 12/12) |

## O2 — Quy tắc chọn ngưỡng lịch sử (max macro-F1, tie-break gần 0,5)
| Mục | Nội dung |
|---|---|
| Hạn chế quan sát | H2: 185/1.080 unit recall = 0 tại ngưỡng lịch sử, trong đó 111 unit không có alert nào; có ở cả LightGBM lẫn TFM. Nhiều ngưỡng phục hồi bằng 0,5 (ví dụ các hàng đầu của file 14) |
| Baseline | Prereg §4: lưới 0,01–0,99, max macro-F1 trên mẫu ngưỡng source, tie-break gần 0,5 rồi chọn thấp hơn |
| Thay đổi kỹ thuật khả dĩ | Đổi quy tắc tie-break/plateau, hoặc ngưỡng theo quantile/budget, hoặc hiệu chỉnh prior-shift |
| Prior art / tương đương | Tie-break là một tham số; quantile/top-K ≡ C-R1 (NO-GO); hiệu chỉnh label-shift ≡ C2 (NO-GO); Chow/NP/LTT ≡ C1 (NO-GO) |
| Có phải chỉ tinh chỉnh? | **Có** |
| Kiểm được? | Cần chọn lại ngưỡng (threshold fitting), bị cấm ở đây. Mọi đánh giá trên target đã xem là chọn hậu nghiệm |
| Quyết định | **NO-GO** |

## Kết luận
Không có cơ hội nào đạt đủ ba điều kiện: gắn với hạn chế quan sát, có baseline mạnh, và khác biệt không phải tinh chỉnh/đổi tên. Không đề xuất cơ chế mới để lấp yêu cầu. Ví dụ, bộ nhớ dài hạn cân bằng theo attack family không có bằng chứng quan sát và sẽ là phát minh không căn cứ.
