# Step 2 — Baseline caching đã công bố

| Cơ chế | Nguồn | Nội dung | Mức xác minh |
|---|---|---|---|
| TabICL KV cache | README `github.com/soda-inria/tabicl` | "To speed up repeated inference on the same training data, enable KV caching. The cache is built during `fit` and reused across `predict` calls"; "fast: only processes test data by reusing the cached context"; có thể lưu cache (`save_training_data=False`) | WebFetch README 2026-10-25 |
| TabICL CPU/disk offloading | Như trên | `offload_mode` tự quyết định offload CPU/disk; "a million samples in a few minutes without RAM overflow" | Như trên |
| TabPFN cached inference | README `github.com/PriorLabs/TabPFN` | `fit_mode="fit_with_cache"`; "Test rows are conditionally independent given the KV cache, so chunking is mathematically equivalent"; batch test rows (`TABPFN_MAX_BATCHED_TEST_ROWS`) | WebFetch README 2026-10-25 |
| Batch prediction | README TabPFN | "Each `predict` call recomputes the training set", nên khuyến nghị gọi predict theo lô | Như trên |
| TFM trên stream: context gần như không đổi vẫn bị mã hóa lại | arXiv:2610.05352 (đã đọc ở gate trước) | Tái dùng encoding cần thay đổi kiến trúc hoặc huấn luyện lại; chỉ làm được trên mô hình nhỏ tự huấn luyện | Theo `new_method_research_design/problem_selection.md` §P-B |
| Bộ nhớ context trên stream | DualFIFO (KDD '26) | Quản lý context ngắn hạn và dài hạn | Đã đọc ở gate trước |
| Tái dùng KV khi chỉ nối thêm (prefix caching) | Thực hành chuẩn cho mô hình attention nhân quả | Chỉ hợp lệ khi biểu diễn của phần cũ không phụ thuộc phần mới. Điều này không đúng với attention hai chiều giữa các hàng context | Kiến thức chung; chưa áp lên mã TabFM |

**Kết luận Step 2:** tái dùng trạng thái context cho nhiều lần predict, chia lô test và offload đều đã có sẵn trong thư viện. Bật các tính năng này **không** phải tính mới.
