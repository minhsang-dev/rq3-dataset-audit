# Đặc tả phương pháp đề xuất: FF-CURE (Family-Floor CURE)

**Thay đổi duy nhất so với CURE:** bước chọn nạn nhân và phạm vi tìm cặp khi long bank tràn. Admission (entropy-gated), short bank FIFO, biểu diễn `ϕ`, quy tắc centroid, backbone và nhãn ICL nhị phân **giữ nguyên như CURE**.

## Đầu vào và trạng thái
- Luồng flow có nhãn theo thời gian, ở ngân sách nhập cố định: `z = (x, y, a)`.
  - `x`: 46 đặc trưng (hoặc tập đặc trưng của nguồn xác nhận).
  - `y ∈ {0,1}`: nhãn benign/attack; **là nhãn duy nhất đưa vào ICL**.
  - `a`: họ tấn công, chỉ dùng cho quản lý bộ nhớ; với benign, `a = ∅`.
- Khóa bộ nhớ: `κ(z) = 'B'` nếu y = 0, ngược lại `κ(z) = a`.
- Trạng thái: `S_t` (FIFO, |S| ≤ M_s); `L_t` (|L| ≤ M_l); với mỗi mẫu: entropy `h(z)` tính khi dự đoán (như CURE); bộ đếm `n_κ` trong `L_t`; tập họ đã thấy `F_t`.
- Context ICL tại thời điểm dự đoán: `S_t ∪ L_t`, chỉ với nhãn y.

## Hạn ngạch (khóa trước khi thí nghiệm)
- Phần benign `β` của `M_l`: mặc định 0,5, đúng tỉ lệ cân bằng hai lớp của CURE/DualFIFO.
- Phần attack `(1 − β)·M_l` chia đều cho các họ trong `F_t` theo water-filling: họ có ít mẫu hơn phần chia được giữ hết, phần dư chia lại cho các họ còn lại. Ta được `q_a` cho từng họ, và `q_B = β·M_l`.
- Floor: không loại mẫu của họ `a` nếu `n_a ≤ m_floor` (`m_floor` khóa trước, ví dụ 20 = P_min của D3).

## Quy tắc loại bỏ (khi |L_t| > M_l)
```
victim_key = argmax_{κ : n_κ > floor(κ)} (n_κ − q_κ)          # floor(B) = 0; floor(a) = m_floor
  # hòa: chọn κ có n_κ lớn nhất; vẫn hòa: thứ tự cố định đã khóa
if không κ nào thỏa (mọi họ đều ở floor):
    victim_key = 'B' nếu n_B > 0, ngược lại họ có n_a lớn nhất   # suy biến có kiểm soát; ghi log
(i*, j*) = argmin_{i ≠ j, κ(i)=κ(j)=victim_key} ‖ϕ(x_i) − ϕ(x_j)‖   # cặp gần nhất TRONG CÙNG khóa
μ = centroid của các mẫu y(victim_key) trong S_t                    # như CURE; nếu rỗng dùng centroid victim_key trong L_t
loại điểm trong {i*, j*} xa μ hơn
```
Nếu `victim_key` chỉ còn 1 mẫu (không có cặp): loại mẫu cũ nhất của khóa đó (ghi log).

## Điều kiện vận hành
- Cập nhật theo luồng (từng mẫu như CURE), hoặc theo lô ngày với thứ tự thời gian trong ngày.
- Ngân sách nhập nhãn mỗi ngày giống nhau cho mọi arm: mọi arm nhìn **cùng** luồng nhãn, chỉ khác những gì giữ lại.
- Nhãn họ `a` có cùng thời điểm có sẵn với `y`, trễ `d` (d = 0 là oracle chính; d = 1 ngày là độ nhạy). **Không có timestamp xác nhận nhãn thật** → mọi kết quả là offline/oracle.
- Họ mới (chưa thấy) khi vào `L_t` được thêm vào `F_t`; hạn ngạch tính lại.
- Chi phí suy luận bằng CURE: cùng |S ∪ L| = M. Chi phí thêm chỉ là bookkeeping CPU cho bộ đếm theo khóa và tìm cặp **trong khóa**; vùng tìm nhỏ hơn hoặc bằng CURE.

## Cơ chế được kỳ vọng tạo khác biệt
Floor bảo đảm mỗi họ đã thấy còn ≥ `m_floor` mẫu trong context. Tìm cặp trong cùng họ ngăn việc một dòng họ hiếm bị loại vì nằm gần dòng họ lũ. Khi họ hiếm tái xuất hiện ở ngày sau, context vẫn còn bằng chứng về nó.

## Giới hạn đã biết của đặc tả
- Cần nhãn họ cho lịch sử. Nếu nhãn họ nhiễu hoặc không nhất quán (như CTU-SME-11), khóa sai làm hỏng cơ chế. Ablation cụm không giám sát kiểm điều này.
- Với ít họ (ví dụ CIDDS-001 có 4 loại), khác biệt với CURE có thể nhỏ.
- Floor chiếm chỗ của benign/họ lớn khi `|F_t|·m_floor` gần `(1−β)·M_l`; phải báo phần chiếm chỗ và kiểm guard AP nhị phân.
