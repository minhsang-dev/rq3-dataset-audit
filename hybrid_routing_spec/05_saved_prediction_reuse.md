# Tái sử dụng dự đoán LightGBM/TabFM/TabICL đã lưu

## 1. Có gì (theo file 08, 13; vector **không** có trong gói)
- 1.080 vector điểm float32 (6 arm × 180 tập eval × 10.000 hàng). Thứ tự `eval_rowid` cố định; nối với `eval_y` và `eval_attack`.
- `comparability_key` đầy đủ: transition, seed, raw ZIP SHA, CSV member, membership hash, ordered rowid hash, ordered label hash.
- Context `ctx_recent_*` / `ctx_mixed_*` (10.000 hàng × 46 đặc trưng). Mẫu ngưỡng source `thr_rowid/thr_y` kèm điểm `thr` cho từng unit (chỉ provenance).
- 1.080 ngưỡng phục hồi (source-only, float_hex).
- **File 14** chỉ là 1.620 hàng so sánh tóm tắt (180 tập eval × 3 arm cloud × 3 arm LightGBM; AUC/AP/recall/precision/FPR đã lưu và trạng thái). Đây **không phải** input routing theo flow.

## 2. Điều kiện để tái dùng (tất cả phải đúng)
Cùng scorer và checkpoint, cùng context, cùng query/batch semantics, cùng tiền xử lý (46 đặc trưng, float32, missing→0), cùng thứ tự `eval_rowid`, cùng hash raw release/member. Vị trí phần tử chỉ hợp lệ bên trong vector đã kiểm; không ghép vị trí giữa các mảng xáo trộn độc lập.

## 3. Tái dùng được gì
| Chính sách / phân tích | Tái dùng? | Lý do / giới hạn |
|---|---|---|
| B1 edge-only, B2 cloud-only trên cùng `E_(t,s)` | Có điều kiện | Điểm có sẵn cho mọi hàng |
| B3/B4/B5 (ngưỡng, hạng, ngẫu nhiên) dùng `s_e` để chọn hàng, rồi lấy `s_c` đã lưu cho hàng được route | Có điều kiện | Chỉ hợp lệ nếu `s_c` của một hàng **không phụ thuộc** các hàng khác cùng batch query. Gói ghi "frozen scorer có thể dùng batch covariates để ICL" (file 13). Tính độc lập theo query của Google TabFM/TabICL **chưa xác minh** → phải kiểm bằng tài liệu/mã nguồn trước khi coi điểm toàn batch là điểm của tập con đã route |
| B6 post-hoc deferral fit trên mẫu ngưỡng source | Có điều kiện | Cần điểm `thr` của **cả** edge và cloud cho cùng hàng mẫu ngưỡng. Gói nói có `thr` theo unit nhưng ghi "provenance ONLY, no replay"; dùng cho fit cần phê duyệt riêng |
| B7 HINT | Không | Cần kNN edge mới và context láng giềng → inference mới |
| B8 H2T2 online | Có điều kiện, hạn chế | Mô phỏng thứ tự trong batch 10.000 hàng của **mẫu**, không phải stream thật; feedback từ offload là oracle |
| C-R3 và B11–B13 (context mới theo nhãn thu được) | **Không** | Cần điểm phản thực cho mỗi context mới; artifact hiện có không cung cấp |
| Bất kỳ tuyên bố vận hành (alerts/ngày, latency, overhead mạng) | Không | Mẫu 10.000 hàng; không có time coverage được chứng nhận; không có trace end-to-end |

## 4. Giới hạn khoa học của mọi replay retrospective
- Các target đã được xem (development exposure) → chỉ chẩn đoán, **không** xác nhận.
- 15 seed có tập eval chồng nhau; 12 transition không độc lập.
- Ngưỡng là bản phục hồi source-only, không phải scalar lịch sử đã serialize.
- Giữ 487 nhãn rounding-sensitive, 42 dấu chưa chứng nhận, NULL/LOW_SUPPORT/INDETERMINATE.
- S0/S1 không có SHA eval_X trực tiếp theo run (binding qua reader đã khóa).

## 5. Khuyến nghị
**Không** đề nghị replay hay chạy lại lúc này. Nếu sau này được duyệt, bước đầu tiên chỉ là **kiểm tài liệu/mã nguồn**: điểm TFM có độc lập theo query batch không, và điểm `thr` của cả hai mô hình có tồn tại không. Cả hai đều không cần mở vector điểm. Chưa thể ước lượng lợi ích từ dữ liệu hiện có.
