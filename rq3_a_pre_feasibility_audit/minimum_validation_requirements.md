# Yêu cầu xác nhận tối thiểu (task 7)

**Trạng thái: KHÔNG ĐỊNH NGHĨA giao thức xác nhận cho A.** Theo quy tắc của nhiệm vụ ("Do not recommend experiments unless the proposed method is distinguishable from strong baselines"), không có thí nghiệm nào được đề xuất, vì `prior_art_overlap.md` §5 không tìm thấy thay đổi kỹ thuật nào phân biệt được.

Phần dưới đây chỉ ghi lại **điều kiện tối thiểu mà một đề xuất *khác* phải thỏa** trước khi một cổng tương tự được mở lại. Đây là tiêu chí lọc, không phải kế hoạch chạy.

## 1. Điều kiện tiên quyết (phải đạt cả bốn trước khi viết giao thức)
| # | Điều kiện | Trạng thái hiện tại |
|---|---|---|
| P1 | Có một thay đổi kỹ thuật **không** tương đương thứ hạng với top-K/quantile ở cùng ngân sách cảnh báo, và **không** quy về một quy tắc chọn K hay chọn ngưỡng thuộc họ C2/C-R1/O2 | KHÔNG ĐẠT |
| P2 | Giả định nhận dạng của bộ ước lượng (nếu có) được kiểm tra trên nguồn, và **giải thích được** các transition `new_class`, thay vì chỉ đúng ở `prior_shift_only` | KHÔNG ĐẠT (10/12 là `new_class`) |
| P3 | Truy cập được vector điểm theo flow, nhãn, `eval_X`, mẫu ngưỡng và tệp ngưỡng gốc, kèm hash khớp `08` | MISSING trong Cloud |
| P4 | Có một nguồn held-out sạch, được chọn và khóa **trước** khi xem bất kỳ kết quả nào | KHÔNG CÓ (NONE VERIFIED) |

## 2. Nếu P1–P4 đạt, giao thức tối thiểu phải có (khung, không chạy)
- **Baseline mạnh nhất phải thắng:** top-K/quantile thuần (B4). Ngoài ra còn ngưỡng lịch sử, ACI, BBSE/EM kèm hiệu chỉnh posterior (D3-C2), và BBSE importance-weighted retrain. Không được so chỉ với ngưỡng lịch sử.
- **Đối chứng LightGBM công bằng:** S1-ctx trên cùng context, cùng lô và cùng quy trình hậu xử lý. Với mọi biến thể đổi context, cần thêm S1-ctx huấn luyện lại với trọng số lớp tương ứng. Phần "đặc thù TabFM" là phần vượt S1-ctx đã nhận cùng xử lý.
- **Endpoint:**
  - Track K: recall@K ở K khóa trước, cùng quy tắc tie-break.
  - Track α: recall ở FAR danh nghĩa α đặt bằng dữ liệu nguồn, kèm FAR thực đo.
  - Báo đồng thời số unit có recall = 0, coverage và số alert.
  - Không chỉnh để khớp FAR thực đo bằng nhãn đích.
- **Ablation:**
  - Bỏ ước lượng prevalence, thay bằng prevalence oracle (cận trên) và bằng prevalence nguồn.
  - Biến đổi điểm có và không.
  - Cân lại context có và không, với cùng số hàng.
  - Tách transition `prior_shift_only` khỏi `new_class`.
- **Đơn vị suy luận:** transition. Seed là đo lặp, không gộp flow, không coi seed là mẫu độc lập.
- **Tiêu chí bác bỏ (khóa trước):** bị bác nếu lợi ích so với top-K thuần ở cùng K bằng 0 ngoài các trường hợp hòa điểm; hoặc nếu lợi ích biến mất khi áp cùng quy trình cho LightGBM; hoặc nếu lợi ích chỉ xuất hiện ở transition `prior_shift_only`; hoặc nếu số transition dương ≤ một nửa.
- **Xác nhận độc lập:** chỉ trên nguồn ở P4. NF-ToN, NF-CIC và NF-UNSW chỉ dùng phát triển.

## 3. Không được làm trong lúc chờ
Không replay, không tìm ngưỡng, không hiệu chỉnh trên 1.080 vector đã lưu với mục đích "thử xem có gain không". Việc này sẽ làm phơi nhiễm thêm dữ liệu phát triển và trùng với O2/C2 đã bác.
