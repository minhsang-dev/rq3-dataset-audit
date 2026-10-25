# Thesis method-improvement recovery gate — **NO_GO**

**Ngày:** 2026-10-25. Chỉ phân tích tài liệu và văn liệu; không code, inference, huấn luyện, GPU, dữ liệu mới, replay hay fitting ngưỡng. Mọi quyết định đã khóa giữ nguyên.

## Kết luận
Bằng chứng hiện có của luận án **không** chứa cải tiến phương pháp/quy trình khả thi nào vừa khác biệt kỹ thuật với baseline mạnh, vừa gắn với hạn chế quan sát, vừa kiểm được công bằng.
- **O1 (mixed-history context S3):** thuộc họ DualFIFO (KDD '26, DOI 10.1145/3770854.3780305). Đây là áp dụng một phương pháp đã có, không phải cải tiến. Bằng chứng cũng yếu ở cấp transition (7/5; MIXED 12/12).
- **O2 (quy tắc ngưỡng):** chỉ là tham số, tương đương các hướng C1/C2/C-R1 đã đóng, và cần fitting ngưỡng.
- Không phát minh cơ chế mới để thỏa yêu cầu.

## Giá trị còn lại (không phải phương pháp)
- O1 là kết quả thực nghiệm đăng ký trước: mixed context > recent-only trên NIDS theo ngày, nhất quán với ablation của DualFIFO.
- H1/H2 là đóng góp chẩn đoán mô tả.

## Hệ quả
Với yêu cầu đã xác nhận ("bắt buộc ít nhất một cải tiến chứng minh được"), hồ sơ hiện tại **không đáp ứng**. Đáp ứng cần một nghiên cứu phương pháp mới ngoài bằng chứng hiện có. Gate này không thiết kế nghiên cứu đó.

## Một bước tiếp theo
Người nghiên cứu quyết định có cấp phép một **nghiên cứu phương pháp mới, độc lập** (ngoài bằng chứng hiện có, với dữ liệu xác nhận sạch) hay không. Mọi đề xuất cơ chế chỉ được soạn sau quyết định đó.
