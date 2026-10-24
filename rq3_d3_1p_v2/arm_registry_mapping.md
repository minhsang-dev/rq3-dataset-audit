# Ánh xạ sáu arm lịch sử → lớp giao thức (CẦN NGƯỜI NGHIÊN CỨU ĐIỀN)

Dữ kiện xác minh: có **6 arm** trong Study 1. Cloud **không có** tên, định nghĩa hay cấu hình của 6 arm; tôi không suy đoán. Nguyên tắc: **không gộp, không loại, không đổi tên arm khi chưa có quyết định**; mọi arm đi vào mọi bảng với `arm_id` lịch sử của nó.

| # | historical_arm_id | protocol_class (`TABFM` / `LGBM_MATCHED` / `LGBM_FULL` / `OTHER`) | train_source_def | context/source rows | Cùng eval với các arm khác trong (t,s) | Ghi chú |
|---|---|---|---|---|---|---|
| 1 | _điền_ | _điền_ | _điền_ | _điền_ | Có (dữ kiện xác minh) | |
| 2 | _điền_ | _điền_ | _điền_ | _điền_ | Có | |
| 3 | _điền_ | _điền_ | _điền_ | _điền_ | Có | |
| 4 | _điền_ | _điền_ | _điền_ | _điền_ | Có | |
| 5 | _điền_ | _điền_ | _điền_ | _điền_ | Có | |
| 6 | _điền_ | _điền_ | _điền_ | _điền_ | Có | |

## Quy tắc áp dụng sau khi điền
1. Nhiều arm cùng `protocol_class` (ví dụ nhiều biến thể TabFM) **không được gộp**; so sánh theo từng cặp arm_id.
2. Lớp `OTHER` được báo cáo đầy đủ nhưng không tham gia cặp "primary" trừ khi người nghiên cứu chỉ định.
3. Hai arm chỉ được ghép cặp khi cùng `comparability_key` (đúng như đã xác minh) và `train_source_def` được ghi:
   - `TABFM` vs `LGBM_MATCHED`: cùng ngân sách ngữ cảnh (chỉ khi `LGBM_MATCHED` thật sự dùng đúng các hàng context của TabFM — cần xác nhận);
   - `TABFM`/`LGBM_MATCHED` vs `LGBM_FULL`: khác ngân sách thông tin, mô tả.
4. Cặp primary (D-A1) do người nghiên cứu chỉ định: _điền_.
5. Điểm TabFM là xác suất binary attack hợp lệ (dữ kiện xác minh); điểm của các arm còn lại phải qua J8 (trong [0,1], không NaN) — nếu không phải xác suất, không tính Brier/ECE cho arm đó (`NOT_A_PROBABILITY`) và không biến đổi.
