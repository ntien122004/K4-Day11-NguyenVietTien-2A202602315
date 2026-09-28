# Escalation ticket

## Ticket 1 — Nhầm class Bus/Truck tại vùng edge

- **Frame:** adasind_014670.jpg
- **Ảnh chụp:** `submission/screenshots/escalation_bus_truck_class.png`
- **Expected impact:** Nếu không giải quyết, mọi thống kê precision/recall của class Bus và Truck ở zone edge bị sai lệch. Reference ghi R5 là Truck, annotator (L2) và model (M3) ghi Bus. Sai class ảnh hưởng confusion matrix và khiến model training nhận tín hiệu sai về phân bố Bus/Truck tại vùng méo fisheye.
- **Owner:** qa — cần người soát xem crop gốc ở zoom 100% và so sánh hình dáng phương tiện với ảnh R04 để quyết định class đúng. Nếu R04 không đủ phân biệt trong trường hợp fisheye méo, chuyển tiếp cho `guideline` để bổ sung hướng dẫn.
- **Recommendation:** Mở ảnh gốc adasind_014670.jpg, zoom vào vùng chứa đối tượng L2/R5/M3. So sánh kích thước tương đối (Bus thường dài và cao hơn Truck bán tải) và hình dáng cabin. Nếu xác nhận được class, cập nhật reference R5 hoặc annotator L2. Nếu ảnh méo quá mạnh không phân biệt được, ghi rule mới R12 (xem `20_guideline_patch.md`) và gán E2_guideline_gap.

## Ticket 2 — Model bỏ sót nhiều đối tượng mà L và R đồng thuận (LR_noM)

- **Frame:** adasind_001320.jpg, adasind_014670.jpg, adasind_034080.jpg
- **Ảnh chụp:** `submission/screenshots/ped_disappear_finding.png`
- **Expected impact:** Có ít nhất 8 ca LR_noM (annotator và reference đều có box nhưng model không detect). Nếu model này được dùng làm pre-label cho batch tiếp theo, tỷ lệ missing sẽ cao ở các đối tượng nhỏ hoặc bị occlude ở vùng edge/mid. Ảnh hưởng recall toàn bộ pipeline nếu không được phát hiện sớm.
- **Owner:** ai_team — cần kiểm tra recall của YOLO26m trên tập ADASIND fisheye, đặc biệt với ThreeWheeler và Bike ở vùng edge. Có thể do model gốc huấn luyện trên ảnh phẳng (E4_model_domain), nhưng cần nhiều ca hơn ba frame để kết luận.
- **Recommendation:** Thu thập recall theo class × zone trên ít nhất 20 frame ADASIND. Nếu recall < 0.7 ở class cụ thể tại edge, cân nhắc fine-tune model trên ảnh fisheye hoặc hạ confidence threshold cho pre-label. Không dùng model output làm proxy cho quality check ở vùng edge cho đến khi có bằng chứng recall đủ cao.
