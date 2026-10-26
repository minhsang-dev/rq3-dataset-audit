# Đặc tả ứng viên: LUCoS-CQ (LUCoS với hạn ngạch cụm chặt)

**Phân loại:** đề xuất sửa đổi. Chưa cài đặt, chưa kiểm chứng.

## 1. Ý tưởng
LUCoS phân bổ medoid theo khối lượng. Trong NIDS, nhiều họ tấn công tự động (DoS, scan, brute force) sinh ra các flow gần giống nhau. Vì vậy cụm tấn công thường **nhỏ về khối lượng nhưng rất chặt**. Đó chính là kiểu "lớp hiếm chặt" mà He & Carbonell (2007) nhắm tới.

LUCoS-CQ dành một phần cố định ρ của ngân sách cho **đại diện của các cụm chặt nhỏ**, phát hiện bằng chênh lệch mật độ cục bộ trong cùng không gian PIN. Phần còn lại giữ nguyên LUCoS.

**Lưu ý:** cụm chặt **không** đồng nghĩa với tấn công. Benign cũng có cụm chặt (heartbeat, NTP, DNS lặp). Phương pháp chỉ tăng xác suất có mặt của các mode nhỏ, không nhận diện được tấn công.

## 2. Pseudocode
Mọi tham số được khóa trước khi có bất kỳ nhãn nào.
```
INPUT  X_pool (N×d, chưa nhãn, chỉ ngày/tập nguồn), B ∈ {100,300}, ρ (khóa trước), k=20,
       q=0.99, s_min=5, s_max=ceil(0.01·N)   # 0.01 là cận trên thiết kế, KHÔNG phải prevalence thật
       seed, preprocess() cố định (log1p cho đặc trưng đếm/byte, chuẩn hóa robust; khóa trước)
OUTPUT tập chỉ số S, |S| = B

1  Z ← PIN_Encoder(preprocess(X_pool))            # đóng băng, như LUCoS
2  B_c ← B − floor(ρ·B);  B_q ← B − B_c
3  M  ← KMedoids(Z, B_c, init='k-medoids++', method='alternate', max_iter=300, seed)   # = LUCoS
4  # điểm chặt (label-free, một vòng)
   for i in 1..N:  r_i ← khoảng cách tới láng giềng thứ k;  dens_i ← 1/r_i
   for i in 1..N:  Δ_i ← max_{j ∈ kNN(i)} (dens_i − dens_j)      # chênh lệch mật độ cục bộ (He–Carbonell)
5  T ← {i : Δ_i ≥ quantile_q(Δ)}
6  G ← các thành phần liên thông của đồ thị kNN đối xứng thu hẹp trên T
   G ← {g ∈ G : s_min ≤ |g| ≤ s_max}                              # bỏ nhiễu đơn lẻ và mode lớn
7  sắp G giảm dần theo median(Δ_g); d_sep ← median khoảng cách láng giềng gần nhất giữa các phần tử của M
8  Q ← ∅
   for g in G:
       m_g ← medoid(Z_g)
       if min_{m ∈ M ∪ Q} ||z_{m_g} − z_m|| > d_sep:  Q ← Q ∪ {m_g}
       if |Q| = B_q: break
9  if |Q| < B_q:  bổ sung bằng medoid kế tiếp của KMedoids(Z, B) không thuộc M ∪ Q   # giữ đúng B
10 S ← M ∪ Q;  ghi lại nguồn gốc (core/quota) của từng chỉ số
```
**Ràng buộc:**
- Không dùng nhãn, không dùng tập test.
- `s_max` dựa trên cận thiết kế, không dựa trên prevalence quan sát.
- `ρ` chọn trong lưới khóa trước, ví dụ {0; 0,1; 0,2; 0,3}. Báo cáo cả lưới, không chọn ρ theo kết quả test.

## 3. Vì sao khác phương pháp mạnh nhất
| So với | Khác ở đâu |
|---|---|
| LUCoS | Thêm hạn ngạch **không phụ thuộc khối lượng** cho mode nhỏ và chặt. LUCoS chỉ phụ thuộc khoảng cách có trọng số khối lượng |
| TypiClust / ProbCover | Hai phương pháp này ưu tiên vùng đông và cụm lớn. CQ cố ý lấy mode nhỏ |
| k-Center / anomaly top-B | Hai phương pháp này lấy điểm **thưa** (outlier). CQ lấy **điểm dày đặc nằm trong cụm nhỏ** (mật độ cao, khác hẳn láng giềng), nên cụm DoS gần trùng lặp được tính |
| He & Carbonell NNDB | NNDB lặp, dùng phản hồi nhãn và ước lượng tỉ lệ lớp hiếm. CQ chạy một vòng, không nhãn, trong không gian PFN, và chia ngân sách với phần phủ |
| HaCon | HaCon phân bổ đều theo cụm K-means toàn cục. CQ chỉ dành hạn ngạch cho cụm chặt nhỏ được phát hiện cục bộ |

**Mức tính mới:** gia tăng. Các thành phần đều đã có (xem `prior_art_comparison.md` §3). Giá trị chỉ có thể được khẳng định nếu thắng các baseline ở cùng B, đặc biệt là k-Center, HaCon-style và "LUCoS + anomaly quota", với cùng ρ.

## 4. Điều kiện thất bại
1. **Tấn công nằm trong đuôi benign** (brute force chậm, infiltration, flow giống benign). Không có tín hiệu mật độ hay phủ nào tách được; mọi phương pháp label-free đều thất bại. Phải báo theo họ tấn công.
2. **Benign có nhiều cụm chặt nhỏ** (dịch vụ hiếm, lưu lượng định kỳ). Hạn ngạch bị tiêu cho benign. Đo bằng "tỉ lệ hạn ngạch trúng tấn công".
3. **Tấn công không hiếm trong pool.** Nếu π > s_max/N (ví dụ ngày DoS), cụm tấn công bị loại khỏi G. Phần lõi LUCoS vẫn phủ, nhưng CQ không giúp.
4. **Không gian PIN làm sụp cấu trúc đuôi nặng.** Đặc trưng byte/gói có đuôi nặng; nếu tiền xử lý không phù hợp, mật độ trong không gian latent không phản ánh cụm tấn công. Phải kiểm cục bộ.
5. **Giới hạn đầu vào của TabClustPFN** (số hàng, số cột, bộ nhớ) chưa biết. Có thể phải lấy mẫu con pool, làm giảm xác suất chứa cụm hiếm.
6. **Context có 0 tấn công.** TabFM không học được lớp tấn công. Mọi chỉ số hạ nguồn suy biến; phải báo riêng, không gộp.
