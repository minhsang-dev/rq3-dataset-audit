# Final method recovery gate — Sequential DoP: **NO-GO**

**Ngày:** 2026-10-25. Kiểm toán văn liệu và cấu trúc.
- Không chọn hay tải dataset; không GPU, training, replay hay mô phỏng.
- Giữ nguyên các phán quyết: DoP NO-GO Level 2/3, DA-FAR E1.3 NO-GO, M7 SHARED_OR_MIXED_FAILURE, RQ3-A NO-GO.

## Lý do (theo đúng quy tắc "tương đương thì NO-GO ngay")
1. **Không có khác biệt kỹ thuật.** Mọi thành phần của tầng tuần tự đều có bản đã công bố, đối chiếu từng mục trong `technical_delta.md` §1:
   - calibrator p→e, test martingale và Ville;
   - e-process kiểu betting và kiểm định hạng bằng betting;
   - optional continuation cho phản hồi trễ hoặc không đều;
   - e-BH và e-LOND cho nhiều nguồn;
   - kiểm định hai mẫu tuần tự cho trạng thái "chưa xác định";
   - conformal test martingale trên p-value hạng.
2. **Không hơn baseline mạnh.** Sequential peer-score có cùng p-value theo ngày, cùng tầng tuần tự và cùng cơ hội dừng sẽ cho cùng e-process và cùng quyết định. Nếu có khác biệt, khác biệt đó đến từ hàm điểm theo ngày, tức là phần đã được kiểm toán tương đương và đã NO-GO. Xem `strong_baseline_comparison.md` §4.
3. **Không giải quyết phản hồi chọn lọc.** Khi nhãn về có chọn lọc phụ thuộc dữ liệu, tính đối xứng giữa các nguồn bị phá vỡ và p-value theo ngày mất hợp lệ, nên không e-process nào sửa được. Phản hồi trễ hoặc không đều chỉ được xử lý bằng tính chất tiêu chuẩn của e-process.
4. **Bất lợi về năng lực.** P-value hạng rời rạc (`p̃ ≥ 1/(K − m_max)`) làm e-value mỗi ngày nhỏ. Ở cùng horizon, quy trình tuần tự kém năng lực hơn phép gộp cố định.
5. **Không có dữ liệu xác nhận sạch** mà không làm nhiễm các tập đã khóa; không có nguồn gán nhãn thật.

## Tệp
- `strong_baseline_comparison.md`: công thức DoP, prior art, baseline sequential peer-score.
- `technical_delta.md`: ánh xạ từng thành phần, bất lợi cấu trúc, khả năng đo.
- `minimum_confirmation_protocol.md`: không đề xuất; tình trạng dữ liệu xác nhận.

## Kết luận một câu
Sequential DoP **không thể** trở thành cải tiến bắt buộc được chứng minh của luận án. Tầng tuần tự anytime-valid trùng hoàn toàn với các e-process, test martingale và e-BH/e-LOND đã công bố, và không tạo khác biệt nào so với sequential peer-score có cùng bằng chứng và cùng cơ hội dừng.
