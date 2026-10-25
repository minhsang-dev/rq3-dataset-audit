# Nguồn cho G0 (lần chạy lại) — kiểm kê và provenance

Lần trước: G0_HOLD vì thiếu bảng family × day. Lần này dùng ZIP do người nghiên cứu cung cấp: `ff_cure_g0_inputs.zip`, SHA-256 `a5f34a1c2df55a884c6909d97e61c50e3faf8fc49bea14a640a6a93f718f1068`, 8 tệp. Chỉ dùng dữ liệu trong ZIP; không sao chép dữ liệu nguồn vào repo.

## Toàn vẹn
`manifest_sha256.md` trong ZIP là manifest đầu ra gốc của Gate M (31 tệp). 7 tệp có trong ZIP **khớp** SHA-256 và kích thước trong manifest đó:
- daily_family_counts.csv (37.362 B, 2eb44326…)
- transition_family_status.csv (fe75f516…)
- day_coverage_and_gaps.csv (c25e26fd…)
- count_reconciliation.csv (042028bf…)
- dataset_identity_and_hashes.csv (da114112…)
- metadata_quality_report.md (ff3604a0…)
- source_evidence_and_scope.md (4f3fe24d…)

`final_verdict.md` của Gate M (58f634e8…) có trong manifest và trùng hash với bản trích trong gói handoff (file 13). Manifest không tự băm; mức tin cậy giới hạn ở chuỗi manifest do người nghiên cứu cung cấp.

## Kiểm tra trước khi đếm
| Mục | Kết quả |
|---|---|
| Dataset identity | NF-ToN-IoT-v3 (archive SHA b3b6256e…, member SHA 53ec8f46…, 27.520.260 dòng); NF-CICIDS2018-v3 (7399bdef…, 242a6971…, 20.115.529 dòng; tương đương chính thức với NF-CSE-CIC-IDS2018-v3 **UNVERIFIED**); NF-UNSW-NB15-v3 (ngoài phạm vi). Archive SHA trùng với Study 1 (file 07) |
| Ngày UTC | `utc_day` từ `FLOW_START_MILLISECONDS` (epoch ms UTC). 7 ngày ToN, 11 ngày CIC. Mọi ngày gắn cờ `BOUNDARY_OR_PARTIAL_OBSERVED_DAY`, `complete_monitored_day_verified=False` |
| Taxonomy | Cột `Attack`; Benign + 9 họ ToN, Benign + 14 họ CIC. Mỗi (dataset, ngày) liệt kê đủ danh mục họ (kể cả n = 0) → danh mục nhất quán giữa các ngày trong cùng dataset |
| Định nghĩa | Benign: n_attack = 0; mọi họ khác: n_benign = 0 (đã kiểm từng dòng); `n_invalid_label` = 0 ở mọi dòng |
| Mẫu số | Tổng theo ngày = `day_total`; tổng attack của họ = `source_attack_n`; benign khớp; **21/21 dataset-day đối soát PASS** với count_reconciliation.csv |
| Transition | 12 transition Study 1 (file 07 §2). Ngày giữ lại theo prereg là ≥ 100.000 dòng → CIC 2018-02-27 (828 dòng) bị loại khỏi lịch sử. CIC 02-16→02-20 cách 3 ngày lịch, giữ nguyên |
| Thời điểm xác nhận nhãn | `confirmation_time_column = NOT FOUND` ở cả ba nguồn |
