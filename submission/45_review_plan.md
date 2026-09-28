# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| Zone edge, SPURIOUS (L_only) — frame adasind_014670.jpg, adasind_034080.jpg | 5 ca SPURIOUS (L3, L6, M6, M7 ở 014670; L6, M7–M12 ở 034080) | Vùng edge tập trung nhiều box thừa nhất do méo fisheye. Nếu annotator vẽ box cho vật dưới H=40 hoặc trong ignore_region, toàn bộ FP rate của zone edge bị thổi phồng, ảnh hưởng precision report | Crop 100% tại vị trí mỗi box SPURIOUS; polygon ignore_region và lens_border overlay; chiều cao pixel đo trên ảnh gốc |
| Zone edge, WRONG_CLASS và MISSING kết hợp — frame adasind_014670.jpg (L2+R5 Bus/Truck) và adasind_034080.jpg (R9 ThreeWheeler) | 3 ca WRONG_CLASS + MISSING tại edge, trong đó 1 ca class mâu thuẫn giữa L, R và M | Class confusion ở vùng méo ảnh hưởng confusion matrix và recall/precision theo class. ThreeWheeler dễ bị nhầm với Truck/Bus; Bus dễ nhầm Truck do hình dáng tương tự khi bị biến dạng. Sai class ảnh hưởng training data | Ảnh crop zoom gốc kèm so sánh R04 mapping; confusion matrix từ local_quality_confusion.csv; overlay compare.html |

Giới hạn của kết luận từ ba frame ADASIND: chỉ có ba frame từ một camera, một slice (B1-edge). Phân bố lỗi SPURIOUS và WRONG_CLASS tập trung ở edge có thể do đặc điểm riêng của slice này chứ không đại diện cho toàn bộ ADASIND hay bốn camera thật. Không thể suy ra tỷ lệ lỗi cho camera khác (front/rear/left/right) vì mỗi camera có vị trí lắp, FOV và đặc điểm méo khác nhau. Các kết luận chỉ là giả thuyết cần kiểm trên nhiều slice và nhiều camera hơn.

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv`:

1. **Kiểm tra coverage theo risk:** Mỗi dòng trong sampling plan có cột `risk`. Đối chiếu risk với hard case thực tế của từng camera (glare cho front/rear, fisheye distortion cho left/right, seam cho mọi cặp camera liền kề). Nếu có hard case không nằm trong bất kỳ dòng nào → sampling plan thiếu coverage.

2. **Tránh đếm frame liền nhau là mẫu độc lập:** Theo D-06 (consecutive frame illusion, docs/08-degrade-vi.md), 10 frame liên tiếp của cùng cảnh chỉ là 1 ca. Khi phân bổ frame, ghi rõ mỗi dòng là frame từ **scene khác nhau**, không phải frame liên tiếp. Ví dụ: 30 frame cho front-hard nên là 30 frame từ ít nhất 10 scene khác nhau (mỗi scene lấy tối đa 3 frame).

3. **Giới hạn của kế hoạch giả lập:** 200 frame từ 50.000 chỉ là 0.4% — kế hoạch giúp **tìm ca cần soi**, chưa **đo được tỷ lệ lỗi** thực tế. Để ước lượng tỷ lệ lỗi với confidence interval hẹp, cần sample size lớn hơn nhiều (ít nhất vài nghìn frame). Kế hoạch 200 frame phù hợp cho pilot review và phát hiện vùng rủi ro cao, không phải cho statistical quality estimation.
