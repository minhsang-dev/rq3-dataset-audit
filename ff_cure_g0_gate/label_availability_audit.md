# Khả dụng nhãn family tại thời điểm quyết định

| Câu hỏi | Bằng chứng (ZIP Gate M + tài liệu) | Trạng thái |
|---|---|---|
| Nhãn family có sẵn khi mẫu vào long bank? | Family = cột `Attack`, ground truth retrospective. `transition_family_status.csv`: `availability_status = FAMILY_VERIFICATION_TIME_UNKNOWN` ở mọi dòng | **OFFLINE_ORACLE_ONLY** |
| Analyst xác nhận nhãn khi nào? | `confirmation_time_column = NOT FOUND` (dataset_identity_and_hashes.csv) | **OFFLINE_ORACLE_ONLY** (thời điểm UNKNOWN) |
| Chỉ có trong ground truth offline? | Có | **OFFLINE_ORACLE_ONLY** |
| Taxonomy nhất quán giữa các ngày trong dataset? | Danh mục họ đầy đủ ở mọi ngày; đối soát 21/21 PASS | **Nhất quán trong dataset (theo bảng)**; tương đương chính thức tên CIC vẫn UNVERIFIED |
| Taxonomy giữa các dataset? | ToN 9 họ và CIC 14 họ, tên khác nhau | **INCOMPATIBLE** để gộp (FF-CURE không gộp) |
| Quan sát ngày đầy đủ? | `complete_monitored_day_verified = False`; `reappearance_observability = OBSERVED_DAYS_ONLY; MONITORING_UNVERIFIED` | Vắng trong dữ liệu **không** chứng minh vắng trong mạng thực |
| CURE dùng nhãn nhị phân trong memory? | Trong thiết kế FF-CURE, CURE chạy tác vụ nhị phân (y ∈ {0,1}); nhãn nhị phân cũng không có timestamp xác nhận | **OFFLINE_ORACLE_ONLY** |

Hệ quả: dù G0 có đạt, mọi kết quả cũng chỉ là offline oracle; không có tuyên bố triển khai online.
