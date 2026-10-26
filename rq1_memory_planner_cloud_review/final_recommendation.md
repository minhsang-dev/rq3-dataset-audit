# RQ1 — Phase-aware memory planner: **HOLD**

**Sửa đổi đề xuất:** bộ kiểm soát nhận chạy theo pha. Nó dự đoán cận trên của peak RSS cho load, fit/context và predict (từ thang profiling nhỏ cộng ngoại suy cộng hệ số an toàn), chọn cấu hình bảo toàn dự đoán rẻ nhất vừa cap, hoặc từ chối trước khi chạy. Chi tiết ở `candidate_planner_spec.md`.

**Workflow gần nhất:**
- FlexGen (ICML 2023): mô hình chi phí cộng LP chọn offload và batch dưới ràng buộc bộ nhớ.
- xMem / VeritasEst / DNNMem: dự đoán peak trước khi chạy để tránh OOM.
- Baseline trong miền: TabICL 2.2.0 `offload_mode=auto`.

## Bốn tiêu chí
| Tiêu chí | Đánh giá |
|---|---|
| 1. Workflow gốc đã công bố | Có: TabICL auto offload; FlexGen; xMem |
| 2. Sửa đổi cụ thể | Có ở mức đặc tả. **Chưa xác định được** nó đổi quyết định nào so với `auto`, vì không có mã nguồn |
| 3. Đo hiệu quả so với baseline mạnh | Có kế hoạch: OOM tránh được, thời gian lãng phí, an toàn, peak, runtime, tương đương dự đoán |
| 4. Dữ liệu và tính toán khả thi | Có: CPU, tái dùng container và cap của RQ1 |

## Blocker duy nhất (nguồn)
Cloud không có mã TabICL 2.2.0, nên không biết quy tắc của `offload_mode=auto`, phát hiện cgroup và batching. Không có mã thì không chứng minh được planner **khác** cơ chế sẵn có, và có nguy cơ chỉ là wrapper. Thêm vào đó, ý tưởng **trùng RQ1 C2 (HOLD)** và giáp **C3 (NO-GO)** trong sổ đóng. Nếu người nghiên cứu mở lại, cần ghi rõ đây là C2 được mở lại với cơ chế cụ thể.

## Phải kiểm ở máy cục bộ
1. Mã nguồn `tabicl` 2.2.0 đúng phiên bản: hàm quyết định `offload_mode='auto'`, phát hiện cgroup, chia lô, bộ nhớ của `kv_cache`.
2. `run_fm.py` và log theo pha của RQ1 (peak từng pha, thời điểm OOM).
3. Danh sách cấu hình bảo toàn dự đoán thực sự. Ví dụ: `batch_size` có ảnh hưởng tới kết quả số học không?
4. Với Google TabFM: có tùy chọn nạp nào (dtype, mmap, device map) không. Nếu không, planner chỉ có thể từ chối sớm, không thể làm cho chạy được.
