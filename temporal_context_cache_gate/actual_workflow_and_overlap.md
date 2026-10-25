# Step 1 — Workflow thực tế và độ chồng lấn context

Không có mã nguồn (xem `access_inventory.md`). Các câu trả lời dưới đây **chỉ dựa trên tài liệu đã lưu**; hành vi chưa được ghi lại trong tài liệu được đánh dấu UNVERIFIED, không suy đoán.

| Câu hỏi | Trả lời từ tài liệu | Mức |
|---|---|---|
| 1. Chuẩn bị context và inference lặp ở đâu? | **RQ1:** "Mỗi predict container nạp/fit lại" (`05`). **RQ3 Study 1:** mỗi unit (transition × seed × arm) dựng context 10.000 hàng của ngày nguồn rồi chấm 10.000 hàng eval. Chi phí S2 là 338,7 s GPU L4 mỗi lần cập nhật (`07` dòng 226). Không biết thời gian chia giữa context và query thế nào | FOUND (tóm tắt); phân rã thời gian UNVERIFIED |
| 2. Trạng thái context có tái dùng được giữa các lần gọi không? | TabICL 2.2.0 có tham số `kv_cache` và đã chạy với **`kv_cache=False`** (`05`). README chính thức của TabICL: "The cache is built during `fit` and reused across `predict` calls". Với Google TabFM: không có tài liệu về giao diện cache | TabICL: FOUND; TabFM: UNVERIFIED |
| 3. Các context liên tiếp theo thời gian có chung bản ghi không? | **S2/S1-ctx/S2-ICL (recent):** context của transition t→t+1 là 10.000 hàng của **ngày t**; transition kế tiếp dùng **ngày t+1**. Hai context **không chung bản ghi nào** (cửa sổ là ngày UTC rời nhau, `07` dòng 28; J7 chronology/disjointness PASS, `08`). Giữa các seed của cùng ngày nguồn, context là các mẫu khác nhau từ cùng ngày; mức trùng chưa được ghi lại (J13 chỉ ghi trùng ở **eval**: 1.260 cặp seed). **S3 (mixed):** có trộn ngày cũ, nên *có thể* chung hàng giữa các transition, nhưng S3 ≡ DualFIFO (O1, NO-GO) | Recent: FOUND (không chồng lấn); S3: UNVERIFIED về mức chung |
| 4. Cập nhật context có làm mất hiệu lực cache không? | Trong ICL kiểu TabPFN/TabICL, biểu diễn của hàng context phụ thuộc vào các hàng context khác (attention giữa các hàng huấn luyện; TabICL còn có bước embedding theo cột trên toàn tập). Vì vậy thêm hay bớt bất kỳ hàng nào thì về nguyên tắc **mọi** biểu diễn và KV đã cache đều mất hiệu lực. Đây là suy luận từ mô tả kiến trúc đã công bố; **chưa kiểm trên mã** của phiên bản đã dùng | UNVERIFIED (cần mã) |
| 5. Phiên bản đã triển khai có giao diện cache dùng được không? | TabICL 2.2.0: **có** (`kv_cache`). TabPFN (không dùng trong luận án): **có** (`fit_mode="fit_with_cache"`). Google TabFM 1.0.0: chưa xác định | Một phần |

## Hệ quả
- Trong workflow chính (recent context), **không có bản ghi chung** giữa các context theo thời gian. Do đó không có phần tính toán nào để "tái dùng tăng dần" qua các lần cập nhật.
- Phần tái dùng **trong** một context (một lần fit, nhiều lần predict) chính là `kv_cache` sẵn có của TabICL và `fit_with_cache` của TabPFN.
