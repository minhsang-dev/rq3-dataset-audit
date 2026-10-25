# Kiểm toán phơi nhiễm trước với CIC-IDS2017 / CIC-IDS2017 Improved

**Phạm vi:**
- Repo (`git ls-files`, 91+ tệp) và gói handoff, tìm bằng `grep` các chuỗi "2017", "IDS2017", "CICIDS2017", "distrinet", "Engelen".
- Đề cương v9.16 đã được tải lên trong phiên này.

| Nguồn | Nội dung | Loại |
|---|---|---|
| Đề cương v9.16, Bảng 2.1 | "CIC-IDS2017 … 2.830.743 bản ghi luồng … Bộ dự phòng; tránh dùng cho trôi dạt thời gian vì lịch tấn công theo ngày" | **Chỉ nhắc đến** (quyết định vai trò); không có metric |
| Đề cương v9.16, tham khảo [1] | Liu et al. 2022 (CNS), chính là bài phát hành bản Improved | Chỉ nhắc đến |
| Repo: `hybrid_routing_spec/prior_art_matrix.csv` | Chuỗi "2017" chỉ xuất hiện trong năm của các bài tham khảo | Không liên quan |
| Repo: `gate_e2_outputs/dataset_decision_matrix.csv` | Không nhắc đến CIC-IDS2017 | — |
| Gói handoff 01–15 | Không có kết quả, ngưỡng hay metric nào trên CIC-IDS2017 | Không thấy phơi nhiễm |
| **Hồ sơ DA-FAR E1–E1.3** | Chỉ có trích dẫn hash trong `04_thesis_gap_and_inventory_EXTRACT.md` dòng 173–177: `D:\Personal\PhD\IUH\dafar\explore_e13\{decision_record_20261021_dafar_e1_close.md, e13_decision.md, e13_report.md, e13_analysis.json, e13_checks.json}`. **Không biết E1–E1.3 dùng dataset nào** | **MISSING** |
| Paper-1 / paper1_rerun (CSE-CIC-IDS2018, UNSW-NB15) | Theo đề cương, Bài báo 1 dùng CSE-CIC-IDS2018 | Không phải CIC-IDS2017, nhưng **cùng nhóm tác giả, cùng công cụ CICFlowMeter, cùng kiểu kịch bản** |

## Kết luận
- **Trong phạm vi đọc được:** chỉ có nhắc đến, không thấy phơi nhiễm kết quả (không metric, không ngưỡng, không quyết định dựa trên kết quả).
- **Chưa xác lập được** trạng thái phơi nhiễm, vì các tệp E1–E1.3 và mã DA-FAR chỉ nằm trên máy người nghiên cứu. Cần người nghiên cứu xác nhận bằng văn bản rằng E1–E1.3, paper-1 và mọi thử nghiệm khác **chưa** chạy trên CIC-IDS2017, dù là bản gốc hay Improved. Bản Improved được tái tạo từ cùng PCAP với bản gốc, nên phơi nhiễm với bản gốc **cũng tính là phơi nhiễm một phần** đối với cùng lưu lượng.
