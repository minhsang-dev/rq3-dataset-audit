# Temporal context cache — Cloud feasibility gate: **HOLD_ACCESS**

**Ngày:** 2026-10-25. Kiểm toán read-only.
- Không GPU, không inference, không training, không tải dữ liệu.
- Không sửa báo cáo LOCKED.
- Không mở lại K1–K3, DA-FAR, Sequential DoP hay RQ3-A.

## Vì sao HOLD_ACCESS
Theo Step 0 bắt buộc: các tệp triển khai thiết yếu đều **MISSING** trong Cloud, gồm wrapper TabFM/TabICL, `run_fm.py`, runner Study 1/2, mã nguồn gói đúng phiên bản và log thời gian theo pha. Hồ sơ K1–K3 cũng MISSING. Danh sách cần chuyển nằm trong `access_inventory.md`.

## Điều đã xác định được mà không cần mã (để người nghiên cứu cân nhắc trước khi chuyển tệp)
- **Khác biệt kỹ thuật: NONE** trên bằng chứng hiện có:
  - Context recent liên tiếp **không chung bản ghi**, vì là các ngày UTC rời nhau.
  - Tái dùng trong một context **đã có sẵn**: TabICL `kv_cache` (đã chạy với `False`) và TabPFN `fit_with_cache`. Bật chúng không phải tính mới.
  - Cập nhật tăng dần chính xác dưới attention hai chiều cần đổi kiến trúc hoặc huấn luyện lại, đã bị loại ở §P-B.
- Có mã cũng chỉ giúp kiểm được giao diện cache của Google TabFM và quy tắc mất hiệu lực cache. Các kiểm tra đó **khó có thể** làm xuất hiện một khác biệt kỹ thuật, vì tiền đề "context chung bản ghi qua thời gian" không đúng trong workflow chính.

## Một bước tiếp theo
Người nghiên cứu chuyển từ máy cục bộ năm mục trong `access_inventory.md`, hoặc tối thiểu là mục 1–3: `run_fm.py`, runner Study 1/2 và mã gói đúng phiên bản. Nếu không chuyển được, hồ sơ hiện tại chỉ ủng hộ việc đóng hướng này dưới dạng NO-GO, chứ không ủng hộ mở protocol.
