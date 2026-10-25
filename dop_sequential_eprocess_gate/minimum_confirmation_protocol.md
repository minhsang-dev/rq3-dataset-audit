# Giao thức xác nhận tối thiểu

**KHÔNG ĐỀ XUẤT.** Task 6 chỉ cho phép đề xuất một cài đặt tối thiểu nếu còn khác biệt kỹ thuật nhận diện được. `technical_delta.md` §1 kết luận không còn khác biệt nào, nên không có cài đặt, mô phỏng hay giao thức nào được đề xuất.

## Dữ liệu xác nhận độc lập (task 7)
- **Không lấy được** dữ liệu xác nhận độc lập mà không làm nhiễm các tập xác nhận đã khóa.
- DoP đã có xác nhận nguồn mô phỏng được đăng ký trước, trên TON_IoT/NetFlow, theo `dop_confirm_report.md` (MISSING với Cloud). Dùng lại các dữ liệu này cho một phiên bản tuần tự sẽ tái phơi nhiễm tập xác nhận đã khóa.
- Gate cấm chọn hay tải dataset mới. Các ứng viên đã kiểm toán đều không đạt hoặc đã phơi nhiễm:
  - CTU-SME-11: chưa phù hợp.
  - NF-UNSW-NB15-v3: đã phơi nhiễm.
  - CIC-IDS2017 Improved: RQ4 gate NO-GO; phơi nhiễm chưa xác lập.
- Không có nguồn gán nhãn **thật** với phân công ngẫu nhiên. Mọi "xác nhận" vẫn chỉ dựa trên nguồn mô phỏng.
