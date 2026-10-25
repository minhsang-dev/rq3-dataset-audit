# Ma trận baseline và bằng chứng

| Tiêu chí | O1 mixed-history (S3) | O2 quy tắc ngưỡng |
|---|---|---|
| Hạn chế quan sát cụ thể | Có (recent-only, transition lớp mới) | Có (H2 recall = 0, 111 unit không alert) |
| Baseline xác định | S2 (yếu so với prior art); baseline mạnh đúng là DualFIFO | Prereg §4 |
| Khác biệt kỹ thuật không tầm thường | Không (họ DualFIFO) | Không (tham số; ≡ C1/C2/C-R1) |
| Rò rỉ nhãn | Không (chỉ ngày ≤ t) | Nguy cơ nếu chọn ngưỡng trên target đã xem |
| Ghép artifact không tương thích | Không | Không |
| Baseline yếu | Có, nếu chỉ so với S2 | — |
| Tuyên bố triển khai không có căn cứ | Không | Không |
| Artifact hiện có kiểm được | Chỉ S3 vs S2 (development, đã phơi nhiễm) | Không (cần fitting ngưỡng) |
| Bằng chứng tối thiểu còn thiếu | DualFIFO cùng ngân sách; LightGBM-mixed; nguồn held-out sạch | Không áp dụng (đã đóng theo tương đương) |
| Quyết định | NO-GO (phương pháp); đóng góp thực nghiệm | NO-GO |

Mức xác minh prior art mới: DualFIFO (arXiv:2502.16840v2) và 2610.05352v1 đã đọc các phần nêu trên; các nguồn khác tái dùng `hybrid_routing_spec/prior_art_matrix.csv`.
