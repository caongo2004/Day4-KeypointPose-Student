# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Ngô Văn Cao   Nhóm: ______   Ngày: 16/9/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

| Chỉ số                       |         Giá trị |
| ---------------------------- | --------------: |
| Số ảnh đã gán                |              20 |
| Số skeleton                  |              28 |
| v=2 / v=1 / v=0              |   366 / 87 / 23 |
| Thời gian trung bình mỗi ảnh | Chưa có dữ liệu |

Ba khớp có `%v=1` cao nhất:

1. `left_ear`: 36%
2. `left_eye`: 25%
3. `right_ear`: 25%

Ngoài ra, `right_knee` và `right_ankle` cũng có `%v=1 = 25%`, nên đang đồng hạng với `left_eye` và `right_ear`.

Các khớp này nhìn chung đúng là những vị trí tương đối khó gán. Đặc biệt, tai và mắt dễ bị tóc, mũ, góc quay của đầu hoặc vật thể khác che khuất nên thường phải ước lượng vị trí và sử dụng `v=1`. Tuy nhiên, trong quá trình gán tôi cũng thấy các khớp ở đầu gối và mắt cá chân khó xử lý, nhất là khi người bị cắt ở mép ảnh hoặc phần chân bị vật thể khác che. Vì vậy mức độ khó khi gán không chỉ thể hiện qua `%v=1`, mà còn liên quan đến các trường hợp phải phân biệt giữa `v=1` và `v=0`.


<!-- Trả lời 2–4 câu. Phân biệt “hay bị che” với “khó xác định vị trí giải phẫu”; nêu bằng
chứng nhìn thấy thay vì chỉ nêu cảm giác. -->

## 2. Chấm với gold

| Chỉ số                |                   Trước rework |    Sau rework |
| --------------------- | -----------------------------: | ------------: |
| OKS trung bình        |                          0.913 | Chưa chạy lại |
| OKS@0.50              |                          0.966 | Chưa chạy lại |
| OKS@0.75              |                          0.966 | Chưa chạy lại |
| Lỗi `dao_trai_phai`   |                              0 | Chưa chạy lại |
| Lỗi `nham_nguoi`      |                              3 | Chưa chạy lại |
| Lỗi `xoa_khop_bi_che` | Chưa thấy trong output tóm tắt | Chưa chạy lại |

**Các lỗi chính cần rework**

* Ảnh `train_13.jpg`, người #1: thiếu hẳn một người, OKS = **0.000**. Cần bổ sung đầy đủ skeleton 17 keypoint cho người bị thiếu.
* Ảnh `train_04.jpg`, người #1, `left_wrist`: bị **nhầm người**, keypoint rơi sang cơ thể bên cạnh. Cần chuyển `left_wrist` về đúng người.
* Ảnh `train_03.jpg`, người #1, `left_hip` và `right_hip`: bị **nhầm người**, hai keypoint hông rơi sang người bên cạnh. Cần đặt lại hai điểm hông trên đúng cơ thể.

Ngoài ra kết quả còn ghi nhận **13 lỗi lệch nhẹ**, **43 trường hợp cờ visibility khác gold nhưng vị trí vẫn đúng**, và **72 trường hợp gold để `v=0` trong khi tôi có gán keypoint**. Hai nhóm sau không bị trừ điểm OKS.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Không có lỗi đảo trái/phải được liệt kê trong kết quả chấm hiện tại.


## 3. Kiểm chéo

Bạn cùng nhóm: ______

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| | | | | |
| | | | | |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

<!-- Viết một rule kiểm chứng được: điều kiện nhìn thấy/căn cứ vị trí → chọn v=1 hoặc v=0.
Không chỉ ghi “cẩn thận hơn khi gán”. -->

-

## 4. Model

| Chỉ số         | yolo26n-pose gốc | Sau fine-tune |   Chênh |
| -------------- | ---------------: | ------------: | ------: |
| pose_mAP50     |           0.8450 |        0.8450 | +0.0000 |
| pose_mAP50-95  |           0.6853 |        0.6908 | +0.0055 |
| pose_precision |           0.9734 |        0.9792 | +0.0058 |
| pose_recall    |           0.8462 |        0.8462 | +0.0000 |
| box_mAP50-95   |           0.8119 |        0.8041 | -0.0078 |

Sau fine-tune, `pose_mAP50-95` tăng 0.0055 và `pose_precision` tăng 0.0058, trong khi `pose_mAP50` và `pose_recall` không thay đổi. `box_mAP50-95` giảm 0.0078. Đây chỉ là kết quả quan sát trên tập test của bài thực hành, không phải kết luận về chất lượng sản phẩm thực tế.

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. pose_mAP50-95 thay đổi bao nhiêu sau fine-tune?

pose_mAP50-95 tăng từ 0.6853 lên 0.6908.

Mức thay đổi:

+0.0055
tương đương khoảng +0.55 điểm phần trăm.

Vì chỉ số này tăng chứ không giảm nên không cần trả lời phần giả định “nếu nó giảm”.

2. box_mAP và pose_mAP chênh nhau bao nhiêu? Model tìm người hay tìm khớp dễ hơn?

Sau fine-tune:

box_mAP50-95 = 0.8041
pose_mAP50-95 = 0.6908

Chênh lệch:

0.8041 - 0.6908 = 0.1133

Tức box_mAP50-95 cao hơn khoảng 11.33 điểm phần trăm.

Model tìm người dễ hơn tìm chính xác các khớp, vì bounding box chỉ cần xác định vùng chứa người, còn pose phải xác định chính xác nhiều keypoint nhỏ như mắt, tai, cổ tay, đầu gối và mắt cá chân.

### 3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43

Chưa đủ bằng chứng để gọi tên chính xác lỗi của model theo bốn loại chỉ từ bảng OKS. Cần mở ảnh prediction/overlay để xác định đó là `lệch nhẹ`, `đảo trái/phải`, `nhầm người` hay `trượt hẳn`.

### 4. Ảnh nào có OKS thấp nhất giữa nhãn của tôi và model?

Ảnh có OKS thấp nhất là **`train_11` — OKS = 0.558**.

Điều này cho thấy model và nhãn của tôi bất đồng nhiều nhất ở `train_11`. Tuy nhiên, OKS thấp chỉ cho biết hai bên khác nhau, không tự chứng minh model hay nhãn của tôi sai. Kết quả chấm với gold cho thấy ba skeleton cần sửa trước nằm ở `train_13`, `train_04` và `train_03`, vì vậy chưa có đủ bằng chứng để kết luận bên nào đúng ở `train_11`.

### 5. Ảnh tôi gán tệ nhất có cũng là ảnh model đoán tệ nhất không?

**Không.**

Theo kết quả chấm nhãn của tôi với gold, ảnh tôi gán tệ nhất là **`train_13.jpg`, người #1**, với **OKS = 0.000** do **thiếu hẳn một người**.

Trong khi đó, khi so model với nhãn của tôi, ảnh có OKS thấp nhất là **`train_11` với OKS = 0.558**.

Như vậy ảnh tôi gán tệ nhất và ảnh model bất đồng với tôi nhiều nhất không phải cùng một ảnh. Điều này cho thấy lỗi annotation của tôi và lỗi/bất đồng của model không nhất thiết xuất hiện ở cùng một tình huống.


## 5. Một rule evidence bạn đã dùng

Ở ảnh 1, người thứ 2, khớp gối, tôi phải quyết định giữa v=1 và v=0. Quan sát ảnh cho thấy khớp vừa giống nằm trong ảnh những cũng giống nằm ngaoif. Vì khớp vẫn nằm trong khung ảnh nhưng bị che và có thể ước lượng được vị trí nên tôi chọn v=1 và vẫn đặt chấm. Nếu vị trí giải phẫu của khớp đã nằm ngoài mép ảnh thì tôi mới sử dụng v=0 và không đặt chấm.

<!-- Cấu trúc gợi ý: (1) train_XX + người thứ mấy + keypoint; (2) căn cứ thị giác như phần cơ
thể liền kề, trang phục hoặc vật che; (3) vì sao khớp còn trong khung (v=1) hay đã ra khỏi
khung (v=0). -->
