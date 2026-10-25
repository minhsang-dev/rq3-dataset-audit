# Sổ đăng ký các hướng đã đóng (giữ nguyên quyết định đã khóa)

Phân loại: **REJECTED_MECHANISM** (cơ chế tương đương baseline/prior art hoặc kết quả âm) · **MISSING_EVIDENCE** (cơ chế có thể hợp lệ, thiếu bằng chứng) · **OPEN_DISTINCT** (khác biệt kỹ thuật chưa giải quyết).

| Hướng | Quyết định hiện có | Phân loại | Căn cứ (file gói / repo) |
|---|---|---|---|
| RQ1 C1 giảm footprint nạp Google TabFM | NO-GO | REJECTED_MECHANISM | 06: chưa có cơ chế khác compression/loader chuẩn |
| RQ1 C2 full-context TabICL với bảo đảm RAM | HOLD | MISSING_EVIDENCE (không có cơ chế, không có bound) | 06; prior art chunking/offloading/caching đã có |
| RQ1 C3 adaptive precision/batch/exit | NO-GO | REJECTED_MECHANISM | 06 |
| RQ2 DoP | Có bằng chứng có điều kiện; tương đương peer-score rank-calibrated | REJECTED_MECHANISM (như cải tiến độc lập) | 02, 04 |
| RQ3 D3 C1 admission contract | NO-GO | REJECTED_MECHANISM (≡ joint-risk LTT) | 13 |
| RQ3 D3 C2 recalibration/label-shift rồi budget | NO-GO | REJECTED_MECHANISM | 12 |
| RQ3 D3 C3 label-budgeted ICL context refresh | NO-GO | MISSING_EVIDENCE (không có timestamp nhãn, không có điểm phản thực) | 12 |
| RQ3 H1/H2 | SUPPORTED_DESCRIPTIVELY | Đóng góp thực nghiệm, không phải phương pháp | 10 |
| RQ3 Study 2 (ít nhãn) | Inconclusive | Không phải cải tiến | 04 |
| RQ4 DA-FAR | Đóng, kết quả âm | REJECTED_MECHANISM | 04 |
| P1 deployment/failure diagnosis workflow | HOLD, 0 case primary-ready | MISSING_EVIDENCE | 15 — không mở lại |
| Hybrid C-R1 rank-budget | NO-GO | REJECTED_MECHANISM | hybrid_routing_spec/03 |
| Hybrid C-R2 band theo drift | NO-GO | REJECTED_MECHANISM | hybrid_routing_spec/03 |
| Hybrid C-R3 thu nhãn gắn routing | NO-GO | REJECTED_MECHANISM + không kiểm được | hybrid_c_r3_decision_gate/ |
| Ứng viên mới xét ở gate này: O1 mixed-history context (S3) | **NO-GO (mới)** | REJECTED_MECHANISM: ≡ DualFIFO | improvement_opportunities.md |
| Ứng viên mới xét ở gate này: O2 quy tắc chọn/tie-break ngưỡng lịch sử | **NO-GO (mới)** | REJECTED_MECHANISM: tham số của C2/C-R1; cần fitting ngưỡng | improvement_opportunities.md |

Không có mục nào được xếp OPEN_DISTINCT.
