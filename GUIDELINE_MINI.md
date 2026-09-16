# Mini guideline - nhóm: ______  |  người gán: ______  |  ngày: ______

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | `v = 2` (visible) | Quần áo che da là trạng thái bình thường, không phải occlusion. Nếu vẫn thấy rõ đường viền cơ thể (vai→hông→gối) qua lớp vải thì vị trí khớp xác định trực tiếp, không cần suy luận — không tính là bị che |![alt text](image.png)
| Tai bị tóc hoặc mũ bảo hiểm che một phần | `v = 1` (occluded) | Vật thể khác (tóc/mũ) chắn giữa camera và tai, nhưng vẫn suy luận được vị trí gần đúng nhờ đối xứng khuôn mặt / vành mũ. Chỉ hạ xuống `v = 0` nếu tai khuất hoàn toàn không còn dấu vết nào để đoán (ví dụ quay đầu gần 90°) |![alt text](image-1.png)
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Phần thân trên: `v = 2`/`v = 1` tuỳ có bị vật che hay không. Phần chân/mắt cá/đầu gối bị cắt hẳn ra ngoài khung: `v = 0` | Phân biệt rõ hai loại "không thấy": bị vật che (đoán được vị trí → v=1) khác với ra khỏi khung hình (không còn cơ sở đoán → v=0). Không đoán tọa độ ảo cho phần đã ra ngoài ảnh. |![alt text](image-2.png)
| Cổ tay nằm sau tay lái / sau thân mình | `v = 1` (occluded) | Tay lái/thân xe/chính cơ thể người che một phần cổ tay, nhưng vẫn đoán được vị trí nhờ hướng cẳng tay và tư thế ngồi (self-occlusion vẫn tính là occluded nếu còn suy luận được). |![alt text](image-3.png)
| Hai người chồng lên nhau | Gán riêng từng người đủ 17 điểm; khớp bị người kia che nhưng đoán được vị trí → `v = 1`; khớp bị che hoàn toàn, không còn manh mối (ví dụ bị người đứng trước che khuất 100%) → `v = 0`. Luôn xác định trái/phải theo hướng cơ thể của **chính người đang gán**, không lấy theo người đứng cạnh. | Đây là nguồn lỗi "đảo trái/phải" phổ biến nhất (gặp thực tế ở `train_06.jpg` người #1 trong nhóm mình) — khi hai người sát nhau, dễ nhầm hướng mặt của người này sang người kia nếu không dừng lại đối chiếu mắt/mũi trước khi gán. |![alt text](image-4.png)
| Người nhỏ đến mức nào thì không gán nữa | Còn phân biệt được tư thế tổng thể (đầu/vai/hông) bằng mắt thường ở độ phân giải gốc thì vẫn gán, dùng `v=1` cho các khớp nhỏ không phân biệt rõ. Nếu người chỉ còn là một chấm mờ không phân biệt được hướng cơ thể thì bỏ qua, không tạo skeleton. | Tránh tạo nhãn nhiễu (noise) từ suy đoán mù — model học từ tọa độ bịa sẽ hại hơn là không có nhãn. |![alt text](image-5.png)

Với mỗi luật, chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_13`, người thứ `3`, khớp `left_eye` / `right_eye`

- Mơ hồ ở chỗ nào: người này đứng nghiêng người, một phần cơ thể quay đi so với camera.
  Khi xác định trái/phải, em nhìn theo hướng của mình (từ ngoài nhìn vào) thay vì hướng
  cơ thể của chính người đó, nên đặt LEFT_EYE và RIGHT_EYE gần sát nhau ở cùng
  một điểm thay vì hai bên đối xứng.
- Bạn quyết thế nào: đặt ước lượng theo vai phải của người này
- Vì sao: vì phần bên phải nhìn khá rõ nên em đối chiếu theo đó để gán
- Nếu người khác quyết ngược lại thì model học sai cái gì:nếu một nửa dữ liệu train gán
  trái/phải theo góc nhìn người ngoài và nửa kia gán theo cơ thể nhân vật, model sẽ nhận
  hai tín hiệu ngược nhau cho cùng một tư thế nghiêng người — dẫn đến việc model học sai
  quy tắc trái/phải một cách hệ thống ở mọi ảnh có người đứng nghiêng hoặc quay lưng một
  phần, chứ không chỉ riêng ảnh này.

### Ca 2 - ảnh tran_6`, người thứ `1`, khớp `left_eye,right_eye,noise,left ear, right ear`

- Mơ hồ ở chỗ nào:người này quay ngược với cam nên không thể nhận diện vị trí khuân mặt
- Bạn quyết thế nào: đặt ước lượng theo vị trí của vai 
- Vì sao: có thể thấy rõ 2 vai của người này
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu gán ngược trái/phải ở khuôn mặt, model sẽ học sai quy ước LEFT/RIGHT của keypoint. Ví dụ mắt trái có thể bị học thành RIGHT_EYE và mắt phải thành LEFT_EYE, dẫn đến dự đoán vị trí trái/phải bị đảo hoặc không nhất quán.

### Ca 3 - ảnh `train_15`, người thứ `1`, khớp `right_eye`

- Mơ hồ ở chỗ nào: chỉ có thể nhìn nửa bên trái của người này 
- Bạn quyết thế nào: đặt vị trí mắt dựa theo vị trí mắt trái và mũi
- Vì sao:chỉ nhìn thấy một phần và bị góc quay/khuôn mặt che khuất nên khó xác định chính xác vị trí.
- Nếu người khác quyết ngược lại thì model học sai cái gì:LEFT_EYE và RIGHT_EYE sẽ bị gán ngược, khiến model học sai quy ước trái/phải của keypoint mắt và có thể dự đoán vị trí hai mắt bị đảo.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `______` (bạn `___%` / họ `___%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**:
- Luật mới bổ sung vào mục 2 sau khi thống nhất:
