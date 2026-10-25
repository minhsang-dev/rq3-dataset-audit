# Chẩn đoán phân phối điểm

**INSUFFICIENT_EVIDENCE cho phân phối điểm.** Vector điểm theo flow không có trong không gian làm việc, nên không có histogram, quantile hay biên độ điểm theo lớp.

Chỉ có thể mô tả **gián tiếp** từ metric đã lưu (không tính lại, không fitting):
- **Ngưỡng lịch sử của TabFM S2 thường bằng đúng 0,5.** Quy tắc prereg (max macro-F1 trên mẫu ngưỡng, khi hòa chọn gần 0,5) cho kết quả 0,5 ở nhiều unit. 41/180 cặp có **cả hai** ngưỡng bằng 0,5. Ngưỡng LightGBM phân tán rộng hơn (0,01–0,98).
- **CIC 2018-02-16→02-20:**
  - TabFM có AUC trung vị 0,998, AP 0,972, nhưng recall@τ = 0 ở 15/15 seed. Điểm của các flow tấn công ở ngày đích **nằm dưới ngưỡng lịch sử** dù được xếp hạng gần hoàn hảo. Đây là thất bại tại điểm quyết định, không phải thất bại xếp hạng.
  - LightGBM ở cùng transition có AUC 0,372 (thất bại xếp hạng ở 13/15 seed) **và** recall = 0.
  - Không xác định được vị trí phân phối điểm nếu thiếu vector.
- **CIC 2018-03-01→03-02:** TabFM có AUC trung vị 0,479 (ranking failure 12/15) với recall gần 0 (0,003); LightGBM AUC 0,564, ranking failure 4/15, nhưng threshold failure 13/15.

Các mô tả này không suy ra nguyên nhân (ví dụ do ICL) và không thay cho phân tích phân phối.
