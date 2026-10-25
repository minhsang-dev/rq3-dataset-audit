# Bản đồ chèn mục M7 vào chương RQ3

## 1. Tìm chương RQ3 có thẩm quyền
| Nơi tìm | Kết quả |
|---|---|
| Repo `minhsang-dev/rq3-dataset-audit` (mọi thư mục; tìm theo tên và nội dung "chương", "chapter", "luận án") | **Không có** bản thảo chương RQ3. Repo chỉ chứa các báo cáo gate và kiểm toán. |
| Gói handoff `claude_hybrid_handoff` (19 tệp) | **Không có** chương luận án. Có báo cáo kết quả Study 1 (`07`, phần "Bài 3 — kết quả theo kế hoạch đã đăng ký"), báo cáo H1/H2 (`10`) và D3-1E (`11`), nhưng đều là báo cáo, không phải chương. Đường dẫn gốc nằm trên máy người nghiên cứu (`D:\Personal\PhD\IUH\...`) và Cloud không truy cập được. |

**Kết luận:** mục M7 được giao dưới dạng **bản nháp độc lập** (`m7_thesis_ready_section_vi.md`). Tôi không tạo bản sửa của chương nào, không ghi đè tài liệu chuẩn và không bịa nội dung xung quanh.

## 2. Vị trí dự kiến (người nghiên cứu xác nhận khi chèn)
Mục M7 đặt **ngay sau tiểu mục phân tích H1/H2** trong phần kết quả chẩn đoán D3 của chương RQ3, và **trước** phần tổng hợp hoặc thảo luận của chương.

```
Chương RQ3 (số chương theo luận án)
 └─ … Kết quả Study 1 (so sánh chính S2 − S1, so sánh phụ)            [đã có, ngoài repo]
 └─ … Chẩn đoán D3 (D3-1E PARTIAL, giới hạn số học)                    [đã có, ngoài repo]
     └─ [x.y−1] Phân tích H1/H2 (SUPPORTED_DESCRIPTIVELY)              [đã có, ngoài repo]
     └─ [x.y]   Phân tích đối chứng thất bại theo mô hình trong điều kiện ngữ cảnh tương đồng   ← CHÈN MỚI
 └─ … Thảo luận / giới hạn của chương                                   [đã có, ngoài repo]
```

## 3. Quy tắc khi chèn
1. Thay `[x.y]` bằng số tiểu mục kế tiếp sau H1/H2, và đổi số các bảng `[x.y]-1`, `[x.y]-2` theo quy ước đánh số bảng của chương.
2. Thay các mã nguồn [M7-…], [H12], [D3-…] bằng hình thức trích dẫn hoặc phụ lục của luận án, nhưng phải giữ được khả năng truy vết tới tệp gốc.
3. Trong tiểu mục H1/H2, chỉ có thể thêm một câu dẫn chuyển ("Mục [x.y] kiểm tra liệu các thất bại này có đặc thù cho TabFM khi ghép ngữ cảnh"). Việc này cần người nghiên cứu duyệt; tôi không tự sửa.
4. Không chuyển nội dung M7 sang phần "đóng góp" hay "phương pháp".
5. Nếu chương dùng "lần chuyển" hay "transition" với nghĩa khác, đồng bộ thuật ngữ theo chương. Bản nháp dùng "lần chuyển (transition)" theo báo cáo kết quả Study 1 (`07`).
