# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 14.34 khớp có v > 0 mỗi người
- Tổng: v=2 334 | v=1 82 | v=0 77

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 22 | 1 | 6 | 3% |
| 1 | left_eye | 20 | 3 | 6 | 10% |
| 2 | right_eye | 20 | 3 | 6 | 10% |
| 3 | left_ear | 12 | 12 | 5 | 41% |
| 4 | right_ear | 14 | 11 | 4 | 38% |
| 5 | left_shoulder | 25 | 4 | 0 | 14% |
| 6 | right_shoulder | 28 | 1 | 0 | 3% |
| 7 | left_elbow | 23 | 4 | 2 | 14% |
| 8 | right_elbow | 25 | 2 | 2 | 7% |
| 9 | left_wrist | 20 | 5 | 4 | 17% |
| 10 | right_wrist | 19 | 6 | 4 | 21% |
| 11 | left_hip | 20 | 8 | 1 | 28% |
| 12 | right_hip | 21 | 7 | 1 | 24% |
| 13 | left_knee | 19 | 3 | 7 | 10% |
| 14 | right_knee | 18 | 4 | 7 | 14% |
| 15 | left_ankle | 15 | 3 | 11 | 10% |
| 16 | right_ankle | 13 | 5 | 11 | 17% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
