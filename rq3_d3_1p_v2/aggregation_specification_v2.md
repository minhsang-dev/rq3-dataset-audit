# Aggregation specification v2 — D3-1

## 1. Cấp và thứ tự (cố định)
| Cấp | Đối tượng | Phép tính |
|---|---|---|
| L0 | unit `(t,s,a)` hoặc `(t,s,a,f)` | metric trên `E_(t,s)` (hoặc `E_(t,s,f)`), kèm `n,P,N,π`, baseline và trạng thái hiệu lực **của chính seed đó** |
| L0p | cặp `(t,s,a,b)` | `Δ_(t,s) = v_(t,s,a) − v_(t,s,b)` chỉ khi cả hai unit `VALID` cho metric đó và cùng `comparability_key` |
| L1 | `(t,a)` | trên **seed hợp lệ** `V_(t,a,m)`: mean, SD (n−1), min, max, median, `n_valid`; mean baseline `π̄` đi kèm |
| L1p | `(t,a,b)` | trên **seed cặp hợp lệ** `V_(t,a,b,m)`: mean Δ, SD, min, max, số seed `Δ>0 / Δ=0 / Δ<0`, `n_paired` |
| L2 | qua 12 transition | **mô tả**: giá trị từng transition (bảng đầy đủ), median, mean, min, max và số transition có giá trị; mỗi transition trọng số bằng nhau |

## 2. Seed hợp lệ và low-support (thống nhất, ràng buộc 5)
Một seed `s` của transition `t` là **hợp lệ cho metric `m`** (`VALID`) khi đồng thời:
1. `E_(t,s)` thỏa điều kiện support của `m` (cột `valid_when` trong dictionary; tính **riêng seed**), và
2. unit `(t,s,a)` không `INVALID_SCORES` và (với metric dùng ngưỡng) có `τ`, và
3. với phân tích cặp: cả hai arm cùng thỏa 1–2 trên cùng `E_(t,s)`.
Seed không thỏa điều kiện support nhưng tính được là **`LOW_SUPPORT`**: giá trị được ghi và gắn cờ, **không** vào giá trị chính L1/L2, **có** vào bản nhạy ("tất cả seed tính được"). Cả hai bản được báo cáo song song với `n_valid` và `n_low_support`. Seed `NULL` (không tính được) không vào cả hai.
Vì support là thuộc tính của `E_(t,s)` (chung cho 6 arm), điều kiện support cho tập seed hợp lệ **giống nhau giữa các arm**; sai khác tập seed giữa arm chỉ do `INVALID_SCORES`/`THRESHOLD_MISSING`.

## 3. Điều kiện giá trị transition
- L1 chỉ được công bố khi `n_valid ≥ 8` (`PROPOSED`, = ceil(15/2)); ngược lại `INDETERMINATE (FEW_VALID_SEEDS)` kèm số `LOW_SUPPORT`.
- L1p tương tự với `n_paired ≥ 8`.
- Transition `INDETERMINATE` được liệt kê và không vào L2.

## 4. So sánh arm bằng hiệu cặp theo seed (ràng buộc 8)
- Chỉ ghép cặp seed với **cùng `(t,s)`** (cùng tập eval); không ghép giữa seed khác nhau.
- Báo: mean Δ, min/max Δ, số seed `Δ>0/Δ=0/Δ<0` (so sánh chính xác float64, không dung sai), `n_paired`. Nhãn mô tả (parameter-free): `PAIRED_ALL_POSITIVE` (Δ>0 mọi seed cặp hợp lệ), `PAIRED_ALL_NEGATIVE`, `PAIRED_MIXED`, `PAIRED_ALL_ZERO`.
- **Không** kiểm định, khoảng tin cậy, p-value, hay bootstrap: 15 seed là lặp đo trong một transition, không phải mẫu độc lập; chênh lệch seed gồm cả biến thiên eval resampling và biến thiên mô hình/context, không tách được. Nếu muốn bootstrap/hoán vị ở cấp transition (n=12) → `exploratory`, cần phê duyệt riêng.
- Cặp thực hiện: toàn bộ C(6,2)=15 cặp mô tả; cặp primary do người nghiên cứu chỉ định (D-A1). Cặp chỉ có nghĩa so sánh "cùng ngân sách thông tin" khi `train_source_def` tương ứng được xác nhận.

## 5. Binary và family
Tổng hợp riêng. Family: `(t, s, f)` → `(t, f)` theo §2–3 với `P_f` của từng seed (family vắng ở một số seed thì seed đó không thuộc `V`). `FAM_AP` và `FAM_RECALL_BUDGET` được báo ở **bảng tách biệt** và không so sánh số với nhau. Family chỉ xuất hiện trong < 8 seed hợp lệ → `INDETERMINATE`.

## 6. Cấm
- Không pool hàng/flow hay điểm qua nhiều `E_(t,s)` để tính một metric; không trọng số theo `n`.
- Không coi 15 seed hoặc số flow là cỡ mẫu; không suy diễn thống kê trên 12 transition.
- Không loại seed/transition "xấu". Không biểu đồ từ dự đoán thô (D3-1E chỉ vẽ từ bảng L1/L2 đã khóa).
- Không dùng nhãn ngày đích để chọn ngưỡng, K hay `P_min`.

## 7. Báo cáo
Mỗi bảng L1/L2 đi kèm `T_evalset` tóm tắt (`n̄, P̄, π̄` và khoảng min–max qua seed). AP luôn đi cùng `π̄` và `AP_excess`/`AP_lift`. Câu chữ: "trong *k* trong *m* transition có `n_valid ≥ 8`…". Dùng nhóm theo dataset chỉ để hiển thị (transition cùng dataset / dùng chung ngày không độc lập hoàn toàn).
