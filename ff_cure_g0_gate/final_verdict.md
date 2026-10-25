# FF-CURE G0 — **G0_HOLD**

**Ngày:** 2026-10-25. Gate đủ điều kiện chỉ đọc; không mô hình, không GPU, không tải dữ liệu, không sửa nguồn/LOCKED.

| Mục | Kết quả |
|---|---|
| Nguồn family × day | **MISSING** trong Cloud. Chỉ có tổng hợp Gate M; bảng chi tiết nằm ở thư mục local `D:\Personal\PhD\IUH\rq3_gate_m_metadata_20261024\`, tên tệp/hash UNKNOWN |
| Tiêu chí | Thiết kế gốc chưa khóa giá trị (`G0_CRITERION_NOT_PRE_REGISTERED`). Bộ duy nhất đã khóa trong `eligibility_definition.md` **trước khi có count**: n_hist = 20, low-volume ≤ trung vị lịch sử, q = 0,01, E_f(t') ≥ 20, đơn vị transition, ngưỡng ≥ 4 |
| Transition kiểm tra | 12 trong phạm vi; **0 được đếm** (không có dữ liệu) |
| Distinct eligible transitions / family | Không xác định |
| Nhãn family tại thời điểm quyết định | **OFFLINE_ORACLE_ONLY**; taxonomy trong dataset UNKNOWN; giữa các dataset INCOMPATIBLE |
| `family_recurrence_counts.csv` | Không tạo (không tạo dữ liệu giả) |

## Giới hạn
G0 chỉ là cổng đủ điều kiện, không kiểm chứng cải tiến. Kể cả khi PASS về sau, dữ liệu dev đã phơi nhiễm không thành xác nhận độc lập. Nhãn oracle chặn tuyên bố online.

## Có được soạn pilot protocol không?
**Chưa.** Pilot protocol chỉ được soạn sau G0_PASS.

## Blocker duy nhất
Bảng count family × ngày UTC (toàn ngày) của Gate M cho NF-ToN-IoT-v3 và NF-CICIDS2018-v3, kèm đường dẫn và SHA-256, chưa có trong không gian làm việc Cloud.
