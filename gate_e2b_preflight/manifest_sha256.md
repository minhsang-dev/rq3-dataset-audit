# Manifest SHA-256 — Gate E2b-PREFLIGHT

## Báo cáo đầu ra (không tự băm chính nó)
```
dd5558a54879e462c96af80bfe795fb66715a91052198d4fce87a7081002cb70  archive_access_options.md
56010b35086f01b43eaa0f8cfab5e5041904f5098a996fad2f4b3a6bbb4bb2b5  taxonomy_evidence_gaps.md
f85960d4abdfd7420afd21cd85174d4bdcb410f47c11ead53f08dd3099f800e3  minimal_data_acquisition_plan.md
b25ff1dcfffb2894a92fb437acc67caad43e2ee2da494c7d8efae96807f93e3b  label_anomalies_review.md
3493e2ce317cdb5f662d510ae37ffe20229a47cb4646898eaf335cd86f0c2726  final_go_no_go.md
```

## Xác minh manifest Gate E2 (đầu vào)
```
README.md: OK
source_evidence.csv: OK
ctu_label_taxonomy.md: OK
schema_mapping.md: OK
temporal_feasibility.md: OK
dataset_decision_matrix.csv: OK
limitations_and_unknowns.md: OK
final_verdict.md: OK
sha256 of gate_e2_outputs/manifest_sha256.md:
d36fbdaba3b7f418cdabd11feec01c977f868fd8d1ca5c2791fdff823bf34398  ../gate_e2_outputs/manifest_sha256.md
```

## Nguồn tải thêm trong preflight (nằm ngoài repo, không commit)
```
aedb605821222bf8a19493a7857b8027ebf941a6849bcb32c0d4f1dba1a1fce7  pf/labeler.pdf
87a57a8d37a5128565a245ebe6da9cf7cd457a7ee9d1754d9000323e3931d2dd  pf/versions.json
a4796d2bc05a7237ad02c61225ff9869e3a461d5421d24e393d9e4409028a2f9  pf/nlreadme.md
787f8f73175e7d10b93ef302f16ac16f8e8072c394afdf6401318a93c2f71ad7  pf/heads/CTU-SME-11_Experiment-VM-Linux-Ubuntu220.bin
001d5b5b7bc176bf0ed5fdbb4c53ea0afbe35f54e4e15e5756f5ceea70f5d2bd  pf/heads/CTU-SME-11_Experiment-VM-Microsoft-Windo.bin
24c8e1ee3f3a92e12905232b0ad69999f098223c787eae9268c438e8238844b9  pf/heads/CTU-SME-11_Hardware-Laptop-MacOS-Macbook.bin
2bdf781ed943b332f8170f2b2b5d026046e1a6e6394ec11dbee52f75f1da75f1  pf/heads/CTU-SME-11_Hardware-Smartphone-Android-N.bin
5b17f6beee20807519b5bc249d20ebf106b4c3922adf679e8b3e157eb677c0da  pf/heads/CTU-SME-11_Hardware-Smartphone-iOS-iPhon.bin
2830e8eed60ef6118219b398f7ec5684f9a139b699a5c36abf050ec1f2af7ab8  pf/heads/CTU-SME-11_Honeypot-Minicomputer-Raspber.bin
eb7432590a70d2c5158fcde4633ca8788a1c8e5426956f5fcee822704a29a5bc  pf/heads/CTU-SME-11_Honeypot-TVAssistant-Google-C.bin
5c5359c5e8a76d0e4de968179576f5e510a5243a06116b60c39b0fa533071999  pf/heads/w7full1_b.bin
97b617880dcaf852a139ddf54804cc2146152fdff56afafa512f40d9ccc79d82  pf/heads/w7full2.bin
8c862fabad756d851ba97c44ec8b7b22e690b496f6e2dbfe393f7cc02046f3ed  pf/heads/w7full3.bin
```

Tổng truyền preflight ≈ 12.8 MB (< 20 MB). Các tệp pf/probes/*.bin (3.6 MB) là các probe không thu được block hoàn chỉnh.
