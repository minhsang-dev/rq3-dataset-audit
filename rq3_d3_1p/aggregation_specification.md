# Aggregation specification — D3-1

## 1. Thứ tự tổng hợp (cố định)
1. **Seed → (transition, arm):** với mỗi `(t, a, m, subset)`, trên 15 giá trị seed hợp lệ: `mean`, `SD` (mẫu, n−1), `min`, `max`, `median`, `n_valid_seeds`. Seed là lặp đo trong transition.
2. **Transition-level contrast:** với cặp arm `(a, b)` cùng `t` và cùng `eval_set_hash`: `Δ_t = mean_a − mean_b`; kèm nhãn **tách khoảng seed** (§3).
3. **Qua transition:** mỗi transition **một trọng số bằng nhau**. Báo (i) **giá trị từng transition** (bảng đầy đủ), (ii) `median`, `mean`, `min`, `max` qua các transition có giá trị hợp lệ, (iii) `m = số transition hợp lệ` trên 12.
4. Không có bước thứ tư. Không pool flow hay seed qua transition.

## 2. Cấm
- Không tính metric trên dữ liệu gộp nhiều transition; không trọng số theo số flow (số flow chỉ là mô tả).
- Không dùng 15 seed hoặc số flow làm cỡ mẫu cho bất kỳ khoảng tin cậy/p-value nào.
- Không bootstrap CI bắt buộc; nếu muốn dùng bootstrap/hoán vị ở cấp transition (n=12) thì ghi nhãn **exploratory** và cần phê duyệt riêng; không đưa vào kết luận.
- Không cắt tỉa transition/seed "xấu" hoặc "outlier".

## 3. Nhãn tách khoảng seed (parameter-free)
Với metric `m`, transition `t`, hai arm `a, b` cùng tập đánh giá:
- `A_SEPARATED_ABOVE_B` nếu `min_s v_{t,a,s} > max_s v_{t,b,s}`;
- `B_SEPARATED_ABOVE_A` nếu `min_s v_{t,b,s} > max_s v_{t,a,s}`;
- `OVERLAP` (không xác định thứ tự) trong các trường hợp khác.
Không phải khoảng tin cậy. Chỉ áp dụng khi cả hai arm có `n_valid_seeds ≥ 8` (`ceil(15/2)`); dưới đó `INDETERMINATE (FEW_SEEDS)`.

## 4. Xử lý null/thiếu
| Trường hợp | Xử lý |
|---|---|
| Null do tính chất tập đánh giá (một lớp, low-support…) | Giống nhau cho mọi arm/seed; transition đó `NOT_EVALUABLE` cho metric; **không** vào tổng hợp qua transition; vẫn liệt kê |
| Null theo seed (NO_ALERTS, INVALID_SCORES, thiếu dự đoán) | `n_valid_seeds` giảm; hiển thị; `mean` chỉ tính trên seed hợp lệ |
| `n_valid_seeds < 8` | giá trị transition-arm đó `INDETERMINATE (FEW_SEEDS)`, loại khỏi tổng hợp qua transition và liệt kê |
| Cờ `LOW_SUPPORT_*` | Giữ giá trị, hiển thị cờ; tổng hợp qua transition được báo **hai lần**: gồm tất cả, và loại các transition có cờ |

## 5. Nhóm dataset (báo cáo mô tả)
12 transition thuộc `D` dataset; hiển thị bảng theo dataset (số transition, giá trị từng transition, median trong dataset). Không gán trọng số hay suy luận dựa trên nhóm. Ghi chú đi kèm: transition dùng chung ngày nguồn/đích **không độc lập** (`NEEDS_LOCAL_VERIFICATION` về danh sách chia sẻ ngày).

## 6. Family-vs-benign
Tổng hợp riêng theo `(transition, family)`; **không** gộp family qua transition thành một "family" trừ khi cùng nhãn family trên ≥ 2 transition và khi đó chỉ liệt kê từng giá trị. `family` có `P_f < P_min` hiển thị nhưng không đưa vào median.

## 7. Báo cáo
- Mỗi bảng đi kèm `T_evalset` (n, P, N, π, hàng bị loại).
- AP luôn đi cùng π của đúng tập đánh giá; so sánh giữa transition dùng `AP_excess`/`AP_lift`.
- Câu chữ được phép: "trong *k* trong *m* transition đánh giá được, …". Không dùng "mô hình A tốt hơn B" mà không nêu `train_source_def` và tập đánh giá.
- Không biểu đồ từ dự đoán trong D3-1P. Ở D3-1E, biểu đồ chỉ vẽ từ `T_transition`/`T_global` đã khóa, không từ điểm dự đoán thô.
