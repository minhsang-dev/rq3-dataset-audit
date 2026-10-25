# Tính mới phương pháp và baseline (task 5–6)

## 1. Các quyết định DA-FAR được giữ nguyên
Nguồn: gói handoff `README_FOR_CLAUDE.md` dòng 14; `04_thesis_gap_and_inventory_EXTRACT.md` dòng 30, 41, 98; `thesis_method_recovery_gate/closed_directions_registry.md`.

- **E1–E1.3** là thăm dò và **đã đóng** ngày 21/10/2026.
- **P1:** lợi ích không tách được khỏi thay đổi effective gain; khi so ở cùng số FP, không có ưu thế controller nhất quán. Kết quả: NO-GO, không phân biệt được với P0 đã khớp gain.
- **P2:** đánh đổi FAR, recall, recovery và cumulative deviation lẫn lộn; không chứng minh được ưu thế recovery.
- Không có bảo đảm FAR, không có E2 và không có xác nhận cloud.
- Kết quả âm được giữ nguyên, không diễn giải lại.

Đặc tả chi tiết của P0/P1/P2 chỉ có trên máy người nghiên cứu (`D:\…\dafar\explore_e13\…`) và **MISSING** đối với Cloud.

## 2. Baseline tuần tự mạnh phải vượt
**Chọn một baseline: Conformal PID control** (Angelopoulos, Candès & Tibshirani, NeurIPS 2023; [25] trong đề cương). Đây là bộ điều khiển ngưỡng trực tuyến gồm quantile tracking, khâu tích phân sai số và tùy chọn scorecaster. Nó mạnh hơn ACI thuần và mạnh hơn ngưỡng cố định.

Để công bằng với bài toán DA-FAR, baseline phải được dùng ở dạng **cùng thông tin phản hồi** với phương án đề xuất:
- cập nhật chỉ khi nhãn đến, sau độ trễ `d`;
- sai số FAR được ước lượng bằng IPW trên mẫu kiểm ngẫu nhiên;
- gain được tinh chỉnh với **cùng ngân sách tìm kiếm** và trên **cùng dữ liệu phát triển**.

Các ứng viên khác được nêu trong đề cương nhưng **chưa xác minh toàn văn** ở đây: ACI dưới phản hồi trễ (El Halabi & Brandt 2026, [24]); online conformal với phản hồi gián đoạn (Wang, Zecchin & Simeone 2025, [26]). Nếu một trong hai bài cho cơ chế xử lý trễ hay phản hồi chọn lọc được chứng minh, baseline phải được nâng lên bài đó.

## 3. Có cơ chế kỹ thuật mới vượt P0/P1/P2, IPW, gain tuning, clipping và cập nhật phản hồi trễ chuẩn không?
**Không.**
- Đề bài của gate này không nêu cơ chế mới nào; nó chỉ đề nghị **đổi dataset**.
- Trong hồ sơ đọc được, mọi thành phần của DA-FAR (điều chỉnh ngưỡng theo phản hồi, IPW cho mẫu kiểm, gain, clipping, cập nhật trễ) đều là thành phần chuẩn, hoặc là chính các biến thể đã NO-GO.
- Đổi sang CIC-IDS2017 Improved **không tạo ra** khác biệt kỹ thuật nào. Theo quy tắc đã giữ, dataset mới không phải lời giải cho việc thiếu tính mới phương pháp.

Hệ quả: không có giả thuyết kỹ thuật nào phân biệt được, nên không có "thành phần mới" để làm ablation.
