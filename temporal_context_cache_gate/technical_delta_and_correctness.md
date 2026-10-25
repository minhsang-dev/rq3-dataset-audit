# Step 3 — Khác biệt kỹ thuật và tính đúng

**Khác biệt kỹ thuật: KHÔNG XÁC ĐỊNH ĐƯỢC (NONE).** Lý do:
1. **Không có gì để tái dùng qua thời gian trong workflow chính.** Context recent của các transition liên tiếp là các ngày rời nhau, không chung bản ghi (`actual_workflow_and_overlap.md` mục 3).
2. **Tái dùng trong một context đã có sẵn.** Đó là `kv_cache` của TabICL và `fit_with_cache` của TabPFN, cùng với predict theo lô. Bật các tính năng này là enabling feature, không phải cải tiến.
3. **Cập nhật context tăng dần với attention hai chiều.** Khi có một hàng thay đổi, biểu diễn của toàn bộ context thay đổi theo. Cập nhật tăng dần **chính xác** vì vậy cần thay đổi kiến trúc (attention nhân quả hoặc tách khối) hoặc huấn luyện lại backbone. Hướng này đã bị loại ở §P-B (`new_method_research_design/problem_selection.md`) và giáp RQ1 C2/C3. Cập nhật tăng dần **xấp xỉ** (giữ KV cũ cho các hàng chung) thì không có lập luận đúng: nó đổi đầu ra của mô hình, nên phải được đánh giá như một mô hình khác, không phải một cách tăng tốc.
4. **Chỉ S3 (mixed) có thể có hàng chung**, và S3 ≡ DualFIFO (O1, NO-GO). Mức chung chưa xác minh, và lý do ở điểm 3 vẫn áp dụng.

**Baseline phải dùng nếu có đề xuất sau này (ghi lại, không đề xuất chạy):**
- Baseline mạnh có cache: TabICL `kv_cache=True` kết hợp predict theo lô và `offload_mode` tối ưu, cùng checkpoint, cùng context và cùng precision.
- Baseline không cache: cấu hình RQ1/RQ3 hiện tại (`kv_cache=False`).
- Nếu là TabPFN: dùng `fit_with_cache`.

**Cải thiện đo được:** không có ứng viên nên không ước lượng. Riêng bật `kv_cache` có thể giảm thời gian ở các lần predict lặp lại trên cùng context, nhưng đó là chức năng có sẵn.

Phần thiếu mã nguồn chỉ ảnh hưởng việc **kiểm** mục 3 (mất hiệu lực cache) và giao diện cache của Google TabFM. Nó không ảnh hưởng mục 1–2, vốn dựa trên tài liệu đã lưu và README chính thức.
