# Đặc tả ứng viên cải thiện (tối đa ba) — chỉ trên giấy, không triển khai

Ký hiệu: batch đánh giá `E` (trong Study 1 là `E_(t,s)` với 10.000 hàng; ở vận hành là một cửa sổ đã định nghĩa trước). `s_e(x)` là điểm edge (LightGBM, P(attack)). `s_c(x)` là điểm cloud (TabFM S2 hoặc TabICL S2-ICL với cùng recent context). `τ_e, τ_c` là ngưỡng lịch sử source-only của từng arm. Ngân sách: cloud-call `b_c` (tỉ lệ flow gọi cloud), nhãn analyst `b_l`, cảnh báo cuối `K`, độ trễ nhãn `d`. Mọi tham số số học là **TBD bởi người nghiên cứu trước khi xem bất kỳ kết quả nào**.

Đơn vị phân tích (kế thừa D3): transition; seed và flow không độc lập; không pool flow qua batch; không p-value/CI coi seed là mẫu độc lập.

---

## C-R1 — Rank-budget routing (quantile theo batch) thay ngưỡng tuyệt đối
| Trường | Nội dung |
|---|---|
| Baseline kế thừa | **B-HT**: routing theo ngưỡng tuyệt đối lấy từ source-day. Gửi cloud nếu `s_e` nằm trong band cố định quanh `τ_e` (band học trên ngưỡng mẫu nguồn, kiểu H2T2/Chow offline). |
| Thay đổi policy chính xác | Trong mỗi batch, xếp `s_e` giảm dần; gửi cloud đúng `⌈b_c·n⌉` flow có hạng trong một **band hạng** cố định (ví dụ ngay dưới top-K của edge); cảnh báo cuối = top-K theo điểm kết hợp (cloud cho flow đã route, edge cho flow còn lại), hoặc theo `τ_c` cho flow đã route. |
| Khác biệt kỹ thuật tiềm năng | Ngân sách gọi cloud được thỏa theo cấu trúc và không phụ thuộc hiệu chuẩn tuyệt đối. Tránh sụp "không alert nào" (H2: 111 unit recall=0 không có alert). |
| Ánh xạ prior art | Đây là quantile threshold / top-K alert budget; HI với ràng buộc chi phí trung bình; H2T2 với ngưỡng đặt tại quantile của batch. **Tồn tại ánh xạ đầy đủ** → không có khác biệt không tương đương. |
| Giả định | Có batch xác định trước; edge buffer được một batch (thêm latency); dùng thống kê không nhãn của batch đích là hợp lệ. |
| Inputs | `s_e`, `s_c` trên cùng hàng; `b_c`, `K` khóa trước. |
| Điều kiện thất bại | Prevalence trong batch biến động: ngân sách cố định gọi cloud thừa hoặc thiếu. Khi `P > K`, recall ≤ K/P. Buffer batch vi phạm deadline nếu có. |
| Kiểm soát leakage | `b_c, K`, band hạng khóa trước, không dùng nhãn đích; báo realized FPR, không tune FPR bằng nhãn đích. |
| Bằng chứng cần | Không cần cho quyết định tính mới (đã có ánh xạ). Làm baseline thì tái dùng được điểm đã lưu, có điều kiện (`05`). |
| **Tiêu chí** | **NO-GO cho tuyên bố cải thiện.** **Bắt buộc dùng làm baseline (B4)**: mọi ứng viên khác phải thắng nó ở cùng ngân sách. |

---

## C-R2 — Band deferral điều kiện theo mức drift (post-hoc, học qua các transition trước)
| Trường | Nội dung |
|---|---|
| Baseline kế thừa | Post-hoc deferral (Jitkrittum et al. 2023); H2T2 band online; ACI dưới shift. |
| Thay đổi policy chính xác | Biên band `(θl, θu)` là hàm của `s_e` và các thống kê shift **không nhãn** của batch (khoảng cách phân phối `s_e` giữa source và batch đích; tỉ lệ alert edge so với lịch sử). Hàm được fit chỉ trên các transition **trước** (leave-future-out), rồi áp cho transition mới. |
| Khác biệt kỹ thuật tiềm năng | Học "khi nào độ tin cậy edge mất giá trị" qua nhiều lần drift, thay vì thích nghi online trong một stream. |
| Ánh xạ prior art | Thêm covariate cấp batch vào bộ ước lượng deferral post-hoc chỉ là mở rộng tập đặc trưng của `g(x)` trong Jitkrittum et al. (Table 1). Nó nằm trong họ chính sách của post-hoc deferral/ACI → **có ánh xạ**. |
| Giả định | Quan hệ shift→độ tin cậy ổn định qua các transition; các transition đủ nhiều và độc lập. |
| Inputs | Điểm edge/cloud trên ngưỡng mẫu nguồn và batch đích; nhãn của các transition trước. |
| Điều kiện thất bại | Chỉ có 12 transition, 2 dataset, không độc lập và **đã phơi nhiễm** (đã xem kết quả) → không đủ để fit và không thể xác nhận sạch. Shift có cấu trúc khác (lớp mới hay prior shift) làm hàm học không chuyển giao được. |
| Kiểm soát leakage | Hàm chỉ fit trên transition có ngày đích < ngày nguồn của transition kiểm; không dùng flag H1/H2 hay prevalence đích để chọn đặc trưng. |
| Bằng chứng cần | Nhiều transition độc lập ngoài Study 1 (chưa có). |
| **Tiêu chí** | **NO-GO** (tương đương post-hoc deferral; dữ liệu không đủ). Chỉ giữ ý tưởng như một ablation của B6. |

---

## C-R3 — Thu nhãn gắn với routing để làm mới context TFM, dưới ngân sách call+nhãn và trễ nhãn
| Trường | Nội dung |
|---|---|
| Baseline kế thừa | (i) Study 1 S2: recent-context refresh với 10.000 nhãn ngày `t` (nhãn "miễn phí", oracle); (ii) Tab-AICL (thu nhãn bất định từ pool, tĩnh); (iii) HINT (context là láng giềng truy xuất, nhãn prequential không trễ); (iv) H2T2/HI (feedback từ offload chỉ dùng chỉnh ngưỡng); (v) Chen et al. 2023 (chọn mẫu cho analyst rồi **huấn luyện lại**). |
| Thay đổi policy chính xác | Mỗi batch: edge chấm điểm; router chọn tập `R` (\|R\| ≤ b_c·n); cloud chấm `R` với context hiện tại `C_t`. Một tập `A ⊆ R` (\|A\| ≤ b_l·n) được gửi analyst theo **quy tắc khóa trước**; nhãn về sau trễ `d` rồi được chèn vào `C_{t+1}` theo quy tắc cập nhật khóa trước (ví dụ recency có giữ cân bằng lớp). Có một phần cố định `ε·b_l` nhãn lấy ngẫu nhiên đều trong batch để ước lượng prevalence và hạn chế thiên lệch chọn mẫu. LightGBM edge được huấn luyện lại trên **đúng** tập nhãn đó (cùng wrapper). |
| Khác biệt kỹ thuật tiềm năng (chưa chứng minh) | Cơ chế thu nhãn bị **ràng buộc vào tập đã được escalate** (đúng thực tế SOC), có trễ, và đi qua context ICL nên mô hình cập nhật ngay không cần huấn luyện. Tab-AICL chọn tự do từ pool, không trễ, không routing. HINT không có chi phí nhãn. H2T2 không cập nhật context. Chen et al. huấn luyện lại. |
| Ánh xạ prior art cần thử (rủi ro cao) | Có thể biểu diễn C-R3 thành {HI band (H2T2) + quy tắc acquisition Tab-AICL giới hạn trên pool `R` + recency window (2610.05352) + ε-exploration (H2T2 cũng offload một phần để ước lượng)} với cùng ngân sách. Nếu ánh xạ này đầy đủ và không đổi giả định → **NO-GO**. Đây là kết cục **khả dĩ nhất**; chưa kiểm trên giấy. |
| Giả định | Thời điểm có nhãn xác nhận đã biết, hoặc được chấp nhận rõ là **offline oracle** (không tuyên bố khả năng thích nghi online). Analyst capacity cố định. Cloud TFM nhận context cập nhật mỗi batch. Context giữ 10.000 hàng (như Study 1) hoặc giá trị khóa khác. |
| Inputs | Đặc trưng 46 cột, thứ tự thời gian, nhãn với độ trễ mô phỏng `d`, mô hình edge/cloud. Điểm đã lưu **không đủ**: cần điểm phản thực cho mỗi context mới (inference mới, ngoài phạm vi hiện tại). |
| Điều kiện thất bại | (1) `d` dài hơn thang thời gian drift → không có lợi. (2) Thiên lệch chọn mẫu (chỉ nhãn flow đã escalate) làm lệch phân bố lớp trong context. (3) Edge im lặng (all-clear) → không escalate → không có nhãn → không thích nghi (rủi ro được H2 minh họa). (4) Escalation vượt analyst capacity → overflow/backlog phải được tính vào accounting. (5) Lợi thế chỉ đến từ scorer cloud mạnh hơn (thấy khi cùng wrapper áp cho LightGBM retrain). |
| Kiểm soát leakage | Nhãn chỉ dùng sau thời điểm có nhãn (thật hoặc oracle đã khai báo). Không dùng nhãn đích để chọn `b_c, b_l, d, ε` hay quy tắc. Tách vai trò development (ToN-IoT, CICIDS2018 — đã phơi nhiễm), selection, certification và held-out (**hiện không có nguồn sạch**). Không xem đổi seed/subset là dữ liệu mới. |
| Bằng chứng cần | (a) Chứng minh hoặc phản ví dụ không-tương-đương trên giấy. (b) Nguồn có timestamp xác nhận nhãn, hoặc chấp nhận oracle. (c) Năng lực inference phản thực (phê duyệt riêng, sau này). (d) Nguồn held-out mới. Ghi chú: CTU-SME-11 đã kiểm toán trong repo này (Gate E2/E2b) là Zeek, không phải NetFlow 46 đặc trưng; nhãn theo quy tắc IP; không có timestamp xác nhận → **chưa phù hợp** nếu không trích lại đặc trưng và có trả lời của tác giả. (e) Nhiều transition hơn 12. |
| Giả thuyết bác bỏ được (khóa trước) | Ở cùng `b_c, b_l, d, K` và cùng ngân sách tìm kiếm policy, C-R3 có recall@K cao hơn **baseline mạnh nhất** trong B4–B9 (`04`) ở đa số transition held-out đánh giá được. Đồng thời không giảm coverage, không tăng deferral/overflow, không tăng realized FPR quá mức khóa trước. **Bị bác** nếu số transition dương ≤ một nửa, hoặc lợi thế biến mất khi áp cùng wrapper cho LightGBM, hoặc chỉ xuất hiện khi tăng ngân sách. |
| **Tiêu chí** | **HOLD.** → GO (chỉ đặc tả giấy tiếp) nếu (a) cho witness không tương đương **và** (b) vai trò dữ liệu khả thi. → NO-GO nếu ánh xạ ở dòng "Ánh xạ prior art" đầy đủ. |

## Tóm tắt
| Ứng viên | Tuyên bố cải thiện | Vai trò còn lại |
|---|---|---|
| C-R1 rank-budget | NO-GO | Baseline bắt buộc B4 |
| C-R2 drift-conditioned band | NO-GO | Ablation của B6 |
| C-R3 coupled context acquisition | HOLD | Ứng viên duy nhất chưa bị loại; khả năng cao sẽ thành NO-GO sau kiểm tương đương |
