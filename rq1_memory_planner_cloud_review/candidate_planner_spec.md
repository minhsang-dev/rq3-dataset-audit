# Ứng viên: Bộ kiểm soát nhận chạy (admission planner) theo pha cho TabICL

**Trạng thái:** đề xuất ở mức workflow. Chưa cài đặt. **Không** tuyên bố tính mới ở mức cài đặt, vì mã nguồn TabICL 2.2.0 và mã RQ1 không có trong Cloud.

## 1. Sửa đổi đề xuất (một)
Trước khi chạy, dự đoán **cận trên của peak RSS theo từng pha** (load → fit/context → predict) cho mỗi cấu hình thực thi **bảo toàn dự đoán**. Chọn cấu hình rẻ nhất thỏa `peak_upper ≤ cap − margin`. Nếu không cấu hình nào thỏa thì **từ chối chạy** và báo pha nào sẽ vượt.

Cận trên được hiệu chỉnh từ một **thang profiling nhỏ** ở các kích thước context và query thấp, chạy trong cùng container và cùng cap. Sau đó ngoại suy theo dạng hàm của từng pha, kèm hệ số an toàn được hiệu chỉnh để cận trên phủ dữ liệu profiling đã giữ lại.

## 2. Đầu vào và đại lượng dự đoán
| Đầu vào | Nguồn |
|---|---|
| Kích thước checkpoint và dtype; số tham số (27.552.258 với TabICLv2) | Checkpoint |
| N_ctx, d (số cột), số lớp, N_query | Dữ liệu (không cần nhãn) |
| `n_estimators`, `batch_size`, `kv_cache`, `offload_mode`, số luồng | Cấu hình |
| cap (cgroup memory.max), swap, kiến trúc (x86/ARM) | Môi trường |

| Đại lượng | Theo pha |
|---|---|
| peak RSS cận trên | load, fit/context, predict |
| thời gian dự kiến | từng pha |
| cờ khả thi và pha vượt | từng cấu hình |

## 3. Quyết định nào thay đổi so với baseline
- **Baseline:** TabICL `offload_mode=auto` quyết định **trong lúc chạy**. Theo hồ sơ RQ1, với predict full-context ở ≤ 4 GiB, cơ chế này vẫn dẫn tới OOM sau khi đã tốn thời gian load và fit.
- **Planner thay đổi ba quyết định:**
  - (a) **Từ chối trước khi chạy** khi không có cấu hình khả thi, để tránh lần chạy hỏng.
  - (b) **Chọn cấu hình khác** (batch nhỏ hơn, ép offload disk, tắt hoặc bật `kv_cache`) khi cấu hình tự động không khả thi mà cấu hình khác thì khả thi.
  - (c) **Báo pha giới hạn** để người vận hành biết, ví dụ "TabFM: load vượt 4 GiB".
- **Điểm chưa xác định:** không biết quy tắc của `offload_mode=auto` (ngưỡng nào, theo pha nào), nên **không chứng minh được** (b) khác với những gì auto đã làm.

## 4. Yêu cầu đúng và an toàn
1. **Bảo toàn dự đoán.** Chỉ xét cấu hình cho dự đoán giống hệt hoặc khác trong dung sai số học đã khóa: batch, offload, kv_cache. Không đổi context hay `n_estimators` để "vừa" bộ nhớ. Những thay đổi đó đổi mô hình và thuộc họ C3 đã NO-GO.
2. **An toàn.** Tỉ lệ dự đoán khả thi nhưng thực tế bị OOM phải ≤ mức khóa trước (ví dụ 0 trên tập kiểm). Báo riêng các trường hợp từ chối oan.
3. **Không rò rỉ.** Hệ số an toàn được hiệu chỉnh trên tập profiling, kiểm trên tập cấu hình giữ lại.
4. **Chi phí profiling** được tính vào tổng chi phí.

## 5. Vì sao không chỉ là wrapper quanh offload (và giới hạn của lập luận này)
- Cơ chế auto quyết định trong lúc chạy và không có cận trên theo pha trước khi chạy. Planner thêm **dự đoán theo pha cộng quyết định nhận chạy hoặc từ chối**.
- Tuy vậy, về khái niệm đây là **áp dụng** dự đoán peak trước khi chạy (họ xMem/VeritasEst/DNNMem) và chọn cấu hình theo mô hình chi phí (họ FlexGen) cho suy luận TFM trên CPU dưới cgroup.
- Ý tưởng cũng **trùng mạnh với RQ1 C2** ("bảo đảm ngân sách RAM", HOLD vì thiếu bound) và giáp **C3** (chọn batch thích nghi, NO-GO).
- Phần có thể bảo vệ chỉ là ở mức **workflow**: phân rã theo pha cho TFM, từ chối có lý do, kèm bảo đảm an toàn đo được. Phần này cần mã nguồn TabICL để chứng minh khác với auto.
