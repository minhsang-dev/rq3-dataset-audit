# FF-CURE G0 (chạy lại) — **G0_FAIL**

**Ngày:** 2026-10-25. Chỉ đọc; không mô hình, không GPU, không tải dữ liệu, không fitting, không sửa nguồn hay LOCKED. Thay thế G0_HOLD trước đó (lần trước thiếu đầu vào).

| Mục | Kết quả |
|---|---|
| Nguồn | ZIP Gate M (SHA-256 a5f34a1c…f1068); 7/7 tệp khớp manifest gốc; 21/21 dataset-day đối soát PASS; 0 nhãn invalid |
| Tiêu chí | Đúng bộ đã khóa trong `eligibility_definition.md` (không đổi; commit 5b06a5a, trước khi có dữ liệu): n_hist ≥ 20; low-volume ≤ trung vị lịch sử; tỉ lệ ở ngày nguồn < 0,01; E_f(t') ≥ 20; đơn vị transition; PASS khi ≥ 4. Khóa trong gate, **không** preregister từ thiết kế gốc |
| Transition kiểm tra | **12** (6 ToN-IoT, 6 CICIDS2018) |
| Transition đủ điều kiện | **0** |
| Family đủ điều kiện | **0** (77 dòng ứng viên trong `family_recurrence_counts.csv`) |
| Fail chỉ vì support (đạt c1–c3, trượt c4) | 0 |

## Vì sao fail (mô tả, không phải nhân quả)
- Trong ToN-IoT và CICIDS2018, **không họ nào có mặt → vắng → quay lại**.
- Mỗi họ chỉ xuất hiện 1 ngày, hoặc 2 ngày liên tiếp:
  - ToN: scanning 04-23/24; dos 04-24/25; ddos 04-25/26; password 04-26/27; Backdoor 04-28/29.
  - CIC: Infilteration 02-28/03-01; các họ còn lại chỉ 1 ngày.
- Với mọi họ có support ở ngày đích, thì hoặc là **mới hoàn toàn** trong lịch sử (c1 sai), hoặc **có mặt ở ngày nguồn** (c3 sai).
- Kết luận này không phụ thuộc giá trị ngưỡng: điều kiện "tái xuất hiện sau vắng" không có trường hợp nào ở hai dataset development.
- Chỉ NF-UNSW-NB15-v3 có 9 họ xuất hiện ở 2015-01-22 và 2015-02-18. Nhưng nguồn này ngoài phạm vi (không sạch, 1 transition, cách 25 ngày) và không được dùng để cứu G0.

## Nhãn family
**OFFLINE_ORACLE_ONLY.** `FAMILY_VERIFICATION_TIME_UNKNOWN`, không có cột thời điểm xác nhận. Ngày quan sát không được chứng nhận là đầy đủ.

## Giới hạn
Kết quả chỉ thuộc **development data** (đã phơi nhiễm), không phải xác nhận độc lập. Đây là cổng đủ điều kiện, không phải phép thử phương pháp. "Vắng trong dữ liệu" chỉ là quan sát trên các ngày đã ghi.

## Quyết định
Theo quy tắc G0: **đóng FF-CURE trong phạm vi dữ liệu development (ToN-IoT, CICIDS2018) và tiêu chí hiện tại.** Không soạn pilot protocol. Không đề xuất cơ chế hay dataset khác để cứu FF-CURE.

## Một bước tiếp theo
Người nghiên cứu ghi nhận việc đóng FF-CURE (G0_FAIL) vào hồ sơ quyết định của luận án.
