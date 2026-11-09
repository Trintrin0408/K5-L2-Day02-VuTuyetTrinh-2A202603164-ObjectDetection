# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` |Xe ô tô chở người, thường có từ 4–7 chỗ ngồi. | Sedan, SUV, hatchback, coupe. Thường có 4 bánh.|
| `truck` |Xe tải được thiết kế chủ yếu để vận chuyển hàng hóa, vật liệu hoặc thiết bị. |Xe tải thùng, xe ben, xe container, xe tải đông lạnh. |
| `bus` |Xe cơ giới được thiết kế để chở nhiều hành khách, thường có sức chứa lớn hơn ô tô con. | Xe buýt công cộng, xe khách, xe buýt trường học.|
| `van` |Xe có khoang thân kín, kích thước trung bình, dùng để chở người hoặc hàng hóa. | Minivan, xe van chở hàng, xe 16 chỗ dạng van.|

## Quy tắc chung

- Box bám sát vật thể nghĩa là: 4 cạnh box chạm đúng điểm ngoài cùng của phần thân xe nhìn thấy được, tính cả gương chiếu hậu nếu nhìn rõ. Không lấy thêm mặt đường, bóng xe, trời hay xe bên cạnh. Mỗi xe có một box riêng, kể cả khi các xe chồng lên nhau. Nếu xe bị nhòe do chuyển động, box ôm phần thân xe rõ nhất, không ôm cả vệt nhòe.
- Vật thể bị che khuất: vẫn gán box quanh phần nhìn thấy nếu phần đó đủ để nhận ra lớp. Không vẽ đoán phần bị xe khác, lan can, cột hay cây che. Nếu xe bị che gần hết và không nhận ra lớp thì không gán, mà ghi lại để hỏi giảng viên.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán box quanh phần nhìn thấy nếu phần đó đủ để nhận ra lớp. Không vẽ đoán phần bị xe khác, lan can, cột hay cây che. Nếu xe bị che gần hết và không nhận ra lớp thì không gán, mà ghi lại để hỏi giảng viên.
- Vật thể rất nhỏ/mơ hồ: chỉ gán khi thấy được hình dạng xe và xác định được lớp, khoảng từ 10 px mỗi chiều trở lên trên ảnh 640×640. Xe ở xa chỉ còn là đốm màu, hoặc không chắc lớp, thì không tự đoán: ghi số ảnh và hỏi giảng viên. Không gán mô tô/xe máy, xe đạp, người, biển báo, hình xe trên biển quảng cáo hay hình phản chiếu.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_001` | **Bị che khuất:** xe con đỗ ven đường bên trái bị cột đèn và hàng rào che một phần; xe ở sát rìa trái bị cắt bởi khung ảnh. | Gán `car`, box ôm phần thân xe nhìn thấy; xe bị cắt thì kéo box chạm đúng rìa ảnh. | Quy tắc che khuất/rìa ảnh: chỉ vẽ phần nhìn thấy, không đoán phần bị che hay nằm ngoài khung; phần còn lại vẫn đủ nhận ra là xe con. |
| `drive_001` | **Vật thể nhỏ, ở xa:** các xe ở cuối đường phía trên bên phải (xe đỏ, xe trắng, xe vàng đang chạy) chỉ vài chục pixel, hình ảnh mờ do mưa. | Gán `car` cho xe còn thấy rõ hình dạng (≥ ~10 px mỗi chiều); xe chỉ còn là đốm màu thì không gán. | Quy tắc vật thể nhỏ/mơ hồ: chỉ gán khi xác định được lớp, không tự đoán nhãn. |
| `drive_001` | **Xe xa, khó phân lớp:** xe dài màu trắng ở cuối đường (góc trên phải) có thể là `bus`, nhưng quá nhỏ để chắc chắn. | Không gán; ghi lại case ID để hỏi giảng viên. | Schema phân biệt `bus`/`van` dựa vào chiều dài thân và số cửa sổ — ở kích thước này không thấy được đặc điểm đó. |
| `drive_001` | **Nhòe chuyển động:** xe bạc ở giữa phía dưới và xe trắng làn phải bị nhòe do chạy nhanh. | Gán `car`, box ôm phần thân xe rõ nhất, không ôm cả vệt nhòe. | Quy tắc box bám sát: box chạm điểm ngoài cùng của thân xe, không lấy thêm vệt nhòe hay bóng xe. |
| `drive_001` | **Xe thân hộp màu trắng** đỗ trước dãy nhà (sau xe con trắng), bị cây và xe khác che một phần. | Gán `van` nếu thấy rõ mui cao thẳng tới đuôi; nếu không chắc giữa `van`/`car` thì ghi case ID và hỏi giảng viên, không tự đoán. | Schema: `van` = thân hộp một khối, mui cao; khác `car` ở mui thấp/đuôi dốc. |
| `drive_002` | **Dòng xe dày đặc, chồng lấn nhiều:** hàng xe xếp hàng chờ đèn ở giữa phía trên, xe sau bị xe trước che gần hết, các box chồng lên nhau. | Mỗi xe một box `car` riêng, ôm phần thân nhìn thấy (thường chỉ là nóc + kính sau); xe bị che gần hết, không nhận ra lớp thì không gán. | Quy tắc che khuất: chỉ vẽ phần nhìn thấy, không vẽ phỏng đoán phần bị che; xe chồng nhau vẫn tách box riêng. |
| `drive_002` | **Xe buýt bị che một phần:** xe buýt đỏ-trắng bên trái và xe buýt phía sau xe buýt trắng (giữa ảnh) bị xe khác và cây che. | Gán `bus`, box ôm phần thân nhìn thấy; xe buýt trắng lớn phía trước gán `bus` riêng, không gộp với xe phía sau. | Schema `bus`: thân dài hình hộp, nhiều cửa sổ, cao hơn hẳn xe con — vẫn thấy rõ ở phần không bị che. |
| `drive_002` | **Xe bị cắt bởi rìa ảnh:** xe van trắng sát rìa phải và xe ở rìa trái chỉ thấy một phần thân. | Xe rìa phải gán `van`, xe rìa trái gán theo phần nhìn thấy; box kéo chạm đúng rìa ảnh. | Quy tắc rìa ảnh: không đoán phần nằm ngoài khung, vẫn gán nếu phần trong ảnh nhận ra lớp. |
| `drive_002` | **Phân biệt `car`/`van`/`truck`:** taxi đỏ-vàng, SUV trắng, xe thân hộp trắng ở xa làn phải (gần cột đèn). | Taxi và SUV gán `car`; xe thân hộp mui cao gán `van`; nếu ở xa không thấy rõ là van hay xe tải nhỏ thì ghi case ID và hỏi giảng viên. | Schema: taxi/SUV thuộc `car`; `van` mui cao thẳng tới đuôi; `truck` phải thấy cabin tách với thùng hàng. |
| `drive_002` | **Vật thể ngoài scope và xe rất nhỏ:** người đi bộ, xe máy, biển báo, đèn giao thông; xe ở cuối đường chỉ còn vài pixel. | Không gán người, xe máy, biển báo. Xe xa chỉ gán khi thấy rõ hình dạng (≥ ~10 px mỗi chiều). | Schema chỉ có 4 lớp xe; vật thể quá nhỏ không xác định chắc lớp thì không tự đoán. |
