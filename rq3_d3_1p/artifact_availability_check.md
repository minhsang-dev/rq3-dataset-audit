# Kiểm tra khả dụng artifact (mức metadata/provenance)

**Kết quả:** `NEEDS_LOCAL_VERIFICATION` cho **mọi** artifact. Không dữ liệu thay thế nào được tạo.

## 1. Đã kiểm tra trong Cloud
| Mục | Kết quả |
|---|---|
| Repo `minhsang-dev/rq3-dataset-audit`, nhánh `claude/sleepy-cray-jhn01z` (HEAD `2e19c6c`) | Chỉ có `gate_e2_outputs/`, `gate_e2b_preflight/`, `gate_e2b_limited/` (báo cáo kiểm toán CTU-SME-11). Không có tệp dự đoán, bảng eval, ngưỡng lịch sử, danh sách transition, seed, checksum hay tài liệu D3-1 gốc |
| Tìm chuỗi `D3-1`, `transition` trong repo | Không có |
| Truy cập máy Windows của người nghiên cứu | Không có trong phiên Cloud |

## 2. Artifact cần cho D3-1E (đều chưa xác minh)
| ID | Artifact | Trường/thuộc tính cần xác minh | Trạng thái |
|---|---|---|---|
| AR1 | Danh sách 12 transition | `transition_id, dataset_id, source_day, target_day, is_valid` | NEEDS_LOCAL_VERIFICATION |
| AR2 | Bảng eval | `eval_row_id, y_binary, [attack_family], [ts]`, `eval_set_hash` | NEEDS_LOCAL_VERIFICATION |
| AR3 | Bảng dự đoán (3 arm × 15 seed) | `transition_id, arm_id, seed_id, eval_row_id, score`; kiểu số; hàng thiếu | NEEDS_LOCAL_VERIFICATION |
| AR4 | Định nghĩa context/train cho từng arm | `train_source_def`, `context_id`, kích thước | NEEDS_LOCAL_VERIFICATION |
| AR5 | Ngưỡng lịch sử đã phục hồi | `tau`, khóa, nguồn, SHA-256 | NEEDS_LOCAL_VERIFICATION |
| AR6 | Metadata khoảng thiếu dữ liệu | `gap_start, gap_end, gap_type` | NEEDS_LOCAL_VERIFICATION |
| AR7 | Checksum SHA-256 của mọi tệp ở trên | khớp với giá trị đã khóa trước (nếu tồn tại) | NEEDS_LOCAL_VERIFICATION |

## 3. Việc cần làm cục bộ (không thực hiện ở đây)
Xem `pre_execution_checklist.md` phần B. Kết quả nên được ghi vào `artifact_registry` (chỉ metadata: đường dẫn, kích thước, SHA-256, số hàng, tên cột, kiểu) và gửi lại để tôi cập nhật báo cáo; **không** cần gửi nội dung dự đoán hay nhãn.
