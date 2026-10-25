# Xác minh gói bàn giao và ràng buộc áp dụng

## 1. Toàn vẹn gói
| Mục | Kết quả |
|---|---|
| ZIP | `ad20bd98-claude_hybrid_handoff.zip`, 274.764 byte, SHA-256 `179fd1640697e2854cef5f8cc366db8aed46f495df9876c688c2d8bc57dcd553` (do tôi tính; không có giá trị tham chiếu bên ngoài để so) |
| Manifest | 18/18 tệp (trừ chính manifest) khớp SHA-256 trong `manifest_sha256.md`; manifest tự nó không có digest nội bộ (đúng như gói nêu) |
| Nội dung | 15 tài liệu + 4 tệp hướng dẫn/toàn vẹn = 19 tệp |
| Phạm vi | SHA kiểm tra byte, **không** kiểm tra đúng đắn khoa học hay phê duyệt (gói tự nêu) |

## 2. Thứ tự đọc đã thực hiện
README_FOR_CLAUDE → 02, 06, 10, 11, 13 (đọc đủ) → 07, 08, 09 (đọc đủ), 14 (chỉ header + số dòng: 1.620 hàng so sánh + 1 header; không phân tích kết quả) → 01, 03, 04, 05, 12, 15 (đọc có chọn lọc: 01 các đoạn RQ/hybrid; 04 phần gap analysis; 05 các phần model identity/thiết bị/failure; 12 đọc đủ; 15 chỉ phần mở đầu và dòng định danh, vì README nói không phải hybrid pipeline).

## 3. Quyết định đã khóa — giữ nguyên, không đảo
| Đối tượng | Giữ |
|---|---|
| D3-1E | PARTIAL; historical BLOCK không đổi (AP=1.0000000000000002 tại NF-CICIDS2018-v3_2018-02-15_7_S3, không clip, không dung sai) |
| H1/H2 | SUPPORTED_DESCRIPTIVELY, đọc hẹp (ranking vượt reference không bảo đảm recall dương tại ngưỡng lịch sử); H2 có ở cả LightGBM và arm cloud |
| D3-C1 | NO-GO cho claim phương pháp |
| RQ1 novelty | NO-GO hiện tại; C2 HOLD |
| Thesis | PATH B |
| P1 evaluation | DRAFT_UNAPPROVED/HOLD; **không phải** hybrid pipeline |
| Nhãn nhạy | 487 nhãn rounding-sensitive; 3.186 seed-sign disagreements; 42 dấu chưa chứng nhận; NULL/LOW_SUPPORT/INDETERMINATE giữ nguyên |
| RQ2/RQ4 | DoP NO-GO Level 2/3; DA-FAR đóng, kết quả âm giữ |

## 4. Quy ước và nhãn gói đã áp dụng
- Delta file 14 = LightGBM − arm cloud, mỗi arm dùng ngưỡng lịch sử riêng; **không phải** matched-FPR confirmation; cột `TabFM_arm` trong CSV lịch sử có thể là **S2-ICL = TabICL**, không phải TabFM.
- S2/S1-ctx và S2-ICL/S1-ctx là so sánh recent-context cùng context; S3 mixed không cùng context; S0/S1 là full/static với thông tin huấn luyện khác. Ánh xạ: S0=OTHER, S1=LGBM_FULL, S1-ctx=LGBM_MATCHED, S2=TABFM, S3=TABFM(mixed), S2-ICL=OTHER(TabICL) (file 08).
- Không join phép đo tài nguyên RQ1 (CPU full-context, ARM cloud) với chất lượng RQ3 (GPU L4, recent context 10.000 hàng) thành một deployment profile; ARM cloud không phải bo mạch nhúng; cùng checkpoint không bảo đảm cùng runtime/context/precision.
- Không tuyên bố alerts/giờ hay ngày từ mẫu 10.000 hàng; seed và eval có chồng lấn (1.260 cặp seed-overlap, J13), không độc lập.

## 5. Điều gói không chứa (xác nhận)
Không có dữ liệu thô NetFlow, trọng số mô hình, cache, vector dự đoán từng flow. Phần "đang thiếu": latency end-to-end của hybrid, overhead mạng, profile tài nguyên+chất lượng gộp, timestamp xác nhận nhãn, xác nhận độc lập sạch, hybrid được chứng thực (`missing_evidence.md`). Đây là phủ định trong phạm vi tìm kiếm của gói, không phải chứng minh không tồn tại ở nơi khác.

## 6. Ghi chú về bản giao thức D3-1P trong Cloud
File 07 (D3 protocol v2 final candidate) cho biết **O6, O7, O10 là ba quyết định REVISE** — điều mà báo cáo Cloud `rq3_d3_1p_v2/O1_O10_resolution.md` trước đó không biết. Khớp với cách Cloud v2 đã xử lý O6 (giải quyết bằng dữ kiện), O7 (false alert theo giờ-ngày DROPPED), O10 (không dung sai, hiệu cặp theo seed). Không cần sửa Cloud v2 trong nhiệm vụ này; không đổi gì ở artifact đã khóa.
