# RQ1 — Workflow lập kế hoạch thực thi đã công bố

**Ngày:** 2026-10-26.

**Mức xác minh:**
- **ABS**: abstract hoặc trang chính thức qua web.
- **README**: tài liệu chính thức của thư viện.
- **KN**: theo hiểu biết.
- **REC**: hồ sơ RQ1 trong gói handoff.

| Nhóm | Công trình / hệ thống | Nội dung | Mức |
|---|---|---|---|
| Ước lượng bộ nhớ trước khi chạy | DNNMem, Gao et al., ESEC/FSE 2020 (Industry) | Ước lượng giải tích bộ nhớ GPU (đồ thị và runtime) trước khi chạy để tránh job hỏng vì hết bộ nhớ. Không có bản cài đặt công khai, theo một khảo sát 2026 | ABS |
| | LLMem (2024) | Ước lượng bộ nhớ GPU cho fine-tune LLM; báo DNNMem sai trung bình 42,6% | ABS (gián tiếp) |
| | VeritasEst, Shi & Elkhatib, arXiv:2504.03887 (2025) | Dự đoán peak GPU trên CPU trước khi xếp lịch, để tránh OOM | ABS |
| | xMem, Shi, Pezaros & Elkhatib, arXiv:2510.21048 (Middleware '25) | Dùng profiler trace và mô phỏng allocator hai mức để ước lượng peak trước khi chạy; so với DNNMem, SchedTune, LLMem | ABS |
| Lập kế hoạch offload theo ràng buộc bộ nhớ | **FlexGen**, Sheng et al., ICML 2023 | Mô hình chi phí giải tích và **LP chọn cách đặt tensor, lịch tính toán, batch** trên phân cấp GPU/CPU/disk dưới ràng buộc bộ nhớ | ABS |
| | HF Accelerate `infer_auto_device_map(max_memory=…)`; DeepSpeed ZeRO-Inference | Lập bản đồ đặt tầng theo ngân sách bộ nhớ từng thiết bị | KN |
| | vLLM | Chạy profile một lượt để đo peak, rồi cấp phát phần còn lại cho KV cache | KN |
| Thực thi TFM có ý thức tài nguyên | **TabICL 2.2.0** | `offload_mode` tự quyết định offload CPU/disk; `batch_size`; `kv_cache`. Hồ sơ RQ1 ghi có phát hiện cgroup. README: "a million samples in a few minutes without RAM overflow" | README + REC |
| | TabPFN | `fit_mode` (bao gồm `fit_with_cache`), chia lô test (`TABPFN_MAX_BATCHED_TEST_ROWS`) | README |
| | Gangwani & Bansal, arXiv:2512.00888 | Benchmark chi phí phần cứng của TFM zero-shot | Theo đề cương [13] |

## Workflow mạnh nhất đã công bố
- **Cho bài toán chọn cấu hình thực thi dưới ràng buộc bộ nhớ:** FlexGen (mô hình chi phí kết hợp tối ưu hóa).
- **Cho dự đoán peak trước khi chạy:** xMem / VeritasEst.
- **Baseline trực tiếp trong miền:** cơ chế tự động của TabICL 2.2.0 (`offload_mode=auto`, cùng batching và phát hiện cgroup).

## Bằng chứng RQ1 liên quan (REC, `05_rq1_resource_findings.md`, `06_rq1_novelty_verdict.md`)
- TabICL DS2/DS3 ở bước predict bị **OOM ở 1/2/4 GiB dù đã bật `offload_mode=auto`**. Fit DS2 xong ở 2 GiB với peak 1,17–1,42 GB.
- Google TabFM bị OOM ngay ở bước load tới 4 GiB; không giới hạn thì peak load là 13,42 GB.
- Sổ đóng: **RQ1 C2** (full-context TabICL với bảo đảm RAM) là HOLD vì "thiếu bound và cơ chế mới". **RQ1 C3** (adaptive precision/batch/exit) là NO-GO.
