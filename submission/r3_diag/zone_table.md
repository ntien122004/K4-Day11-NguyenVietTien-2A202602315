# Zone table (slice của bạn)

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 7 | 2 | 3 | 3 | 5 | SPURIOUS (2) |
| mid | 9 | 1 | 1 | 6 | 7 | WRONG_CLASS (1) |
| edge | 4 | 0 | 0 | 1 | 1 | ATTRIBUTE (1) |

## Nhận xét

- **L:** center gãy rõ nhất về số lỗi hình học/phạm vi: có 2 missing và 3 spurious, trong đó lỗi chính là `SPURIOUS (2)`. mid chỉ có 1 missing và 1 spurious; edge không có missing/spurious. **M:** mid là vùng kém ổn định nhất (6 missing và 7 thừa), kèm lỗi chính `WRONG_CLASS (1)`; center cũng có 3 missing và 5 thừa. edge ít lỗi đếm hơn (1 missing, 1 thừa) nhưng vẫn có một lỗi `ATTRIBUTE`, nên không thể xem là hoàn toàn đúng. Các số này chỉ mô tả slice và giữ nguyên kết quả do `model` tạo.
- Một số giả thuyết phù hợp với mẫu lỗi là méo fisheye và biến dạng hình học làm box ở vùng rìa/đối tượng chồng lấn khó khớp; box lỏng hoặc quá chặt có thể làm thay đổi kết quả ghép khi IoU đổi ngưỡng. Che khuất, cắt mép ảnh và bối cảnh phương tiện cũng có thể gây missing hoặc box thừa; QA mù đã nêu các trường hợp khó tách người với xe ba bánh, hai box `Bike` chồng lấn, và `truncated` ở mép ảnh. Việc thiếu dấu hiệu `ego_body`/ngữ cảnh xe trong slice cũng làm suy luận vị trí và thuộc tính kém chắc chắn. Tuy nhiên đây chỉ là các giả thuyết, không phải quan hệ nhân quả đã được kiểm chứng.
- Cần thận trọng khi tổng quát hóa: slice chỉ có ba frame và số mẫu mỗi zone nhỏ, nên một vài box có thể chi phối tỷ lệ. `center`/`mid`/`edge` chỉ là các dải theo **khoảng cách tương đối tới tâm vòng kính**; chúng không cho biết vật thể gần hay xa xe, cũng không cho biết đó là camera trước hay sau. Vì vậy không nên diễn giải zone như độ sâu, khoảng cách thực, hướng camera, hay mức khó cố định; cần thêm frame và thông tin hiệu chuẩn/ego để kiểm chứng.
