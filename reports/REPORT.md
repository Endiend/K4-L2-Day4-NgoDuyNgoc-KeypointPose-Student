# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Ngô Duy Ngọc   Nhóm: ______   Ngày: 16/09/2026

> Cách dùng: copy file này thành `reports/REPORT.md`. Điền bằng số liệu do công cụ sinh ra;
> không tự ước lượng hoặc sửa số trong file JSON.

## 1. Nhãn của tôi

<!-- Lấy số từ reports/visibility_report.md hoặc outputs/visibility_report.json sau Chặng 4.
Số ảnh phải là 20; số skeleton là tổng số người trong 20 ảnh. Thời gian trung bình = tổng
thời gian gán / 20. -->

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 341 / 125 / 27 |
| Thời gian trung bình mỗi ảnh | 25p |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1.left_ear — 48%
2.right_ear — 41%
3.left_wrist — 38%

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

<!-- Trả lời 2–4 câu. Phân biệt “hay bị che” với “khó xác định vị trí giải phẫu”; nêu bằng
chứng nhìn thấy thay vì chỉ nêu cảm giác. -->

## 2. Chấm với gold

<!-- Lấy hai cột từ outputs/eval_vs_gold.json: một lần ngay khi protected release mở và một
lần sau rework. Đếm số phần tử trong từng danh sách lỗi, không tự làm tròn. -->

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.877 | 0.912 |
| OKS@0.50 | 0.966 | 1.000 |
| OKS@0.75 | 0.931 | 0.966 |
| Lỗi `dao_trai_phai` | 1 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 1 | 0 |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):
- train 6 đã sửa lỗi ngược từ phần vai đổ xuống tới chân; 
- train 1 người thứ 2(19) đổi phần knee cả 2 chân từ outside thành inside 
- train 14 sửa người 1(325) bị ngược từ phần hip đổ xuống

<!-- Mỗi dòng phải có: tên ảnh + người thứ mấy + keypoint + thao tác sửa. Không viết “đã sửa
lại một số lỗi”. -->



**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
bạn nghĩ vì sao mình vẫn sai?

<!-- Nếu không có lỗi, ghi rõ “Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh.” -->
- train 6 và train 14 đã nhầm lẫn do em click các điểm khớp theo thứ tự cố định trong tool mà không đối chiếu lại với hướng mặt (mắt trái/phải) trước khi xác nhận.

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

<!-- Chép số từ outputs/eval_model.json sau Chặng 6. “Chênh” = sau fine-tune trừ baseline;
đây là quan sát trên tập test, không phải chất lượng sản phẩm. -->

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450	| +0.0000 |
| pose_mAP50-95 | 0.6853	| 0.6908	| +0.0055 |
| pose_precision | 0.9734	| 0.9792	| +0.0058 |
| pose_recall | 0.8462	| 0.8462	| +0.0000 |
| box_mAP50-95 | 0.8119	| 0.8041	| −0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

> Mỗi câu cần trỏ tới ảnh/chỉ số cụ thể. Một con số thấp không tự chứng minh nhãn sai;
> kiểm lại bằng bằng chứng thị giác và kết quả gold.

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model
   điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?
   pose_mAP50-95 tăng nhẹ +0.0055 (không giảm). 20 ảnh không phá điều gì đáng kể — model gần như giữ nguyên khả năng cũ và cải thiện chút ít trên khớp, dù box_mAP50-95 giảm nhẹ (−0.0078), có thể do 20 ảnh làm model hơi lệch phân bố box so với COCO gốc.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm
   *khớp* dễ hơn? Vì sao?
   Ở baseline, box_mAP50-95 (0.8119) cao hơn pose_mAP50-95 (0.6853) — chênh 0.1266. Sau fine-tune chênh còn 0.1133. Model tìm người (box) dễ hơn tìm khớp (pose) — vì phát hiện một hộp bao quanh người "lỏng" hơn nhiều so với định vị chính xác 17 điểm khớp nhỏ, dễ bị che/khó phân biệt trái-phải.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43
   (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):
   Ảnh test_07: model đặt right_wrist lên cái vòm kính đựng bánh trên bàn, cách xa cơ thể — trong khi tay thật của người đó bị áo khoác che, gần như không lộ ra. Đây là lỗi trượt hẳn (loại 4/4 theo slide 43): điểm khớp đoán vào một vật thể hoàn toàn không phải cơ thể người.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?
   Ảnh OKS thấp nhất giữa nhãn của en và model là train_13 (0.551) — thấp nhất toàn bộ 29 skeleton, và cũng chính là ảnh có lỗi "lệch nhẹ" chưa sửa ở gold. Vì gold đã xác nhận nhãn train_13 chưa hoàn hảo (OKS gold chỉ 0.744, thấp nhất trong các skeleton còn lỗi), nhiều khả năng nhãn của em đúng hơn là sai hoàn toàn, nhưng vẫn có phần lệch cần xem lại — không nên vội cho rằng model đúng.
5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó
   nói gì về bức ảnh đó?
   Có — train_13 vừa là skeleton duy nhất còn tồn đọng trong gold-check (lệch nhẹ, chưa sửa), vừa là 2 trong 2 giá trị OKS thấp nhất so với model (0.551 và 0.583).

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người,
khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.
- Ở train_11.jpg, em phải quyết định giữa v=1 và v=0 cho phần knee và ankle của cả trái và phải. Ban đầu tôi đặt v=0 vì không nhìn thấy trực tiếp. Tuy nhiên cuối cùng em vẫn quyết định để nó là v=1 vì dựa theo phần trên của người trong ảnh em nghĩ phần thân dưới chỉ bị che bởi bàn chứ chưa vượt qua phạm vi bức ảnh
<!-- Cấu trúc gợi ý: (1) train_XX + người thứ mấy + keypoint; (2) căn cứ thị giác như phần cơ
thể liền kề, trang phục hoặc vật che; (3) vì sao khớp còn trong khung (v=1) hay đã ra khỏi
khung (v=0). -->
