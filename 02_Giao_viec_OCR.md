# Nhiệm vụ OCR — nhận dạng từng ký tự

Ngày 20/11/2026 · Đọc kèm `00_Doc_truoc_Quy_uoc_chung.md` và `03_Checklist_nghiem_thu.md`.

## Yêu cầu đưa cho AI

> Bạn phụ trách chuẩn bị dữ liệu OCR 32 ký tự cho đồ án biển số, chạy trên Google Colab. Đọc tài liệu chung và tài liệu nhiệm vụ trước khi sửa mã. Chỉ gộp Roboflow, Winter2897, CCPD2019 để tạo train/validation khoảng 80/20 theo số ảnh ký tự, giữ nguyên nhóm biển/biến thể trong một tập. VNLP chỉ làm test riêng, không bổ sung vào train hay dùng để chọn ngưỡng. Hướng lọc mới đánh giá từng ký tự: một ký tự đạt vẫn có thể giữ nếu ảnh cắt đúng vị trí và khớp nhãn; bỏ ngưỡng cứng tối thiểu 4/6 hoặc 5/6 ký tự/biển của mã cũ. Phải giữ chỉ số vị trí gốc, không ghép các vùng tìm được với chuỗi nhãn sau khi đã bỏ vị trí lỗi. Chưa có ngưỡng chất lượng và quy mô 3k/10k nào được chốt cho bản mới. Tạo notebook riêng theo cách trình bày sách thực hành, có chế độ xử lý/xem báo cáo và tiến độ chạy tiếp. Bắt đầu kiểm tra nguồn, mapping lớp và thử crop có đối chiếu nhãn trên mẫu thật; chưa rõ thì ghi cụ thể, không tự suy diễn. Thống kê từng nguồn/ký tự trước khi gộp, kiểm tra quan hệ trùng qua nguồn, chia nhóm rồi mới xem xét cân bằng train. Bàn giao bộ dữ liệu, báo cáo, manifest và các giới hạn còn lại. Không sửa YOLO1, không huấn luyện mô hình và không tự gọi bộ cũ là test VNLP mới.

## 1. Phạm vi đã chốt

| Nội dung | Quy định |
|---|---|
| Nhận dạng | Một ảnh đầu vào chứa một ký tự |
| Bộ lớp | `0123456789ABCDEFGHKLMNPQRSTUVXYZ` |
| ID | 0–31 theo đúng thứ tự chuỗi; cùng ánh xạ cho mọi tập |
| Train/validation | Gộp Roboflow + Winter2897 + CCPD2019, mục tiêu khoảng 80/20 theo số crop ký tự |
| Test | VNLP riêng, từ nguồn gốc, không tham gia cân bằng hoặc chọn ngưỡng |
| Nhóm chia tập | Biển/ảnh gốc, các biến thể và các quan hệ trùng đã xác nhận cùng một tập |
| Lọc | Theo từng ký tự nếu vị trí–nhãn còn đáng tin cậy |
| Cân bằng | Chỉ train, sau thống kê; chưa áp mục tiêu số lượng mới |

Các ký tự I/J/O/W và ký tự ngoài tập không tự đổi thành ký tự gần giống. Ghi riêng số vị trí ngoài phạm vi, không gọi mọi trường hợp này là “ảnh lỗi”. CCPD có ký tự tỉnh Trung Quốc; bỏ vị trí ngoài phạm vi nhưng phải giữ nguyên chỉ số của những vị trí còn lại.

## 2. Những gì chưa được xác minh

- Bảng ID–ký tự Winter và từng loại nhãn bên trong nguồn cần đối chiếu lại.
- CCPD: cách giải mã tên tệp, thứ tự đỉnh, hệ tọa độ, phép nắn biển và cách ghép vị trí–nhãn phải kiểm tra bằng ảnh thật. Không mặc định mọi biển đều có đúng sáu ký tự thuộc phạm vi.
- VNLP: cần xác định có nhãn từng ký tự, khung ký tự hay chỉ có chuỗi biển. Chưa thể hứa số lượng test hoặc crop hoàn toàn tự động chính xác.
- Chưa có ngưỡng độ nét/diện tích/độ sáng của bộ mới đã được xác nhận.
- Chưa chốt lấy toàn bộ ba nguồn hay giới hạn theo số mẫu. Kiểm kê metadata đủ phạm vi; lượt thử và bất kỳ giới hạn quét/crop nào phải báo rõ.

Không bắt đầu bằng việc đổi `TARGET=10000` rồi chạy pipeline cũ. Bộ cũ từng dùng VNLP để bổ sung train và có ngưỡng 4/6–5/6, không phù hợp phương án này.

## 3. Bố cục notebook

1. **Mỗi nguồn cung cấp những gì?** Đường dẫn, cấu trúc, loại nhãn, số ảnh biển, lớp và mức xác minh.
2. **Chúng ta lấy ký tự từ ảnh như thế nào?** Quy tắc từng nguồn, ảnh biển có chỉ số vị trí, crop và ký tự gán tương ứng.
3. **Quy tắc lọc có giữ đúng ký tự không?** Mẫu được giữ, sát ngưỡng, bị loại, nhóm ít mẫu và lỗi ghép nhãn.
4. **Mỗi ký tự có bao nhiêu mẫu từ từng nguồn?** Trước gộp/cân bằng; nêu cả lớp không có mẫu.
5. **Chia train/validation và bảo vệ test ra sao?** Nhóm, trùng qua nguồn, tỷ lệ thực tế và phạm vi kiểm tra leak.
6. **Dữ liệu có mất cân bằng không?** Kết quả trước–sau nếu có áp dụng phương án cân bằng train được chọn.
7. **Bộ OCR và ảnh đại diện bàn giao.** Cấu trúc nạp, mapping, thống kê, thời gian và giới hạn.

Giải thích ngắn bằng tiếng Việt dễ hiểu. Mọi bảng/hình ghi “mẫu thử” hoặc “toàn bộ phạm vi đã cấu hình”; không lấy output lần chạy cũ làm kết quả mới.

## 4. Cách đọc từng nguồn

### Roboflow

Nhận `bang_lop_goc.json`, `anh_bien_goc.csv`, `ky_tu_goc.csv` và quan hệ nhóm từ người làm YOLO2. Đối chiếu mapping với nguồn thật. Mỗi khung giữ `source_box_id`/dòng gốc; crop bằng tọa độ của chính ảnh tương ứng, không dùng nhãn detector đã đổi thành 0 để suy chữ.

OCR dùng Roboflow như một nguồn trong pool train/val, **không bắt buộc giữ split Roboflow như YOLO2**. Giữ cả `original_split` và split OCR mới. Nếu bảng YOLO2 chỉ có ảnh xuất cuối, yêu cầu thêm bảng nguồn thay vì tự mất các mẫu test gốc. Các quyết định chất lượng toàn ảnh của detector không thay việc đánh giá từng crop OCR.

### Winter2897

Đọc cấu trúc ZIP và `winter_classes.txt`, đối chiếu nhãn thực tế, số dòng/tên lớp và ảnh có ký tự hiển thị. Không mặc định ID giống Roboflow, không sắp xếp lại bảng lớp theo alphabet rồi gán lại ID. Nếu có crop sẵn, xác định quan hệ với ảnh biển cha từ metadata, không tự coi mỗi crop là cha độc lập để chia ngẫu nhiên.

### CCPD2019

Phân biệt rõ **nhãn chuỗi trong tên ảnh** với **khung/vị trí từng ký tự**: chuỗi đúng không tự tạo ra khung ký tự đúng. Giải mã chuỗi bằng mapping có nguồn chứng cứ; không sao chép bảng index không rõ phiên bản.

Kiểm tra vùng biển và thứ tự đỉnh bằng ảnh vẽ điểm; nắn biển nếu dùng phải lưu ma trận/khung tham chiếu và hệ tọa độ. Thuật toán tìm vị trí ký tự phải được kiểm chứng. Không chia đều biển thành các ô rồi coi đó là ground truth mà không kiểm tra độ lệch/biển nhiều hàng/ký tự bị che.

Nếu vị trí–nhãn của một biển không xác định đáng tin cậy, tạm giữ ngoài và ghi lý do; không dùng dự đoán của một mô hình chưa kiểm chứng làm nhãn chuẩn. Nếu một vị trí bị mờ hoặc crop lỗi nhưng các vị trí khác vẫn khớp độc lập, chỉ bỏ vị trí lỗi.

### VNLP

Dùng `01_Du_lieu_goc/VNLP/VNLP.zip` làm nguồn test. Không lấy 4.063 crop từng được chọn để bổ sung train trong pipeline cũ làm toàn bộ test mới.

Nếu chỉ có chuỗi biển mà không có vị trí ký tự, ghi rõ bước xác định vị trí, mức kiểm chứng và phần chưa thể tạo nhãn đáng tin. Không tự gán mọi crop tìm được theo thứ tự để làm test. Giữ danh sách mọi ảnh nguồn thuộc phạm vi đánh giá, số ảnh/slot không xử lý được và lý do để tránh che giấu thiên lệch chọn mẫu.

Đóng băng quy tắc test trước khi đánh giá mô hình; dùng chính sách cố định có mô tả. Nếu cần xem mẫu VNLP để kiểm tra định dạng hoặc độ đúng ground truth, đó là kiểm tra nhãn, không dùng kết quả test để chọn ngưỡng/thiết kế mô hình. Nếu dùng một phần VNLP để hiệu chỉnh phương pháp, phải tách phần đó khỏi test và báo người tích hợp; không âm thầm vừa hiệu chỉnh vừa báo cùng tập là test độc lập.

## 5. Quy tắc lọc từng ký tự

Thứ tự xử lý gợi ý:

1. Kiểm tra ảnh đọc được và vùng biển có hình học đáng tin cậy.
2. Giải mã nhãn; xác định tương ứng từng vị trí ký tự gốc và vùng ảnh.
3. Với từng vị trí: xác định ký tự có thuộc 32 lớp; kiểm tra khung/crop/độ rõ; giữ hoặc loại có lý do.
4. Ghi crop được giữ cùng **vị trí gốc**, kể cả khi những vị trí trước/sau không đạt.
5. Chỉ loại toàn biển khi lỗi nền tảng làm mọi phép ghép nhãn/vị trí không đáng tin. Số vị trí đạt không phải ngưỡng loại toàn biển.

Ví dụ: vị trí trong phạm vi là `0:A, 1:B, 2:C, 3:1, 4:2, 5:3`. Vị trí 1 lỗi thì các crop còn lại vẫn mang nhãn `A, C, 1, 2, 3` với chỉ số `0,2,3,4,5`. **Không** lấy năm crop còn lại rồi gán năm ký tự đầu `A,B,C,1,2`.

Một ký tự duy nhất vẫn được giữ nếu mapping vị trí–nhãn của nó đáng tin cậy. “Cho giữ một ký tự” không có nghĩa mọi lát ảnh nhìn giống chữ đều được nhận.

Các ngưỡng hình học/chất lượng là tham số có giải thích, được chọn bằng mẫu hiệu chỉnh từ nguồn train/val; không tự đặt ngưỡng cao để làm số lượng đẹp. Bản crop xuất giữ thông tin hình ảnh cần thiết; resize/padding/chuẩn hóa cho kiến trúc chưa chọn nên để bước nạp dữ liệu, trừ khi có lý do đã kiểm chứng. Không biến mọi crop về một kích thước rồi báo đó là phân bố kích thước gốc.

## 6. Thử và kiểm chứng trước khi chạy lớn

Chọn mẫu có seed từ mỗi nguồn, gồm ngẫu nhiên và nhóm khó: ký tự hiếm, ảnh nhiều hàng, mờ/tối/sáng, sát mép, che khuất, sát ngưỡng giữ/loại. Số mẫu thử là cấu hình, không phải số đã được chạy trong tài liệu này.

Tạo trang ảnh cho từng trường hợp: ảnh biển, vùng/slot đánh số, nhãn gốc, crop, nhãn crop, kết quả giữ/loại và lý do. Với CCPD, hiển thị cả thứ tự đỉnh và biển đã nắn nếu dùng. Xem riêng mẫu bị loại để phát hiện bộ lọc đang bỏ nhầm quá nhiều.

Điều chỉnh trên mẫu hiệu chỉnh, sau đó xem một **mẫu khác chưa dùng điều chỉnh** từ các nguồn train/val. Ghi số mẫu đã xem và tỷ lệ lỗi quan sát được, không suy thành bảo đảm toàn dataset. Không tuyên bố đạt độ chính xác crop nếu chưa có đối chiếu đủ để tính.

## 7. Trùng lặp và chia nhóm

1. Lập ID biển cha có namespace nguồn, nối biến thể gốc đã xác nhận. Kiểm tra ảnh biển trùng qua nguồn trước khi chia.
2. Xem thêm pixel crop trùng sau cắt; nhãn khác trên cùng pixel phải tạm giữ ngoài để đối chiếu, không bỏ phiếu tự động. Pixel trùng cùng nhãn đi qua split cũng phải được xử lý/ghi nhận.
3. Không nối toàn bộ biển khác nhau chỉ vì cùng một ký tự có crop giống nhau. Lưu riêng cạnh quan hệ biển và cạnh trùng crop. Với crop trùng không chứng minh cùng biển, có thể giữ một đại diện ở một tập hoặc tạm giữ crop gây giao nhau theo quy tắc xác định, rồi tính lại; không tạo một nhóm khổng lồ không có bằng chứng.
4. Giữ VNLP test riêng. Nếu có ảnh/biển hoặc crop trùng đã xác nhận với ba nguồn học, ưu tiên bảo vệ test và tạm giữ ngoài những mẫu liên quan trong pool học; ghi phạm vi ảnh hưởng. Không chuyển mẫu VNLP sang train, không âm thầm loại mẫu test khó.
5. Với pool ba nguồn còn lại, chia **theo nhóm**, seed cố định, hướng đến 80/20 theo tổng crop. Có thể dùng thuật toán cố gắng cân đối nguồn/ký tự nhưng không phá nhóm để đạt tỷ lệ đẹp.
6. Sau chia, tính lại tỷ lệ thực tế, lớp thiếu, số nhóm và giao nhau. Nếu một lớp chỉ có một nhóm, có thể không thể có đủ ở cả hai tập; báo rõ, không sao chép sang val.

Trùng ảnh/nhóm qua nguồn có thể có tính bắc cầu. Dùng các quan hệ đã xác nhận để nối nhóm; hash gần giống chỉ là ứng viên. Khi một crop/ảnh bị tạm giữ ngoài vì nhiều lý do, đếm mẫu duy nhất một lần ở tổng và có bảng đa lý do riêng.

Kết luận kiểm tra theo phạm vi: không còn giao nhau được phát hiện theo quy tắc đã áp dụng, không khẳng định đã chứng minh mọi xe/biển độc lập trên thế giới.

## 8. Thống kê và mẫu số bắt buộc

Lưu riêng số **ảnh/biển xét**, **vị trí ký tự xét**, **crop tạo được**, **crop giữ lại**, **crop xuất thực tế**. Không dùng chúng thay nhau khi tính tỷ lệ lọc. Nếu không xác định được vị trí nào, ghi ở bảng biển thay vì tự tạo sáu vị trí lỗi.

| Bảng/hình | Nội dung |
|---|---|
| Kiểm kê nguồn | Số ảnh/biển, kiểu nhãn, phạm vi đã quét và chưa quét |
| Ký tự × nguồn trước gộp | Đủ 32 hàng, nguồn thiếu ghi 0; có số trước và sau lọc |
| Lý do lọc theo nguồn | Cấp biển và cấp slot tách riêng; tổng mẫu duy nhất và lý do chính/phụ |
| Nguồn × split | Số crop, số nhóm và tỷ lệ thực tế sau chia |
| Ký tự × split | Train/val của pool và VNLP test ghi rõ khác nguồn |
| Tỷ trọng nguồn theo ký tự | Trong mỗi lớp/tập, Roboflow/Winter/CCPD đóng góp bao nhiêu phần trăm |
| Mất cân bằng train | Lớp nhiều/ít, tỷ lệ, lớp vắng; trước–sau nếu có xử lý |
| Kích thước crop | Theo nguồn; thống kê trước resize, có ảnh đại diện |

Công thức phải được ghi trong báo cáo: `% nguồn trong tập = số crop nguồn đó/tổng crop của tập × 100`; `% nguồn trong ký tự = số crop nguồn đó của ký tự/tổng crop của ký tự trong cùng tập × 100`. Mẫu số bằng 0 ghi “không có mẫu”, không chia hoặc vẽ phần trăm giả.

Các biểu đồ nên có: cột ký tự phân màu nguồn, cột tỷ trọng nguồn theo train/val, heatmap ký tự × tập và biểu đồ trước/sau lọc theo nguồn. Không tự ép ba nguồn bằng nhau hoặc 32 lớp bằng nhau. CCPD có thể nhiều hơn hẳn; báo tỷ trọng rồi đề xuất phương án dựa trên mục tiêu Việt Nam.

## 9. Cân bằng chỉ trên train

Mặc định bàn giao trước một bản thống kê dữ liệu hợp lệ chưa cân bằng. Có thể đề xuất trọng số lớp, sampler hoặc lấy mẫu có giới hạn nguồn/lớp; không tự cắt xuống theo lớp hiếm nhất.

Nếu nhân bản/augmentation được chọn, thực hiện sau chia và chỉ train. Ghi số crop gốc duy nhất và số mẫu hiệu dụng/biến thể riêng; bản sao không phải dữ liệu độc lập mới. Validation/test giữ nguyên chính sách đã đóng băng. Không dùng thiếu hụt của lượt cũ 3k để báo thiếu hụt của bản mới.

## 10. Tối ưu CCPD và chạy tiếp

- Kiểm tra dung lượng nguồn, ổ tạm, crop dự kiến trước khi chọn chiến lược. Không giải nén mọi ảnh rời lên Drive.
- TAR.XZ đọc tuần tự; đọc theo lô một lượt và lưu metadata/crop trung gian lên SSD, đồng bộ phần hoàn tất cần thiết về Drive. Nếu phần nén không hỗ trợ nhảy nhanh, nêu chi phí đọc lại khi resume.
- Không viết checkpoint sau mỗi crop nhỏ hoặc quét lại toàn bộ bộ sạch mỗi lần bổ sung. Mỗi phần có số mẫu, dấu kiểm tra, cấu hình và trạng thái hoàn tất.
- Nếu đổi ngưỡng cần xem lại mẫu từng loại, phải giữ đủ dữ liệu trung gian/chỉ mục để làm lại đúng phần; cache chỉ gồm mẫu giữ không đủ để đánh giá ngưỡng mới.
- Chỉ đóng gói kết quả sau khi dữ liệu/split ổn định. Đổi biểu đồ không làm lại crop. Chỉ tính các hash/bước kiểm tra sâu một lần cho đúng phiên bản thay vì mỗi lần mở báo cáo.
- Đo đọc nguồn, định vị/nắn, crop/lọc, lưu phần, ghép/chia tập, thống kê và ZIP riêng; không cộng nhầm thời gian resume thành thời gian toàn bộ.

## 11. Bộ OCR đầu ra

Tại `02_Du_lieu_huan_luyen/OCR/<Ban>/ocr_ky_tu/`, giữ bố cục bộ nạp hiện có:

| Đường dẫn | Nội dung |
|---|---|
| `images/train/`, `images/val/`, `images/test/` | Mỗi ảnh một ký tự, tên duy nhất |
| `labels/train/`, `labels/val/`, `labels/test/` | TXT tương ứng chứa một ký tự |
| `manifest.csv` | Cách nạp và truy vết từng crop |
| `class_to_idx.json` | 32 lớp theo thứ tự đã chốt, ID cố định |
| `README.md` | Cấu trúc, chính sách lọc/chia, số thực tế, cách nạp và giới hạn |

Manifest tối thiểu: `crop_id`, `image`, `label_file`, `character`, `class_id`, `split`, `source`, `source_file`, `source_member`, `source_image_id`, `original_split`, `parent_id`, `group_id`, `source_box_id`, `original_slot_index`, `width_px`, `height_px`, `pixel_sha256`, `rule_version`.

Với nguồn có bbox từng ký tự, `source_box_id` là khóa tương ứng dòng nguồn; `original_slot_index` chỉ điền khi xác định được vị trí thật, không dùng thứ tự dòng TXT như thứ tự đọc chữ. Với CCPD có nhãn theo slot, giữ chỉ số slot gốc kể cả vị trí ngoài 32 lớp. Trường không áp dụng để rỗng và giải thích; không tạo giá trị giả.

Lưu riêng `nguon_va_cau_hinh.json`, `nhat_ky_loc_bien.csv`, `nhat_ky_loc_ky_tu.csv`, `quan_he_nhom.csv`, `kiem_tra_giao_nhau.json`, bảng thống kê và ghi nhận xem ảnh trong `03_Bao_cao/04_OCR/<Ban>/`. Nhật ký giữ cả mẫu loại, lý do chính/phụ và phạm vi đã xét.

Ảnh đại diện: tối đa 5 kích thước crop phổ biến, 3 ảnh/kích thước, riêng từng nguồn trong `04_Anh_dai_dien/OCR/<Nguon>/<Ban>/<rong>x<cao>/`. Đây là crop thực xuất trước resize, không phải ảnh nguyên biển; bảng mẫu ghi nhãn, split và biển cha. Hình có chú thích và ảnh biển đối chiếu lưu riêng trong báo cáo.

## 12. Mốc và điều kiện bàn giao

1. **Mốc A — Xác minh nguồn/mapping:** bảng lớp từng nguồn, kiểu nhãn, ví dụ có chỉ số slot, những chỗ chưa rõ. Chưa rõ mapping thì chưa chạy crop toàn nguồn đó.
2. **Mốc B — Hiệu chỉnh trên mẫu:** trang ảnh giữ/loại/sát ngưỡng, kiểm tra mẫu khác, ca một ký tự đạt và ca thiếu slot không lệch nhãn.
3. **Mốc C — Thống kê trước gộp:** số nguồn/ký tự, lý do lọc, phạm vi đã quét; nếu giới hạn mẫu thì ghi rõ.
4. **Mốc D — Gộp/chia nhóm:** VNLP test riêng, giao nhau, tỷ lệ thực tế, lớp vắng; chưa cân bằng để nhìn đúng nguồn.
5. **Mốc E — Xuất:** manifest, 32 lớp, ảnh/nhãn, thống kê khớp bộ xuất, đọc thử DataLoader/dataset nhỏ không cần train.
6. **Mốc F — Bàn giao:** notebook xem báo cáo mở đủ bảng/hình, mã, dữ liệu, ảnh đại diện, checklist và giới hạn. Nếu có đề xuất cân bằng chưa áp thì ghi đúng trạng thái.

Kiểm tra phải có ca tổng hợp chứng minh thiếu slot giữa chuỗi không làm lệch nhãn; một ký tự đạt được giữ; mapping lớp nguồn khác nhau vẫn đúng; nhóm nhiều nguồn không rơi hai tập; crop trùng khác nhãn bị tạm giữ ngoài; resume không nhân đôi; đổi quy tắc làm cache liên quan hết hiệu lực. Trên dữ liệu thật còn phải xem crop và kiểm tra bộ xuất; test giả không thay thế việc này.
