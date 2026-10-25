# Hybrid LightGBM (Edge) → Selective Routing → TabFM/TabICL (Cloud): kiểm toán prior art và đặc tả trên giấy

**Ngày:** 2026-10-25 · **Phạm vi:** chỉ nghiên cứu giấy và đặc tả. Không lập trình, không thí nghiệm, không replay/huấn luyện/suy luận/benchmark/GPU, không fitting policy, không sửa Study 1/2 hay bất kỳ artifact đã khóa. Hybrid là **ứng viên chưa được duyệt; cải thiện chưa được chứng minh.**

## Kết luận: **HOLD** cho việc đặc tả tiếp trên giấy

| Câu hỏi | Trả lời |
|---|---|
| Kiến trúc "edge model → routing → cloud TFM" có phải mới không? | **Không.** Đã có Hierarchical Inference (HI) và cascade/deferral; riêng bài **HINT** (arXiv:2609.07956v1, 7/9/2026) đã dùng đúng cấu trúc edge → ngưỡng tin cậy → cloud TFM (TabPFN) kèm chuyển context. Đã có cả bản IDS edge→cloud theo độ tin cậy (Simioni et al., IEEE IoTJ 2025). Thay edge bằng LightGBM hay thay cloud bằng TabFM/TabICL không tự tạo tính mới (xem `02`). |
| Routing theo ngưỡng/band/budget có mới không? | **Không.** Ngưỡng tin cậy (HI-learning), band hai ngưỡng bất đối xứng chi phí (H2T2, AAAI 2026), cascade deferral và điều kiện thất bại của nó (Jitkrittum 2023), L2D có capacity/workload (DeCCaF), risk control (LTT/CRC), conformal cascade đã tồn tại. |
| Có ứng viên cải thiện nào sống sót như một tuyên bố phương pháp không? | **Không có ứng viên nào được chứng nhận.** Hai trong ba ứng viên đặc tả (C-R1, C-R2) bị loại (NO-GO) làm *tuyên bố cải thiện*; chỉ giữ như baseline bắt buộc/chẩn đoán. Một ứng viên (C-R3) chưa bị loại nhưng **HOLD**. |
| Có thể tái dùng dự đoán đã lưu không? | **Có điều kiện, chỉ cho chẩn đoán retrospective** của chính sách là hàm xác định của điểm đã lưu trên cùng context/hàng đánh giá (xem `05`). Không đủ cho C-R3 (cần điểm phản thực). **Không** đề nghị chạy lại ngay. |
| Còn dữ liệu xác nhận sạch không? | **Không có** (Gate M: `Clean independent confirmation: NONE VERIFIED`; ToN-IoT/CICIDS2018 đã phơi nhiễm; UNSW không sạch). Không có timestamp xác nhận nhãn. |

## Bảng bằng chứng ngắn
| Hạng mục | Bằng chứng | Nguồn | Hàm ý |
|---|---|---|---|
| Hybrid đã biết | HINT (đọc toàn văn); H2T2 (đọc phần giới thiệu/bài toán); HI-learning, Behera 2025 | `prior_art_matrix.csv` P01–P08 | Không tuyên bố tính mới của kiến trúc hay routing theo độ tin cậy |
| Hybrid cho IDS đã biết | Simioni et al. (early-exit + reject option + offloading, IoTJ 2025); CECN-DNN; DQN offloading | P22–P24 (chỉ metadata/abstract) | Không tuyên bố "chưa ai làm edge–cloud IDS" |
| Deferral ở SOC/IDS | L2DHF (2506.18462), risk certification chống all-abstain (2608.12444) | P25–P26 | Guard chống all-defer/all-clear đã là ý tưởng đã công bố |
| TFM context acquisition | Tab-AICL (2603.27385); TFM on streams (2610.05352) | P29–P30 | C-R3 phải khác biệt với active ICL và với HINT |
| Ngưỡng lịch sử thất bại dưới drift | H1/H2: 185/1080 unit có recall=0 tại ngưỡng lịch sử, cả LightGBM và TabFM; 141 unit có AUC>0.5 và AP>π mà recall=0 | 10 | Động cơ thực nghiệm cho routing **không phụ thuộc ngưỡng tuyệt đối**; không chứng minh nguyên nhân; không riêng TabFM |
| Cùng context | S2 và S1-ctx có cùng context XY và eval X ở 180/180 cặp; S3 mixed khác; S0/S1 khác thông tin | 08 | So sánh edge/cloud công bằng chỉ hợp lệ trên cặp cùng context |
| C1 NO-GO | Wrapper "admission contract" tương đương joint-risk LTT baseline | 13 | Không tái đặt tên C1 thành ứng viên hybrid |
| Tài nguyên | Google TabFM nạp thất bại ở mọi cap tới 4 GiB; no-limit load peak ≈13,42 GB; TabICL predict không hoàn tất tới 4 GiB; ARM cloud ≠ bo mạch nhúng; chưa có đo end-to-end hybrid hay network | 05, missing_evidence | Mọi "total cost" trong đặc tả là mô hình tham số, không phải số đo |

## Điều kiện falsification toàn cục (bác bỏ ngay trên giấy)
1. Với mỗi ứng viên: nếu tồn tại ánh xạ đầy đủ policy/loss/ràng buộc/state vào một baseline đã công bố (H2T2, HI, Chow/quantile, post-hoc deferral, DeCCaF, LTT/CRC, Tab-AICL, HINT) **không đổi dữ liệu, giả định, ngân sách** → NO-GO (quy tắc của C1, file 13).
2. Nếu một cải thiện chỉ xuất hiện khi tăng cloud-call, nhãn, FPR hoặc ngân sách tìm kiếm policy, hoặc khi policy lùi về all-clear/all-defer → không tính là cải thiện.
3. Nếu cải thiện chỉ có khi tune policy bằng nhãn của chính target rồi gọi chính target là xác nhận → không hợp lệ.
4. Nếu cải thiện biến mất khi cùng một wrapper được áp cho từng scorer (kể cả scorer yếu/mạnh) → đó là lợi thế scorer, không phải wrapper.

## Điều kiện để chuyển HOLD → GO (chỉ cho *tiếp tục đặc tả trên giấy*)
(a) Một chứng minh/witness trên giấy rằng C-R3 **không tương đương** với tổ hợp {HINT + H2T2 + Tab-AICL + Chen et al. 2023 (active labelling dưới drift)} khi cùng dữ liệu, giả định và ngân sách; (b) người nghiên cứu khóa vai trò dữ liệu (development/selection/certification/held-out) và nguồn held-out có timestamp xác nhận nhãn hoặc chấp nhận rõ ràng "offline oracle"; (c) người nghiên cứu xác nhận bằng văn bản (với supervisor) rằng cải thiện phương pháp/quy trình độc lập là bắt buộc (PATH B/C, file 02) — nếu không, đây không nên là ưu tiên.
**GO** ở đây **không** cho phép triển khai và **không** chứng nhận tính mới.

## Một bước tiếp theo cần phê duyệt
Phê duyệt một **bản đặc tả giấy duy nhất cho C-R3** (chứng minh không-tương-đương/bác bỏ và khóa vai trò dữ liệu), kèm xác nhận bằng văn bản về yêu cầu đóng góp phương pháp; chưa chạy code, replay hay inference.

## Các tệp
`01_package_verification.md` · `02_prior_art_audit.md` · `prior_art_matrix.csv` · `03_candidate_specifications.md` · `04_baselines_and_ablations.md` · `05_saved_prediction_reuse.md` · `manifest_sha256.md`
