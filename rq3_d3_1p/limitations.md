# Giới hạn — D3-1

1. **Cỡ mẫu 12 transition**, không độc lập hoàn toàn (cùng dataset, chia sẻ ngày). Không có suy luận thống kê; kết quả mô tả. Seed (15) và flow là lặp phụ thuộc, không làm tăng bằng chứng độc lập.
2. **Khoảng seed không phải CI.** Quy tắc tách khoảng chỉ nói về biến thiên seed, không về biến thiên giữa ngày/dataset.
3. **Baseline oracle.** π và Brier hằng prevalence dùng nhãn của chính tập đánh giá chỉ như tham chiếu mô tả, không phải bộ dự đoán khả thi.
4. **Không hiệu chuẩn, không đặt ngưỡng mới.** Mọi ECE/precision/recall tại `τ_hist` phản ánh điểm số và ngưỡng gốc; mismatch giữa ngưỡng nguồn và phân phối đích không được sửa trong D3-1.
5. **Ngân sách và low-support là đề xuất** (`PROPOSED`) chưa được người nghiên cứu xác nhận.
6. **ECE phụ thuộc binning và prevalence thấp**; chỉ mô tả.
7. **FAR theo flow ≠ cảnh báo sai theo thời gian.** `FA_hour/day` phụ thuộc có `ts` cấp flow và metadata khoảng thiếu; nếu không có, chỉ có biến thể không hiệu chỉnh. Khoảng im lặng không được xem là khoảng quan sát hợp lệ hay không hợp lệ.
8. **Family-vs-benign** loại các family khác nên không phản ánh phân bố thực của ngày đích; chỉ dùng mô tả; Brier/ECE không báo.
9. **Arm không đối xứng:** `LGBM_FULL` có nhiều thông tin nguồn hơn; so sánh với nó là thứ cấp.
10. **Artifact chưa được xác minh trong Cloud** (`NEEDS_LOCAL_VERIFICATION`): danh sách transition, seed, cột, kiểu điểm số (float32/64), `τ_hist` và khóa, checksum. Các giả định O1–O10 trong `D3-1_protocol.md` §10 chưa được xác nhận.
11. **Nhãn Study 1** là nhãn của nghiên cứu hiện hữu; không có kiểm chứng độc lập của nhãn trong D3-1. Không dùng làm confirmation độc lập, không kết luận nhân quả hay tính mới.
12. **Không liên quan CTU-SME-11:** các gate E2/E2b không cung cấp bằng chứng cho D3-1.
13. Phụ thuộc môi trường: so sánh điểm số với `τ_hist` ở float64; điểm số lưu float32 có thể làm hàng gần ngưỡng lệch → kiểm tra ở checklist.
