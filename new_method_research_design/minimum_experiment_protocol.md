# Giao thức thí nghiệm tối thiểu (thiết kế; chưa duyệt; chưa chạy)

## Giả thuyết
**H1 (chính):**
- Điều kiện giữ nguyên: cùng backbone/checkpoint (TabICL-v2), cùng `M` và `M_s/M_l`, cùng luồng nhãn và ngân sách nhập, cùng `ϕ`, cùng τ entropy, cùng tập eval.
- Dự đoán: FF-CURE có **FAM_AP_excess trung bình trên các họ tái xuất hiện khối lượng thấp** cao hơn CURE ở đa số transition đánh giá được.
- Guard: AP nhị phân không thấp hơn CURE quá mức δ khóa trước.

**Định nghĩa họ đủ điều kiện** cho transition t→t+1, khóa từ metadata, không dùng kết quả mô hình:
- có ≥ `n_hist` dòng trong các ngày ≤ t−1;
- chiếm < `q` phần attack của ngày t (vắng hoặc hiếm);
- có ≥ P_min = 20 dòng dương trong tập eval ngày t+1;
- là "khối lượng thấp" nếu nằm dưới trung vị khối lượng họ của lịch sử.

**Endpoint chính:** với mỗi (t, seed), trung bình FAM_AP_excess = AP(benign ∪ f) − π_f trên các họ đủ điều kiện. Lấy hiệu Δ = FF-CURE − CURE theo cặp seed cùng tập eval, rồi trung bình theo transition. Báo số transition có Δ > 0.
**Phụ:** AP nhị phân, AP_excess, recall@K ngân sách cố định (tie theo kỳ vọng), FAM_RECALL_BUDGET, FPR. Không dùng ngưỡng mới; không fitting ngưỡng (bài học H1/H2).

## Comparator và ablation (mọi arm cùng backbone, cùng M, cùng luồng nhãn)
| Arm | Vai trò |
|---|---|
| CURE (bản tác giả, code công bố) | **Baseline chính được cải tiến** |
| FF-CURE | Đề xuất |
| A1: CURE với khóa = họ nhưng **không floor** | Tách đóng góp của khóa họ |
| A2: floor + chọn cũ nhất trong họ (không redundancy) | ≈ CBRS-family cho context; tách đóng góp redundancy |
| A3: khóa = cụm không giám sát (k-means trên dòng attack của lịch sử, k = |F_t|) | Có cần nhãn họ không |
| A4: CURE đa lớp (ICL với y = họ, gộp xác suất attack), ở nơi số lớp ≤ giới hạn backbone | Rủi ro tương đương "chỉ đổi nhãn" |
| DualFIFO; FIFO (recency); uniform-history (kiểu S3) | Mốc prior art |
| LightGBM huấn luyện trên **đúng** context của từng arm (CPU) | Lợi ích do chọn dữ liệu hay do tương tác với TFM |
| Cùng chính sách trên backbone thứ hai (TabFM) | Chỉ ở xác nhận, nếu tài nguyên cho phép |

## Giữ cố định
Checkpoint, số ensemble/permutation, `M`, `M_s`, `β`, `m_floor`, τ, `ϕ` (chuẩn hóa fit chỉ trên lịch sử), tiền xử lý, thứ tự luồng, mẫu eval, seed, ngân sách nhập nhãn, số lớp ICL (nhị phân, trừ A4). Mọi tham số khóa **trước** khi mở dữ liệu xác nhận.

## Tách development và xác nhận
| Vai trò | Nguồn | Ghi chú |
|---|---|---|
| G0 kiểm khả thi (chỉ metadata, không mô hình) | NF-ToN-IoT-v3, NF-CICIDS2018-v3 (bảng họ × ngày của Gate M) | Đếm transition có ≥ 1 họ đủ điều kiện |
| Pilot development | Cùng hai nguồn (đã phơi nhiễm) | Chỉ để quyết định đi tiếp/dừng và sửa lỗi; **không** dùng làm bằng chứng cải tiến |
| Xác nhận độc lập | **Chưa có nguồn sạch được xác minh** (xem `data_and_compute_requirements.md`) | Đăng ký trước, khóa toàn bộ tham số, mở một lần |
Không coi đổi seed/subset/ngày của nguồn đã phơi nhiễm là xác nhận.

## Điều kiện dừng
- **G0:** < 4 transition dev có họ đủ điều kiện → **NO-GO** (không kiểm được).
- **Pilot:** FF-CURE ≤ CURE trên endpoint chính ở ≥ một nửa transition dev đủ điều kiện → **dừng, NO-GO**.
- **Pilot:** A1 hoặc A2 ≈ FF-CURE (|Δ| nhỏ hơn biến thiên seed) → thu hẹp claim theo ablation thắng **trước** xác nhận, hoặc dừng nếu chỉ còn CBRS-family.
- **Pilot:** A3 ≈ FF-CURE → đặc tả lại với khóa cụm (không cần nhãn họ) trước xác nhận.
- **Guard:** AP nhị phân thấp hơn CURE quá δ ở đa số transition → dừng.
- **Xác nhận:** số transition Δ > 0 dưới ngưỡng khóa trước, hoặc guard vi phạm → claim bị bác; báo cáo kết quả âm.
- **Dữ liệu:** không có nguồn xác nhận sạch → không có claim cải tiến; chỉ báo pilot development.

## Đơn vị và thống kê
Transition là đơn vị; seed là lặp đo, cặp theo seed trên cùng tập eval. Không p-value coi seed/flow là mẫu độc lập; không pool flow. Ngưỡng thành công (số transition dương, δ) khóa trước xác nhận.
