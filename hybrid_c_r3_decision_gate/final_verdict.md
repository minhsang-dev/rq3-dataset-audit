# C-R3 decision gate — **NO-GO**

**Ngày:** 2026-10-25. Chỉ phân tích trên giấy; không code, inference, replay, huấn luyện, GPU, fitting ngưỡng, thu dữ liệu, hay sửa artifact đã khóa.

## Cải thiện chính xác được xét
Ghép thu nhãn analyst vào routing: nhãn (ngân sách b_l, trễ d) lấy từ các flow đã escalate R (cộng phần ε ngẫu nhiên), được chèn vào context TFM ở cloud. LightGBM edge được huấn luyện lại trên cùng nhãn.

## Đối chứng mạnh nhất (B\*)
Gồm: router H2T2/rank-budget với cùng b_c → TFM cloud → recency context refresh bằng cùng b_l nhãn. Nhãn chọn theo quy tắc tốt nhất khả thi trong ngân sách call: ngẫu nhiên, Tab-AICL dùng điểm edge trên toàn batch, hoặc Tab-AICL dùng điểm cloud trên R. Cùng d, ε và LightGBM retrain. Pipeline gốc gần nhất là HINT; thành phần routing mạnh nhất là H2T2.

## Lý do NO-GO
1. **Không phân biệt được với baseline mạnh:**
   - Router, acquisition và update của C-R3 đều TBD.
   - Phần cố định duy nhất (pool = R) chỉ là tham số của Tab-AICL.
   - Mọi cấu hình của C-R3 là một cấu hình của B\*; không có witness quyết định khác ở cùng cấu hình.
2. **Không kiểm được công bằng:**
   - Phép so sánh đặc trưng (A1) cần điểm TFM với context mới: REQUIRES_NEW_INFERENCE.
   - Nhãn chỉ là ORACLE-ONLY, không có timestamp xác nhận.
   - Study 1 không có chuỗi batch theo thời gian và không có nguồn held-out sạch.
3. **Không sinh thêm ứng viên** (theo yêu cầu). Thêm một cơ chế mới, ví dụ hiệu chỉnh thiên lệch chọn mẫu bằng trọng số, sẽ là ứng viên khác, không phải C-R3.

Điều này **không** phủ nhận giá trị của A1 như một câu hỏi thực nghiệm mô tả. Nhưng A1 không phải cải thiện phương pháp/workflow phân biệt được, và hiện cũng không kiểm được.

## Hệ quả
- Không còn ứng viên hybrid nào sống sót (C-R1, C-R2, C-R3 đều NO-GO làm cải thiện).
- Các quyết định đã khóa (D3-1E PARTIAL/BLOCK, H1/H2, C1 NO-GO, PATH B) giữ nguyên.

## Một bước tiếp theo
Người nghiên cứu đưa kết quả này (cả ba ứng viên hybrid NO-GO) vào trao đổi bằng văn bản với supervisor. Mục tiêu là xác định chiến lược đóng góp theo PATH B/C, trước khi cho phép bất kỳ đặc tả phương pháp mới nào.
