# Kế hoạch baseline, ablation và endpoint

**Chỉ là kế hoạch, KHÔNG phải lệnh chạy.** Không chạy gì cho đến khi giao thức và script phân tích được khóa ở máy cục bộ.

## 1. Thiết lập dữ liệu (khóa trước)
- **Pool:** lưu lượng NetFlow của một ngày nguồn, lấy mẫu con có kiểm soát để có **prevalence π ∈ {0,1%; 0,5%; 1%}**.
  - Việc lấy mẫu con tấn công dùng nhãn **chỉ để dựng thí nghiệm**, không đưa vào selector. Cách này tạo ra mất cân bằng nhân tạo và phải ghi rõ như vậy.
- **Test:** tập tách biệt, theo thời gian hoặc ngẫu nhiên tùy mục tiêu. Không dùng khi chọn mẫu.
- **Phơi nhiễm dữ liệu:** NF-ToN/NF-CIC là dữ liệu phát triển đã phơi nhiễm. Mọi kết quả trên chúng chỉ là phát triển. Xác nhận cần nguồn khác, hiện chưa có (xem `RESEARCH_DECISION_INDEX.md`).
- **Đơn vị phân tích:** (dataset, ngày, π). Seed là đo lặp, không phải mẫu độc lập.

## 2. Ma trận so sánh (cùng B ∈ {100, 300}, cùng pool, cùng mô hình hạ nguồn)
| # | Selector | Không gian | Vai trò |
|---|---|---|---|
| 1 | Random | — | Mốc |
| 2 | K-Medoids | Gốc (tiền xử lý cố định) | LUCoS-ablation (biểu diễn) |
| 3 | **LUCoS** | PIN | Baseline chính |
| 4 | TypiClust | PIN | Cold-start mạnh |
| 5 | ProbCover | PIN | Cold-start mạnh |
| 6 | k-Center greedy | PIN | Phủ không theo khối lượng |
| 7 | HaCon-style: phân tầng cụm + typical | PIN | Gần nhất về "phủ thiểu số" |
| 8 | Anomaly top-B (Isolation Forest) | Gốc | "Hiếm ≠ tấn công" |
| 9 | LUCoS + anomaly quota (ρ) | PIN | **Ablation quyết định:** thay tiêu chí chặt bằng tiêu chí bất thường |
| 10 | LUCoS + random quota (ρ) | PIN | Ablation: hạn ngạch không có tiêu chí |
| 11 | CQ-only (B_c = 0) | PIN | Ablation: chỉ hạn ngạch |
| 12 | **LUCoS-CQ (ρ ∈ lưới khóa trước)** | PIN | Đề xuất |
| 13 | LUCoS-CQ trên không gian gốc | Gốc | Ablation biểu diễn |
| R | Random có phân tầng theo nhãn thật | — | **Chỉ là tham chiếu oracle**, không phải đối thủ |

**Mô hình hạ nguồn:** một TabFM cố định (TabICL 2.2.0 hoặc TabPFN; khóa trước), cùng tiền xử lý. Có thể thêm LightGBM huấn luyện trên cùng B nhãn làm đối chứng phụ.

## 3. Endpoint
- **Chính 1 — hit rate:** P(context chứa ≥ 1 tấn công), cùng số họ tấn công khác nhau được phủ. Đo sau chọn mẫu, dùng nhãn **chỉ để chấm**.
- **Chính 2 — hạ nguồn:** AUPRC của TabFM trên test.
  - Báo riêng các lần context có 0 tấn công, không gộp và không gán 0.
- **Phụ:**
  - recall ở FAR danh nghĩa cố định;
  - tỉ lệ hạn ngạch trúng tấn công;
  - tỉ lệ ngân sách rơi vào benign hiếm;
  - thời gian chọn mẫu và bộ nhớ.
- **Tiêu chí bác bỏ (khóa trước):** LUCoS-CQ bị bác nếu:
  - không hơn LUCoS về hit rate ở đa số đơn vị (dataset, ngày, π); hoặc
  - không hơn #9 (anomaly quota) và #6 (k-Center); hoặc
  - lợi ích hit rate không chuyển thành AUPRC; hoặc
  - lợi ích chỉ có ở một ρ được chọn sau khi xem kết quả.
- **Thống kê:** mô tả theo đơn vị cùng khoảng bootstrap theo đơn vị. Không coi seed hay flow là độc lập.

## 4. Ngân sách tính toán (ước lượng, chưa đo)
- **Chọn mẫu:** chạy trên CPU. K-Medoids `alternate` trên N ≈ 10⁴ có thể tốn thời gian và bộ nhớ (ma trận khoảng cách N² ≈ 10⁸ phần tử, tức khoảng 0,8 GB ở float64). Có thể cần giới hạn N; quy tắc giới hạn phải khóa trước.
- **Hạ nguồn:** TabFM với context B ≤ 300 rất nhẹ.
