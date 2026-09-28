# QA review · B1-edge

Mã khóa: DAE1-CFFC

| frame | object_ref | rule_id | nhận xét |
|---|---|---|---|
| 1 (adasind_001320.jpg) | L1 | R03 | Box `Pedestrian` chồng lên `ThreeWheeler` L4; ảnh chưa cho thấy rõ đây là người đi bộ tách biệt hay người ngồi trên xe. Theo R03, người ngồi trong phương tiện khác không gán nhãn riêng; cần kiểm tra lại box này. |
| 2 (adasind_014670.jpg) | L6 | R05 | Box `Car` chạm mép phải ảnh (x=1080) nhưng `truncated=false`; kiểm tra xem xe có bị khung hình cắt không và bật `truncated` nếu có. |
| 3 (adasind_034080.jpg) | L4 + L5 | R03 | Hai box `Bike` chồng lấn nhiều; kiểm tra chúng là hai xe riêng hay đang tách người lái và xe thành hai box. R03 yêu cầu người lái cùng xe hai bánh nằm trong một box `Bike`. |


