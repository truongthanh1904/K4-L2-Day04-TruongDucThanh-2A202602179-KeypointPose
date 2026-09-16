# Mini guideline - người gán: Trương Đức Thành  |  ngày: 2026-09-16

> Điền file này **trong lúc** gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có **đủ 17 điểm**. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo **cơ thể người**, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, **vẫn đặt chấm** ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, **không** đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật cá nhân của tôi

| Tình huống | Luật cá nhân chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Nếu hông không lộ rõ nhưng vẫn còn nằm trong thân và có thể ước lượng trên trục giữa người, giữ `v = 1` và đặt chấm ước lượng. Chỉ đặt `v = 0` khi hông thật sự ra ngoài khung ảnh. | Hông là điểm giải phẫu ước lượng, không phải điểm có bề mặt rõ ràng như mắt hoặc cổ tay. Trong nhiều ảnh quần áo dài, hông được che, nhưng vẫn còn nằm trong hình. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu phần tai vẫn có thể phán đoán từ đầu và vị trí tương đối với mắt/đầu, đánh `v = 1`. Nếu tai đã mất hẳn khỏi khung hoặc không còn đủ căn cứ để đoán, mới đánh `v = 0`. | Trong visibility report, `left_ear` và `right_ear` có tỷ lệ `v = 1` cao nhất. Đây là khớp hay bị che hoặc sát mép đầu, nên cần phân biệt rõ “mất nhìn nhưng còn trong khung” với “ra ngoài ảnh”. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Nếu điểm còn nằm trong khung dù chỉ là 1 phần, vẫn đặt chấm và đánh `v = 1`. Nếu điểm đã vượt ngoài mép hình, giữ `v = 0` và không đặt chấm. | Định dạng lab yêu cầu `v = 1` cho điểm bị che nhưng còn trong khung; `v = 0` chỉ cho điểm mất hẳn khỏi ảnh. |
| Cổ tay nằm sau tay lái / sau thân mình | Nếu cổ tay còn ở bên trong thân, hoặc có thể ước lượng theo cánh tay và hướng cẳng tay, đặt `v = 1`. Nếu không nhìn thấy và không còn căn cứ, gán `v = 0`. | Cổ tay hay bị che bởi thân, tay lái hoặc vật thể; nếu còn trong khung, model cần chấm ước lượng để giữ thông tin. |
| Hai người chồng lên nhau | Gán theo người có hình dáng rõ nhất và đường thân rõ ràng; chỉ sửa cho đúng cơ thể, không gán theo vị trí xấp xỉ trên ảnh. | Sai nhầm người là lỗi nặng nhất vì augmentation lật ảnh sẽ dạy lại cái sai này hai lần. |
| Người nhỏ đến mức nào thì không gán nữa | Nếu người vẫn đủ rõ để định hướng vai, hông, tay chân và không phải “một vùng đen”, vẫn gán. Chỉ bỏ gán khi người quá nhỏ và không có đủ điểm giải phẫu để căn cứ. | Với dataset này, người đã được chọn đủ lớn để gán; phần lớn “khó” là do mơ hồ visibility hơn là kích thước người. |

Với mỗi luật, nên chèn **một ảnh mẫu** (screenshot từ CVAT) thay vì chỉ viết một câu.
Slide 12 nói rõ: khớp không có bề mặt nhìn thấy được thì phải có ảnh mẫu, không phải
một câu văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_15`, người thứ `1`, khớp `left_shoulder / right_shoulder`

- Mơ hồ ở chỗ nào: Vai bị đảo trái/phải vì người trong ảnh có thân nghiêng, và cả hips lẫn shoulders đều gần trùng nên dễ gán sai bên.
- Bạn quyết thế nào: Dựa vào đường giữa ngực, vị trí mặt/nose và hướng của hông để xác định người đang quay kiểu nào; sau đó gán trái/phải theo cơ thể người, không theo vị trí trên ảnh.
- Vì sao: Trong gold, lỗi `dao_trai_phai` xuất hiện ở ảnh dễ này. Nếu gán nhầm, augmentation lật ảnh sẽ dạy model lệch trái/phải hai lần.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model học nhầm hướng vai/hông, dẫn tới xương cắt chéo và trái/phải bền vững trên mọi ảnh tương tự.

### Ca 2 - ảnh `train_13`, người thứ `1`, khớp `left_ear / right_ear`

- Mơ hồ ở chỗ nào: Tai bị che bởi tóc hoặc cạnh đầu, và khó xác định phần tai nào còn hiện lên trên đầu.
- Bạn quyết thế nào: Nếu điểm tai còn nằm trong khung và có thể xác định từ vị trí mắt + đầu, đặt chấm và đánh `v = 1`; nếu tai đã rời khung, đánh `v = 0`.
- Vì sao: Gold cho thấy các lỗi kiểu “thieu khop” và “xoa_khop_bi_che” thường xuất phát từ việc bỏ luôn khớp bị che dù còn trong khung.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu bỏ hẳn khớp, model không thấy đầu và tai ở các ảnh tương tự, dẫn tới mất thông tin cấu trúc đầu.

### Ca 3 - ảnh `train_04`, người thứ `2`, khớp `left_ankle`

- Mơ hồ ở chỗ nào: Khớp chân ra ngoài khung, chỉ còn một phần chân ở mép ảnh, không rõ có còn nằm trong khung hay không.
- Bạn quyết thế nào: Dựa vào vị trí thực tế của bàn chân trong khung hình; nếu chân vượt khỏi mép, không đặt chấm và đánh `v = 0`.
- Vì sao: Theo rule lớp, khớp ra khỏi ảnh phải là `v = 0`, không phải `v = 2` vì điểm không còn nằm trong bất kỳ vùng nhìn thấy nào.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Model được dạy một điểm ở nơi không tồn tại trên ảnh, làm tăng lỗi pose và làm méo khớp chân khi đầu vào hình mới.

## 4. Rule cuối cùng đã chốt

- Khớp có tỷ lệ `v = 1` cao nhất trong bài này là `left_ear` và `right_ear`, sau đó là hông khi bị che.
- Nguyên nhân chủ yếu là **guideline chưa rõ** ở các khớp mà khớp đó còn nằm trong khung nhưng bị che hoặc sát biên ảnh.
- Luật mới bổ sung vào mục 2:
  - Nếu điểm còn nằm trong khung dù bị che, phải giữ `v = 1` và đặt chấm ước lượng.
  - Chỉ đánh `v = 0` khi điểm thực sự vượt khỏi mép ảnh.
  - Trái/phải phải lấy theo cơ thể người, không theo vị trí trong ảnh.
