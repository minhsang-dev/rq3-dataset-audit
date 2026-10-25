# RQ3-A Pre-feasibility Gate — **NO-GO**

**Ngày:** 2026-10-25. Kiểm toán read-only.
- Không chạy Python, GPU, inference, training, replay, calibration hay tìm ngưỡng.
- Không sửa artifact LOCKED.
- Không mở lại H1/H2/M7: M7 vẫn SHARED_OR_MIXED_FAILURE; D3-1E vẫn PARTIAL/BLOCK.

## Kết quả theo từng cổng
| Cổng | Kết quả | Lý do chính | Chi tiết |
|---|---|---|---|
| **G1 — Khả thi dữ liệu** | **KHÔNG ĐẠT trong Cloud (MISSING)** | Vector điểm, `eval_y`, `eval_X`, `thr/thr_y` và `study1_thresholds_restored.csv` không có trong repo hay gói handoff. Gói ghi rõ là cố ý loại ra, và `thr` còn bị khóa "provenance ONLY, no replay". Chỉ còn metric tổng hợp. Đây là MISSING, chưa chứng minh không tồn tại trên máy người nghiên cứu | `data_inventory.md` §1–2 |
| **G2 — Giá trị tăng thêm** | **KHÔNG ĐẠT** | Hiệu chỉnh posterior theo prior là hàm tăng nghiêm ngặt, nên ở cùng K nó chọn **đúng cùng tập flow** như top-K thuần, trừ artefact hòa điểm do làm tròn. Ở cùng FAR, nó không thêm gì dưới label shift. Lõi của A **trùng D3-C2 (NO-GO)**; top-K là C-R1/B4; mọi tinh chỉnh ngưỡng hay tie-break là O2 (NO-GO). Biến thể cân lại lớp trong context là bản ICL của importance-weighted ERM trong BBSE và thuộc họ C3 (NO-GO) | `prior_art_overlap.md` §1–5 |
| **G3 — Hợp lệ dưới dịch chuyển** | **KHÔNG ĐẠT** | 10/12 transition là `new_class` (bảng §2 của `07`). Cả ba transition có recall = 0 ở S2 đều là `new_class`. Dưới label shift thuần, ngưỡng cố định giữ nguyên TPR, nên recall = 0 cho thấy P(X\|Y) đã đổi, tức là giả định của BBSE nhiều khả năng sai đúng ở chỗ A cần sửa. Điều kiện "recall tại τ trên mẫu nguồn là dương" chưa kiểm được vì `thr` MISSING | `data_inventory.md` §3 |
| **Held-out sạch** | **KHÔNG CÓ** | NONE VERIFIED (`missing_evidence.md`); CTU-SME-11 không phù hợp; không tìm nguồn mới | `data_inventory.md` §4 |

## Vì sao là NO-GO, không phải HOLD
HOLD chỉ hợp lý nếu chặn duy nhất là thiếu dữ liệu (G1). Ở đây G2 thất bại vì **lập luận cấu trúc**, không phụ thuộc dữ liệu: bảo toàn thứ hạng, và trùng với D3-C2/C-R1/O2 đã bác. Có thêm vector điểm cũng không làm A phân biệt được với top-K. Thêm vào đó G3 bất lợi ở chính các transition mục tiêu.

## Ghi nhận
Lõi của A trùng D3-C2, đã NO-GO trong `12_d3_method_feasibility.md`. Lượt gợi ý trước của tôi đã không đối chiếu với hồ sơ này. Người nghiên cứu đã đúng khi không chấp thuận A.

## Không đề xuất thí nghiệm
`minimum_validation_requirements.md` chỉ liệt kê điều kiện tiên quyết P1–P4 mà một đề xuất **khác** phải đạt trước khi mở lại cổng. Không có giao thức chạy nào được đề xuất cho A.
