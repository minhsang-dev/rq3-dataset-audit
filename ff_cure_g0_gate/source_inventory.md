# Nguồn cho G0 — kiểm kê và provenance

**Kết quả: bảng family × day của Gate M KHÔNG có trong không gian làm việc Cloud → `HOLD_MISSING_INPUT`.**

## Đã tìm ở đâu
| Vị trí | Kết quả |
|---|---|
| Repository `minhsang-dev/rq3-dataset-audit`, nhánh `claude/sleepy-cray-jhn01z` (HEAD 78509ad, đã fetch origin) | 9 thư mục báo cáo do Claude tạo; **không có** CSV/Parquet/JSON nào chứa count theo family × day. Tìm theo tên và nội dung (`gate_m`, family/day, Attack count): không có |
| Gói bàn giao `claude_hybrid_handoff.zip` (SHA-256 179fd164…53; 18/18 tệp khớp manifest) | Chỉ có trích dẫn verbatim `final_verdict.md` của Gate M trong file 13 (nguồn `D:\Personal\PhD\IUH\rq3_gate_m_metadata_20261024\final_verdict.md`, SHA-256 `58f634e85c210b1ad24437401af832b6c7582ee20e10cf2c1143cc756809d56d`) |
| Hệ thống tệp phiên Cloud (tìm theo tên `gate_m`, family/day, metadata matrix) | Không có |

## Gate M cho biết gì (mức tổng hợp — không đủ để đếm)
| Mục | Giá trị trong trích dẫn | Đủ cho G0? |
|---|---|---|
| Dataset/phiên bản | NF-ToN-IoT-v3 (27.520.260 dòng, 7 ngày UTC, 9 family thô; Development), NF-CICIDS2018-v3 (20.115.529 dòng, 11 ngày UTC, 14 family thô; Development), NF-UNSW-NB15-v3 (2.365.424 dòng, 3 ngày; exploratory stress-test, NOT CLEAN). Archive SHA khớp tham chiếu; member CSV có SHA-256 | Định danh có; count không |
| Định nghĩa family/benign | `Attack` raw (family) và `Label` (1 = attack) theo prereg Study 1 §2; 21 dataset-day đối soát family/attack/benign PASS | Định nghĩa có; bảng không |
| Count family × day | **Không có**, chỉ có câu tổng hợp "A qua ngày lịch min20 = 8 families (ToN), 11 (CIC)" | **Không** — không được suy count từ đây |
| Thứ tự thời gian / transition | 12 transition Study 1 (file 07 §2, file 10): ToN 2019-04-23→…→04-29; CIC 2018-02-14→15, 15→16, 16→20, 20→21, 28→03-01, 03-01→03-02 | Có |
| Vai trò | ToN/CIC = Development (đã phơi nhiễm); UNSW không sạch | Có |
| Ngưỡng low-volume/recurrence khóa trước | Không có trong Gate M; trong `new_method_research_design/` có ký hiệu `n_hist`, `q` nhưng **chưa có giá trị** | Xem `eligibility_definition.md` |
| Đường dẫn bảng chi tiết | Gate M nhắc "matrix" và "metadata report" (`xem matrix`) nằm trong thư mục local `D:\Personal\PhD\IUH\rq3_gate_m_metadata_20261024\` | Tên tệp chính xác và hash: **UNKNOWN** |

Không tạo, đoán hay suy count. `family_recurrence_counts.csv` **không được tạo**.
