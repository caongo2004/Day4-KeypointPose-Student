# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 28 skeleton, trung bình 16.18 khớp có v > 0 mỗi người
- Tổng: v=2 366 | v=1 87 | v=0 23

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 22 | 6 | 0 | 21% |
| 1 | left_eye | 21 | 7 | 0 | 25% |
| 2 | right_eye | 22 | 6 | 0 | 21% |
| 3 | left_ear | 18 | 10 | 0 | 36% |
| 4 | right_ear | 21 | 7 | 0 | 25% |
| 5 | left_shoulder | 26 | 2 | 0 | 7% |
| 6 | right_shoulder | 27 | 1 | 0 | 4% |
| 7 | left_elbow | 23 | 5 | 0 | 18% |
| 8 | right_elbow | 25 | 3 | 0 | 11% |
| 9 | left_wrist | 22 | 6 | 0 | 21% |
| 10 | right_wrist | 24 | 3 | 1 | 11% |
| 11 | left_hip | 26 | 2 | 0 | 7% |
| 12 | right_hip | 24 | 4 | 0 | 14% |
| 13 | left_knee | 19 | 6 | 3 | 21% |
| 14 | right_knee | 18 | 7 | 3 | 25% |
| 15 | left_ankle | 15 | 5 | 8 | 18% |
| 16 | right_ankle | 13 | 7 | 8 | 25% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
