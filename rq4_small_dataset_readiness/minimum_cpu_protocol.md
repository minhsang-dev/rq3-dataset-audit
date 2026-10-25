# Giao thức CPU tối thiểu

**KHÔNG CHUẨN BỊ.** Giao thức chỉ được soạn khi phán quyết là `GO_FOR_PROTOCOL`. Phán quyết ở đây là `NO-GO` (xem `decision.md`), vì không có giả thuyết kỹ thuật phân biệt được với baseline. Soạn giao thức lúc này sẽ là một benchmark lại các biến thể P0/P1/P2 trên dữ liệu khác. Hồ sơ đã loại hướng này và quy tắc của gate cũng cấm.

## Điều kiện tiên quyết nếu muốn mở lại (không phải kế hoạch chạy)
| # | Điều kiện tiên quyết còn thiếu | Loại |
|---|---|---|
| M1 | Một cơ chế kỹ thuật **cụ thể**, khác P0/P1/P2, IPW, gain tuning, clipping và cập nhật trễ chuẩn, kèm lập luận vì sao conformal PID có cùng phản hồi trễ/IPW/ngân sách tinh chỉnh **không** biểu diễn được nó | Phương pháp (chặn chính) |
| M2 | Xác nhận bằng văn bản rằng E1–E1.3, paper-1 và mọi thử nghiệm khác chưa dùng CIC-IDS2017 (cả bản gốc lẫn Improved) | Phơi nhiễm |
| M3 | Số flow theo ngày, schema, tỉ lệ Attempted và thứ tự timestamp UTC trên CSV Improved thật, cộng checksum của zip 328M. Cần tải dữ liệu, ngoài phạm vi gate này | Dữ liệu |
| M4 | Quyết định gỡ bỏ hoặc giữ ghi chú trong đề cương v9.16 "tránh dùng [CIC-IDS2017] cho trôi dạt thời gian" | Nhất quán hồ sơ |
| M5 | Một cách chia phát triển/xác nhận chấp nhận được với 5 ngày: thứ Hai chỉ benign, 4 ngày tấn công mỗi ngày một họ, đợt tấn công 13–90 phút | Thiết kế |

Ngay cả khi M1–M5 đạt, giới hạn thống kê vẫn còn: chỉ có 4 ngày tấn công không lặp lại, nên mọi kết luận là theo từng đợt hay từng ngày, không suy rộng được. Nếu độ trễ nhãn dài hơn một đợt tấn công, đánh giá chỉ đo được FAR, không đo được recovery của recall.
