# Baseline mạnh, ablation và quy tắc ghép ngân sách (đặc tả, chưa chạy)

## 1. Baseline
| Mã | Baseline | Định nghĩa | Arm Study 1 tương ứng (nếu có) |
|---|---|---|---|
| B1 | Edge only | LightGBM trên recent context; cảnh báo theo `τ_e` hoặc top-K | S1-ctx (cùng context với S2) |
| B1′ | Edge only, nhiều dữ liệu hơn | LightGBM trên toàn source-day | S1 (khác ngân sách thông tin; chỉ tham chiếu) |
| B2 | Cloud only | TFM chấm mọi flow (b_c = 1) | S2 (TabFM), S2-ICL (TabICL); S3 mixed tách riêng |
| B3 | Routing theo ngưỡng/độ tin cậy chuẩn | Chow/HI: gửi cloud khi độ tin cậy edge thấp; band hai ngưỡng kiểu H2T2 (offline từ source) | — |
| B4 | Rank-budget routing | C-R1: band hạng theo batch, đúng `⌈b_c·n⌉` call | — |
| B5 | Random routing | Chọn ngẫu nhiên `⌈b_c·n⌉` flow gửi cloud (seed khóa) | — |
| B6 | Post-hoc deferral | Bộ ước lượng `g` (Jitkrittum et al.) fit trên mẫu ngưỡng source có điểm của cả hai mô hình | — |
| B7 | HINT (áp cho IDS) | kNN sliding-window ở edge + θ + k(t) láng giềng làm context cloud; tái hiện từ mô tả bài báo (không thấy mã → độ trung thành không kiểm được) | — |
| B8 | H2T2 online | Band hai ngưỡng học online với feedback giới hạn (theo bài báo) | — |
| B9 | L2D có capacity cho analyst | DeCCaF-style hoặc top-K cho analyst trong capacity | — |
| B10 | Same wrapper, scorer khác | Cùng policy routing áp lần lượt cho mọi cặp (edge, cloud) gồm S1-ctx→S2, S1-ctx→S2-ICL, và S1-ctx→S1-ctx (cloud = chính edge, kiểm soát "wrapper không lợi ích") | — |

Riêng C-R3 cần thêm: **B11** recency refresh không routing, với cùng `b_l` nhãn lấy ngẫu nhiên; **B12** Tab-AICL acquisition (margin) trên toàn batch cùng `b_l`; **B13** LightGBM huấn luyện lại trên đúng tập nhãn của C-R3.

## 2. Ablation
| Ablation | Tách câu hỏi |
|---|---|
| Bỏ routing (b_c=0 / b_c=1) | Hybrid có hơn edge-only/cloud-only không, ở đâu trên đường cong chi phí |
| Ngưỡng tuyệt đối vs hạng | Lợi ích do loại bỏ phụ thuộc hiệu chuẩn hay do ngân sách |
| Band một phía (chỉ verify edge-positive) vs hai phía (rescue edge-negative) | Đóng góp recall đến từ đâu |
| Cùng wrapper trên LightGBM/TabFM/TabICL | Lợi thế scorer vs lợi thế wrapper |
| `ε` exploration = 0 vs > 0 (C-R3) | Ảnh hưởng thiên lệch chọn mẫu |
| Độ trễ nhãn `d` ∈ tập khóa trước | Giới hạn thích nghi |
| Context transfer (HINT) bật/tắt | Lợi ích từ context láng giềng |

## 3. Ghép ngân sách (bắt buộc)
- **Cloud-call:** cùng `b_c` (số call thực hiện, kể cả call thất bại/timeout) cho mọi policy so sánh; báo **realized** call rate.
- **Compute:** cùng scorer/checkpoint, cùng context size, precision, query/batch semantics; không gộp số đo RQ1 (CPU full-context) với RQ3 (GPU recent context).
- **Nhãn:** cùng `b_l`, cùng `d`, cùng quy tắc có-nhãn; oracle phải được gọi là oracle.
- **Network:** cùng mô hình chi phí bytes/call (query 46×4 byte + context nếu gửi kèm; HINT gửi `k` láng giềng). Đây là chi phí **tham số**, chưa có số đo.
- **Ngân sách tìm kiếm policy:** cùng số cấu hình thử và cùng dữ liệu selection cho mọi policy; không tune ứng viên nhiều hơn baseline.
- **FPR:** hai track (theo C1, file 13). **Track K:** cùng ngân sách cảnh báo cuối `K` và cùng quy tắc tie. **Track α:** cùng FPR mục tiêu danh nghĩa `α`, đặt chỉ bằng dữ liệu source/selection; báo realized FPR. **Cấm** chỉnh để khớp realized FPR bằng nhãn đích.

## 4. Chỉ số báo cáo (không có điểm vô hướng duy nhất)
- **Chính:** recall@K (Track K) hoặc recall tại α danh nghĩa với realized FPR kèm theo (Track α).
- **Kèm bắt buộc:**
  - coverage (tỉ lệ flow có quyết định cuối), deferral fraction, cloud-call fraction, label spend;
  - overflow/backlog (yêu cầu vượt capacity không được giấu);
  - số alert, precision (NULL nếu không có alert);
  - FAR = FP/N theo flow;
  - tổng chi phí theo vector chi phí khóa trước, cùng các thành phần của nó.
- **Chống thắng giả:**
  - policy all-clear (0 alert) hoặc all-defer phải được đánh dấu và **không** được tính là thắng;
  - so sánh tại điểm có coverage và recall dương, theo sàn tối thiểu khóa trước (TBD);
  - báo đủ các policy/batch bị từ chối trong mẫu số.
- **Không** báo alerts/giờ hay ngày từ mẫu 10.000 hàng khi chưa xác minh time coverage.

## 5. Vai trò dữ liệu
| Vai trò | Nguồn hiện có | Trạng thái |
|---|---|---|
| Development | NF-ToN-IoT-v3, NF-CICIDS2018-v3 (Study 1) | Đã phơi nhiễm; chỉ dùng phát triển/chẩn đoán |
| Selection/calibration | Mẫu ngưỡng source-day (`thr_rowid`, 10.000 hàng/transition) | Tách khỏi context theo prereg; chưa xác minh độc lập với scorer/context cho mọi vai trò |
| Certification | Chưa có tập riêng hợp lệ | TBD |
| Held-out confirmation | **Không có nguồn sạch** (Gate M) | Chặn mọi tuyên bố xác nhận |
Thời điểm capture không phải thời điểm có nhãn xác nhận. Target đã xem không trở lại "sạch" khi đổi seed, subset hay cách chia hàng.
