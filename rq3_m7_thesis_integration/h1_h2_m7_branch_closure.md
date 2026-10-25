# Biên bản đóng nhánh chẩn đoán H1/H2/M7 (RQ3)

**Ngày:** 2026-10-25.

**Phạm vi:** biên bản này chỉ là tài liệu. Nó không sửa tệp nghiên cứu gốc, phán quyết hay artifact LOCKED nào. Các trạng thái dưới đây đều chép từ nguồn đã có.

| Hạng mục | Trạng thái ghi nhận | Nguồn |
|---|---|---|
| H1 | **SUPPORTED_DESCRIPTIVELY**, đọc hẹp: xếp hạng vượt tham chiếu (AUC > 0,5; AP > π) không bảo đảm recall dương tại ngưỡng lịch sử. Phát biểu mạnh hơn ("xếp hạng và hiệu quả ngưỡng không liên hệ") vẫn **INDETERMINATE**. | `10_h1_h2_verdict.md` |
| H2 | **SUPPORTED_DESCRIPTIVELY**: recall bằng 0 ở 185/1.080 đơn vị VALID, trên 7/12 lần chuyển, có ở cả LightGBM và arm cloud; không phải phát hiện riêng của TabFM. | `10_h1_h2_verdict.md` |
| Ràng buộc lịch sử của H1/H2 | Giữ nguyên: D3-1E **PARTIAL**, historical BLOCK không đổi; 487 nhãn nhạy làm tròn; 42 dấu chưa chứng nhận; NULL, LOW_SUPPORT và INDETERMINATE giữ nguyên. | `11_d3_interpretation_limits.md` |
| M7 | **SHARED_OR_MIXED_FAILURE** | `m7_tabfm_failure_specificity/final_verdict.md` |
| Mẫu thất bại đặc thù TabFM | **Không có mẫu nhất quán đã kiểm chứng** trong 180 cặp S2 – S1-ctx đã lưu. Điều này không khẳng định TabFM không thể có thất bại đặc thù ở nơi khác. | M7 |
| Khám phá phương pháp chẩn đoán D3 dựa trên H1/H2 | **CLOSED** theo bằng chứng hiện có. | Biên bản này; nhất quán với `thesis_method_recovery_gate/` và `new_method_research_design/` (các hướng D3 C1/C2/C3 đã đóng) |
| Phân tích M7/D3 bổ sung | **Không được phép** | Biên bản này |
| Chạy GPU, suy luận, huấn luyện, khớp ngưỡng hay phát lại dự đoán cho nhánh này | **Không được phép** | Biên bản này |

## Điều kiện mở lại
Nhánh này chỉ được mở lại khi có **đủ cả ba** điều kiện:

1. Một câu hỏi khoa học **mới, nêu rõ ràng**, không phải tái diễn đạt H1/H2/M7.
2. Một cơ sở bằng chứng mới, nêu rõ nguồn, vai trò dữ liệu và mức độc lập. Ví dụ: vector điểm theo flow đã được phê duyệt truy cập, hoặc nguồn held-out sạch. Đổi seed, đổi tập con hay đổi cách chia hàng trên hai bộ dữ liệu đã phơi nhiễm **không** được tính là bằng chứng mới.
3. **Phê duyệt bằng văn bản của người nghiên cứu** trước khi làm bất kỳ phân tích nào.

## Điều biên bản này không làm
- Không đổi phán quyết của H1/H2, D3-1E, M7, FF-CURE, P1 hay các ứng viên hybrid.
- Không mở lại FF-CURE (G0_FAIL), P1 hay C-R1/C-R2/C-R3.
- Không biến M7 thành đóng góp phương pháp.
