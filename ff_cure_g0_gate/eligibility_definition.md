# Tiêu chí G0 — trạng thái và định nghĩa khóa

## Trạng thái preregistration
`new_method_research_design/minimum_experiment_protocol.md` chỉ định nghĩa bằng ký hiệu: `n_hist`, `q` không có giá trị; P_min = 20; "khối lượng thấp = dưới trung vị khối lượng họ của lịch sử"; ngưỡng G0 là < 4 transition → NO-GO.
→ **`G0_CRITERION_NOT_PRE_REGISTERED`** (tiêu chí định lượng chưa đầy đủ).

Định nghĩa dưới đây được khóa **tại tệp này, trước khi truy cập bất kỳ count family × day nào** (không count nào có trong Cloud). Nó không phải đăng ký trước từ thiết kế gốc, và kết quả theo nó **không phải** xác nhận độc lập. Không được sửa sau khi đếm.

## Định nghĩa (một bộ duy nhất)
**Phạm vi:** 12 transition đủ điều kiện của Study 1 (6 ToN-IoT, 6 CICIDS2018), đúng thứ tự và các cặp ngày ở file 07 §2. Chỉ dùng các ngày ≥ 100.000 dòng theo prereg. UNSW bị loại (không sạch, 1 transition).

**Ký hiệu:**
- `N_f(d)`: số dòng family `f` trong ngày UTC `d` (toàn ngày, từ bảng Gate M).
- `N_A(d)`: tổng số dòng attack của ngày `d`; `N(d)`: tổng số dòng.
- `H_t`: các ngày đã giữ lại, đứng **trước** ngày nguồn `t` trong cùng dataset.

Với transition `t → t'` (`t'` là ngày đích), family `f` **đủ điều kiện** khi đạt cả bốn:
1. **Có lịch sử:** `Σ_{d∈H_t} N_f(d) ≥ n_hist = 20`. Lý do: bằng P_min và `m_floor` = 20, để f có thể chiếm đủ một floor.
2. **Khối lượng thấp:** `Σ_{d∈H_t} N_f(d)` **≤ trung vị** của cùng tổng trên các family có tổng > 0 trong `H_t`. Chỉ dùng lịch sử đến trước `t`; không dùng `t'`.
3. **Vắng hoặc giảm ở ngày nguồn:** `N_f(t) / N_A(t) < q = 0,01` (gồm cả `N_f(t) = 0`). Lý do: dưới 1% attack của ngày t thì short bank recent (≈ 5.000 dòng, nửa là attack ở cấu hình cân bằng) giữ kỳ vọng < 25 dòng của f. Điều này phân biệt với family có mặt liên tục.
4. **Tái xuất hiện đủ support:** số dương kỳ vọng trong mẫu eval 10.000 dòng của Study 1, `E_f(t') = N_f(t') · 10.000 / N(t') ≥ 20` (= P_min).
   - Đây là điều kiện **đánh giá endpoint**, không phải đầu vào quyết định của phương pháp.
   - Đây là ước lượng kỳ vọng, chưa phải count trong tập eval thực của từng seed (cần `eval_attack` local).

**Transition đủ điều kiện:** có ≥ 1 family thỏa 1–4. **Đếm theo transition**: nhiều family trong một transition vẫn tính là 1; không nhân theo seed hay arm. Transition đầu của mỗi dataset (`H_t` rỗng) tự động không đủ điều kiện.

**Quy tắc G0:**
- G0_PASS: ≥ 4 transition đủ điều kiện **khác nhau**, count có provenance (đường dẫn + hash), taxonomy family nhất quán trong mỗi dataset.
- G0_FAIL: dữ liệu hợp lệ nhưng < 4.
- G0_HOLD: thiếu nguồn hoặc không phân giải được taxonomy/support.

**Báo cáo bắt buộc khi đếm** (tệp `family_recurrence_counts.csv`, một dòng mỗi transition × family ứng viên):
- transition_id; family; `N_f` của từng ngày trong `H_t`, ngày `t`, ngày `t'`;
- khoảng cách tái xuất hiện, tính bằng số ngày từ lần cuối `N_f/N_A ≥ q` đến `t'`;
- cờ từng điều kiện; trạng thái transition.

Kèm tổng số transition, số distinct family, số transition fail riêng vì support, và các trường hợp taxonomy không nhất quán.
