# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| unknown |  |  | 40 |

## Top defects

## Phân tích của bạn

Hai bảng trên do `python3 lab11.py card` tính từ `findings.csv`; chạy lại lệnh sẽ cập nhật bảng và giữ nguyên mục này. Viết cho lỗi nổi bật nhất, dẫn frame/`object_ref`.

### 1. SPURIOUS — box thừa không có trong reference hoặc model

- **Nguyên nhân:** Các box L_only (chỉ annotator thấy) tập trung ở vùng edge, nơi méo fisheye khiến vật nhỏ gần ngưỡng H=40 bị phóng đại hoặc bị biến dạng hình học. Annotator dễ vẽ box cho vật thực ra dưới ngưỡng hoặc nằm trong ignore_region. Ví dụ: `adasind_014670.jpg` L3 và `adasind_034080.jpg` L6 đều là L_only SPURIOUS ở vùng edge.
- **Cách sửa:** Trước khi vẽ box ở vùng edge, đo chiều cao pixel thực tế; nếu dưới 40 px thì không box. Owner: `annotator` cho lỗi vẽ thừa, `guideline` nếu R01 chưa đủ rõ ràng cho vùng méo.
- **Bằng chứng:** Findings dòng `r1_craft, B1-edge, adasind_014670.jpg, L3, na, SPURIOUS` và `r1_craft, B1-edge, adasind_034080.jpg, L6, na, SPURIOUS`. Ảnh minh chứng: `submission/screenshots/escalation_bus_truck_class.png`.

### 2. MISSING — box reference có mà annotator bỏ sót

- **Nguyên nhân:** Các trường hợp R_only (chỉ reference có) cho thấy annotator bỏ sót đối tượng, thường ở vùng edge hoặc bị occluded một phần. Ví dụ: `adasind_034080.jpg` R9 (ThreeWheeler chỉ reference thấy) và nhiều ca R_only ở r3_diag.
- **Cách sửa:** Dùng checklist mục 7 (thiếu/trùng) trước khi khoá, quét đặc biệt vùng edge. Owner: `annotator`.
- **Bằng chứng:** Findings dòng `r1_craft, B1-edge, adasind_034080.jpg, R9, na, MISSING`.

### 3. WRONG_CLASS — nhầm class phương tiện

- **Nguyên nhân:** Nhầm Bus/Truck với ThreeWheeler hoặc Car/Truck do hình dạng tương tự trên ảnh fisheye méo ở rìa. R04 yêu cầu ánh xạ chính xác nhưng vùng méo khiến phán đoán khó. Ví dụ: `adasind_014670.jpg` L2+R5 sai class giữa Bus và Truck.
- **Cách sửa:** Khi nghi ngờ class ở vùng edge, xem nhiều frame liền kề; dùng hướng di chuyển và kích thước tương đối để phân biệt. Nếu chưa rõ, dùng E5_unresolved và escalate. Owner: `annotator` hoặc `qa`.
- **Bằng chứng:** Findings dòng `r3_diag, B1-edge, adasind_014670.jpg, L2+M3, LM_noR, SPURIOUS` (Bus vs Truck). Ảnh: `submission/screenshots/ped_disappear_finding.png`.
