# Giải quyết O1–O10 (v2)

**Nguồn đối chiếu:** `open_decisions_O1_O10.md` và `protocol_compatibility_findings.md` (local) **không có trong Cloud**, nên tôi **không biết ba quyết định nào được ghi là REVISE** và không suy đoán. Cột "Báo cáo local" ghi `KHÔNG_ĐỌC_ĐƯỢC` cho cả 10 mục; khi bạn cung cấp báo cáo, cần đối chiếu từng dòng (đặc biệt ba mục REVISE) trước khi duyệt.
Cột "Trạng thái v2" dùng:
- `RESOLVED_BY_FACT`: được giải quyết bằng dữ kiện artifact bạn đã nêu.
- `RESOLVED_BY_DESIGN`: giải quyết bằng thay đổi thiết kế trong v2; người nghiên cứu vẫn cần xác nhận.
- `NEEDS_APPROVAL`: tham số/lựa chọn của người nghiên cứu; v2 chỉ đề xuất.
- `AWAITING_LOCAL`: không thể quyết nếu thiếu báo cáo local/artifact.

| Mã | Nội dung (v1) | Xử lý trong v2 | Căn cứ | Trạng thái v2 | Việc người nghiên cứu cần làm |
|---|---|---|---|---|---|
| O1 | D3-1E chỉ tính metric từ dự đoán đã lưu trên CPU, không suy luận lại | Giữ giả định; D3-1E đọc 1.080 unit đã lưu; không huấn luyện/suy luận TabFM | Dữ kiện: score TabFM, 1.080 ngưỡng, 180 eval đã kiểm; không nêu rõ có lưu điểm cho đủ 6 arm | NEEDS_APPROVAL + AWAITING_LOCAL | Xác nhận điểm số của **6 arm × 180 tập eval** đã được lưu; xác nhận không chạy lại mô hình |
| O2 | `P_min = N_min` | Đề xuất 20, **đánh giá riêng từng `E_(t,s)`**; low-support loại khỏi giá trị chính, giữ ở bản nhạy | Eval đổi theo seed → support đổi theo seed | NEEDS_APPROVAL | Chốt 20 hoặc giá trị khác **trước khi xem phân phối `P_(t,s)`** (nếu đã xem, ghi lại) |
| O3 | Ngân sách rate `b` | Đề xuất {0.001, 0.005, 0.01, 0.05}, `K` theo `n_(t,s)` của từng seed; cờ `K<20` | `n` thay đổi theo seed. `b=0.001` chỉ có `K≥20` khi `n ≥ 20.000` | NEEDS_APPROVAL | Chốt tập `b`; cân nhắc bỏ `b` quá nhỏ nếu `n_(t,s)` nhỏ (cần số liệu local) |
| O4 | Ngân sách tuyệt đối `K` | Đề xuất {100, 1000}; `K ≥ n` → null | Như O3 | NEEDS_APPROVAL | Chốt tập `K` |
| O5 | Số bin ECE | 10 bin đều rộng, bin cuối đóng, mô tả | — | NEEDS_APPROVAL | Xác nhận 10 bin |
| O6 | Granularity của `τ_hist` | **Khóa ngưỡng = unit `(transition_id, seed_id, arm_id)`**, 1.080 ngưỡng; thay khóa v1 `(dataset, source_day, arm)` | Dữ kiện đã xác minh: 1.080 ngưỡng nối đúng full unit/seed | **RESOLVED_BY_FACT** | Không cần quyết thêm; xác nhận không dùng nhãn ngày đích để tạo ngưỡng (J10) |
| O7 | `ts` cấp flow + metadata khoảng thiếu → false alert theo giờ/ngày | **DROPPED mặc định**: bỏ false alerts/giờ-ngày; chỉ báo FAR (FP/N) và `FA_count` | Eval thay đổi theo seed → `E_(t,s)` chưa được chứng minh là toàn bộ quần thể ngày; không có xác nhận về `ts`/coverage | RESOLVED_BY_DESIGN (+ AWAITING_LOCAL nếu muốn phục hồi) | Xác nhận DROPPED, hoặc cung cấp bằng chứng đủ ba điều kiện (ts, coverage, quần thể đầy đủ) |
| O8 | Ngưỡng "alert flood" | Không đặt; `INDETERMINATE` kèm bằng chứng mô tả | Không có tham chiếu hợp lý trước kết quả | RESOLVED_BY_DESIGN | Xác nhận không đặt (hoặc tự cung cấp quy tắc **trước** khi mở kết quả) |
| O9 | Nhãn family ở mức eval row | Phần family giữ **có điều kiện**: `FAM_AP` và `FAM_RECALL_BUDGET` chỉ chạy nếu có nhãn family; nếu không → `NO_FAMILY_LABEL` | Không có xác nhận trong Cloud | AWAITING_LOCAL | Xác nhận cột family, tập family, và số family xuất hiện/seed |
| O10 | Dung sai tie giữa hai arm | Không dung sai; so sánh chính xác float64 theo **hiệu cặp theo seed**; bỏ quy tắc khoảng seed không chồng | Eval đổi theo seed → khoảng min/max qua seed không so sánh được | RESOLVED_BY_DESIGN | Xác nhận |

## Mục mới phát sinh từ v2 (cần quyết định)
| Mã | Nội dung | Đề xuất | Trạng thái |
|---|---|---|---|
| D-A1 | Ánh xạ 6 arm lịch sử → lớp giao thức; chỉ định cặp primary | Mặc định: không gộp/loại, 15 cặp mô tả, không cặp primary | NEEDS_APPROVAL (điền `arm_registry_mapping.md`) |
| D-V1 | Số seed hợp lệ tối thiểu mỗi transition | 8 (= ceil(15/2)) | NEEDS_APPROVAL |
| D-L1 | Chính sách low-support: loại khỏi giá trị chính, giữ ở bản nhạy | Như §2 aggregation | NEEDS_APPROVAL |
| D-K1 | Tên cột và kiểu dữ liệu thật; quy ước tuần tự hóa `eval_set_hash` | Điền sau xác minh local | AWAITING_LOCAL |

**Ghi chú về "ba quyết định REVISE":** không xác định. Nếu bạn đã biết chúng, gửi mã mục và nội dung; tôi sẽ cập nhật bảng, không tự suy đoán.
