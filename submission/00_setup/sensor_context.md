# Sensor context

- Rig: Camera fisheye gắn trên nóc xe (roof-mounted), hướng xuống dưới và ra phía trước/sau tùy camera. ADASIND là dữ liệu tổng hợp từ một camera fisheye duy nhất với FOV khoảng 190°. Xe ego là loại sedan/SUV, camera lắp ở vị trí trung tâm phía trên kính chắn gió (quan sát từ vùng ego_body ở đáy frame).
- `ego_body` nhìn thấy ở phần đáy frame: nắp capo (hood) của xe chiếm khoảng 15-20% diện tích dưới cùng, hai bên có thể thấy gương chiếu hậu. Ở 46/48 frame ADASIND có thân xe nhìn thấy; hai frame ngoại lệ (006840, 271039) không thấy thân xe.
- Vòng kính (lens circle) gần như chiếm toàn bộ khung hình, tâm vòng kính nằm gần tâm ảnh. Vành đen ngoài vòng kính (lens_border) chiếm khoảng 5-10% diện tích ở các góc ảnh. Ảnh có độ phân giải 1080×1080 (vuông), vòng kính có bán kính khoảng 500-520 px tính từ tâm.
