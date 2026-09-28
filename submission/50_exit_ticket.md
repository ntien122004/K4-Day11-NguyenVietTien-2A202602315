# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy
   tắc riêng? Vì sao?

   **Trả lời:** Đó **không phải** lỗi DUPLICATE — đó là trường hợp hợp lệ cần một quy tắc riêng. Lý do:

   - Hai box nằm trong **hai không gian ảnh khác nhau** (mỗi box thuộc camera riêng), nên chúng không "trùng" theo nghĩa DUPLICATE trên cùng một ảnh. DUPLICATE là khi hai box cùng camera, cùng frame, bao cùng một đối tượng — như L4+L5 ở adasind_034080.jpg (nhưng thực tế L4 khớp R8, L5 khớp R5 → hai vật khác nhau, không phải duplicate).
   - Theo `docs/10-svm360-reading-vi.md`, policy cho seam annotation là: (1) **giữ cả hai box riêng biệt**, mỗi box thuộc camera gốc; (2) **liên kết bằng cùng `object_id`** để hệ thống biết đó là cùng một vật vật lý; (3) **timestamp phải khớp** (frame đồng bộ < 5ms); (4) **cần calibration extrinsic** để xác minh hai box chỉ cùng một đối tượng.
   - Nếu coi hai box seam là DUPLICATE và xóa một, hệ thống mất thông tin quan sát từ một camera → ảnh hưởng BEV stitching và cross-camera tracking. Quy tắc riêng cần quy định rõ: giữ cả hai, link ID, và dùng calibration để verify.

   Ảnh minh chứng: `submission/screenshots/escalation_bus_truck_class.png` (minh họa overlay multi-source).

2. Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái
   Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera.

   **Trả lời:**

   **Trên cùng một camera:**
   - **Giữ cùng track ID** khi đối tượng vẫn quan sát được liên tục qua các frame, kể cả khi bị occlude tạm thời (< 95%) rồi hiện lại. Theo R-06 (identity consistency), mỗi đối tượng vật lý có **duy nhất một** `object_id` trong toàn bộ sequence. Sau occlusion, không tạo ID mới — giữ ID cũ.
   - **Thêm keyframe** khi có thay đổi đáng kể: đối tượng xuất hiện lần đầu, thay đổi thuộc tính (occluded → visible, truncated thay đổi), kích thước/vị trí thay đổi lớn.
   - **Đánh dấu Outside=true** ở keyframe cuối khi đối tượng rời hoàn toàn FOV (R-04). Không xóa track — chỉ đánh dấu trạng thái.

   **Ba điều kiện tối thiểu để nối track qua hai camera:**
   1. **Timestamp liên tục:** Frame cuối (Outside=true) trên camera cũ và frame đầu (xuất hiện) trên camera mới phải liền nhau hoặc chồng trong vùng seam. Cần đồng bộ timestamp < 5ms giữa hai camera.
   2. **Spatial consistency qua calibration:** Vị trí dự đoán (projected position) của đối tượng khi chiếu từ camera cũ sang camera mới bằng extrinsic calibration phải khớp với vị trí box mới (sai lệch ≤ ngưỡng cho phép, ví dụ 20 px).
   3. **Appearance / class match:** Cùng loại đối tượng (class match), kích thước hợp lý (box size consistent sau projection), và ngoại hình tương tự (màu sắc, hình dáng không thay đổi đột ngột).

   Ví dụ trong bài: Finding `r3_diag, adasind_014670.jpg, L2+M3, LM_noR` cho thấy tầm quan trọng của identity consistency — nếu L2 và R5 là cùng một đối tượng nhưng khác class, không thể nối track mà cần resolve class trước.

3. Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`),
   bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm?

   **Trả lời:**

   **Bất đồng:** Ở frame `adasind_034080.jpg`, tôi giữ hai box L4 và L5 (hai Bike) mặc dù chúng chồng nhau đáng kể. QA round ghi nhận `DUPLICATE` vì overlap cao. Tuy nhiên, khi đối chiếu với reference, L4 match R8 và L5 match R5 — hai đối tượng Bike riêng biệt đứng cạnh nhau.

   **Cách xử lý:** Tôi ghi finding `r2_qa, B1-edge, adasind_034080.jpg, L4+L5, L_only, DUPLICATE` với action `keep_with_reason`, giải thích rằng "each matches a different reference object. Overlap alone is not evidence of duplication." Đồng thời ghi vào decision log (entry #5) với status `resolved`.

   **Nếu làm lại:** Tôi sẽ thêm bước **kiểm tra overlap ratio** trước khi kết luận DUPLICATE: nếu hai box chồng > 50% IoU nhưng match hai reference object khác nhau, đó là hai vật cạnh nhau, không phải trùng lặp. Tôi cũng sẽ ghi rõ hơn trong findings cột `evidence` với IoU giữa L4-L5 và IoU giữa L4-R8, L5-R5 để người review sau có đủ số liệu. Ngoài ra, tôi sẽ đọc kỹ D-06 (consecutive frame illusion) sớm hơn để tránh coi frame liền nhau là mẫu độc lập khi phân tích pattern lỗi.

   Ảnh minh chứng: `submission/screenshots/ped_disappear_finding.png`.
