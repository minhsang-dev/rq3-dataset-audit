# Final verdict — **GO_FOR_PILOT_PROTOCOL**

**Ngày:** 2026-10-25. Chỉ thiết kế nghiên cứu: không code, không chạy mô hình, không tải dữ liệu, không fitting, không sửa artifact LOCKED.

## Đề xuất được khuyến nghị (duy nhất)
- **Bài toán:** bộ nhớ context của TFM với nhãn nhị phân có thể quên họ tấn công khối lượng thấp nhưng tái xuất hiện.
- **Baseline:** CURE (arXiv:2606.18677v1, FMSD@ICML 2026, có mã, backbone TabICL-v2).
- **Cải tiến (FF-CURE):** khi long bank tràn, chọn nạn nhân theo **khóa họ tấn công** (hạn ngạch water-filling, floor `m_floor` cho mỗi họ đã thấy). Tìm cặp dư thừa **trong cùng họ**. Nhãn ICL vẫn nhị phân; admission, short bank, `ϕ` và quy tắc centroid giữ như CURE.

## Vì sao có thể tốt hơn
CURE và DualFIFO cân bằng theo y nhị phân nên không thấy họ. Cặp gần nhất "liên họ" có thể loại dòng họ hiếm. Floor cộng tìm cặp trong họ giữ lại bằng chứng về họ hiếm. Đây là giả thuyết, chưa có bằng chứng nhân quả; witness quyết định khác CURE được nêu trong `baseline_and_prior_art.md`.

## Thí nghiệm bác bỏ được
Endpoint FAM_AP_excess trên họ tái xuất hiện khối lượng thấp, theo cặp seed, theo transition, guard AP nhị phân, so với CURE ở cùng M, luồng nhãn và backbone. Có các ablation A1–A4 (khóa không floor, CBRS-family, cụm không giám sát, CURE đa lớp). Có điều kiện dừng âm ở G0, pilot và xác nhận.

## Vì sao GO cho pilot protocol, không phải cho xác nhận
- **Không có nguồn xác nhận sạch được xác minh**. Ứng viên CIDDS-001 và CTU-SME-11 đều còn điều kiện mở. Claim cải tiến **chỉ** có thể đưa ra sau khi có nguồn đó.
- Chưa kiểm được dev có đủ họ tái xuất hiện khối lượng thấp không (G0).
- Rủi ro tương đương còn lại: CBRS-family (A2) và CURE đa lớp (A4). Pilot được thiết kế để phát hiện.
- Mọi kết quả chỉ offline/oracle vì không có timestamp xác nhận nhãn.

## Một bước tiếp theo
Người nghiên cứu duyệt soạn **pilot protocol** khóa toàn bộ tham số, với cổng đầu tiên là **G0**: đếm từ bảng họ × ngày của Gate M (chỉ metadata, không mô hình) số transition dev có họ tái xuất hiện khối lượng thấp đủ điều kiện.
