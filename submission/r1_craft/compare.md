# So sánh L với R

Chỉ số L/R là thứ tự box cao ≥ H=40 trong từng frame, theo thứ tự XML; bắt đầu từ 1.
Box L trong ignore_region được báo IGNORE_SCOPE, không tính SPURIOUS.

## adasind_001320.jpg
## adasind_014670.jpg
- L6+R1 edge ATTRIBUTE
- L2+R5 mid WRONG_CLASS
- L3 center SPURIOUS
## adasind_034080.jpg
- L3+R1 mid ATTRIBUTE
- L6 center SPURIOUS
- L7+R2 center BOX_GEOMETRY
- R9 center MISSING

## Theo zone
| zone | n_ref | matched | missing | spurious |
|---|---|---|---|---|
| center | 7 | 5 | 2 | 3 |
| mid | 9 | 8 | 1 | 1 |
| edge | 4 | 4 | 0 | 0 |
