# Research decision index

**Cập nhật lần cuối:** 2026-10-25 (lần ghi RQ3-A NO-GO).

Tệp này **chỉ là chỉ mục**. Nó không thay thế, không sửa và không làm mất hiệu lực phán quyết hay artifact khóa nào. Nguồn có thẩm quyền là các báo cáo được liên kết bên dưới, cùng manifest SHA-256 của từng thư mục. Sổ `thesis_method_recovery_gate/closed_directions_registry.md` vẫn giữ nguyên, vì nó nằm trong manifest của gate đó; các mục phát sinh sau gate đó được ghi ở đây.

## 1. Mục mới nhất: RQ3-A — **CLOSED (NO-GO)**

| Trường | Nội dung |
|---|---|
| Đề xuất | Ước lượng prevalence ngày đích (BBSE/EM) → hiệu chỉnh posterior → chọn ngưỡng theo ngân sách cảnh báo; tùy chọn cân lại tỉ lệ lớp trong context của TabFM |
| Phán quyết | **NO-GO**, được người nghiên cứu khóa ngày 2026-10-25 |
| Lý do 1 | Hiệu chỉnh posterior theo prevalence là biến đổi đơn điệu tăng. Khi giữ nguyên ngân sách và quy tắc xử lý hòa điểm, nó không đổi tập flow được chọn so với top-K |
| Lý do 2 | Lõi kỹ thuật trùng D3-C2 (đã NO-GO). Top-K tương ứng C-R1/B4. Tinh chỉnh ngưỡng tương ứng O2. Cân lại context thuộc họ D3-C3. Các phần này đều đã có tiền lệ hoặc đã bị loại |
| Lý do 3 | Không xác định được thay đổi kỹ thuật nào có thể chứng minh giá trị bổ sung vượt baseline mạnh |
| Lý do 4 (không phải lý do chính) | Dữ liệu theo flow không có trong phạm vi Cloud (MISSING, không phải đã chứng minh là không tồn tại) |
| Lý do 5 | Chưa xác nhận held-out độc lập; giả định label shift bị hạn chế (10/12 transition là `new_class`) |
| Báo cáo | [`decision.md`](rq3_a_pre_feasibility_audit/decision.md) · [`data_inventory.md`](rq3_a_pre_feasibility_audit/data_inventory.md) · [`prior_art_overlap.md`](rq3_a_pre_feasibility_audit/prior_art_overlap.md) · [`minimum_validation_requirements.md`](rq3_a_pre_feasibility_audit/minimum_validation_requirements.md) · [`manifest_sha256.md`](rq3_a_pre_feasibility_audit/manifest_sha256.md) |
| Hệ quả | A **không** nằm trong danh sách đóng góp phương pháp dự kiến của luận án. Không replay, không tìm ngưỡng và không hiệu chỉnh trên vector đã lưu để "thử gain" |

## 2. Trạng thái giữ nguyên (không đổi bởi lần cập nhật này)

| Hạng mục | Trạng thái | Nguồn |
|---|---|---|
| H1 / H2 | SUPPORTED_DESCRIPTIVELY; nhánh chẩn đoán **CLOSED** | [`rq3_m7_thesis_integration/h1_h2_m7_branch_closure.md`](rq3_m7_thesis_integration/h1_h2_m7_branch_closure.md) |
| M7 | SHARED_OR_MIXED_FAILURE; **CLOSED** | [`m7_tabfm_failure_specificity/final_verdict.md`](m7_tabfm_failure_specificity/final_verdict.md) |
| Tích hợp M7 vào luận án | PARTIAL (bản nháp độc lập, chờ duyệt) | [`rq3_m7_thesis_integration/integration_final_verdict.md`](rq3_m7_thesis_integration/integration_final_verdict.md) |
| D3-1E | **PARTIAL**, historical BLOCK không đổi | Gói handoff `11_d3_interpretation_limits.md` |

## 3. Các phán quyết khác trong repo

| Thư mục | Phán quyết |
|---|---|
| [`thesis_method_recovery_gate/`](thesis_method_recovery_gate/final_verdict.md) | NO_GO; sổ hướng đã đóng: [`closed_directions_registry.md`](thesis_method_recovery_gate/closed_directions_registry.md) |
| [`new_method_research_design/`](new_method_research_design/) | GO_FOR_PILOT_PROTOCOL (FF-CURE), sau đó bị G0 đóng |
| [`ff_cure_g0_gate/`](ff_cure_g0_gate/) | G0_FAIL; FF-CURE đóng |
| [`hybrid_routing_spec/`](hybrid_routing_spec/) | C-R1 NO-GO; C-R2 NO-GO; C-R3 HOLD, sau đó → |
| [`hybrid_c_r3_decision_gate/`](hybrid_c_r3_decision_gate/final_verdict.md) | C-R3 NO-GO |
| [`gate_e2_outputs/`](gate_e2_outputs/), [`gate_e2b_preflight/`](gate_e2b_preflight/), [`gate_e2b_limited/`](gate_e2b_limited/) | CTU-SME-11 chưa phù hợp làm held-out NetFlow |
| [`rq3_d3_1p/`](rq3_d3_1p/), [`rq3_d3_1p_v2/`](rq3_d3_1p_v2/) | Hồ sơ giao thức D3-1P (lịch sử) |

## 4. Ý tưởng chỉ mới nêu trong trao đổi, **chưa mở** (không tự động mở)

| Mã (lượt gợi ý 2026-10-25) | Mô tả ngắn | Trạng thái | Ghi chú chồng lấn đã biết |
|---|---|---|---|
| B | TabFM trên cloud làm "thầy" cập nhật LightGBM ở biên bằng nhãn mềm cho flow chưa nhãn | NOT_OPENED | Gần chưng cất ở Study 2 (inconclusive) và họ C-R3/B13; chưa kiểm prior art |
| C | DoP tuần tự bằng e-process | NOT_OPENED | Người nghiên cứu lưu ý: phải vượt một đối chứng anytime-valid tuần tự đúng chuẩn, không chỉ vượt Fisher cố định |
| D | Giảm bộ nhớ nạp TabFM (bf16/int8/nạp lười) | NOT_OPENED | **Có khả năng trùng RQ1 C1 (NO-GO, "chưa có cơ chế khác compression/loader chuẩn")** theo sổ hướng đã đóng |

Việc mở bất kỳ mục nào ở bảng 4 cần quyết định riêng của người nghiên cứu, kèm một bước đối chiếu với `closed_directions_registry.md` và với chỉ mục này **trước** khi đặc tả.
