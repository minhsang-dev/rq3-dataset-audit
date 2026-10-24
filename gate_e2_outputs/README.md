# Gate E2 — Kiểm toán metadata dataset xác nhận ngoài (CTU-SME-11; dự phòng CIDDS-001/002)

**Ngày kiểm toán:** 2026-10-24 · **Phạm vi:** chỉ metadata suitability. **Không** phải independent confirmation.

## 1. Phạm vi
- Dataset chính: CTU-SME-11 (Zenodo 10.5281/zenodo.7958259, v1.0.0).
- Dự phòng: CIDDS-001, CIDDS-002 — **chỉ đọc trang chính thức + 2 technical report**, không tải dữ liệu.
- Không GPU, không huấn luyện/inference, không AUC/F1/recall, không phát triển thuật toán, không chạm hồ sơ RQ3 đã khóa.

## 2. Phương pháp và mức bằng chứng
Mỗi dòng trong `source_evidence.csv` gắn một **mức bằng chứng** (cột `evidence_level`):

| Mức | Nghĩa |
|---|---|
| `DOC` | Tài liệu công khai (metadata Zenodo, README, summary table, luận văn, technical report) |
| `SAMPLE` | Tệp dữ liệu nhỏ thật: `conn.log.labeled` + `labels.config` + README của **đúng 2 device-day** (xem §3) |
| `REPO` | Bằng chứng trong repository này |

Mọi kết luận chỉ dựa trên ba mức trên. **Không flow nào ngoài 2 device-day mẫu được kiểm tra**; số liệu toàn dataset (99,993,509 flows…) lấy từ summary table/luận văn, không tự đếm lại.

## 3. Dữ liệu đã tải (tổng ≈ 28 MB mạng, < 100 MB)
| Mục | Kích thước | Ghi chú |
|---|---|---|
| Zenodo API JSON, README.md, summary_table.csv | ≈ 25 KB | MD5 khớp checksum Zenodo (README, summary) |
| Luận văn Bendl 2023 (DSpace CVUT, handle 10467/109254) | 5.56 MB | tải qua nhiều lần resume do timeout |
| Technical report CIDDS-001 (WISENT) và CIDDS-002 | 0.85 MB | |
| **HTTP Range 0–10 MiB** đầu archive `…Amazon-Echo1stGen-1….tar.bz2` | 10 MiB | giải nén phần tiền tố; lấy ngày 2023-02-22 |
| **HTTP Range 0–10 MiB** đầu archive `…Windows7AD-1….tar.bz2` | 10 MiB | idem |

Lưu ý ranh giới: **không tải archive nhiều GB**; chỉ lấy 10 MiB đầu của 2 archive để lấy mẫu nhỏ (trong tar, `zeek/` đứng trước `raw/*.pcap` của mỗi ngày). Tệp tạm nằm ngoài repo (scratchpad), không commit. SHA-256 các nguồn đã tải ghi ở `manifest_sha256.md`.

Không đọc tệp ngoài quyền truy cập (toàn bộ nguồn là open access / công khai). Một lần fetch DSpace bằng WebFetch trả trang rỗng (SPA) → dùng REST API công khai của DSpace.

## 4. Danh sách file đầu ra
`source_evidence.csv` · `ctu_label_taxonomy.md` · `schema_mapping.md` · `temporal_feasibility.md` · `dataset_decision_matrix.csv` · `limitations_and_unknowns.md` · `final_verdict.md` · `manifest_sha256.md`

## 5. Quy ước trạng thái
PASS / CONDITIONAL / FAIL / UNKNOWN. Không dùng số lượng family, hiệu năng mô hình hay AUC/F1 để ưu tiên nguồn. Không chọn nguồn theo kết quả thuận lợi: CIDDS được đánh giá bằng đúng thang như CTU.
