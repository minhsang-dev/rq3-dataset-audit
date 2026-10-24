# Metric dictionary — D3-1 (chưa chạy; chỉ định nghĩa)

Ký hiệu: tập đánh giá `E` (của transition `t`, hoặc `E_{t,f}` cho family), `n = |E|`, `y_i ∈ {0,1}`, điểm `p_i ∈ [0,1]`, `P = Σ y`, `N = n − P`, `π = P/n` (prevalence **của chính E**). Mọi metric tính **theo (transition, arm, seed)** trên `E` chung của transition. Phiên bản số: float64.

## 0. Quy tắc chung (khóa trước)
| Mục | Quy tắc |
|---|---|
| Hàng nhãn null | Loại ở bước join, giống nhau cho mọi arm, số hàng loại được ghi |
| Điểm số null/NaN | Không loại, không điền. Arm-seed đó `INVALID_SCORES` (`NAN_SCORE`), mọi metric null |
| Điểm ngoài [0,1] | `INVALID_SCORES` (`INVALID_SCORE_RANGE`); không biến đổi, không hiệu chuẩn |
| Điểm đúng bằng 0 hoặc 1 | Hợp lệ. Brier/ECE/xếp hạng dùng bình thường (ECE: 0 vào bin đầu, 1 vào bin cuối). **Không dùng log-loss** (không định nghĩa tại 0/1) |
| Ties điểm | **Không** phá hòa theo `eval_row_id`, thời gian hay RNG. ROC-AUC: tie = 0.5. AP: gộp theo ngưỡng riêng biệt. Ngân sách: kỳ vọng (§5) |
| Một lớp | Xem bảng §1 |
| Low-support | Tính và báo cáo kèm cờ `LOW_SUPPORT_*`; không bỏ im lặng. Ngưỡng `P_min = N_min = 20` (`PROPOSED`) |
| Làm tròn | Không làm tròn trước khi tổng hợp; báo 6 chữ số thập phân |
| Metric null | Giá trị rỗng + `reason_code`; không thay bằng 0 |

## 1. Xử lý nhãn một lớp (binary hoặc family-vs-benign)
| Tình huống | ROC-AUC | AP | Brier | ECE | Precision@τ | Recall@τ | FPR/FAR | Ghi chú |
|---|---|---|---|---|---|---|---|---|
| `P = 0` (không positive) | null `NO_POSITIVES` | null `NO_POSITIVES` | tính (`mean p²`) | tính, cờ | = 0 nếu có alert, null `NO_ALERTS` nếu không | null `NO_POSITIVES` | tính | Chỉ mô tả |
| `N = 0` (không negative / **chỉ positive**) | null `NO_NEGATIVES` | null `POSITIVES_ONLY` (π=1) | tính | tính, cờ | tính | tính | null `NO_NEGATIVES` | Không bao giờ tính ROC-AUC trên tập chỉ positive |
| `P ≥ 1, N ≥ 1` | tính | tính | tính | tính | tính | tính | tính | Cờ low-support nếu `P < P_min` hoặc `N < N_min` |

## 2. Xếp hạng
**ROC-AUC** = `(1/(P·N)) Σ_{i:y_i=1} Σ_{j:y_j=0} [1(p_i>p_j) + 0.5·1(p_i=p_j)]` (tương đương Mann–Whitney với midrank). Định nghĩa khi `P≥1, N≥1`. Baseline ngẫu nhiên = 0.5 (chỉ cho AUC).
Cờ `LOW_SUPPORT_AUC` nếu `P<P_min` hoặc `N<N_min`.

**Average Precision** = `Σ_k (R_k − R_{k−1})·Pr_k` trên các **ngưỡng điểm riêng biệt** theo thứ tự giảm dần (điểm bằng nhau gộp thành một ngưỡng; `R_0 = 0`), với `Pr_k = TP_k/(TP_k+FP_k)`, `R_k = TP_k/P`. Định nghĩa khi `P≥1` (nếu `N=0` thì π=1: ghi `POSITIVES_ONLY`, không báo AP).
**Baseline AP = π của chính `E`.** Báo kèm:
- `AP_excess = AP − π`;
- `AP_lift = AP/π` (null nếu `π=0`);
- `AP_skill = (AP − π)/(1 − π)` (null nếu `π=1`).
**Không dùng 0.5 làm baseline AP.** Không so AP giữa hai tập có π khác nhau bằng giá trị thô; so sánh giữa transition dùng `AP_excess`/`AP_lift`, kèm π trong bảng.
Cờ `LOW_SUPPORT_AP` nếu `P<P_min`.

## 3. Chất lượng xác suất
**Brier** = `(1/n) Σ (p_i − y_i)²`. Tham chiếu mô tả: `Brier_const = π(1−π)` (dự đoán hằng bằng prevalence **của E**, oracle, không khả thi); `BSS = 1 − Brier/Brier_const`, null nếu `π∈{0,1}`.
**ECE (10 bin đều rộng):** bin `b_k = [k/10, (k+1)/10)` cho `k=0..8`, `b_9 = [0.9, 1.0]` (đóng). `ECE = Σ_k (n_k/n)·|ȳ_k − p̄_k|`, bỏ bin rỗng. Báo bảng bin (`n_k, ȳ_k, p̄_k`). Cờ `LOW_SUPPORT_ECE` nếu `n < 500` hoặc `P < P_min`; cờ bin `n_k<10`. ECE chỉ mô tả hiệu chuẩn của **điểm gốc** — không hiệu chuẩn lại.

## 4. Ngưỡng lịch sử `τ_hist`
`alert_i = 1 ⇔ p_i ≥ τ_hist`. `τ_hist` lấy nguyên từ artifact đã khóa theo khóa `(dataset_id, source_day, arm_id)` (`NEEDS_LOCAL_VERIFICATION` về độ chi tiết). Thiếu → các metric dưới null `THRESHOLD_MISSING`. Không tính lại.
- `TP, FP, FN, TN`, `alerts = TP+FP`, `alert_rate = alerts/n`.
- **Precision** = `TP/alerts`; null `NO_ALERTS` nếu `alerts=0`. Cờ `LOW_SUPPORT_PRECISION` nếu `alerts<20`.
- **Recall** = `TP/P`; null `NO_POSITIVES` nếu `P=0`.
- **FAR (flow-level) ≡ FPR = FP/N**; null `NO_NEGATIVES` nếu `N=0`. Đây là định nghĩa duy nhất của "FAR" trong D3-1.
- **False alerts per valid observed time** (khác FAR): `FA_hour = FP / H_valid`, `FA_day = FP / (H_valid/24)` với `H_valid` = (độ dài khoảng quan sát hợp lệ của target-day tính bằng giờ). `H_valid` = độ dài hợp của cửa sổ capture đã ghi **trừ** các khoảng thiếu dữ liệu **được ghi nhận trong metadata** (bảng `coverage_gaps`). Khoảng im lặng (không có flow) **không** được suy diễn là khoảng thiếu hay khoảng sạch. Thiếu `ts` → null `NO_TIMESTAMP`; thiếu metadata phủ → báo `FA_day_unadjusted` (dùng cửa sổ danh nghĩa) với cờ `NO_COVERAGE_METADATA`.

## 5. Ngân sách cảnh báo (khóa trước, `PROPOSED`)
- Tập ngân sách: tỷ lệ `b ∈ {0.001, 0.005, 0.01, 0.05}` → `K = ceil(b·n)`; tuyệt đối `K ∈ {100, 1000}`. Nếu `K ≥ n` → null `BUDGET_EXCEEDS_N`. Cờ `LOW_SUPPORT_BUDGET` nếu `K < 20`.
- Xếp theo điểm giảm dần. **Tie tại biên:** nếu nhóm điểm bằng nhau chứa `g` hàng, trong đó `a` positive, và còn `r` chỗ trong top-K (`0<r<g`), số TP kỳ vọng thêm = `r·a/g` (không dùng RNG, không dùng ID). Ghi `TIE_AT_CUTOFF` (kèm `g`).
- `Precision@K = E[TP_K]/K`; `Recall@K = E[TP_K]/P` (null nếu `P=0`); cận trên tham chiếu `min(P,K)/K` và `min(P,K)/P`.
- Ngân sách không dùng "số alert của ngưỡng lịch sử" (phụ thuộc arm).

## 6. Family-vs-benign (phân tích tách riêng)
Trên `E_{t,f}` (benign ∪ attack family f; attack khác bị loại): ROC-AUC, AP (baseline `π_f`), `AP_excess`; recall tại `τ_hist` = `TP_f/P_f`; recall@K lấy **ngưỡng xếp hạng từ toàn bộ `E_t`** (top-K của `E_t`, đếm positive thuộc f / `P_f`). Brier/ECE **không** báo cho family-vs-benign (prevalence bị thay đổi do loại family khác). `P_f<P_min` → `LOW_SUPPORT`.

## 7. Ví dụ tay (golden vectors, tính bằng tay; không phải kết quả)
| Ví dụ | Đầu vào | Kết quả kỳ vọng |
|---|---|---|
| G1 | `y=[1,0,1,0]`, `p=[0.9,0.8,0.4,0.1]` | ROC-AUC=0.75; AP=0.5·1+0.5·(2/3)=0.833333; Brier=(0.01+0.64+0.36+0.01)/4=0.255; π=0.5 |
| G2 (tie biên) | `y=[1,0,1,0,0]`, `p=[0.5,0.5,0.5,0.2,0.1]`, K=2 | nhóm tie g=3, a=2, r=2 ⇒ E[TP]=2·2/3=1.333333; Precision@2=0.666667; Recall@2=0.666667 |
| G3 (ECE) | `y=[0,0,1,0]`, `p=[0.05,0.15,0.95,0.95]` | bin0:1·0.05, bin1:1·0.15, bin9: 2·|0.5−0.95|=0.9 ⇒ ECE=(0.05+0.15+0.9)/4=0.275 |
| G4 (một lớp) | `y=[1,1,1]` | ROC-AUC null `NO_NEGATIVES`; AP null `POSITIVES_ONLY` |
| G5 (xác suất 0/1) | `y=[1,0]`, `p=[1.0,0.0]` | AUC=1; Brier=0; ECE=0; log-loss không dùng |
Các vector này dùng làm kiểm thử đơn vị của D3-1E **trước** khi đọc bất kỳ dự đoán thật nào.
