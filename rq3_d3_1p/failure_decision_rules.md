# Failure decision rules — D3-1 (khóa trước khi xem kết quả)

**Nguyên tắc:** chỉ dùng quy tắc định lượng có tham chiếu khách quan (chance, prevalence, thiếu phát hiện, tách khoảng seed). Quy tắc nào cần một ngưỡng chưa có căn cứ trước kết quả → `INDETERMINATE` + bằng chứng mô tả. Quy tắc **không thay đổi sau khi mở kết quả**; quy tắc thêm sau là `POST_HOC_EXPLORATORY` và không dùng cho kết luận chính.
Các nhãn dưới đây là **cờ quan sát theo quy tắc**, không phải kết luận nguyên nhân và không phải phán quyết "arm thất bại" tổng thể.

## 1. Điều kiện đánh giá được (áp dụng trước mọi nhãn)
Một (transition, arm, metric) là *evaluable* khi: tập đánh giá hợp lệ (J1–J12 đạt), `P ≥ P_min` và `N ≥ N_min` (với metric xếp hạng), `n_valid_seeds ≥ 8`. Ngược lại: `NOT_EVALUABLE` hoặc `INDETERMINATE` kèm mã lý do (`LOW_SUPPORT_*`, `NO_POSITIVES`, `NO_NEGATIVES`, `FEW_SEEDS`, `THRESHOLD_MISSING`, `INVALID_SCORES`, `EVAL_SET_MISMATCH`).

## 2. Quy tắc
| Mã | Nhãn | Điều kiện kích hoạt (trên seed-mean của transition–arm) | Tham chiếu khách quan | Nếu không đánh giá được |
|---|---|---|---|---|
| R1a | `AUC_AT_OR_BELOW_CHANCE` | `mean ROC-AUC ≤ 0.5` | 0.5 = xếp hạng ngẫu nhiên | `INDETERMINATE` |
| R1b | `AP_AT_OR_BELOW_PREVALENCE` | `mean AP ≤ π` của chính tập đánh giá (⇔ `AP_excess ≤ 0`) | π của `E_t` | `INDETERMINATE` |
| R2 | `NO_DETECTION_AT_TAU_HIST` | `Recall@τ_hist = 0` ở **cả** các seed hợp lệ (tối đa = 0) với `P ≥ P_min` | 0 positives phát hiện | `INDETERMINATE` (`THRESHOLD_MISSING` / `LOW_SUPPORT`) |
| R2b | `PARTIAL_SEED_NO_DETECTION` (mô tả) | Có seed với `Recall@τ_hist = 0` nhưng không phải tất cả | — | — |
| R3 | `BRIER_WORSE_THAN_CONSTANT_PREVALENCE` | `mean Brier > π(1−π)` (BSS < 0) với `0<π<1` | Dự đoán hằng prevalence của `E_t` (oracle, mô tả) | `INDETERMINATE` |
| R4 | `ALERT_FLOOD_VS_HISTORY` | **Không đặt** — thiếu tham chiếu hợp lý trước kết quả | — | **`INDETERMINATE`** (báo `alert_rate`, `FAR`, `FA_day` mô tả) |
| R5 | `CALIBRATION_FAILURE` | **Không đặt** — ECE không có chuẩn chance khách quan | — | **`INDETERMINATE`** (báo ECE, bảng bin mô tả) |
| R6 | Tách cặp arm (xem `aggregation_specification.md` §3) | `A_SEPARATED_ABOVE_B` / `B_SEPARATED_ABOVE_A` / `OVERLAP` trên AUC và AP | Khoảng seed không chồng | `INDETERMINATE (FEW_SEEDS / EVAL_SET_MISMATCH)` |

R6 áp dụng: **primary** `TABFM` vs `LGBM_MATCHED`; **secondary** `TABFM` vs `LGBM_FULL` và `LGBM_MATCHED` vs `LGBM_FULL` (khác ngân sách thông tin; chỉ mô tả).

## 3. Báo cáo failure
- Bảng `(transition × arm × rule)` với nhãn, giá trị vào quy tắc, π, P, N, cờ.
- Tóm tắt chỉ ở dạng "*k* trong *m* transition đánh giá được kích hoạt R*x*" cho từng arm, kèm danh sách transition, và số `INDETERMINATE` riêng.
- **Cấm:** "arm X thất bại", nguyên nhân nhân quả ("vì domain shift…"), tính mới thuật toán, gọi Study 1 là confirmation độc lập, kết luận ngoài 12 transition.
- Bằng chứng mô tả cho `INDETERMINATE`: giá trị metric, π, `alert_rate`, `FAR`, `FA_day`, ECE + bảng bin, kích thước tập, cờ; không diễn giải nguyên nhân.

## 4. Đóng băng
Tham số `P_min=N_min=20`, `n_valid_seeds ≥ 8` và danh sách R1–R6 được khóa cùng SHA-256 của giao thức. Sửa → giao thức phiên bản mới, trước khi mở dự đoán.
