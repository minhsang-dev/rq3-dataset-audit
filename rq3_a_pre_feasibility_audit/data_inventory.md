# Kiểm kê dữ liệu cho đề xuất A (G1, G-task 2, 5, 6)

**Phạm vi tìm.** Repo `minhsang-dev/rq3-dataset-audit`, nhánh `claude/sleepy-cray-jhn01z` (91 tệp được git theo dõi) và bản giải nén của gói handoff tại `/tmp/claude-0/handoff/claude_hybrid_handoff/` (19 tệp).

**Cách tìm.** Chỉ dùng `ls`, `git ls-files` và `grep` trên văn bản. Không chạy Python, không mở hay tính trên vector điểm, không replay.

**Quy ước trạng thái:**
- **MISSING (trong phạm vi Cloud):** không truy cập được từ Cloud. Đây **không phải** chứng minh rằng tệp không tồn tại.
- **ABSENT_BY_DESIGN:** gói handoff ghi rõ là cố ý loại ra.

## 1. Vector điểm theo flow, nhãn, ngưỡng, khóa ghép
| Hạng mục cần cho A | Có trong repo/handoff? | Bằng chứng tồn tại ở nơi khác | Trạng thái với Cloud |
|---|---|---|---|
| Vector điểm eval theo flow, S2 và S1-ctx (`float32[10000]` × 180 mỗi arm) | Không. `git ls-files` không có tệp `.npz/.npy/.zip/.parquet/.pkl` nào | `08_rq3_arms_and_score_join_EXTRACT.md` dòng 60: "archive/member in prior primary_units_verified.csv: NPZ eval:float32[n] … 1080 vectors; 10.800.000 rows". `missing_evidence.md`: "large prediction vectors are deliberately excluded, not lost" | **MISSING / ABSENT_BY_DESIGN**. Có thể nằm trên máy người nghiên cứu (`D:\Personal\PhD\IUH\…`) nhưng chưa được kiểm từ Cloud |
| Nhãn eval (`eval_y`, `eval_attack`, `eval_rowid`) | Không | `08` dòng 59: `colab_pkg_bai3.zip!colab_pkg_bai3/data/{dataset}/{source_day}_{seed}.npz` | **MISSING** |
| Đặc trưng chưa nhãn của ngày đích (`eval_X`) | Không | `08` dòng 30: S2/S1-ctx "cùng hash … eval_X ở đủ 180 cặp", nghĩa là `eval_X` có tồn tại trong NPZ đầu vào | **MISSING** |
| Ngưỡng lịch sử theo unit | Không | `08` dòng 62: `rq3_study1_threshold_restore_20261024/study1_thresholds_restored.csv` (1.080 dòng, kèm float_hex). Repo chỉ có giá trị ngưỡng **đã chép** trong `m7_tabfm_failure_specificity/failure_pair_matrix.csv` (180 cặp S2/S1-ctx), không có tệp gốc | **MISSING** (tệp gốc); chỉ có bản sao giá trị cho 180 cặp |
| Mẫu ngưỡng ngày nguồn: `thr_rowid`, `thr_y` và điểm `thr` | Không | `08` dòng 64: "score NPZ thr:float32[10000] — **provenance ONLY, no replay**" | **MISSING**. Nếu có thì vẫn bị khóa "no replay" và cần phê duyệt riêng |
| Ngữ cảnh (`ctx_recent_X/y/rowid`) | Không | `08` dòng 63 | **MISSING** |
| Comparability key, transition/seed | Có, nhưng chỉ ở dạng tóm tắt | `14_matched_lightgbm_tabfm_comparisons.csv` (SHA-256 `5078e321…bf6c0`) và `failure_pair_matrix.csv` | **CÓ** |
| Metric tổng hợp: AUC, AP, recall@τ, FPR, precision | Có | Tệp `14` và ma trận M7 | **CÓ**, nhưng chỉ là tổng hợp. `08` dòng 5: "Summary table 14 is not sufficient for a routing replay" |

**Kết luận G1: KHÔNG ĐẠT trong phạm vi Cloud.** Mọi vector điểm, nhãn theo flow, `eval_X`, mẫu ngưỡng và tệp ngưỡng gốc đều MISSING. Chỉ còn metric tổng hợp, mà theo đúng G1 thì metric tổng hợp không đủ để thử hiệu chỉnh ngưỡng.

## 2. Đầu vào riêng cho BBSE/EM (task 5)
| Đầu vào | Yêu cầu | Trạng thái |
|---|---|---|
| Ma trận nhầm lẫn ở nguồn, `C[ŷ, y]` trên dữ liệu nguồn có nhãn **độc lập với dữ liệu fit/context** | Mẫu ngưỡng `thr` + `thr_y` là ứng viên duy nhất được ghi nhận | MISSING. Mẫu này còn **đã được dùng để chọn ngưỡng** (07 dòng 51), nên dùng lại để ước lượng `C` là tái dùng dữ liệu, cần khai báo. File `13` dòng 155 ghi: "chưa xác nhận split D_sel/D_cert độc lập với scorer/context" |
| Dự đoán chưa nhãn của ngày đích | Vector `eval` (cũng là tập chấm điểm) | MISSING. Nếu dùng cùng tập vừa để ước lượng prevalence vừa để chấm điểm thì cần tách và khai báo trước |
| Nhãn ngữ cảnh, cho biến thể cân lại lớp trong context | `ctx_recent_y` | MISSING. Biến thể này còn cần **điểm phản thực** cho context mới, tức là phải inference mới, nằm ngoài phạm vi (xem `05_saved_prediction_reuse.md` mục 3, dòng C-R3) |

## 3. Rủi ro của giả định label shift (G3) — chỉ dựa trên văn bản đã lưu
- **Loại dịch chuyển.** Bảng §2 của `07_rq3_study1_protocol_and_results_EXTRACT.md` gán loại dịch chuyển cho từng transition từ nhãn họ tấn công, trước khi đọc đầu ra mô hình. Kết quả: **10/12 transition là `new_class`**, chỉ 2 là `prior_shift_only` (ToN 04-28→04-29 và CIC 02-28→03-01). Theo `07`, `new_class` nghĩa là có họ tấn công mới, tức là P(X | Y = attack) thay đổi. Đây chính là điều BBSE giả định không xảy ra.
- **Các transition mà S2 có recall = 0** (CIC 02-16→02-20, CIC 03-01→03-02, ToN 04-27→04-28; theo M7) **đều thuộc loại `new_class`**. Hai transition `prior_shift_only` không có cặp S2/S1-ctx nào có recall = 0 (theo `transition_level_summary.md`).
- **Lập luận lý thuyết** (không phải phân tích dữ liệu). Nếu label shift thuần túy đúng, tức P(X | Y) không đổi, thì một ngưỡng cố định trên điểm gốc giữ nguyên TPR và FPR khi chuyển từ nguồn sang đích; chỉ precision và macro-F1 thay đổi theo prevalence. Một ngưỡng chọn trên nguồn mà ra recall = 0 trên đích không thể giải thích bằng label shift thuần túy, *nếu* recall tại τ trên mẫu ngưỡng nguồn là dương. Điều kiện "nếu" này **chưa kiểm được** vì `thr`/`thr_y` MISSING.
- **Hệ quả.** Ở đúng những transition mà A muốn sửa, giả định nhận dạng của BBSE nhiều khả năng bị vi phạm. Ở hai transition mà giả định có thể đúng, không có thất bại recall = 0 để sửa.

## 4. Nguồn held-out chưa phơi nhiễm (task 6)
| Ứng viên đã biết | Trạng thái | Nguồn |
|---|---|---|
| NF-ToN-IoT-v3, NF-CICIDS2018-v3 | Đã phơi nhiễm trong phát triển | `missing_evidence.md`; `11` |
| NF-UNSW-NB15-v3 | Đã phơi nhiễm (mô tả/dự đoán); chỉ có 1 transition | `missing_evidence.md`; `07` dòng 28 |
| CTU-SME-11 | Zeek, không phải NetFlow 46 đặc trưng; không có timestamp xác nhận nhãn; chưa phù hợp | `gate_e2_outputs/`, `gate_e2b_limited/` |
| CIDDS (dự phòng ở Gate E2) | Chưa kiểm toán cho vai trò này; không có trong repo | — |

**Kết luận:** **KHÔNG có** nguồn held-out sạch nào đã được xác minh. `missing_evidence.md` ghi: "Clean independent held-out confirmation … NONE VERIFIED". Theo quy tắc của nhiệm vụ, tôi không tìm hay tải nguồn mới.
