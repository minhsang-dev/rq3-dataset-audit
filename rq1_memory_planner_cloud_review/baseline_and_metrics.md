# Baseline và chỉ số (kế hoạch, KHÔNG phải lệnh chạy)

## Baseline
1. **TabICL 2.2.0 mặc định:** `offload_mode=auto` + batching + phát hiện cgroup, đúng cấu hình RQ1.
2. **Chọn cấu hình tốt nhất bằng thử nghiệm (oracle):** chạy mọi cấu hình bảo toàn dự đoán và lấy cấu hình rẻ nhất không OOM. Đây là cận trên của mọi planner, với chi phí thử đầy đủ.
3. **Heuristic đơn giản:** ước lượng theo kích thước tham số cộng kích thước tensor context (kiểu DNNMem giải tích), không có thang profiling.
4. *(Tùy chọn)* Ước lượng kiểu vLLM: profile một lượt ở cỡ đầy đủ rồi quyết định. Tốn đúng một lần chạy có thể bị OOM.

## Thí nghiệm công bằng tối thiểu
- **Lưới:** {TabICL, Google TabFM (chỉ pha load)} × {DS1, DS2, DS3_a1} × N_ctx ∈ lưới khóa trước × cap ∈ {1, 2, 4 GiB} × {x86, ARM}. Dùng đúng container và cap của RQ1.
- **Chỉ số:**
  - **Tránh chạy hỏng:** số lần OOM tránh được; thời gian CPU lãng phí cho các lần chạy rồi OOM.
  - **An toàn:** số trường hợp dự đoán khả thi nhưng OOM (mục tiêu 0); số từ chối oan so với oracle.
  - **peak RAM** thực đo (cgroup memory.peak) so với cận trên dự đoán: độ phủ và độ chặt.
  - **runtime** của cấu hình được chọn so với baseline 1 và oracle.
  - **Tương đương dự đoán:** sai khác tối đa của xác suất so với cấu hình tham chiếu, theo dung sai khóa trước.
- **Lặp:** ít nhất 3 lần mỗi ô, như RQ1. Báo tách cold start và warm.
- **Tiêu chí bác bỏ (khóa trước):** planner không tốt hơn baseline 1 về số lần chạy hỏng hoặc thời gian lãng phí, hoặc có bất kỳ trường hợp dự đoán khả thi nhưng OOM vượt mức cho phép, hoặc không khác baseline 1 ở bất kỳ ô nào (tức chỉ là wrapper).
