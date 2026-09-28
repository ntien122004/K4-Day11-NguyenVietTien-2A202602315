# Guideline patch

- **Rule mới đề xuất:** R12 — Ngưỡng chiều cao ở vùng edge cần xác minh trên ảnh gốc
- **Áp dụng cho:** class/zone — tất cả 6 class động khi nằm ở zone `edge` (r/R ≥ 0.6)
- **Vì sao luật hiện tại (`docs/02-rules-vi.md`) không đủ:** R01 quy định H=40 px nhưng không nói rõ cách đo khi ảnh fisheye méo mạnh ở rìa. Vật ở zone edge bị biến dạng hình học lớn, chiều cao pixel đo trên ảnh gốc có thể khác đáng kể so với chiều cao thực tế. Điều này dẫn đến annotator vẽ box cho vật trông cao ≥40 px nhưng thực chất là artifact của méo ống kính, gây ra nhiều SPURIOUS ở edge. Ví dụ: L3 ở adasind_014670.jpg và L6 ở adasind_034080.jpg đều là L_only SPURIOUS ở vùng edge.
- **`rules_version` mới:** v1.1.0
- **Hiệu lực từ:** round r2_qa trở đi (áp dụng ngay sau khi được lead phê duyệt)

## Nội dung rule R12 đề xuất

> **R12 — Xác minh H tại edge:** Khi đối tượng nằm ở zone `edge` (r/R ≥ 0.6), annotator phải xác minh chiều cao bằng cách zoom 100% trên ảnh gốc fisheye và đo pixel thực tế. Nếu chiều cao < 40 px sau khi zoom, không box. Nếu chiều cao ≥ 40 px nhưng hình dạng bị méo nghiêm trọng đến mức không phân biệt được class, gán `unknown` và escalate. Ghi note lý do vào findings nếu giữ hoặc loại box.

## Trước (quy tắc hiện tại)

> R01 — Ngưỡng chiều cao: vật cao ≥ 40 px (H=40, đo trên ảnh gốc) trong vùng hợp lệ phải có box. Vật thấp hơn không box — đây không tính là lỗi thiếu.

## Sau (quy tắc đề xuất bổ sung)

> R01 giữ nguyên. Thêm R12: Khi vật ở zone edge (r/R ≥ 0.6), annotator zoom 100% để xác minh H ≥ 40 trước khi vẽ box. Vật méo không phân biệt được class → `unknown` + escalate.
