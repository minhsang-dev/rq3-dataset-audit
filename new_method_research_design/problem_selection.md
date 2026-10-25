# Chọn bài toán nghiên cứu

Phạm vi: thiết kế nghiên cứu mới; không code, không chạy mô hình. Không mở lại P1, DoP, DA-FAR, D3 C1/C2/C3, Hybrid C-R1/R2/R3, cũng không đổi tên O1 (S3 ≡ DualFIFO).

## Bài toán P-A (được chọn): quản lý bộ nhớ context của TFM làm quên các họ tấn công khối lượng thấp nhưng tái xuất hiện
| Tầng | Nội dung |
|---|---|
| **Quan sát thực nghiệm** (mô tả, không nhân quả) | (1) Study 1: context trộn lịch sử (S3) có macro-F1 trung bình cao hơn context chỉ ngày gần nhất (S2), +0,050, nhưng chỉ 7/5 transition và D3 MIXED 12/12 (file 07, 09). (2) Recall trên lớp tấn công "mới" ở ngày t+1 thấp tại nhiều transition (Study 1 §5: S2 0,00–0,44 ở 6 transition). (3) Các dataset có họ tấn công khối lượng rất lớn bên cạnh họ nhỏ (CIC 14 họ, ToN 9 họ; Gate M). CTU-SME-11 có một thiết bị chiếm 97,8% flow (repo này). (4) Prior art: bộ nhớ kép (DualFIFO) và CURE cân bằng theo **nhãn y**; với NIDS nhị phân, y chỉ là benign/attack |
| **Giả thuyết giải thích** (chưa kiểm) | Khi nhãn ICL là nhị phân, cả DualFIFO (cân bằng benign/attack) lẫn CURE (chọn lớp nhiều nhất theo y, rồi loại cặp gần nhất cùng y) đều **không nhìn thấy họ tấn công**. Ví dụ ở CURE: một dòng thuộc họ hiếm nằm gần một dòng của họ lũ (flood) trong không gian đặc trưng có thể tạo thành cặp gần nhất; nếu dòng hiếm xa centroid của lớp attack hơn (centroid này bị họ lũ chi phối), CURE sẽ loại dòng hiếm. Hệ quả là các họ hiếm có thể bị xóa khỏi bộ nhớ dài hạn trước khi chúng tái xuất hiện |
| **Cơ chế cần cải tiến** | Quy tắc **chọn nạn nhân khi loại bỏ** (eviction) trong bộ nhớ dài hạn của CURE |
| Không được suy | Không coi (1)–(3) là bằng chứng nhân quả; chưa đo tần suất tái xuất hiện của họ hiếm (cần bước kiểm metadata, `minimum_experiment_protocol.md` G0) |

## Bài toán P-B (xét và loại): chi phí phục vụ do mã hóa lại context
Quan sát: S2 cập nhật mất 338,7 s GPU; 2610.05352 ghi nhận context gần như không đổi vẫn bị mã hóa lại mỗi lần dự đoán. Hướng cải tiến (cache/tái dùng encoding) cần thay đổi kiến trúc hoặc huấn luyện lại backbone; 2610.05352 chỉ làm được trên mô hình nhỏ tự huấn luyện. Hướng này giáp RQ1 C2/C3 đã đóng, và không có đường kiểm chứng ngắn. **Loại.**
