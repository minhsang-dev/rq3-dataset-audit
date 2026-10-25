# Kiểm toán prior art — HINT, hierarchical inference, cascade, selective prediction, learning to defer, budgeted routing

**Phương pháp tìm kiếm (giới hạn):** arXiv API (truy vấn theo tiêu đề/abstract), Crossref API, công cụ tìm kiếm web; đọc toàn văn PDF arXiv khi ghi rõ. Không truy cập trực tiếp IEEE Xplore/ACM/Scopus/Google Scholar. Kết quả **không phải** systematic review; "không tìm thấy" không có nghĩa là không tồn tại. Danh mục đầy đủ (37 dòng, có mức xác minh) ở `prior_art_matrix.csv`.

Mức xác minh: **FULL_TEXT_READ** (đọc toàn văn) · **FULL_TEXT_PARTIAL** (đọc các phần nêu rõ; chứng minh không kiểm) · **ABSTRACT_METADATA_ONLY** · **SEARCH_SNIPPET_OR_CROSSREF_ONLY**.

## 1. Định danh HINT
README không ghi tiêu đề/tác giả của "HINT". Tôi chỉ chấp nhận một khớp khi đúng chủ đề *edge → routing chọn lọc → cloud TFM*:

| Ứng viên | Định danh | Khớp? |
|---|---|---|
| **HINT — Hierarchical Inference with Neighborhood Transfer.** Vítor Crista*, Afonso Lourenço* (đóng góp ngang nhau), Diogo Martinho, Goreti Marreiros (GECAD, Polytechnic of Porto). "Streaming Hierarchical Inference with Tabular Foundation Models" | arXiv:**2609.07956v1**, nộp 7/9/2026; lịch sử nộp chỉ có **v1**; comment arXiv: *Streaming Continual Learning ECML PKDD Workshop 2026*; không có DOI; cs.LG | **Có — được chọn.** Đọc toàn văn (PDF v1, 12 trang) |
| HintKD (Luo, Zhang, Xu) | arXiv:2610.08412v1 (6/10/2026) | Gần chủ đề (cloud–edge) nhưng là knowledge distillation; không phải "HINT"; chưa đọc abstract |
| HINT: Hierarchical Invertible Neural Transport | arXiv:1905.10687 | Không |
| HINT: Healthy Influential-Noise based Training | arXiv:2309.08549 | Không |

**Cần người nghiên cứu xác nhận** HINT mà họ muốn nói là arXiv:2609.07956v1. Nếu là bài khác, phần so sánh HINT dưới đây phải làm lại.

### HINT làm gì (đọc toàn văn)
- **Edge:** bộ nhớ sliding-window L=1000 trên đồ thị kNN xấp xỉ (SWINN/NN-Descent, K=20). Dự đoán là vote có trọng số khoảng cách; độ tin cậy `p(x)=s_ŷ(x)`.
- **Routing:** offload nếu `p(x) < θ`, ngược lại chấp nhận cục bộ.
- **Context transfer:** gửi query kèm `k(t)` láng giềng truy xuất ở edge làm context ICL cho TabPFN ở cloud. Có hai bộ phân loại cục bộ song song (k cố định kmin=3 và k(t) theo lịch), chọn theo accuracy tích lũy.
- **Chi phí:** proxy `β(1+k_t)+η_i` nếu offload, `γ_i` nếu xử lý cục bộ. Tác giả nói rõ đã bỏ qua latency, năng lượng, bộ nhớ, batching và runtime.
- **Đánh giá:** prequential (test rồi update, nhãn lộ ngay); 6 stream USP, 20k instance đầu; quét θ∈{0,…,1}, k∈{5,10,20,40}; θ, k **không tối ưu online** (để công việc sau). Báo cáo accuracy/balanced accuracy và Pareto accuracy–chi phí. Không IDS, không FPR/recall/ngân sách cảnh báo, không LightGBM; không thấy link mã nguồn trong văn bản.

### Hệ quả cho hybrid đề xuất
Cấu trúc "edge cho dự đoán + độ tin cậy → offload chọn lọc → cloud TFM với context" **đã được công bố** (HINT). Thay kNN bằng LightGBM là thay mô-đun edge, không tự là tính mới. Phần HINT **không** bao phủ: bối cảnh IDS mất cân bằng, chỉ tiêu FPR/recall/ngân sách cảnh báo, độ trễ nhãn/thu nhãn từ analyst, ngưỡng lịch sử bị chuyển dịch dưới drift theo ngày, và LightGBM đã có context trùng TabFM. Đây là khác biệt **bối cảnh/đánh giá**, chưa phải khác biệt cơ chế.

## 2. Các nhóm prior art và điều chúng đã chiếm chỗ
| Nhóm | Đại diện (định danh) | Đã chiếm chỗ cho ý tưởng |
|---|---|---|
| Hierarchical inference (HI) | Moothedath et al. TMLCN 2024 (2304.00891); Behera et al. IoTJ 2025 (2407.11061); Al-Atat 2023 (2304.11763); Chattopadhyay 2025 (2508.08985); multi-layer HI với feedback một phần (2603.04247) | Ngưỡng offload trên độ tin cậy edge; học ngưỡng online không cần nhãn đầy đủ; đo latency/năng lượng thật |
| HI nhị phân bất đối xứng chi phí | **H2T2**, Moothedath et al. AAAI 2026 (2509.15674v3; đọc phần mở đầu/bài toán) | **Band hai ngưỡng** `θl ≤ f < θu` để offload; quy tắc đóng (tương đương Chow khi chi phí bằng nhau); học online regret O(T^{2/3}) với feedback giới hạn; bền dưới shift (theo tác giả) |
| Cascade deferral | Jitkrittum et al. NeurIPS 2023 (2307.02764v2; đọc phần lý thuyết); FrugalGPT; Gupta et al. 2024; Conformal Cascade (2607.25018) | Quy tắc deferral Bayes-optimal; confidence-deferral thất bại khi model sau là specialist, nhãn nhiễu hoặc **distribution shift**; deferral post-hoc; đảm bảo conformal |
| Selective prediction | Geifman & El-Yaniv 2017/2019; Gangrade et al. NeurIPS 2021 (feedback giới hạn); Chow (reject option) | Coverage–risk; reject option; online selective với nhãn một phần |
| Learning to defer | Madras 2018; Mozannar & Sontag 2020; Verma & Nalisnick 2022; Okati 2021; **DeCCaF** (2403.06906; capacity + chi phí bất đối xứng); budgeted deferral (2510.26706) | Defer tới expert/người có capacity, cost-sensitive, budget truy vấn |
| Risk control | LTT (2110.01052), CRC (2208.02814), ACI dưới shift (2106.00170) | Chứng nhận nhiều rủi ro, reject option, điều chỉnh mức dưới shift — gói đã chỉ ra C1 ≡ joint-risk LTT (file 13) |
| IDS edge→cloud | **Simioni et al.** IoTJ 2025 (DOI 10.1109/JIOT.2025.3544060; early-exit DNN, reject option, temperature scaling, offload); CECN-DNN (Comp. Networks 2026); DQN offloading (ESWA 2022) | Hybrid IDS edge–cloud theo độ tin cậy **đã có** (chỉ xác minh qua metadata/abstract) |
| Deferral/triage ở SOC | L2DHF (2506.18462); risk certification không suy biến (2608.12444) | Defer tới analyst với học từ feedback; certificate loại trừ all-abstain |
| TFM làm scorer IDS | García et al. Electronics 2025; 2605.02519 | TabPFN/TabICL cho IDS (benchmark) |
| TFM trên stream/context | Tab-AICL (2603.27385; active in-context learning); TFM on streams (2610.05352; giữ recent ngang các kỹ thuật quản lý bộ nhớ, có delayed labels); Lourenço et al. (2512.11668) | Thu nhãn để tối ưu context TFM; context refresh theo recency |
| Drift trong security với ngân sách analyst | Chen, Ding, Wagner USENIX Sec 2023; Transcend/Transcendent | Chọn mẫu cho analyst gán nhãn dưới drift; reject dựa conformal |

## 3. Kiến trúc đã biết và cải thiện thực chất
| Thành phần của hybrid đề xuất | Đã biết? | Ghi chú |
|---|---|---|
| Edge model rẻ (LightGBM) + cloud model mạnh (TabFM/TabICL) | Có (HI, HINT, Simioni) | Đổi mô-đun ≠ tính mới |
| Offload khi độ tin cậy thấp | Có (HI, HINT) | |
| Band hai phía, chi phí FN>FP | Có (H2T2) | |
| Ngưỡng theo ngân sách / top-K | Có (alert budget, quantile threshold; HI với ràng buộc chi phí trung bình — được H2T2 trích Beytur et al.) | |
| Học ngưỡng online dưới drift | Có (HI-learning, H2T2, ACI) | |
| Defer tới người có capacity | Có (DeCCaF, L2DHF) | |
| Chứng nhận rủi ro/không suy biến | Có (LTT/CRC; 2608.12444) | C1 NO-GO |
| Chuyển context tới cloud TFM | Có (HINT) | |
| Thu nhãn cho context TFM | Có ở dạng tĩnh (Tab-AICL) | Kết hợp với routing + nhãn trễ chưa thấy trong phạm vi tìm |

**Kết luận phần này:** gói "LightGBM edge → routing → TabFM cloud" như một kiến trúc **không** phải đóng góp mới. Một cải thiện thực chất chỉ có thể nằm ở chỗ **cơ chế quyết định khác về mặt kỹ thuật** dưới ràng buộc IDS mà baseline mạnh tương ứng không biểu diễn được với cùng dữ liệu, giả định và ngân sách. Xem `03_candidate_specifications.md`.

## 4. Giới hạn của kiểm toán này
- Chỉ HINT được đọc toàn văn. H2T2, Jitkrittum và Tab-AICL đọc một phần. Còn lại chỉ dựa trên abstract/metadata/snippet; các con số kết quả của bài khác **không** được xác minh.
- Chưa tra IEEE Xplore/ACM để tìm hybrid IDS khác; có thể tồn tại công trình IDS edge–cloud gần hơn.
- Ngày công bố lấy từ lịch sử nộp arXiv/Crossref, không lấy dòng ngày trong PDF.
