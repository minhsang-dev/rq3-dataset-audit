# Tuyên bố cải thiện bác bỏ được (định nghĩa để xét; không chạy)

## Tuyên bố
Giữ cố định cho mọi arm: cùng scorer edge (LightGBM, recent context) và cloud (TabFM S2; lặp lại với TabICL S2-ICL), cùng kích thước context, cùng ngân sách cloud-call b_c, cùng ngân sách nhãn b_l, cùng trễ d, cùng ε, cùng ngân sách cảnh báo K (Track K) hoặc cùng FPR danh nghĩa α đặt từ dữ liệu nguồn (Track α), cùng số lần refresh context mỗi batch (để chi phí suy luận và latency có thể so sánh), cùng ngân sách tìm kiếm cấu hình.

Khi đó **C-R3 có recall@K (hoặc recall tại α) cao hơn B\*** ở đa số transition held-out đánh giá được. Đồng thời không làm giảm coverage, không làm tăng deferral, overflow analyst hay realized FPR vượt mức khóa trước.

## Bác bỏ nếu
1. Số transition có hiệu (C-R3 − B\*) dương ≤ một nửa số transition đánh giá được.
2. Hoặc lợi thế biến mất khi cùng wrapper được áp cho cặp scorer khác (LightGBM huấn luyện lại thay cloud; TabICL thay TabFM).
3. Hoặc lợi thế chỉ có khi tăng b_c, b_l, K, α hay ngân sách tìm kiếm.
4. Hoặc policy rơi vào all-clear/all-defer.
5. Hoặc cấu hình được chọn trên chính target dùng để báo cáo.

## Ablation bắt buộc (tách routing khỏi scorer)
| Ablation | Câu hỏi |
|---|---|
| A1: pool thu nhãn = R vs toàn batch (cùng b_l) | Ràng buộc ghép nối có tác dụng không — đây là phần duy nhất đặc trưng C-R3 |
| A2: tín hiệu acquisition: điểm cloud trên R / điểm edge trên batch / ngẫu nhiên | Lợi ích đến từ thông tin bất định hay từ số nhãn |
| A3: ε = 0 vs ε > 0 | Hiệu chỉnh thiên lệch chọn mẫu |
| A4: context tĩnh vs refresh (cùng routing) | Lợi ích từ refresh hay từ routing |
| A5: routing-only, context cố định (B3/B4/B5) | Phần đóng góp của router |
| A6: cùng wrapper, scorer khác (LightGBM retrain, TabICL) | Lợi thế scorer hay workflow |
| A7: trễ d ∈ tập khóa trước | Giới hạn thích nghi |
| A8: edge-only / cloud-only (b_c = 0 / 1) | Mốc dưới/trên |

Quy tắc gán: phần lợi ích **do routing** = hiệu giữa các router với scorer và context cố định (A5). Phần **do scorer** = cloud-only TFM vs cloud-only LightGBM trên cùng context (S2 vs S1-ctx). Phần **do acquisition coupling** = A1 với mọi thứ khác cố định. Chỉ A1 có thể biện minh cho tên "C-R3".

## Đơn vị và thống kê
Transition là đơn vị; seed/flow không độc lập; đếm dấu theo transition ở mức mô tả, không p-value. Không có nguồn held-out sạch hiện nay, nên tuyên bố này **chưa thể kiểm** trên dữ liệu đúng vai trò.
