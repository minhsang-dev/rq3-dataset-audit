# Metric dictionary v2 — D3-1 (chưa chạy; chỉ định nghĩa)

**Ký hiệu.** `E = E_(t,s)` là tập eval của transition `t`, seed `s` (chung cho 6 arm). `n = |E|`, `y_i ∈ {0,1}`, điểm `p_i`, `P = Σ y`, `N = n − P`, `π = P/n`. Mọi đại lượng `n, P, N, π` và mọi baseline **tính riêng cho từng `E_(t,s)`**. Metric tính theo **primary unit `(t,s,a)`**; ngưỡng `τ_(t,s,a)` là của chính unit. float64.
Phân tích family dùng `E_(t,s,f)` = benign ∪ attack family `f` (attack family khác bị loại), với `P_f, N, π_f = P_f/(P_f+N)`.

## 0. Quy tắc chung (thay đổi v2 đánh dấu ◆)
| Mục | Quy tắc |
|---|---|
| ◆ Support | Tính riêng từng `E_(t,s)`; không dùng support của transition hay tập gộp |
| Hàng nhãn null | Loại ở bước join, giống nhau cho mọi arm của `(t,s)`, ghi số hàng loại |
| Điểm NaN | Không loại, không điền: unit `INVALID_SCORES` (`NAN_SCORE`) |
| Điểm ngoài [0,1] | `INVALID_SCORES` (`INVALID_SCORE_RANGE`); không biến đổi hay hiệu chuẩn |
| Điểm đúng 0 hoặc 1 | Hợp lệ; ECE: 0 vào bin đầu, 1 vào bin cuối đóng. **Không dùng log-loss** |
| Ties điểm | Không phá hòa theo ID/thời gian/RNG. AUC: tie=0.5. AP: gộp ngưỡng riêng biệt. Ngân sách: kỳ vọng (§5) |
| Một lớp | Bảng §1 |
| ◆ Trạng thái hiệu lực | Mỗi `(unit, metric)` có đúng một trạng thái: `VALID`, `LOW_SUPPORT` (tính được, cờ), `NULL(reason)` |
| Làm tròn | Không làm tròn trước tổng hợp; báo 6 chữ số |
| Metric null | Rỗng + `reason_code`, không thay bằng 0 |

## 1. Một lớp (áp dụng cho `E_(t,s)` hoặc `E_(t,s,f)`)
| Tình huống | ROC-AUC | AP | Brier | ECE | Prec@τ | Recall@τ | FPR/FAR |
|---|---|---|---|---|---|---|---|
| `P=0` | null `NO_POSITIVES` | null `NO_POSITIVES` | tính | tính, cờ | 0 nếu có alert, null `NO_ALERTS` nếu không | null `NO_POSITIVES` | tính |
| `N=0` (chỉ positive) | null `NO_NEGATIVES` | null `POSITIVES_ONLY` | tính | tính, cờ | tính | tính | null `NO_NEGATIVES` |
| `P≥1, N≥1` | tính | tính | tính | tính | tính | tính | tính |
Không bao giờ tính ROC-AUC trên tập chỉ positive.

## 2. Xếp hạng
- **ROC-AUC** = `(1/(P·N)) ΣΣ [1(p_i>p_j) + 0.5·1(p_i=p_j)]`; baseline 0.5.
- **AP** = `Σ_k (R_k − R_{k−1})·Pr_k` trên ngưỡng điểm riêng biệt giảm dần (tie gộp). **Baseline = `π_(t,s)` của chính `E_(t,s)`** (hoặc `π_(t,s,f)`); **không dùng 0.5**.
- ◆ Báo cho **từng unit**: `π`, `AP`, `AP_excess = AP − π`, `AP_lift = AP/π` (null nếu π=0), `AP_skill = (AP−π)/(1−π)` (null nếu π=1). Giá trị baseline và support đi kèm trong cùng hàng; không so AP thô giữa các `E_(t,s)` có π khác nhau — dùng `AP_excess`/`AP_lift`.
- Hiệu lực: `VALID` nếu `P ≥ P_min` và `N ≥ N_min`; ngược lại `LOW_SUPPORT` (tính được nếu có cả hai lớp).

## 3. Xác suất
- **Brier** = `mean (p−y)²`. ◆ Tham chiếu theo seed: `Brier_ref_(t,s) = π_(t,s)(1−π_(t,s))`; `BSS = 1 − Brier/Brier_ref` (null nếu `π∈{0,1}`). Đây là tham chiếu oracle (dự đoán hằng prevalence của chính `E_(t,s)`), chỉ mô tả.
- **ECE** 10 bin đều rộng: `[0,.1)…[.8,.9), [.9,1.0]`; `Σ (n_k/n)|ȳ_k − p̄_k|`, bỏ bin rỗng; báo bảng bin. `LOW_SUPPORT_ECE` nếu `n < 500` hoặc `P < P_min`; bin `n_k<10` có cờ. Không hiệu chuẩn lại. Không báo Brier/ECE cho family-vs-benign.

## 4. Ngưỡng lịch sử `τ_(t,s,a)`
`alert ⇔ p ≥ τ_(t,s,a)`. Ngưỡng lấy nguyên từ artifact đã khóa (1.080 giá trị, nối theo unit). Thiếu → `THRESHOLD_MISSING`. Không tính lại.
- `TP, FP, FN, TN`, `alerts`, `alert_rate = alerts/n`.
- **Precision** = `TP/alerts` (null `NO_ALERTS`); `LOW_SUPPORT_PRECISION` nếu `alerts<20`.
- **Recall** = `TP/P` (null `NO_POSITIVES`); `LOW_SUPPORT` nếu `P<P_min`.
- **FAR ≡ FPR = FP/N**; `LOW_SUPPORT` nếu `N<N_min`. Đây là định nghĩa duy nhất của FAR.
- ◆ **`FA_count = FP`** trên `E_(t,s)` (đếm, không chuẩn hóa thời gian).
- ◆ **Đã bỏ (DROPPED v2):** false alerts per hour / per day (v1 M13/M14). Lý do: `E_(t,s)` thay đổi theo seed, chưa chứng minh là toàn bộ quần thể ngày đích; không có kết luận "toàn mạng". Phục hồi chỉ khi báo cáo local xác nhận `ts` cấp flow + metadata khoảng thiếu + `E_(t,s)` là quần thể đầy đủ.

## 5. Ngân sách cảnh báo (PROPOSED, khóa trước)
- Với mỗi `E_(t,s)`: `K = ceil(b·n)` cho `b ∈ {0.001, 0.005, 0.01, 0.05}`; và `K ∈ {100, 1000}`. `K ≥ n` → null `BUDGET_EXCEEDS_N`. `K<20` → `LOW_SUPPORT_BUDGET`. (Do `n` đổi theo seed, `K` có thể đổi theo seed.)
- Xếp theo điểm giảm dần; tie tại biên: nhóm `g` hàng có `a` positive, còn `r` chỗ (`0<r<g`) → `E[TP]` thêm `r·a/g`; cờ `TIE_AT_CUTOFF`.
- `Precision@K = E[TP_K]/K`; `Recall@K = E[TP_K]/P` (null `NO_POSITIVES`); cận trên `min(P,K)/K`, `min(P,K)/P`.
- Không dùng ngân sách "bằng số alert của ngưỡng lịch sử" (phụ thuộc arm).

## 6. Family (◆ phân biệt rõ hai loại)
| Metric | Tập / xếp hạng | Định nghĩa |
|---|---|---|
| `FAM_AUC`, `FAM_AP` | `E_(t,s,f)`: xếp hạng chỉ giữa benign và family `f` | như §2 với `P_f`, `N`, baseline `π_f`; `FAM_AP_excess = AP − π_f` |
| `FAM_RECALL_BUDGET@K` | xếp hạng và `K` trên **toàn bộ `E_(t,s)`** | `E[TP_f in top-K]/P_f`; tie biên: `r·a_f/g` với `a_f` = số positive family `f` trong nhóm tie; cận trên `min(P_f,K)/P_f` |
| `FAM_RECALL_TAU` | toàn `E_(t,s)`, `p ≥ τ` | `TP_f/P_f` |
`FAM_AP` đo khả năng tách family `f` khỏi benign; `FAM_RECALL_BUDGET` đo family `f` được bắt trong ngân sách **chung** khi cạnh tranh với mọi family khác — không so sánh số hai loại này với nhau. Family có `P_f < P_min` → `LOW_SUPPORT`. Chỉ có tập `E_(t,s,f)` khi family `f` xuất hiện trong `E_(t,s)`; sự có mặt của nhãn family: **O9 chưa xác minh**.

## 7. Ví dụ tay (golden vectors, tính bằng tay; không phải kết quả)
| Ví dụ | Đầu vào | Kết quả kỳ vọng |
|---|---|---|
| G1 | `y=[1,0,1,0]`, `p=[0.9,0.8,0.4,0.1]` | AUC=0.75; AP=0.833333; Brier=0.255; π=0.5; Brier_ref=0.25; BSS=−0.02 |
| G2 (tie biên) | `y=[1,0,1,0,0]`, `p=[0.5,0.5,0.5,0.2,0.1]`, K=2 | E[TP]=2·2/3=1.333333; Precision@2=0.666667; Recall@2=0.666667 |
| G3 (ECE) | `y=[0,0,1,0]`, `p=[0.05,0.15,0.95,0.95]` | ECE=0.275 |
| G4 (một lớp) | `y=[1,1,1]` | AUC null `NO_NEGATIVES`; AP null `POSITIVES_ONLY` |
| G5 (0/1) | `y=[1,0]`, `p=[1.0,0.0]` | AUC=1; Brier=0; ECE=0 |
| ◆ G6 (family recall) | `y=[1,1,1,0]`, familyA=`[1,0,1,0]` (hàng 1 và 3), `p=[0.9,0.8,0.7,0.1]`, K=2 | top-2 gồm hàng 1,2; `Recall_A@2`=1/2=0.5 (hàng 3 ngoài top-K) |
| ◆ G7 (per-seed baseline) | seed1: `P=2,n=10`; seed2: `P=5,n=10` | π=0.2 và 0.5; AP baseline là 0.2 và 0.5 (không dùng 0.5 chung) |
Dùng làm unit test của D3-1E **trước** khi đọc dự đoán thật. (Đối chiếu với "20/20 PASS" nhân tạo của người nghiên cứu: không biết nội dung 20 test; không ghép.)
