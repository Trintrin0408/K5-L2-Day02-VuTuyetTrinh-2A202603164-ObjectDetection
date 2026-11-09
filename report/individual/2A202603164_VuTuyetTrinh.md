# Báo cáo cá nhân — Ngày 2 Object Detection

## 1. Thông tin học viên

- Họ tên: Vũ Tuyết Trinh
- MSSV: 2A202603164
- Email: vutuyettrinh2004@gmail.com
- Lớp: K5 Level 2
- Link repo bài làm: <https://github.com/Trintrin0408/K5-L2-Day02-VuTuyetTrinh-2A202603164-ObjectDetection>
- Link Colab đã chạy: <https://colab.research.google.com/drive/1j0DkyDNS4w0lzFlqp2lfO01FnCOXBRbR?usp=sharing>

## 2. Schema và mini guideline

Chi tiết ở [GUIDELINE_MINI.md](../../GUIDELINE_MINI.md). Schema giữ đúng README/`data.yaml`: `car=0, truck=1, bus=2, van=3`. Các ca mơ hồ chính:

1. **Che khuất / cắt khung** (`drive_001`, `drive_002`): vẫn gán nếu phần nhìn thấy đủ nhận ra lớp; box chỉ ôm phần nhìn thấy, chạm đúng rìa ảnh, không đoán phần bị che.
2. **Xe nhỏ ở xa** (`drive_001`): chỉ gán khi thấy rõ hình dạng xe (≥ ~10 px mỗi chiều); xe chỉ còn là đốm màu thì không gán.
3. **Dòng xe dày đặc, chồng lấn** (`drive_002`): mỗi xe một box riêng trên phần nhìn thấy; xe bị che gần hết không nhận ra lớp thì bỏ.

Cần giảng viên làm rõ: (a) gold/eval dùng thứ tự ID lớp khác schema (xem mục 4–5); (b) gold không gán xe đỗ ven đường, trong khi README ghi "xe đang đỗ vẫn là `car`".

## 3. Annotation và self-QC

- Số file nhãn train: 50 (tổng 1062 box: car 870, truck 29, bus 104, van 59)
- Kết quả checker: `python tools/check_yolo_labels.py` → `ĐẠT cấu trúc: 50/50 file nhãn hợp lệ`. (Trên Windows cần đặt `PYTHONIOENCODING=utf-8` để in được tiếng Việt.)
- Danh sách ít nhất 10 ảnh đã rà bằng mắt (dùng ảnh so sánh nhãn của em với gold trong `outputs/rework_review/`):

  | Ảnh | Quan sát |
  |---|---|
  | `drive_001` | dòng 11 & 12 trùng box; xe nhỏ ở xa |
  | `drive_002` | xe buýt trắng gold ghi `van`; xe van rìa phải em gán `car`; dòng 2 & 30 trùng |
  | `drive_009` | hàng xe buýt gold ghi `van`; dòng 4 & 6 trùng |
  | `drive_016` | dòng 19 & 24, 20 & 22 trùng (20/22 khác lớp van/car) |
  | `drive_022` | dòng 2 & 4 trùng |
  | `drive_028` | xe đỗ hai bên đường gold không gán (11 box dư) |
  | `drive_030` | nhiều box dư ở vùng xa; dòng 13 & 42 trùng (bus/car) |
  | `drive_033` | dòng 3 & 14, 5 & 15 trùng |
  | `drive_038` | dòng 2 & 22 trùng |
  | `drive_046` | dòng 1 & 4 trùng (van/car) |

- Lỗi phát hiện khi rà: 19 cặp box trùng nhau (IoU ≥ 0.75), ví dụ `drive_002` dòng 2 & 30, `drive_016` dòng 20 & 22; xe van ở rìa phải `drive_002` bị gán `car`. Chưa sửa vì giảng viên xác nhận kết quả hiện tại đã đạt (xem mục 4).
- Commit khóa nhãn ban đầu: `5a11164` — "Day02: thêm nhãn train drive_001-050 + cập nhật GUIDELINE_MINI"

## 4. Gold QC và rework

| Chỉ số | Trước sửa | Sau sửa nếu có |
|---|---:|---:|
| TP | 457 | — |
| FP | 605 | — |
| FN | 196 | — |
| Precision | 0.4303 | — |
| Recall | 0.6998 | — |
| F1 | 0.5329 | — |
| mean_iou_of_matches | 0.8448 | — |
| passed | false | — |

Kết quả chấm: [outputs/annotation_evaluation.json](../../outputs/annotation_evaluation.json). Mean IoU = 0.8448 đạt ngưỡng 0.70 (box bám sát). F1 = 0.5329 thấp hơn ngưỡng 0.80 của script, nhưng giảng viên đã xác nhận lớp chỉ cần F1 > 0.5, nên **không rework**.

Nguyên nhân F1 thấp, tìm được khi rà ảnh so sánh nhãn của em với gold:

1. **ID lớp của gold khác schema trong README/`data.yaml`.** Ở `drive_002` và `drive_009`, xe buýt bị gold gán 3 (`van`), xe van bị gán 1 (`truck`). Nhãn của em theo schema (bus = 2, van = 3). Thử đổi ID nhãn của em cho khớp gold thì recall tăng từ 0.70 lên 0.86, nhưng em không đổi vì làm vậy sẽ trái với `data.yaml`.
2. **Gold không gán xe đỗ ven đường và xe ở xa/dày đặc** (ví dụ `drive_028`). README ghi "xe đang đỗ vẫn là `car`" nên em vẫn gán. Có 459 box của em không trùng box gold nào, phần lớn nằm ở nửa trên ảnh.
3. **Lỗi của em:** 19 cặp box trùng nhau và một số nhầm lớp (xem mục 3).

## 5. Kết quả YOLO

- Runtime: Google Colab, **CPU** (không được cấp GPU; train 50 epoch mất ~39 phút). Ultralytics bản mới nhất lúc chạy (`pip install -U ultralytics`); output của notebook không in số phiên bản Ultralytics/Torch.
- Tham số thay đổi so với notebook: không thay đổi (yolo26n.pt, epochs=50, imgsz=640, batch=-1, seed=20260909).
- Best checkpoint trong phiên chạy: `/content/Day2-Lab/outputs/yolo26_runs/vehicle_annotation/weights/best.pt` (không commit file `.pt`)

| Chỉ số trên 10 ảnh eval | Giá trị |
|---|---:|
| Precision | 0.4609 |
| Recall | 0.2357 |
| mAP50 | 0.2754 |
| mAP50–95 | 0.2046 |

Nguồn số liệu: [model_eval_metrics.json](assets/model_eval_metrics.json). Kết quả Colab:

- Ảnh dự đoán trên 10 ảnh eval: [assets/eval_predictions/visuals/](assets/eval_predictions/visuals/)
- Log train theo epoch: [results.csv](assets/yolo26_runs/vehicle_annotation/results.csv), [results.png](assets/yolo26_runs/vehicle_annotation/results.png)
- Confusion matrix: [confusion_matrix.png](assets/yolo26_runs/vehicle_eval/confusion_matrix.png), [normalized](assets/yolo26_runs/vehicle_eval/confusion_matrix_normalized.png); PR curve: [BoxPR_curve.png](assets/yolo26_runs/vehicle_eval/BoxPR_curve.png)

Recall theo lớp, đọc từ confusion matrix (số đoán đúng / số box nhãn eval):

| Lớp | Đúng / nhãn eval | Recall | Bị đoán thành |
|---|---:|---:|---|
| car | 85 / 94 | 0.90 | 5 bỏ sót (background), 4 lẫn sang lớp khác |
| truck | 1 / 5 | 0.20 | 4 → `car` |
| bus | 0 / 2 | 0.00 | 1 → `car`, 1 bỏ sót |
| van | 0 / 12 | 0.00 | **11 → `bus`**, 1 bỏ sót |

**Ảnh cụ thể — [drive_058.jpg](assets/eval_predictions/visuals/drive_058.jpg):** model phát hiện hàng xe buýt bên trái (ví dụ xe buýt vàng `bus 0.92`, xe buýt `bus 0.63` ở giữa) và gán đúng là `bus` theo mắt thường. Nhưng nhãn eval của ảnh này có 6 `van` và chỉ 1 `bus`, nên các dự đoán đúng đó bị tính là sai. Đây cũng là lệch ID lớp giống gold train ở mục 4. Các xe con phía trước (`car 0.79`–`0.87`) được phát hiện tốt.

Trả lời bốn câu hỏi cuối notebook:

1. **Lớp nào recall thấp nhất? Ảnh nào minh họa?** `van` (0/12) và `bus` (0/2) đều có recall 0. Với `van`, 11/12 box bị model đoán là `bus`; ảnh minh họa là `drive_058` (6 box `van` trong nhãn eval thực chất là xe buýt). Vì vậy recall 0 của `van` chủ yếu do nhãn eval dùng ID lớp khác schema mà em đã train, không hẳn do model kém. `truck` cũng thấp (0.20): 4/5 xe tải bị đoán thành `car`, vì tập train chỉ có 29 box `truck`.
2. **Nếu mAP50 cao nhưng mAP50–95 thấp, cần kiểm điều gì trên box?** Khi đó model tìm đúng vật thể nhưng box chưa bám sát, nên cần kiểm tra cạnh box có lỏng hay lệch không, đặc biệt với xe bị che hoặc xe nhỏ ở xa. Ở bài này mAP50 = 0.275 và mAP50–95 = 0.205, tỉ lệ khoảng 0.74, nên vấn đề chính không nằm ở độ sát của box mà ở phân lớp và recall. Điều này khớp với mean IoU 0.84 khi chấm nhãn train.
3. **Lỗi class nào có thể liên quan guideline? Dữ kiện nào hỗ trợ?** Lỗi `van` ↔ `bus` liên quan trực tiếp tới quy ước gán nhãn: nhãn train của em theo schema (bus = 2), còn gold train và eval gán xe buýt là `van` (`drive_009`, `drive_058`). Thêm vào đó, em gán cả xe đỗ ven đường và xe ở xa (theo README), còn nhãn eval thì không. Vì vậy model dự đoán rất nhiều box ở các vùng này mà nhãn eval không có, làm precision thấp (cột background trong confusion matrix: 1287 dự đoán `car` không khớp nhãn nào). Lỗi `truck` → `car` là do mất cân bằng lớp (car 870 box so với truck 29 box) và ranh giới truck/van/car nhỏ khó phân biệt.
4. **Vì sao không dùng 10 ảnh eval để train? Vai trò validation trong notebook?** Nếu train trên eval thì model đã thấy đáp án, nên chỉ số đo trên eval sẽ cao giả và không phản ánh khả năng tổng quát. Trong notebook, 10 ảnh eval được dùng làm tập `val` để theo dõi mAP sau mỗi epoch và chọn `best.pt`. Vì đã dùng để chọn checkpoint, đây không phải test set độc lập; kết quả trên eval vẫn hơi lạc quan.

## 6. Khó khăn và cách xử lý

| Bước/thời điểm | Lỗi quan sát | Cách đã thử | Kết quả/còn thiếu |
|---|---|---|---|
| Chạy notebook trên máy local (Windows) | `UnicodeDecodeError` / `'NoneType' object has no attribute 'strip'` khi đọc `git log` có commit message tiếng Việt | Xác định do Windows dùng bảng mã cp1252; chuyển sang chạy trên Colab | Chạy được trên Colab; đã bỏ output lỗi khỏi notebook trong repo |
| Chạy script `tools/` trên Windows | `UnicodeEncodeError` khi in tiếng Việt | Đặt `PYTHONIOENCODING=utf-8` | Checker và evaluator chạy bình thường |
| Gold QC | F1 = 0.53 < 0.80 | Vẽ ảnh so sánh nhãn với gold, phát hiện gold lệch ID lớp và không gán xe đỗ; hỏi giảng viên | Giảng viên xác nhận F1 > 0.5 là đạt |
| Train Colab | Không được cấp GPU | Chạy trên CPU | Train mất ~39 phút, kết quả đầy đủ |

## 7. Tự đánh giá theo rubric

| Tiêu chí | Tình trạng |
|---|---|
| Hoàn thành và định dạng YOLO (10) | Đủ 50 file, checker đạt 50/50 |
| Đầy đủ vật thể (20) | Recall với gold 0.70; phần thiếu chủ yếu do lệch ID lớp. Gán cả xe đỗ/xe xa theo README nên dư box so với gold |
| Độ bám sát box (20) | Mean IoU 0.84 (mức "Xuất sắc" ≥ 0.80) |
| Phân lớp nhất quán (15) | Theo schema README; còn một số nhầm lớp và 19 box trùng chưa sửa |
| Mini guideline (10) | Có định nghĩa lớp, quy tắc box, ≥ 3 ca mơ hồ |
| Gold-set QC (15) | Có JSON đánh giá; F1 0.53 < 0.80 nhưng đã giải thích nguyên nhân và được giảng viên chấp nhận ngưỡng 0.5 |
| Pipeline YOLO (10) | Colab chạy xong, không dùng eval để train, có metrics, ảnh eval và phân tích |

## 8. Công cụ AI và tài liệu tham khảo

- **Claude Code (Anthropic)**, dùng để: đọc tài liệu repo; chạy `check_yolo_labels.py` và `evaluate_annotations.py`; phân tích FP/FN theo lớp và vẽ ảnh so sánh nhãn với gold (`outputs/rework_review/`); phát hiện gold/eval lệch ID lớp; giải nén kết quả Colab vào `assets/`.
- Phần em tự làm: gán nhãn 50 ảnh, viết mini guideline, hỏi giảng viên về ngưỡng F1, chạy notebook trên Colab.
- Nguồn ngoài repo: tài liệu Ultralytics YOLO (<https://docs.ultralytics.com>).
