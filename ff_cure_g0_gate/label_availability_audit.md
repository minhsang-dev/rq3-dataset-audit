# Khả dụng nhãn family tại thời điểm quyết định (tách khỏi count)

| Câu hỏi | Bằng chứng | Trạng thái |
|---|---|---|
| Nhãn family có sẵn khi mẫu vào long bank không? | Family = cột `Attack` của dataset, nhãn ground truth retrospective. Prereg Study 1 §2: `Attack` chỉ dùng mô tả, không bao giờ là input mô hình (file 07) | **OFFLINE_ORACLE_ONLY** |
| Analyst xác nhận nhãn family khi nào? | Gate M: cả ba nguồn **thiếu label-confirmation timestamps**; chỉ có capture time (file 13) | **OFFLINE_ORACLE_ONLY** (thời điểm xác nhận UNKNOWN) |
| Nhãn family chỉ có trong ground truth offline? | Có; không có hồ sơ analyst/SOC | **OFFLINE_ORACLE_ONLY** |
| Taxonomy nhất quán giữa các ngày trong dataset? | Gate M: đối soát tổng family/attack/benign PASS cho 21 dataset-day; nhưng "Alias CIC official equivalence chưa xác minh offline"; danh sách family theo ngày không có trong Cloud | **UNKNOWN** |
| Taxonomy nhất quán giữa các dataset? | ToN 9 và CIC 14 family thô, taxonomy khác nhau | **INCOMPATIBLE** để gộp; FF-CURE chỉ dùng family bên trong từng dataset |
| CURE dùng nhãn nhị phân trong memory bank? | CURE (arXiv:2606.18677v1 §4) đếm lớp theo nhãn y của tác vụ. Trong thiết kế FF-CURE, tác vụ là nhị phân nên CURE baseline dùng y ∈ {0,1}. Đây là cấu hình của chúng ta, đúng với cách CURE hoạt động. Nhãn nhị phân cũng không có timestamp xác nhận | Cơ chế: phù hợp với văn bản (đọc một phần). Khả dụng nhãn nhị phân: **OFFLINE_ORACLE_ONLY** |

## Hệ quả
- Mọi kết quả FF-CURE trên dữ liệu này là **offline oracle**. Không được tuyên bố khả thi triển khai online, vì cần nhãn family do analyst xác nhận kịp thời.
- FF-CURE dùng `Attack` để quản lý context. Đây là nghiên cứu mới, không vi phạm prereg Study 1 (không sửa Study 1), nhưng phải khai báo rõ là sử dụng thêm nhãn so với CURE.
- `OFFLINE_ORACLE_ONLY` không tự làm G0 thất bại; nó chặn tuyên bố deployability.
