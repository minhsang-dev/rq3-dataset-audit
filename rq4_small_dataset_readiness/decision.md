# RQ4 / DA-FAR — Fast dataset & method readiness gate: **NO-GO**

**Ngày:** 2026-10-25.
- Đọc hồ sơ và trang web công khai.
- Không tải dataset, không training/GPU, không tối ưu ngưỡng.
- Không sửa artifact khóa.
- DA-FAR E1.3 vẫn NO-GO cho P1/P2; kết quả thăm dò cũ giữ nguyên.

## Phán quyết
**NO-GO.** Phương pháp tương đương về cấu trúc với baseline đã có.
- Không có cơ chế kỹ thuật nào ngoài P0/P1/P2, IPW, gain tuning, clipping và cập nhật phản hồi trễ chuẩn.
- Đổi sang CIC-IDS2017 Improved không tạo ra giả thuyết phân biệt được, và dataset mới không bù cho việc thiếu tính mới.

Theo định nghĩa của gate, `GO_FOR_PROTOCOL` cần giả thuyết phân biệt được. `HOLD` chỉ dành cho trường hợp còn **một** blocker cụ thể, trong khi ở đây chặn phương pháp là chặn gốc. Vì vậy phán quyết là NO-GO.

## Tóm tắt theo phase 1
| Task | Kết quả | Tệp |
|---|---|---|
| 1. Quyết định DA-FAR E1–E1.3 | Đã đóng 21/10/2026; P1 không phân biệt với P0 khớp gain; P2 đánh đổi lẫn lộn. Đặc tả chi tiết MISSING (chỉ có trên máy người nghiên cứu) | `method_novelty_and_baselines.md` §1 |
| 2. Phơi nhiễm trước | Chỉ có nhắc đến (đề cương: "bộ dự phòng"; ref [1]). Dataset của E1–E1.3 **chưa xác lập** | `prior_exposure_audit.md` |
| 3. Đủ flow theo thời gian? | Benign có khả năng đủ. Attack là đợt ngắn có kịch bản, mỗi ngày một họ, thứ Hai không có attack. Số flow MISSING | `dataset_provenance_and_temporal_checks.md` §3 |
| 4. Schema, nhãn, thứ tự, rò rỉ | Bản Improved: UTC micro giây, logic nhãn công khai, có Attempted (gộp vào Benign). Chưa kiểm trên CSV. Phải loại IP/port/Flow ID/timestamp khỏi đặc trưng | như trên |
| 5. Baseline tuần tự mạnh | Conformal PID control [25], dùng cùng phản hồi trễ/IPW và cùng ngân sách tinh chỉnh | `method_novelty_and_baselines.md` §2 |
| 6. Cơ chế mới? | **Không có** | `method_novelty_and_baselines.md` §3 |

## Điều kiện tiên quyết còn thiếu (chính xác)
1. **M1 (chặn chính):** chưa có cơ chế kỹ thuật cụ thể nào mà conformal PID, khi được cùng phản hồi trễ, IPW và ngân sách tinh chỉnh, không biểu diễn được.
2. **M2:** xác nhận bằng văn bản rằng E1–E1.3 và các thử nghiệm khác chưa dùng CIC-IDS2017, cả bản gốc lẫn Improved.
3. **M3:** số flow theo ngày, schema, tỉ lệ Attempted, thứ tự timestamp và checksum của `CICIDS2017_improved.zip` (328M, 2023-04-27). Cần tải dữ liệu, chưa được phép trong gate này.
4. **M4:** đề cương v9.16 hiện ghi tránh dùng CIC-IDS2017 cho trôi dạt thời gian.
5. **M5:** chưa có cách chia phát triển/xác nhận hợp lệ với 5 ngày và các đợt tấn công không lặp lại.

`minimum_cpu_protocol.md` không được soạn, đúng theo quy tắc.
