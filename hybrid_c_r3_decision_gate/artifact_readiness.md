# Mức sẵn sàng của artifact Study 1 cho một so sánh retrospective hạn chế

Nguồn: hồ sơ 08/13 trong gói bàn giao (vector không có trong Cloud). Nhãn phân loại: **VALID**, **ORACLE-ONLY**, **MISSING**, **REQUIRES_NEW_INFERENCE**.

| Thành phần cần cho C-R3 vs B\* | Trạng thái | Ghi chú |
|---|---|---|
| Điểm edge S1-ctx và cloud S2/S2-ICL trên cùng E_(t,s), context cố định | **VALID** (có điều kiện) | 180 tập × 10.000 hàng, binding đã kiểm. Chỉ cho các arm context cố định (A5, A8). Điều kiện: điểm TFM của một hàng không phụ thuộc các hàng khác cùng batch query — **chưa xác minh** |
| Chọn R từ điểm edge rồi lấy điểm cloud đã lưu cho R | **VALID** (có cùng điều kiện) | Chỉ khi context không đổi |
| Nhãn eval để "thu nhãn" A | **ORACLE-ONLY** | Nhãn retrospective; không có timestamp xác nhận; mọi trễ d chỉ là mô phỏng |
| Timestamp capture (FLOW_START) | ORACLE-ONLY cho thứ tự | Không phải thời điểm có nhãn |
| Chuỗi batch theo thời gian trong một transition | **MISSING** | Mỗi transition chỉ có một bước: context ngày t → eval mẫu 10.000 hàng ngày t+1; không có chuỗi batch nội ngày để vòng lặp acquisition→update |
| Điểm TFM với context đã cập nhật C_{t+1} | **REQUIRES_NEW_INFERENCE** | Cốt lõi của C-R3 và của B\* (refresh); không có trong artifact |
| LightGBM huấn luyện lại trên nhãn thu được (A6/B13) | **REQUIRES_NEW_INFERENCE** (và huấn luyện mới) | |
| Tab-AICL / HINT baseline | **REQUIRES_NEW_INFERENCE** | |
| Điểm ngưỡng mẫu nguồn `thr` của cả hai arm | Có nhưng "provenance ONLY" | Dùng cho fit cần phê duyệt riêng |
| Analyst capacity, effort, backlog | **MISSING** | |
| Latency/overhead mạng đo thật của hybrid | **MISSING** | Chỉ có chi phí tham số |
| Nguồn held-out sạch | **MISSING** | Gate M: NONE VERIFIED |
| Tài liệu về tính độc lập query-batch của Google TabFM/TabICL | **MISSING** | Kiểm được bằng tài liệu/mã, không cần dữ liệu mới |

## Kết luận
Artifact hiện có chỉ hỗ trợ so sánh **routing với context cố định** (A5, A8), tức các phần **không** đặc trưng cho C-R3. Phép so sánh đặc trưng duy nhất (A1: pool thu nhãn → context mới) đều là **REQUIRES_NEW_INFERENCE** và **ORACLE-ONLY** về nhãn. Do đó **không có so sánh retrospective công bằng nào cho lợi ích của C-R3** từ Study 1.

## Bằng chứng tối thiểu còn thiếu (không cần dataset hay thí nghiệm mới)
1. Định nghĩa khóa của router, quy tắc acquisition và quy tắc update. Hiện cả ba là TBD.
2. Một lập luận trên giấy cho thấy quy tắc acquisition nằm **ngoài** họ Tab-AICL(pool tùy ý) ∪ ngẫu nhiên ∪ edge-margin. Với đặc tả hiện tại, lập luận này không tồn tại (xem ma trận).
3. Tài liệu về tính độc lập query-batch của TFM.

Ngay cả khi có đủ 1–3, việc kiểm lợi ích vẫn cần inference mới và dữ liệu có thời điểm nhãn. Hai thứ này nằm ngoài phạm vi.
