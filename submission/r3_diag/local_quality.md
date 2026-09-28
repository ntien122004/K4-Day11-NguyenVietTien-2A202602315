# Đối chiếu chất lượng cục bộ — rectangle

Teaching reference, không phải gold set đã phê duyệt; không có điểm đạt tự động.
Nguồn: export r1_craft đã khóa SHA256 `dae1cffc0509c9741e6032f1a084638bc49eac11c8ec81d8d9f8c5060ae1c93c`; slice `B1-edge`.
Ghép hình học greedy một-một theo IoU ≥ 0.50, rồi so class; H ≥ 40 px.
Box trái nằm chủ yếu trong ignore_region reference không tính. Polygon, polyline, track không được chấm.
Đây là phép tính offline của lab, không phải báo cáo hay kết quả tương đương CVAT Premium.

Frame được tính: adasind_001320.jpg, adasind_014670.jpg, adasind_034080.jpg. Frame thiếu trong export: không.
TP=17; FP=4; FN=3; số lần đối chiếu=23; mean IoU của TP=0.860.

| Chỉ số | Micro | Macro | Nhãn thấp nhất |
|---|---:|---:|---:|
| accuracy | 0.739 | 0.949 | 0.870 |
| precision | 0.810 | 0.744 | 0.000 |
| recall | 0.850 | 0.681 | 0.000 |
| jaccard | 0.708 | 0.621 | 0.000 |
| dice | 0.829 | 0.698 | 0.000 |

| Nhãn | TP | FP | FN | Accuracy | Precision | Recall | Jaccard | Dice |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Bike | 3 | 0 | 0 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 |
| Bus | 0 | 1 | 0 | 0.957 | 0.000 | 0.000 | 0.000 | 0.000 |
| Car | 3 | 1 | 1 | 0.913 | 0.750 | 0.750 | 0.600 | 0.750 |
| Pedestrian | 5 | 0 | 0 | 1.000 | 1.000 | 1.000 | 1.000 | 1.000 |
| ThreeWheeler | 5 | 2 | 1 | 0.870 | 0.714 | 0.833 | 0.625 | 0.769 |
| Truck | 1 | 0 | 1 | 0.957 | 1.000 | 0.500 | 0.500 | 0.667 |

| Frame | TP | FP | FN | Accuracy | Precision | Recall |
|---|---:|---:|---:|---:|---:|---:|
| adasind_001320.jpg | 6 | 0 | 0 | 1.000 | 1.000 | 1.000 |
| adasind_014670.jpg | 4 | 2 | 1 | 0.667 | 0.667 | 0.800 |
| adasind_034080.jpg | 7 | 2 | 2 | 0.636 | 0.778 | 0.778 |

Confusion matrix: hàng = teaching reference; cột = export đã khóa.
`<missing>` là thiếu box; `<extra>` là box thừa. Xem `local_quality_confusion.csv`.

| Reference \ Export | Bike | Bus | Car | Pedestrian | ThreeWheeler | Truck | <missing> |
|---|---:|---:|---:|---:|---:|---:|---:|
| Bike | 3 | 0 | 0 | 0 | 0 | 0 | 0 |
| Bus | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| Car | 0 | 0 | 3 | 0 | 0 | 0 | 1 |
| Pedestrian | 0 | 0 | 0 | 5 | 0 | 0 | 0 |
| ThreeWheeler | 0 | 0 | 0 | 0 | 5 | 0 | 1 |
| Truck | 0 | 1 | 0 | 0 | 0 | 1 | 0 |
| <extra> | 0 | 0 | 1 | 0 | 2 | 0 | 0 |

Chi tiết xung đột trong `local_quality_conflicts.csv`; dữ liệu máy đọc trong `local_quality.json`.
Mismatching label đóng góp một FP cho class vẽ và một FN cho class reference; attribute khác được báo riêng.
Micro accuracy đếm mỗi cặp ghép sai class là một lần đối chiếu; Jaccard đếm cả FP và FN.
Macro/worst bỏ nhãn không xuất hiện ở cả hai phía; chỉ số không có mẫu là N/A.
