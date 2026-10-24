# Draft — câu hỏi gửi tác giả CTU-SME-11 (CHƯA GỬI)

**Trạng thái:** bản nháp, chờ phê duyệt gửi. Người nhận gợi ý (từ README Zenodo): stratosphere@aic.fel.cvut.cz; sebastian.garcia@agents.fel.cvut.cz.
Dạng gửi: tiếng Anh (bên dưới), kèm tóm tắt tiếng Việt cho người kiểm toán. Các số liệu đều từ dữ liệu phát hành công khai (Zenodo 10.5281/zenodo.7958259, v1.0.0), ngày 2023-02-22, 11 thiết bị.

---

Subject: Questions on label semantics and day structure in CTU-SME-11 v1.0.0 (doi:10.5281/zenodo.7958259)

Dear Dr. García, Dr. Valeros, and Mr. Bendl,

We are using CTU-SME-11 for a metadata audit (no detection results are reported or requested). From the public release and your thesis we have the following questions. Evidence: the `labels.config` and `conn.log.labeled` of the first day in each archive (2023-02-22).

**Q1 — Taxonomy of `detailedlabel`.** Is there a complete list (or generating grammar) of `detailedlabel` values, and what do the segments mean? In 2023-02-22 we see forms such as `Malware_C2-Trickbot`, `Malware-Octo-HTTP`, `Malware-PwnRing_miner-HTTP`, `Malware_C2-Ingress_tool_access-RemcosRAT-HTTP`, `Malware_data_exfiltration-RemcosRAT`, `Malware_C2_connection_attempt-DNS` and `Discovery`. Do these segments correspond to the levels of the ontology in arXiv:2305.01337 (technique, sub-technique, process, app-protocol)? Is there any official mapping from detailed labels to coarser attack categories?

**Q2 — Labeling procedure.** The `labels.config` of each day starts with a default block (`Unknown: srcIP=all` for desktop/mobile devices; `…-Discovery` with `srcIp=all` for the three public-IP devices), followed by IP-list rules. (a) Is the default-then-override reading correct (later rules overwrite earlier ones)? (b) How were the IP lists built and verified, and was any flow reviewed manually? (c) Were labels assigned after all captures were finished, and is there any record of when each IP/rule was confirmed?

**Q3 — Meaning of `Unknown`.** The thesis (Sec 9) defines `Unknown` as flows "confirmed not to be attacks", while the Zenodo description lists `benign, malicious or background`. In the data we only find `Benign`, `Malicious`, `Unknown` (no `background`), and `Unknown` matches the default `srcIP=all` rule. Is `Unknown` the residual of unmatched flows, and is it the same as the "background" class in the Zenodo description?

**Q4 — Naming and coverage inconsistencies for malware labels.** (a) The label is `Nukesped_miner` while Table 5.1 lists Nukesped as a RAT; the MacBook has a Nukesped rule but 0 flows on 2023-02-22 although Sec 7.7 states it was still running. (b) `PwnRing` (label) vs `PwnRig` (thesis); Ubuntu has 1 malicious flow on 2023-02-22. (c) Windows client VM1 runs two samples (NanoCore, RHADAMANTHYS) but has one unnamed malicious rule. (d) For VM3 on 2023-02-22 the label `Malware_data_exfiltration-RemcosRAT` covers 33,739 flows, all tcp/5888 and almost all `REJ`, while the thesis places the DNS exfiltration on day 4. (e) For Windows AD the first Trickbot-labeled flow is at 15:13:09 UTC while Sec 7.5 gives the execution at 16:15:51 CET. Could you clarify which of these are label-rule artefacts, naming errors, or expected behaviour?

**Q5 — iPhone malicious flows.** The summary table reports 1,031 malicious flows for Hardware-Smartphone-iOS-iPhone-Xs-1 (and 54 unknown), whereas Sec 7.8 states there was no malicious traffic on this device. On 2023-02-22 it has 0 malicious flows, so they fall on other days. Which days/rules produced them, and are they intended?

**Q6 — Time and day structure.** (a) Each daily capture appears to span ~24 h from the first packet (e.g. 00:00:02 to 23:59:5x UTC, up to 00:02 for the iPhone), not exactly 00:00–23:59:59 UTC as in Sec 6.3; is that correct? (b) README times are CET (UTC+1) and `ts` is UTC — is this the rule for all files? (c) Which exact device-days contain re-captured or time-shifted data: day 7 re-recorded on 2023-04-30 and shifted with `editcap` (Sec 12.4), and the VM-to-VM traffic of days 5–6 merged from VirtualBox traces for Win VM2, VM3 and AD (Sec 8.1, 12.2)? Is there a list of such files and a statement about label validity there?

Thank you; we will cite the dataset as indicated in the README.

---

## Tóm tắt tiếng Việt
| # | Chủ đề | Mục tiêu |
|---|---|---|
| Q1 | Taxonomy `detailedlabel` | Danh sách đầy đủ, nghĩa các đoạn, ánh xạ sang loại thô |
| Q2 | Quy trình gán nhãn | Quy tắc mặc định/ghi đè, xây danh sách IP, rà soát thủ công, thời điểm xác nhận |
| Q3 | `Unknown` | Phần dư mặc định hay "xác nhận không phải tấn công"; có tương đương `background` không |
| Q4 | Bất nhất tên/độ phủ malware | Nukesped, PwnRing/PwnRig, VM1, VM3 exfiltration, lệch 2 phút 42 giây của Trickbot |
| Q5 | iPhone 1,031 malicious | Ngày/quy tắc sinh ra, có chủ ý không |
| Q6 | Cấu trúc thời gian/ngày | Cửa sổ 24 h từ gói đầu, múi giờ, danh sách tệp tái ghi/dịch giờ |
