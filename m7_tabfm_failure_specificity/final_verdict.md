# M7 — **SHARED_OR_MIXED_FAILURE**

**Ngày:** 2026-10-25. Chẩn đoán read-only trên metric đã lưu (file 14, khóa). Không inference, training, GPU, fitting ngưỡng hay dataset mới. Không sửa artifact LOCKED; D3-1E giữ PARTIAL và historical BLOCK.

## Kết quả (S2 TabFM vs S1-ctx LightGBM, cùng context, 12 transition × 15 seed)
- **Threshold failure:** cả hai 20, chỉ TabFM **1**, chỉ LightGBM **9**. Tập trung ở 2 transition CIC (28/30 cặp).
- **Ranking failure:** cả hai 14, chỉ TabFM 12, chỉ LightGBM 16. Ở 4/12 transition, và arm thất bại đổi chiều theo transition.
- **Kiểu thất bại khác nhau trong cùng transition:** ở CIC 02-16→02-20, TabFM xếp hạng gần hoàn hảo (AUC ≈ 0,998) nhưng recall@τ = 0 (thất bại tại điểm quyết định). LightGBM thì thất bại cả xếp hạng (AUC ≈ 0,37) lẫn ngưỡng.
- H1 witness: TabFM 18, LightGBM 12.
- **Không có mẫu thất bại đặc trưng cho TabFM:** threshold failure riêng TabFM là 1/180; ranking failure riêng TabFM tập trung ở một transition (CIC 03-01→03-02).

## Giới hạn
- Không có vector điểm → không có phân tích phân phối điểm. Row binding dựa trên hồ sơ đã khóa, Cloud không kiểm lại.
- Seed và flow không độc lập. Hai dataset development đã phơi nhiễm; không phải xác nhận độc lập.
- Định nghĩa failure dùng tham chiếu R1/R2 đã khóa; không có cutoff mới. Không suy nguyên nhân (không quy cho cơ chế ICL).
- **M7 không được dùng làm bằng chứng cải tiến phương pháp.**

## Một bước tiếp theo
Người nghiên cứu ghi kết quả M7 (SHARED_OR_MIXED_FAILURE) vào chương chẩn đoán RQ3, cạnh H1/H2, với đúng các giới hạn trên.
