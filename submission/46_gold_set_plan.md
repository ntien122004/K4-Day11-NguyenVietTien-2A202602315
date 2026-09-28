# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. "Gold set" ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front | Glare ngược sáng (sun behind objects), xe chuyển qua seam front-left/front-right, xe sát đầu (< 2m) | Glare làm mất viền đối tượng → box lỏng; xe ở seam xuất hiện trên 2 camera → cần quyết định giữ 2 box hay gộp; xe sát đầu bị truncated nặng | Intrinsic (focal, distortion) của camera front; extrinsic (vị trí/hướng so với thân xe); homography cho BEV; timestamp đồng bộ < 5ms với các camera khác | 2 reviewer độc lập annotate riêng, so sánh IoU ≥ 0.7 và class agreement; bất đồng giải quyết bởi senior reviewer xem ảnh gốc + overlay |
| rear | Mặt đường ướt phản chiếu tạo ghost, bóng đổ dài giống đối tượng thật, xe lùi vào frame | Phản chiếu tạo FP (model và người đều dễ nhầm ghost là xe thật); bóng đổ dài tạo SPURIOUS; xe lùi nhanh khó track | Intrinsic + extrinsic camera rear; homography; timestamp; thêm LiDAR depth nếu có để xác minh ghost vs real object | 2 reviewer xem cả ảnh gốc và BEV projection; case phản chiếu cần xem frame liền kề (vật thật di chuyển, ghost không) |
| left | Xe sát hông trái (< 1m) gây fisheye distortion cực mạnh, người đi bộ ở seam left-front, xe đạp bị occluded bởi xe đỗ | Distortion lớn → box tight trên ảnh gốc rất khác box trên BEV; người ở seam cần 2 box linked identity; xe đạp nhỏ bị che dễ miss hoặc sai occluded attribute | Intrinsic + extrinsic camera left; vòng kính (lens border) chính xác; calibration extrinsic để chiếu sang camera front và rear cho seam | 2 reviewer kiểm box trên ảnh fisheye gốc (không undistort); case seam cần reviewer thứ 3 kiểm cross-camera consistency |
| right | Xe sát hông phải, bóng đổ phía tây chiều tối, ThreeWheeler/Bike nhỏ ở rìa | Tương tự left nhưng thêm bóng đổ dài phía tây; ThreeWheeler nhỏ ở edge dễ nhầm class (Bus/Truck) do méo; Bike nhỏ gần ngưỡng H=40 | Intrinsic + extrinsic camera right; vòng kính; calibration để chiếu sang front/rear | 2 reviewer; case ThreeWheeler cần zoom 100% và so sánh R04 mapping; case bóng cần xem frame liền kề |

## Người review độc lập và giải quyết bất đồng

- **Reviewer:** Hai annotator có kinh nghiệm ≥ 3 tháng annotate riêng biệt, không xem nhãn của nhau (blind review).
- **Cách giải quyết bất đồng:** So sánh IoU và class agreement. Nếu IoU < 0.7 hoặc class khác nhau, cả hai reviewer mở ảnh gốc cùng lúc và thảo luận với senior lead. Senior lead quyết định cuối cùng dựa trên rule (R01–R11) và ảnh crop zoom 100%. Nếu rule không đủ để phân xử, ghi vào guideline patch và gán E2_guideline_gap.
- **Threshold agreement:** Gold set chỉ gồm frame có ≥ 90% agreement (IoU ≥ 0.7 cho tất cả box, 100% class match). Frame dưới threshold cần review vòng 3 hoặc loại khỏi gold.

## Khi nào cần refresh gold set

- Khi **đổi camera hardware** (sensor mới, vị trí lắp thay đổi) → intrinsic và extrinsic thay đổi, gold set cũ không còn đại diện.
- Khi **cập nhật rule/guideline** (ví dụ thêm R12 về H tại edge) → annotation criteria thay đổi, gold set cần re-annotate theo rule mới.
- Khi **thêm class mới** vào taxonomy → cần bổ sung gold set cho class mới.
- Khi **thay đổi calibration pipeline** → homography và projection thay đổi, ảnh hưởng BEV và cross-camera matching.
- **Định kỳ:** Mỗi 6 tháng hoặc sau mỗi 50.000 frame mới, lấy mẫu ngẫu nhiên để kiểm gold set còn đại diện.

## Một ca seam cần policy cross-camera

Một chiếc xe (Car) đang đỗ ở góc trước-trái của ego vehicle, nằm trong vùng seam giữa camera front và camera left. Trên camera front, xe này có bounding box B1 ở vùng edge-right. Trên camera left, cùng xe có bounding box B2 ở vùng edge-front. Hai box có hình dạng khác nhau do góc nhìn và mức méo fisheye khác nhau.

**Policy:**
1. **Giữ cả hai box B1 và B2** — mỗi box thuộc không gian ảnh riêng của camera gốc, không gộp.
2. **Liên kết bằng cùng `object_id`** — cả B1 và B2 mang identity `car_42` để hệ thống biết đó là cùng một xe vật lý.
3. **Timestamp phải khớp** — B1 và B2 từ cùng timestamp (frame đồng bộ < 5ms).
4. **Xác minh bằng calibration extrinsic** — chiếu B1 từ camera front sang tọa độ camera left bằng ma trận extrinsic; vị trí projected phải khớp B2 trong sai số cho phép (≤ 20 px sau projection).
5. **Trước khi gọi là gold:** Cần reviewer thứ 3 (không phải 2 reviewer camera riêng) kiểm cả B1 và B2 cùng lúc, xác nhận cùng xe vật lý, cùng timestamp, và projection consistent.

## Vì sao peer agreement trên ảnh một camera chưa chứng minh gold set đúng cho cả bốn camera

Peer agreement (hai reviewer đồng ý trên cùng ảnh một camera) chỉ chứng minh consistency **trong không gian ảnh của camera đó**. Nó không kiểm tra:
- **Cross-camera identity:** Cùng vật trên hai camera có được gán cùng ID không.
- **Seam consistency:** Hai box ở seam có khớp qua calibration projection không.
- **Camera-specific bias:** Mỗi camera có đặc điểm riêng (vị trí lắp, FOV, mức méo) → lỗi ở camera left không nhất thiết xuất hiện ở camera right.
- **Temporal consistency qua camera:** Track nối từ camera này sang camera kia cần timestamp và spatial consistency, không chỉ label agreement.

Do đó, gold set cho bốn camera cần review **cross-camera** bổ sung, không chỉ dựa vào quality report từng camera riêng lẻ.
