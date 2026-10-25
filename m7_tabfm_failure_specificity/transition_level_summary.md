# Tổng hợp theo transition (S2 TabFM vs S1-ctx LightGBM, cùng context, mỗi arm dùng ngưỡng lịch sử riêng)

Đơn vị là transition; seed là lặp đo (cùng tập eval cho hai arm, các seed chồng lấn nhau). Không p-value; không gộp flow.

| Transition | n seed | P trung vị | Ngưỡng: cả hai / chỉ TabFM / chỉ LGBM / không | Ranking fail (AUC≤0,5 hoặc AP≤π): TabFM / LGBM | AUC trung vị LGBM / TabFM | AP trung vị LGBM / TabFM | Recall@τ trung vị LGBM / TabFM | FPR trung vị LGBM / TabFM |
|---|---|---|---|---|---|---|---|---|
| CIC 2018-02-14→2018-02-15 | 15 | 606 | 0 / 0 / 0 / 15 | 0 / 0 | 0.944 / 0.999 | 0.800 / 0.991 | 0.036 / 0.591 | 0.0000 / 0.0000 |
| CIC 2018-02-15→2018-02-16 | 15 | 1163 | 0 / 0 / 0 / 15 | 0 / 0 | 1.000 / 1.000 | 0.997 / 1.000 | 0.485 / 0.486 | 0.0002 / 0.0000 |
| CIC 2018-02-16→2018-02-20 | 15 | 1577 | 15 / 0 / 0 / 0 | 0 / 13 | 0.372 / 0.998 | 0.210 / 0.972 | 0.000 / 0.000 | 0.0000 / 0.0001 |
| CIC 2018-02-20→2018-02-21 | 15 | 3831 | 0 / 0 / 1 / 14 | 0 / 0 | 0.995 / 1.000 | 0.978 / 1.000 | 0.004 / 0.275 | 0.0002 / 0.0000 |
| CIC 2018-02-28→2018-03-01 | 15 | 496 | 0 / 0 / 0 / 15 | 0 / 0 | 0.837 / 0.850 | 0.409 / 0.488 | 0.329 / 0.381 | 0.0066 / 0.0038 |
| CIC 2018-03-01→2018-03-02 | 15 | 1129 | 5 / 0 / 8 / 2 | 12 / 4 | 0.564 / 0.479 | 0.156 / 0.121 | 0.000 / 0.003 | 0.0008 / 0.0009 |
| ToN 2019-04-23→2019-04-24 | 15 | 2842 | 0 / 0 / 0 / 15 | 0 / 0 | 0.566 / 0.603 | 0.486 / 0.539 | 0.256 / 0.260 | 0.0038 / 0.0032 |
| ToN 2019-04-24→2019-04-25 | 15 | 4612 | 0 / 0 / 0 / 15 | 10 / 9 | 0.433 / 0.396 | 0.492 / 0.471 | 0.123 / 0.084 | 0.1184 / 0.0793 |
| ToN 2019-04-25→2019-04-26 | 15 | 5566 | 0 / 0 / 0 / 15 | 0 / 0 | 0.989 / 0.992 | 0.989 / 0.993 | 0.974 / 0.978 | 0.0486 / 0.0479 |
| ToN 2019-04-26→2019-04-27 | 15 | 9054 | 0 / 0 / 0 / 15 | 0 / 0 | 0.766 / 0.790 | 0.961 / 0.967 | 0.781 / 0.772 | 0.3813 / 0.3295 |
| ToN 2019-04-27→2019-04-28 | 15 | 3445 | 0 / 1 / 0 / 14 | 4 / 4 | 0.605 / 0.587 | 0.386 / 0.359 | 0.072 / 0.008 | 0.1471 / 0.0260 |
| ToN 2019-04-28→2019-04-29 | 15 | 490 | 0 / 0 / 0 / 15 | 0 / 0 | 0.989 / 0.989 | 0.981 / 0.977 | 0.937 / 0.931 | 0.0002 / 0.0000 |

## Tổng số theo cặp (transition, seed), 180 cặp
| | Cả hai | Chỉ TabFM | Chỉ LightGBM | Không | Không kết luận được |
|---|---|---|---|---|---|
| Threshold failure (recall@τ = 0) | 20 | 1 | 9 | 150 | 0 |
| Ranking failure (AUC ≤ 0,5 hoặc AP ≤ π) | 14 | 12 | 16 | 138 | 0 |
H1 witness (xếp hạng vượt tham chiếu nhưng recall = 0): TabFM 18, LightGBM 12.

## Tính nhất quán giữa các transition
- **Threshold failure** chỉ có ở **4/12** transition:
  - CIC 02-16→02-20: cả hai, 15/15 seed.
  - CIC 03-01→03-02: cả hai 5, chỉ LightGBM 8.
  - CIC 02-20→02-21: chỉ LightGBM, 1 seed.
  - ToN 04-27→04-28: chỉ TabFM, 1 seed.
  - Có **28/30** cặp có threshold failure nằm ở hai transition CIC.
- **Ranking failure** chỉ có ở **4/12** transition:
  - CIC 02-16→02-20: chủ yếu LightGBM (13 vs 0).
  - CIC 03-01→03-02: chủ yếu TabFM (12 vs 4).
  - ToN 04-24→04-25: cả hai (10 vs 9).
  - ToN 04-27→04-28: cả hai (4 vs 4).
- **Không có hướng nhất quán:** ở hai transition có khác biệt lớn, arm thất bại xếp hạng đổi chiều (LightGBM ở 02-16→02-20, TabFM ở 03-01→03-02).
- 8/12 transition không có threshold failure nào ở cả hai arm.
