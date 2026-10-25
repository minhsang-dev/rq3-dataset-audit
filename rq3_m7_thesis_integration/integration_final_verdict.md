# Tích hợp M7 vào luận án — **PARTIAL**

**Ngày:** 2026-10-25. Đây là nhiệm vụ viết và lập tài liệu. Không có suy luận mô hình, GPU, huấn luyện, khớp ngưỡng, tải dữ liệu hay kiểm định mới. Không sửa artifact LOCKED nào.

## Vì sao PARTIAL, không phải PASS
- **Đã làm xong:** mục luận án tiếng Việt (`m7_thesis_ready_section_vi.md`) có đủ 8 phần yêu cầu, với số liệu đã đối chiếu và trích nguồn truy vết.
- **Chưa chèn được:** không tìm thấy chương RQ3 có thẩm quyền, cả trong repo lẫn trong gói handoff. Mục này vì vậy vẫn là **bản nháp độc lập** kèm bản đồ chèn (`rq3_insertion_map.md`). Chưa có bản sửa hay bản vá của chương.
- **Chưa duyệt:** người nghiên cứu chưa duyệt nội dung khoa học.

## Kiểm tra số liệu
- Mọi con số chính khớp với ma trận M7 và các báo cáo M7/H1/H2, gồm:
  - hai bảng 2×2 (20/1/9/150; 14/12/16/138);
  - tổng 21/29;
  - đối chiếu cờ 180/180;
  - nhân chứng H1 18/12;
  - 4/12 lần chuyển; 28/30 cặp;
  - AUC ≈ 0,998 / 0,372.
- Có 4 điểm lệch đã báo trong `m7_numbers_and_sources_audit.md` (D1–D4), không tự hòa giải trong nguồn. Trong đó:
  - Khoảng P ghi trong M7-R (453–9.054) khác với dữ liệu (437–9.106).
  - LightGBM ở CIC 02-16→02-20 thất bại xếp hạng ở 13/15 seed, không phải 15/15.
- Không điểm lệch nào thay đổi bảng 2×2 hay phán quyết M7.

## Đóng nhánh
`h1_h2_m7_branch_closure.md` ghi nhận:
- H1/H2 vẫn SUPPORTED_DESCRIPTIVELY.
- M7 = SHARED_OR_MIXED_FAILURE.
- Hướng khám phá phương pháp chẩn đoán D3 dựa trên H1/H2 **CLOSED**.
- Không cho phép phân tích hay chạy GPU thêm. Muốn mở lại cần câu hỏi mới, bằng chứng mới và phê duyệt của người nghiên cứu.

## Một bước tiếp theo
Người nghiên cứu duyệt mục `m7_thesis_ready_section_vi.md` (nội dung, thuật ngữ, số mục), rồi tự chèn vào chương RQ3 ngay sau tiểu mục H1/H2 theo `rq3_insertion_map.md`.
