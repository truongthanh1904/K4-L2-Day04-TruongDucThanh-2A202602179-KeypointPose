# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Trương Đức Thành   Ngày: 2026-09-16

> Báo cáo này được điền dựa trên dữ liệu hiện có ở thời điểm hiện tại.

**Trạng thái:** Bản cá nhân; gold gồm `gold/person_keypoints_train.json` và
`gold/labels/train/*.txt`. Kết quả dưới đây là lần chạy hiện tại, chưa rework nhãn.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 334 / 82 / 77 |
| Thời gian trung bình mỗi ảnh | Chưa đo |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. left_ear — 41%
2. right_ear — 38%
3. left_hip — 28%

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích.

Có, đây là các khớp dễ bị che hoặc khó xác định bằng mắt khi người mặc quần áo dài hoặc khi khớp nằm gần mép khung. Trong báo cáo hiện thời, `left_ear` và `right_ear` có tỷ lệ `v=1` cao, cho thấy việc xác định tai có bị che hay không là yếu tố gây khó hơn nhiều so với vai hoặc mắt. Tương tự, `hip` cũng có nhiều trường hợp bị che hoặc ước lượng giải phẫu, nên cần phải phân biệt rõ “bị che nhưng còn trong khung” với “ra ngoài khung”.

## 2. Chấm với gold (lần chạy hiện tại)

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9180 | Chưa rework |
| OKS@0.50 | 1.0000 | Chưa rework |
| OKS@0.75 | 1.0000 | Chưa rework |
| Lỗi `dao_trai_phai` | 1 | Chưa rework |
| Lỗi `nham_nguoi` | 1 | Chưa rework |
| Lỗi `xoa_khop_bi_che` | 6 | Chưa rework |

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- Chưa rework theo các lỗi gold; riêng lỗi định dạng ở `train_04` đã được sửa.
- Gold có 29 skeleton, bài hiện có đủ 29 skeleton.
- Còn 8 skeleton cần rework theo gold: `train_06`, `train_19`, `train_16`, `train_08`, `train_11`, `train_15`, `train_09`, `train_04`.
- Đã sửa `train_04` người #2: chuẩn hóa box và đổi `left_ankle` ngoài ảnh sang `v=0`.
- Validator hiện không còn lỗi chặn; chỉ còn các cảnh báo visibility/trái-phải được giữ lại theo yêu cầu.
- Cần kiểm tra các cảnh báo `v=0` ở `train_04`, `train_06`, `train_09`, `train_10`, `train_11`, `train_13`, `train_16`, `train_19`, và trái/phải ở `train_15`.
- Các chênh lệch `co_khac_gold` và `gold_khong_gan_nhan` không bị trừ OKS theo guideline.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ,
Bạn nghĩ vì sao mình vẫn sai?

- Dữ liệu hiện tại cho thấy `train_15` có dấu hiệu đảo trái/phải ở vai và hông: `left_shoulder/right_shoulder` và `left_hip/right_hip` bị báo ngược chiều so với hai mắt.
- Đây là lỗi kiểu đảo trái/phải, xảy ra ở ảnh đang được kiểm lại, và thông thường là lỗi của thao tác nhanh hơn là độ khó ảnh.

## 3. Rule đã cập nhật

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi rà soát lại visibility report:

- Nếu khớp còn trong khung dù bị che, phải đánh `v=1` và vẫn đặt chấm ước lượng.
- Chỉ khi khớp thực sự ra khỏi mép ảnh mới đặt `v=0`.
- Nếu giải phẫu hoặc vật che không rõ, cần căn cứ vào cơ thể người và không chuyển sang `v=0` chỉ vì khớp không nhìn rõ.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6853 | 0.0000 |
| pose_precision | 0.9734 | 0.9736 | +0.0002 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50-95 | 0.8119 | 0.8051 | -0.0068 |

Ghi chú về lần chạy lại: sau khi sửa `train_04`, fine-tune đã đọc đủ 20 ảnh train.
Tập validation vẫn là 10 ảnh test cố định; các metric pose không đổi so với baseline.

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?

   `pose_mAP50-95` không đổi: 0.6853 → 0.6853, nên kết quả cho thấy fine-tune trên 20 ảnh hiện tại không cải thiện hay làm giảm độ chính xác pose trên tập test. Điều này phù hợp với dữ liệu nhỏ và tính chất của thí nghiệm: đoàn model gần như không học được thêm gì mới từ bạn trên test set.

2. `box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm *người* dễ hơn hay tìm *khớp* dễ hơn? Vì sao?

   `box_mAP50-95` gốc là 0.8119 và sau fine-tune là 0.8051; trong khi `pose_mAP50-95` là 0.6853. Sự chênh lệch là khoảng 0.1266 ở baseline và 0.1198 ở fine-tune, cho thấy model tìm vùng người dễ hơn tìm vị trí các khớp, vì khớp là nhiệm vụ khó hơn nhiều dù người đã được phát hiện đáng tin cậy.

3. Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):

   Qua chạy local trên test set, không có tín hiệu cải thiện rõ ràng sau fine-tune; model vẫn tương đương baseline. Về mặt kiểu sai, nhìn chung các trường hợp lỗi có xu hướng là lệch nhẹ và/hoặc thừa thiếu chân khớp chứ không phải đổi trái-phải hoặc nhầm người rõ ràng ở mức lớn, do dataset rất nhỏ và model chưa được học đủ.

4. Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?

   Với thí nghiệm hiện tại, số liệu model không cho thấy sự khác biệt rõ rệt giữa baseline và fine-tune; vì vậy không có “ảnh có OKS thấp nhất” được chứng minh bằng sự cải thiện. Chúng ta dựa vào gold evaluation và bằng chứng visual của khuôn khổ dữ liệu, không từ model vì nó chưa học hiệu quả trên 20 ảnh.

5. Ảnh bạn gán tệ nhất có *cũng* là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?

   Theo kết quả metric của model, không có một hiện tượng rõ ràng cho thấy model “đoán tệ” sau fine-tune so với baseline; sự khác biệt gần như bằng 0. Điều này cho thấy tập train quá nhỏ, và nhiều ảnh khó không được model học đủ để tạo ra một cải thiện có ý nghĩa.

## 5. Một rule evidence bạn đã dùng

Chọn một keypoint trong ảnh core mà bạn phải quyết định giữa `v=1` và `v=0`. Nêu ảnh, người, khớp, bằng chứng nhìn thấy và lý do chọn trạng thái đó trong 3-5 câu.

- Ảnh: `train_04`, người thứ 2, keypoint: `left_ankle`.
- Ở bản export ban đầu, keypoint này có tọa độ `(0.607, 1.293)` và bị gán `v=2`, vượt khỏi khung hình.
- Khi rà lại, tôi đã đổi điểm này thành `v=0` và chuẩn hóa box của người đó.
- Đây là ví dụ điển hình cho quy tắc: nếu điểm còn trong khung thì `v=1`, nếu đã ra ngoài mép ảnh thì `v=0`.
- Sau khi sửa, validator đã đạt định dạng; các cảnh báo còn lại là cảnh báo visibility cần cân nhắc theo guideline.
