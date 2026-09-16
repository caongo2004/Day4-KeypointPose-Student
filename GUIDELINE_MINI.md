# Mini guideline - nhóm: ______ | người gán: ______ | ngày: ______

## 1. Luật bắt buộc

* Bộ 17 điểm COCO, đúng tên, đúng thứ tự.
* Mọi người trong ảnh đều có đủ 17 điểm. Điểm không dùng được thì gắn cờ, không xoá.
* Trái/phải tính theo cơ thể người, không theo bức ảnh.
* Bị che nhưng vẫn còn trong khung ảnh → `v = 1`, vẫn đặt chấm tại vị trí ước lượng.
* Ra ngoài mép ảnh → `v = 0`, không đặt chấm.
* Không sử dụng `Hidden (h)`.

---

## 2. Luật của nhóm

| Tình huống                                       | Luật nhóm chọn                                                                                                                                                                                                                        | Vì sao                                                                                                                     |
| ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Hông của người mặc quần áo dài                   | Nếu vị trí khớp hông bị quần áo che nhưng vẫn có thể ước lượng dựa trên tư thế cơ thể thì đặt keypoint tại vị trí giải phẫu ước lượng và gán `v=1`.                                                                                   | Khớp vẫn nằm trong ảnh nhưng bề mặt khớp không nhìn thấy trực tiếp. Cần giữ vị trí để model học cấu trúc cơ thể.           |
| Tai bị tóc hoặc mũ bảo hiểm che một phần         | Nếu vẫn nhìn thấy một phần tai hoặc xác định được vị trí tai từ đầu/khuôn mặt thì đặt điểm và chọn `v=1`. Nếu tai nhìn thấy rõ thì `v=2`.                                                                                             | Cần thống nhất ranh giới giữa visible và occluded để tránh hai người gán cùng tình huống với visibility khác nhau.         |
| Người bị cắt ở mép ảnh, chỉ thấy từ hông trở lên | Các keypoint vẫn nằm trong ảnh được gán bình thường. Các điểm đầu gối và mắt cá chân nằm ngoài khung ảnh được giữ trong bộ 17 điểm nhưng gán `v=0` và không đặt chấm.                                                                 | Phân biệt rõ điểm ra ngoài ảnh với điểm bị vật thể che trong ảnh.                                                          |
| Cổ tay nằm sau tay lái / sau thân mình           | Nếu cổ tay vẫn nằm trong khung hình nhưng bị tay lái hoặc cơ thể che thì ước lượng vị trí khớp, đặt chấm và gán `v=1`.                                                                                                                | Đây là occlusion chứ không phải outside. Nếu dùng `v=0`, keypoint đó sẽ không còn được sử dụng như một vị trí khớp hợp lệ. |
| Hai người chồng lên nhau                         | Gán riêng đủ 17 keypoint cho từng người. Khớp của người phía sau bị người phía trước che nhưng vẫn ước lượng được thì dùng `v=1`. Không lấy keypoint của người phía trước làm keypoint của người phía sau.                            | Tránh trộn skeleton của hai người và giúp model học đúng quan hệ keypoint của từng instance.                               |
| Người nhỏ đến mức nào thì không gán nữa          | Không đặt một ngưỡng pixel tùy ý. Nếu người vẫn được xác định là một person theo phạm vi bài và có thể xác định skeleton một cách hợp lý thì vẫn gán. Chỉ bỏ khi guideline chung của bài quy định đối tượng đó nằm ngoài phạm vi gán. | Tránh mỗi người tự chọn một ngưỡng kích thước khác nhau gây thiếu instance.                                                |

**Ảnh mẫu:** chèn một screenshot CVAT cho từng tình huống trên, đặc biệt các trường hợp `v=1` mà vị trí khớp phải được ước lượng.

---

## 3. Ba ca mơ hồ đã gặp

### Ca 1 

* **Mơ hồ ở chỗ nào:** Tai trái bị tóc/mũ che khá nhiều, chỉ nhìn thấy một phần nhỏ nên khó quyết định `v=1` hay `v=2`.
* **Bạn quyết thế nào:** Đặt keypoint tại vị trí giải phẫu của tai trái và chọn `v=1`.
* **Vì sao:** Tai vẫn nằm trong ảnh và vị trí có thể ước lượng được, nhưng không nhìn thấy rõ toàn bộ vùng khớp.
* **Nếu người khác quyết ngược lại thì model học sai cái gì:** Visibility của cùng một dạng che khuất sẽ không nhất quán. Model có thể học rằng một tai bị che vẫn được xem như hoàn toàn visible.

### Ca 2 

* **Mơ hồ ở chỗ nào:** Cổ tay nằm phía sau tay lái hoặc thân người và gần như không nhìn thấy trực tiếp.
* **Bạn quyết thế nào:** Ước lượng vị trí cổ tay từ cẳng tay và tư thế cơ thể, đặt keypoint và chọn `v=1`.
* **Vì sao:** Cổ tay vẫn nằm trong khung ảnh; chỉ bị vật thể khác che.
* **Nếu người khác quyết ngược lại thì model học sai cái gì:** Nếu sử dụng `v=0`, model có thể học nhầm rằng keypoint bị che là keypoint không tồn tại/ở ngoài ảnh thay vì là một khớp bị occluded.

### Ca 3 

* **Mơ hồ ở chỗ nào:** Phần chân nằm sát mép ảnh, khó xác định mắt cá chân đang bị che hay thực tế đã ra ngoài khung.
* **Bạn quyết thế nào:** Nếu vị trí giải phẫu của mắt cá đã nằm ngoài mép ảnh thì gán `v=0` và không đặt chấm. Nếu mắt cá vẫn nằm trong ảnh nhưng bị vật thể che thì đặt điểm ước lượng và dùng `v=1`.
* **Vì sao:** Cần phân biệt rõ `outside` và `occluded`.
* **Nếu người khác quyết ngược lại thì model học sai cái gì:** Model sẽ nhận tín hiệu visibility không nhất quán và có thể học sai cách xử lý keypoint gần mép ảnh.

---

## 4. Sau khi so visibility report với bạn cùng nhóm

Visibility report của tôi có:

* `left_ear`: `%v=1 = 36%`
* `left_eye`: `%v=1 = 25%`
* `right_ear`: `%v=1 = 25%`
* `right_knee`: `%v=1 = 25%`
* `right_ankle`: `%v=1 = 25%`
* `left_ankle`: có `8` trường hợp `v=0`
* `right_ankle`: có `8` trường hợp `v=0`

Sau khi có visibility report của bạn cùng nhóm, điền:

* **Khớp lệch `%v=1` nhiều nhất:** `________`
  (tôi: `____%` / bạn cùng nhóm: `____%`)

* **Nguyên nhân là guideline chưa rõ hay một trong hai bên gán sai:**
  `________________________________________________________`

* **Luật mới bổ sung vào mục 2 sau khi thống nhất:**
  `________________________________________________________`

Ví dụ nếu hai bên lệch nhiều ở `left_ear`, có thể bổ sung luật:

> Nếu tai bị tóc hoặc mũ che khiến không nhìn thấy đầy đủ vùng tai nhưng vẫn xác định được vị trí giải phẫu, cả nhóm thống nhất đặt keypoint tại vị trí ước lượng và sử dụng `v=1`. Chỉ sử dụng `v=2` khi vị trí khớp có thể quan sát trực tiếp rõ ràng.
