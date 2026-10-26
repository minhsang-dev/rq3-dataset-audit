# RQ3 — LUCoS và prior art về chọn mẫu không nhãn dưới mất cân bằng cực đoan

**Ngày:** 2026-10-26.

**Mức xác minh:**
- **FT**: đọc toàn văn PDF trong phiên này.
- **ABS**: abstract hoặc trang chính thức, xác nhận qua web.
- **KN**: theo hiểu biết, chưa đọc lại trong gate này.

Không tải dataset hay checkpoint. Chỉ tải PDF bài báo.

## 1. LUCoS (FT)
**Thông tin bài:** Ipas, Gomez-Trenado, Romero-Zaliz, Triguero, "LUCoS: Latent Unsupervised Context Selection for Tabular Foundation Models", arXiv:2605.27254v1, 26/5/2026, preprint 18 trang (12 trang chính và phụ lục A–F).

**Phương pháp (§3.1, tr. 4–5):**
- Embed toàn bộ pool chưa nhãn bằng **PIN Encoder của TabClustPFN**, cho `z_i ∈ R^512` (công thức 1).
- Chạy **K-Medoids Euclid** với số medoid K bằng ngân sách nhãn (công thức 2): `I_K = argmin_{|I|=K} Σ_i min_{j∈I} ||z_i − z_j||_2`.
- Gán nhãn một lần cho các medoid, rồi dùng chúng làm context cho TabPFN-2.5 trong không gian đặc trưng gốc.
- Bốn yêu cầu (§3, tr. 4): label-free, test-blind, instance-faithful, single-round.
- Cài đặt (Phụ lục C, tr. 13): `sklearn_extra` KMedoids, khởi tạo `k-medoids++`, `method='alternate'`, `max_iter=300`; đặc trưng phân loại được one-hot.

**Đánh giá (§3.2, tr. 5):**
- 67 bộ OpenML-CC18 (≤ 10 lớp), 10 fold, 5 seed.
- Ngân sách `K = i·C`, với `i ∈ {1, 2, 4, 8, 16, 32}`.
- Chỉ số chính AUC; thêm ACC/F1. Kiểm định Friedman, Nemenyi, Wilcoxon với BH.

**Kết quả (§4.2–4.4, Bảng 1, tr. 7; Hình 3–4, tr. 8–9):**
- Hạng trung bình tốt nhất ở mọi ngân sách.
- Ở 1C–2C, hòa với K-Medoids trên không gian gốc. Tách biệt có ý nghĩa ở 4C–16C, thu hẹp lại ở 32C.
- RDSS và ZCore không thắng random.
- Khoảng cách tới oracle SSMA ở 1C là +0,185 AUC (Bảng 2, tr. 14); LUCoS thu hồi khoảng 13–20% khoảng cách này.

**Giới hạn tác giả tự nêu (§5, tr. 9–10):**
- Chỉ phân loại, ≤ 10 lớp.
- K-Medoids đắt trên pool rất lớn.
- **"It does not explicitly enforce class- or minority-region coverage … severe imbalance remains an open challenge in the cold-start regime"** (tr. 10).
- Phụ thuộc TabClustPFN làm biểu diễn.

**Giới hạn suy ra (không phải tác giả nêu):**
- Mục tiêu K-Medoids có trọng số theo khối lượng. Một cụm chiếm π < 1% pool chỉ nhận medoid nếu khoảng cách tách đủ lớn để bù khối lượng nhỏ. Với B = 100 và π = 0,5%, khả năng cụm tấn công không có medoid nào là đáng kể.
- CC18 không có bộ NIDS. Không có kết quả nào ở π < 1%.

**Mã và checkpoint (ABS, README):**
- `github.com/ari-dasci/S-LUCoS`, giấy phép MIT, 5 commit trên `main`. Hash commit **không hiển thị** qua WebFetch, nên phải ghi lại khi clone ở máy cục bộ.
- Checkpoint TabClustPFN `step-10000.ckpt` phải **tải tay từ Google Drive**. Repo gốc của TabClustPFN: `github.com/Tianqi-Zhao/TabClustPFN`.
- TabClustPFN: Zhao, Wang, Tan, Zhang, arXiv:2601.21656, 29/1/2026 (ABS).

## 2. Các quy tắc chọn mẫu khác
| Phương pháp | Quy tắc | Dưới π < 1% | Mức |
|---|---|---|---|
| Random | Đều | P(0 attack trong B) ≈ e^{−πB}. Với B = 100, π = 0,5% thì ≈ 0,61 | Toán |
| K-Medoids gốc / LUCoS | Medoid tối thiểu tổng khoảng cách | Trọng số theo khối lượng; cụm nhỏ dễ bị bỏ | FT |
| **TypiClust** (Hacohen, Dekel, Weinshall, ICML 2022) | K-means thành `\|L\|+B` cụm; chọn điểm *typical* nhất (mật độ kNN cao) trong các cụm **lớn nhất** chưa được phủ | Ưu tiên cụm lớn, tránh outlier. Cụm tấn công nhỏ bị xếp sau cùng | ABS + KN |
| **ProbCover** (Yehuda, Dekel, Hacohen, Weinshall, NeurIPS 2022) | Tham lam tối đa số điểm được phủ bởi bóng bán kính δ; δ chọn theo độ thuần cụm | Lợi ích biên tỉ lệ số điểm phủ thêm, nên cụm nhỏ được chọn muộn. Có thể đạt tới nếu benign được phủ nhanh | ABS + KN |
| k-Center greedy / Core-set (Sener & Savarese, ICLR 2018) | Cực tiểu bán kính phủ lớn nhất | **Không** theo khối lượng; chọn outlier. Có thể chạm cụm tấn công tách xa, nhưng cũng tốn ngân sách cho benign hiếm | KN |
| **HaCon / "Making Your First Choice"** (Chen et al., arXiv:2210.02442; MIDL 2023, PMLR 227) | Pseudo-label bằng phân cụm để giữ "label diversity", rồi chọn mẫu typical / hard-to-contrast | Phân bổ đều theo cụm, gần với phân tầng giả. Gần nhất với "đảm bảo phủ cụm thiểu số" khi không có nhãn | ABS |
| **Rare-category detection NNDB** (He & Carbonell, NIPS 2007) | Lấy mẫu theo **chênh lệch mật độ cục bộ**, tức các lớp thiểu số *chặt* (compact) dưới giả định benign trơn | Đúng mục tiêu tìm cụm hiếm chặt. **Lặp và dùng phản hồi nhãn**, cần ước lượng tỉ lệ lớp hiếm | ABS |
| Interleave (Pelleg & Moore, NIPS 2004) | Xen kẽ truy vấn theo từng thành phần mô hình hỗn hợp | Có nhãn, lặp | KN (qua He & Carbonell) |
| ALADIN (Stokes, Platt et al., MSR 2008) | Học chủ động kết hợp khám phá lớp hiếm cho IDS (KDD-Cup-99) | Lặp, có nhãn | ABS |
| Cold-start AL mất cân bằng (Brangbour et al., arXiv:2201.10227) | Điểm phần tử từ cấu trúc cụm cộng lan truyền nhãn | Không có bản bình duyệt; lan truyền nhãn cần nhãn | ABS |
| Lấy mẫu bất thường top-B (ví dụ Isolation Forest [10]) | Chọn điểm bất thường nhất | **Hiếm ≠ tấn công**; bỏ lỡ cụm tấn công dày đặc (DoS); tốn ngân sách cho benign hiếm | KN |
| Tab-AICL (arXiv:2603.27385) | Active ICL cho TabPFN | Dùng nhãn lặp; không phải cold-start label-free | ABS |
| Hiệu chỉnh mất cân bằng cho PFN (arXiv:2605.21742) | Sửa thiên lệch lớp đa số khi suy luận | Bổ trợ ở hạ nguồn, không phải chọn mẫu | ABS |

## 3. Kết luận chồng lấn
- **Không tìm thấy** quy tắc giống hệt, tức là "một vòng, không nhãn, K-Medoids trong không gian PFN, cộng hạn ngạch cho cụm chặt nhỏ chọn theo chênh lệch mật độ".
- Tìm kiếm có giới hạn: đọc toàn văn LUCoS; các bài khác ở mức abstract hoặc hiểu biết. "Không tìm thấy" không chứng minh không tồn tại.
- Thành phần nào cũng đã có: K-Medoids latent (LUCoS); chênh lệch mật độ cho lớp hiếm chặt (He & Carbonell); phân tầng theo cụm (HaCon).
- Phần còn lại chỉ là: (i) dạng **một vòng không nhãn** của tiêu chí He & Carbonell; (ii) đặt trong không gian PFN; (iii) chia ngân sách với LUCoS. Đây là **cải tiến gia tăng của LUCoS** nhằm đúng giới hạn tác giả đã nêu, **không phải** phương pháp mới.
