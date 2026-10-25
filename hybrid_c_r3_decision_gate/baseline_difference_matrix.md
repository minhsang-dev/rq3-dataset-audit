# Baseline → thay đổi → lợi ích kỳ vọng, và kiểm tương đương

## 1. Phương pháp gốc để cải thiện
| Vai trò | Chọn | Lý do |
|---|---|---|
| Pipeline gốc gần nhất | **HINT** (arXiv:2609.07956v1) | Edge → offload theo độ tin cậy → cloud TFM với context; đúng kiến trúc |
| Thành phần routing mạnh nhất cho phát hiện nhị phân | **H2T2** (arXiv:2509.15674v3) | Band hai ngưỡng, chi phí FN>FP, học online; mạnh hơn ngưỡng θ một phía của HINT |
| Thành phần thu nhãn cho context TFM | **Tab-AICL** (arXiv:2603.27385v1) | Margin/diversity acquisition tối ưu context ICL |
| Thành phần làm mới context | Recency window (arXiv:2610.05352v1, abstract: giữ dữ liệu gần nhất ngang các kỹ thuật quản lý bộ nhớ); Study 1 S2 | |

**Đối chứng công bằng mạnh nhất B\***: router H2T2 (hoặc rank-budget B4) với cùng b_c → cloud TFM → làm mới context recency mỗi batch bằng **cùng b_l nhãn**, chọn theo quy tắc tốt nhất **khả thi trong cùng ngân sách call**: {ngẫu nhiên đều trong batch; Tab-AICL margin dùng **điểm edge** trên toàn batch; Tab-AICL margin dùng **điểm cloud** trên R}, cùng trễ d, cùng ε; LightGBM huấn luyện lại trên đúng tập nhãn (cùng wrapper).
Không dùng Tab-AICL bằng điểm TFM trên toàn batch: nó cần n cloud call, vi phạm b_c.

## 2. Ánh xạ
| Baseline | Thay đổi C-R3 | Lợi ích kỳ vọng (chưa kiểm) |
|---|---|---|
| HINT: context = k láng giềng truy xuất, nhãn prequential miễn phí, không trễ | Context = nhãn analyst có chi phí b_l, trễ d, lấy từ R | Thực tế hơn với SOC; thích nghi drift qua context |
| H2T2: feedback từ offload chỉ để chỉnh ngưỡng | Feedback (nhãn) dùng để cập nhật context TFM | Cải thiện scorer cloud qua thời gian, không chỉ điểm vận hành |
| Tab-AICL: pool tự do, tĩnh, không trễ | Pool = R; điểm cloud chỉ có trên R; trễ d | Dùng lại tín hiệu bất định đã trả tiền (không tốn call thêm) |
| Recency refresh với nhãn ngẫu nhiên (B11) | Nhãn có chủ đích từ R | Nhiều thông tin hơn mỗi nhãn |

## 3. Kiểm tương đương với B\*
| Thành phần C-R3 | Biểu diễn trong B\* | Khác biệt không tương đương? |
|---|---|---|
| Router chọn R (TBD) | Chọn router của B\* bằng đúng router đó (H2T2/B4 hoặc họ ngưỡng chuẩn) | Không |
| Cloud chấm R với C_t | Như nhau | Không |
| A ⊆ R theo quy tắc acquisition (TBD) | Tab-AICL với **pool = R** và điểm cloud — một thành viên trong tập quy tắc của B\* | Không; giới hạn pool chỉ là **tham số** của Tab-AICL |
| ε·b_l nhãn đều trong batch | Exploration ngẫu nhiên (H2T2 cũng offload một phần để ước lượng); B\* dùng cùng ε | Không |
| Trễ d | Điều kiện dữ liệu chung cho mọi arm | Không (là giả định, không phải cơ chế) |
| Chèn nhãn vào C_{t+1} (TBD; ví dụ recency cân bằng lớp) | Cùng quy tắc update trong B\* | Không |
| LightGBM huấn luyện lại trên cùng nhãn | Cùng trong B\* (B13) | Không |

**Kết luận:** với cùng scorer, dữ liệu, ngân sách, trễ và ε, mọi lựa chọn hợp lệ của C-R3 là **một cấu hình** của B\*. Nếu B\* chọn quy tắc tốt nhất trong tập của nó, C-R3 không thể hơn B\* trừ khi chính cấu hình đó là tốt nhất. Khi đó cải thiện là **việc chọn cấu hình** (pool = R), không phải cơ chế. Không tìm được witness input nào để C-R3 cho quyết định khác B\* ở cùng cấu hình.

Giả thuyết hẹp duy nhất còn lại: "trong họ B\*, acquisition pool = R với điểm cloud tốt hơn pool = toàn batch với điểm edge". Đây là một **so sánh ablation giữa các cấu hình** đã biết. Nó có thể là kết quả thực nghiệm, nhưng không phải cải thiện workflow có cơ chế phân biệt được.
