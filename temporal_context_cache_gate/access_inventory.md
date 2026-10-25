# Step 0 — Kiểm kê truy cập

**Phạm vi:**
- Repo `minhsang-dev/rq3-dataset-audit`, nhánh `claude/sleepy-cray-jhn01z`. Lệnh `git ls-files` liệt kê toàn bộ tệp được git theo dõi: **chỉ có `.md` và `.csv`, không có `.py`, `.ipynb`, `requirements` hay `setup`**.
- Gói handoff giải nén tại `/tmp/claude-0/handoff/claude_hybrid_handoff/` (19 tệp tài liệu).
- Môi trường Cloud: `pip list` không có `tabpfn`, `tabicl` hay `tabfm`.

| Hạng mục | Trạng thái | Đường dẫn thực / bằng chứng |
|---|---|---|
| Wrapper TabFM (Google) và mã inference | **MISSING** | Chỉ được nhắc đến: `05_rq1_resource_findings.md` ghi "TabFMClassifier(model=net)", `google/tabfm-1.0.0-pytorch` rev `77cb9cc1…`. Không có mã |
| Wrapper TabICL và mã inference | **MISSING** | `05` ghi "Package 2.2.0 … kv_cache=False, offload_mode=auto"; `run_fm.py` chỉ được nhắc tên, nằm trên máy người nghiên cứu |
| Workflow thực thi Study 1/2 RQ3 | **MISSING** (mã) / FOUND (mô tả) | Mô tả: `07_rq3_study1_protocol_and_results_EXTRACT.md`. Gói `colab_pkg_bai3.zip`, `analyze_bai3.py` chỉ được nhắc (07, 08). Không có mã |
| Profiling bộ nhớ/inference RQ1 | FOUND (tóm tắt) / **MISSING** (log thô) | `05_rq1_resource_findings.md`. Ghi chú: "Mỗi predict container nạp/fit lại"; không có cache on/off ablation |
| Audit khả thi phương pháp và caching trước đó | FOUND | `new_method_research_design/problem_selection.md` §P-B ("chi phí phục vụ do mã hóa lại context … **Loại**"); `thesis_method_recovery_gate/closed_directions_registry.md` (RQ1 C1 NO-GO, C2 HOLD, C3 NO-GO); `06_rq1_novelty_verdict.md` |
| DualFIFO | FOUND | `thesis_method_recovery_gate/improvement_opportunities.md`, `baseline_and_evidence_matrix.md` (O1 ≡ DualFIFO, NO-GO) |
| **K1–K3** | **MISSING** | Không tìm thấy trong repo hay handoff. Chỉ có "D-K1" (quy ước tuần tự hóa hash, `rq3_d3_1p_v2/O1_O10_resolution.md`, `08`), là thứ khác. Không đoán nội dung |
| Checkpoint, trọng số, cache mô hình | ABSENT_BY_DESIGN | `missing_evidence.md`: "FM checkpoints, model caches … are not copied" |

## Cần chuyển từ máy cục bộ (để thoát HOLD_ACCESS)
1. `run_fm.py` và mọi wrapper TabFM/TabICL được dùng cho RQ1, kèm commit hash.
2. Mã runner Study 1 và Study 2 trong `colab_pkg_bai3` (đoạn dựng context, fit và predict), kèm SHA-256 đã ghi.
3. Phiên bản mã nguồn đúng của gói `tabicl` 2.2.0 và gói `google/tabfm` tại revision `77cb9cc1b4fd3a9c77fbb9552c218200bb4dab83`, kèm lockfile môi trường đã dùng.
4. Log thời gian theo pha của S2/S3 (338,7 s mỗi lần cập nhật), tách được thời gian xử lý context với thời gian xử lý query, nếu có.
5. Hồ sơ quyết định **K1–K3**: đường dẫn và nội dung.
