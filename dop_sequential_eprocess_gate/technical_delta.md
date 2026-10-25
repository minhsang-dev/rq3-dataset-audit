# Khác biệt kỹ thuật (task 3, 5)

## 1. Ánh xạ từng thành phần
| Thành phần của Sequential DoP | Tương đương đã công bố | Còn khác biệt? |
|---|---|---|
| P-value hạng ngang hàng `p_{d,k}` | P-value permutation/conformal (rank) | Không (đã có trong DoP gốc, đã kiểm toán) |
| Hiệu chỉnh `K/(K − m_max)` | Thuộc DoP gốc; đã nằm trong comparator tương đương | Không mới ở tầng tuần tự |
| p → e theo ngày | Calibrator của Vovk & Wang (2021); Shafer et al. (2011) | Không |
| Nhân e-value qua ngày; dừng khi ≥ 1/α | Test martingale + bất đẳng thức Ville | Không |
| Cược thích nghi | E-process kiểu betting (Ramdas et al. 2023; Duan et al. 2022) | Không |
| Phản hồi trễ hoặc không đều | Optional continuation của e-process: bằng chứng cập nhật khi nhãn đến, vẫn hợp lệ nếu p-value hợp lệ có điều kiện trên filtration | Không. Đây là hệ quả tiêu chuẩn |
| Nhiều nguồn | e-BH / e-LOND | Không |
| "Chưa xác định" | Kiểm định hai mẫu tuần tự bằng betting (Shekhar & Ramdas 2023) | Không |
| Áp martingale lên chuỗi p-value hạng để cảnh báo | Conformal test martingale (Vovk et al., COPA 2021) | Không |

**Kết luận:** không còn thành phần nào khác về kỹ thuật so với quy trình tuần tự đã có. Ghép các mô-đun này lại là một ứng dụng chuẩn, không phải cải tiến.

## 2. Các điểm bất lợi về cấu trúc (lập luận, không mô phỏng)
- **P-value rời rạc.** Với K nguồn, `p̃ ≥ 1/(K − m_max)`. Ví dụ K = 10 và m_max = 2 cho `p̃ ≥ 0,125`. Calibrator `κp^{κ−1}` với κ = 0,5 cho e tối đa khoảng 1,41 mỗi ngày, nên cần ít nhất khoảng 9 ngày bằng chứng cực đại liên tiếp mới đạt 1/α = 20 ở α = 0,05. Ở cùng số ngày, tầng tuần tự nói chung **kém năng lực hơn** phép gộp cố định cùng horizon: đó là cái giá của anytime-validity, không phải lợi ích.
- **Phản hồi chọn lọc.** Nếu nhãn chỉ về cho một số dòng và việc chọn dòng phụ thuộc dữ liệu hay quyết định mô hình, tính đối xứng giữa các nguồn trong ngày (điều kiện Mức 1 của DoP) có thể bị phá vỡ. Khi đó `p_{d,k}` không còn hợp lệ, và **không** e-process nào sửa được. Trường hợp này phải chuyển sang trạng thái "chưa xác định". Câu hỏi "cải thiện dưới phản hồi chọn lọc" vì vậy không có lời giải từ tầng tuần tự.
- **Phân công cố định theo chuyên môn.** Đề cương đã ghi bảo đảm không áp dụng; tầng tuần tự không thay đổi điều này.

## 3. Đo công bằng được không (task 5)
| Chỉ số | Có đo công bằng được không |
|---|---|
| Xác suất gắn cờ oan | Chỉ dưới giả định phân công ngẫu nhiên lại mỗi ngày và trên **nguồn mô phỏng**. Không có nguồn thật |
| Độ trễ phát hiện | Chỉ trong mô phỏng với lịch phản hồi khóa trước. Phụ thuộc mạnh vào K, m_max và cường độ đầu độc; không có ý nghĩa vận hành |
| Tỉ lệ "chưa xác định" | Đo được, nhưng chỉ khi kiểm định hai mẫu cũng ở dạng tuần tự. Nếu kiểm định này chạy cố định trong khi kiểm định chính chạy tuần tự thì bảo đảm bị phá vỡ |

Gate này cấm mô phỏng, nên không có số đo nào được tạo ra. Kể cả khi đo, mọi khác biệt so với sequential peer-score có cùng `F` sẽ bằng 0 hoặc quy về hàm điểm (xem `strong_baseline_comparison.md` §4).
