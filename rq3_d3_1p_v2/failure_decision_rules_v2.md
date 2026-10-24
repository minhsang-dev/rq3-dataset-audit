# Failure decision rules v2 — cờ mô tả (khóa trước khi xem kết quả)

**Nguyên tắc:** cờ **quan sát theo quy tắc, mô tả**; không phải kết luận nguyên nhân, không phải phán quyết "arm thất bại"; **không xếp hạng hay chọn một nguyên nhân dominant**; nhiều cờ có thể đồng thời đúng và được liệt kê cạnh nhau. Chỉ dùng tham chiếu khách quan (chance, prevalence của chính `E_(t,s)`, không phát hiện). Không có tham chiếu → `INDETERMINATE` + bằng chứng mô tả. Quy tắc thêm sau khi mở kết quả là `POST_HOC_EXPLORATORY`.

## 1. Đánh giá theo seed, trên chính `E_(t,s)` (thay đổi so với v1)
Vì tập eval đổi theo seed, mỗi cờ được đánh giá **cho từng `(t,s,a)`**, so với baseline của chính `E_(t,s)`; transition-level chỉ đếm.
Điều kiện đánh giá được: seed hợp lệ theo `aggregation_specification_v2.md` §2 cho metric liên quan.

| Mã | Cờ | Kích hoạt (cho unit `(t,s,a)`) | Tham chiếu |
|---|---|---|---|
| R1a | `AUC_AT_OR_BELOW_CHANCE` | `ROC-AUC ≤ 0.5` | 0.5 |
| R1b | `AP_AT_OR_BELOW_PREVALENCE` | `AP ≤ π_(t,s)` (⇔ `AP_excess ≤ 0`) | π của `E_(t,s)` |
| R2 | `NO_DETECTION_AT_TAU` | `Recall@τ = 0` với `P ≥ P_min` | 0 positive bắt được |
| R3 | `BRIER_WORSE_THAN_CONSTANT_PREVALENCE` | `Brier > π(1−π)` (BSS<0) | oracle-constant, mô tả |
| R4 | `ALERT_FLOOD_VS_HISTORY` | **không đặt** (thiếu tham chiếu hợp lý trước kết quả) | → `INDETERMINATE`; báo `alert_rate`, `FAR`, `FA_count` |
| R5 | `CALIBRATION_FAILURE` | **không đặt** (ECE không có chuẩn chance) | → `INDETERMINATE`; báo ECE + bảng bin |
| R6 | Dấu hiệu hiệu cặp (xem aggregation §4) | `PAIRED_ALL_POSITIVE/NEGATIVE/MIXED/ALL_ZERO` trên AUC và AP | chỉ mô tả |

## 2. Báo cáo ở mức transition–arm
Với từng cờ R1a, R1b, R2, R3: bảng `(transition, arm)` gồm `k_flagged / n_valid`, `n_low_support`, `n_null`, cùng với mean baseline (`π̄`) và mean metric. Phân loại hiển thị (không phải phán quyết):
`FLAGGED_ALL_VALID_SEEDS` (k = n_valid ≥ 8), `FLAGGED_SOME_SEEDS` (0<k<n_valid), `NOT_FLAGGED` (k=0, n_valid ≥ 8), `INDETERMINATE` (n_valid < 8 hoặc không đánh giá được).
Tóm tắt qua transition chỉ ở dạng "*j* trong *m* transition đánh giá được có `FLAGGED_ALL_VALID_SEEDS` cho R*x*", kèm danh sách; không thành "arm thất bại".

## 3. Cấm
- Không nhân quả ("vì domain shift", "do thiếu ngữ cảnh", …); không nêu nguyên nhân dominant hoặc xếp hạng nguyên nhân.
- Không kết luận tính mới thuật toán; Study 1 không là confirmation độc lập; không kết luận ngoài 12 transition.
- Không dùng nhãn ngày đích để chọn ngưỡng/tham số; không sửa ngưỡng sau khi thấy cờ.
- Bằng chứng mô tả cho `INDETERMINATE`: metric, `π`, `alert_rate`, `FAR`, `FA_count`, ECE + bảng bin, support, cờ.

## 4. Đóng băng
`P_min=N_min=20`, `n_valid ≥ 8`, danh sách R1–R6 và cách đếm ở §2 được khóa cùng SHA-256 của giao thức. Sửa → phiên bản mới trước khi mở dự đoán.
