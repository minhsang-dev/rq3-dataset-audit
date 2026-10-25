# Yêu cầu dữ liệu và tính toán

## Dữ liệu
| Yêu cầu | Development (có) | Xác nhận (cần) |
|---|---|---|
| Luồng theo thời gian nhiều ngày | ToN-IoT 7 ngày; CIC 10 ngày dùng được (Study 1) | ≥ 5 ngày liên tiếp có tấn công |
| Nhãn nhị phân + nhãn họ cho mọi dòng | Có (cột `Attack`, đối soát Gate M) | Bắt buộc |
| Họ khối lượng thấp tái xuất hiện ở ngày không liền kề | **Chưa kiểm** (G0) | Bắt buộc, kiểm bằng metadata trước |
| Timestamp có nhãn xác nhận | Không có → oracle | Không có ở mọi nguồn đã biết → claim chỉ offline/oracle |
| Chưa từng dùng trong luận án | Không (đã phơi nhiễm) | Bắt buộc |

**Nguồn xác nhận sạch: không có nguồn nào được xác minh.** Các lựa chọn khả dĩ (chưa phê duyệt, chưa tải):
1. **CIDDS-001**
   - Đặc điểm: 4 tuần; tuần 1–2 có 92 tấn công thuộc 4 loại (pingScan, portScan, bruteForce, dos), tức có tái xuất hiện; NetFlow một chiều; ZIP 384 MB.
   - Phải kiểm: lịch sử sử dụng trong luận án (Gate E2: UNKNOWN), license không rõ, ít họ.
2. **CTU-SME-11**
   - Đặc điểm: 7 ngày; malware tồn tại nhiều ngày trên cùng thiết bị; mất cân bằng cực đoan.
   - Phải kiểm: taxonomy nhãn chưa giải quyết (6 câu hỏi tác giả chưa gửi/chưa trả lời); đặc trưng Zeek, cần trích lại; dữ liệu đầy đủ 96,6 GB.
3. **Một bộ NetFlow v3 khác chưa dùng.** NF-BoT-IoT-v3 không có trong phạm vi quét (Gate M); cần kiểm metadata và tính sạch.

Mọi đặt mức theo ngày (ví dụ alert/ngày) đều không được phép khi chỉ có mẫu.

## Tính toán (ước lượng dựa trên số đã lưu, không phải đo mới)
- **TabICL-v2:** Study 1 S2-ICL trung vị **24,9 s** mỗi lần cập nhật, GPU L4, context 10.000 hàng, eval 10.000 hàng (file 07).
- **Pilot dev:** 12 transition × 5 seed × ~10 arm TFM ≈ **600 lần suy luận ≈ 4–5 GPU-giờ L4**. Thêm CPU cho mô phỏng bộ nhớ: tìm cặp gần nhất trong khóa, cùng bậc với CURE.
- **LightGBM theo context:** 600 lần huấn luyện CPU, mỗi lần dưới 1 s (S1-ctx 0,7 s).
- **Xác nhận** (một nguồn, ~10 transition × 15 seed × ~6 arm): ~900 lần × 25 s ≈ 6–7 GPU-giờ. TabFM tùy chọn: 338,7 s/lần → ≈ 85 GPU-giờ cho cùng lưới, nên chỉ chạy cho CURE và FF-CURE nếu cần.
- **RAM:**
  - GPU 24 GB (L4) đủ cho TabICL với context 10.000 (đã chạy trong Study 1).
  - Host ≥ 32 GB khuyến nghị. Google TabFM nạp đạt peak ≈ 13,4 GB (RQ1); không chạy TabFM trong container ≤ 4 GiB.
  - Không dùng số đo RQ1 (CPU full-context) làm hồ sơ triển khai.
- **Lưu trữ:** dev đã có cục bộ. Vector điểm ≈ 4 byte × 10.000 × số run (~24 MB cho 600 run). CIDDS-001 ≈ 0,4 GB nén; CTU-SME-11 đầy đủ ≈ 97 GB (chỉ khi được duyệt).
- **Mã:** dùng mã CURE công bố làm baseline (phải ghim commit/hash). FF-CURE chỉ đổi hàm eviction; phải có unit test tay cho witness trong `baseline_and_prior_art.md`.
