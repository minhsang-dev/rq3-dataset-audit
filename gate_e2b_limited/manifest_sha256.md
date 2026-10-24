# Manifest SHA-256 — Gate E2b-LIMITED

## Báo cáo đầu ra (không tự băm chính nó)
```
750bf23d8d801b2e19b01b6d7bf85e8caa74068118e8ee46da83f922fe0d24cb  raw_label_inventory.csv
a3a21ef6cd2aa4cffd6e284862d15d57fd4da0c9b58c813bb3adf59b81ce5dc2  device_day_label_counts.csv
550a69098083dc69fd96b0acd5f45fa5940a65fb79b6245eeeea576218e3c76f  summary_reconciliation.csv
baf6cadb4d2d6a3aa9bf568e34ebaa6d38834ab99c09c6a0dc05428708c97a73  taxonomy_consistency_audit.md
906b68f3c0820eaef42acf2acd1cb0c032cbebcfc895764d102d322a373dbe50  network_bytes_and_access_log.csv
618bf38659b3fd8b0d2304ab79f7488b52f1595466d607a900324853babad4b4  author_questions_draft.md
3162136beb3b344953a92ee73a5652d7f981c92e295d484691d5746b430d8b49  limitations_and_blockers.md
b2d252809c1fef66ff5aa0184308ba37b6bfc4d239aa9ff2d93a6bc4dfb481ad  final_verdict.md
```

## Xác minh manifest đầu vào (HEAD 76c2e2e)
```
README.md: OK
source_evidence.csv: OK
ctu_label_taxonomy.md: OK
schema_mapping.md: OK
temporal_feasibility.md: OK
dataset_decision_matrix.csv: OK
limitations_and_unknowns.md: OK
final_verdict.md: OK
archive_access_options.md: OK
taxonomy_evidence_gaps.md: OK
minimal_data_acquisition_plan.md: OK
label_anomalies_review.md: OK
final_go_no_go.md: OK
```

## Dữ liệu đầu vào thực sự dùng (nằm ngoài repo, không commit)
SHA-256 của `conn.log.labeled`, `labels.config` và tiền tố nén đã tải của từng archive nằm trong `summary_reconciliation.csv` (cột `conn_log_labeled_sha256`, `labels_config_sha256`, `input_prefix_sha256`).

Công cụ: `fetch_extract.py`, `analyze.py` (scratchpad, không commit).
