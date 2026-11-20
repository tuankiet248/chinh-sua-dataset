# Nhiệm vụ YOLO2 — phát hiện vị trí ký tự

Ngày 20/11/2026 · Đọc kèm `00_Doc_truoc_Quy_uoc_chung.md` và `03_Checklist_nghiem_thu.md`.

## Yêu cầu đưa cho AI

> Bạn phụ trách phần YOLO2 của đồ án nhận diện biển số, chạy trên Google Colab. Hãy đọc tài liệu bàn giao chung và tài liệu này trước khi sửa mã. Chỉ dùng nguồn `roboflow_ocr_v1_yolov8.zip`; không gộp Winter hoặc Global. Giữ vai trò YOLO2 phát hiện vị trí ký tự, bảo toàn bảng nhãn ký tự gốc để bàn giao OCR. Tạo notebook riêng, trình bày như bài thực hành sách Machine Learning: mục đích, mã ngắn, kết quả thật, nhận xét dễ hiểu. Có hai chế độ xử lý/xem báo cáo; hỗ trợ chạy tiếp, cache đúng phiên bản và thao tác nguồn trên SSD khi phù hợp. Bắt đầu bằng kiểm tra cấu trúc, bảng lớp và mẫu ảnh–nhãn thật; không đoán số lớp hoặc ý nghĩa ID. Sau đó thống kê, rà trùng/lỗi, xuất bộ YOLO2 theo cấu trúc đã thống nhất và bàn giao metadata gốc cho OCR. Không sửa nguồn hay notebook YOLO1. Đánh dấu checklist chỉ khi có bằng chứng đã chạy; nếu thiếu quyền hoặc nguồn, báo cụ thể và tiếp tục phần không phụ thuộc. Không tự huấn luyện mô hình, không tự biến YOLO2 thành bộ phân loại 32 ký tự.

## 1. Kết quả cần đạt

Một bộ phát hiện vị trí ký tự từ Roboflow, có train/val/test truy vết về nguồn, báo cáo đầy đủ và bộ bảng nhãn gốc cho OCR. Trong bố cục nạp dữ liệu cũ đã đọc, YOLO2 có **một lớp phát hiện `0: char_num`**. Giữ vai trò này; phân biệt với 32 lớp nhận dạng OCR.

Trước khi chuẩn hóa ID sang 0, phải đọc `data.yaml`, toàn bộ bảng lớp gốc và ảnh minh họa để biết mỗi khung nguồn biểu thị gì. Nếu phát hiện cấu trúc không phải khung ký tự như dự kiến, báo mâu thuẫn trước khi chuyển đổi, không suy luận theo tên ZIP. Không lọc khung YOLO2 chỉ vì ký tự đó ngoài 32 lớp OCR: detector học vị trí ký tự; OCR có phạm vi lớp riêng.

## 2. Nguồn và mốc đối chiếu

Nguồn: `01_Du_lieu_goc/Roboflow/roboflow_ocr_v1_yolov8.zip`.

Danh tính đã kiểm kê: `licence-plate-vn/ocr-stypd`, v1. Số tệp ảnh nguồn: train 16.209, val 2.027, test 2.026, tổng 20.262. Người triển khai kiểm tra lại trước khi xử lý; khác số thì nêu nguyên nhân, không ép kết quả bằng cách bỏ tệp.

Mới có kiểm kê, **chưa có kết quả kiểm tra ảnh–nhãn toàn nguồn theo quy trình mới**. Không lấy số giữ lại từ pipeline gộp Roboflow + Winter cũ.

## 3. Bố cục notebook riêng

1. **Nguồn Roboflow chứa những gì?** Đường dẫn, danh tính, bảng lớp, số ảnh/nhãn theo split, thư viện và cấu hình.
2. **Ảnh và nhãn có khớp nhau không?** Khả năng đọc ảnh, ghép cặp, cú pháp/tọa độ, khung chồng nhau, ví dụ nhãn.
3. **Dữ liệu có đặc điểm gì?** Kích thước, số ký tự/ảnh, kích thước/tỷ lệ khung, tần suất ký tự gốc.
4. **Có ảnh liên quan giữa các tập không?** Pixel, tên gốc, gợi ý gần giống; bảng ảnh đối chiếu và quyết định.
5. **Giữ lại và xử lý như thế nào?** Quy tắc, số lượng trước–sau, tệp tạm giữ ngoài, không thay đổi nguồn.
6. **Ảnh đại diện và dữ liệu bàn giao.** Thư mục mẫu, manifest, kiểm tra bộ xuất và kết luận.

Mỗi mục có lời giải thích và nhận xét theo kết quả thật. Giữ mặc định `xem_bao_cao`; thiếu báo cáo không tự quét toàn nguồn.

## 4. Kiểm tra ảnh–nhãn

- Lập chỉ mục ảnh/nhãn theo đường dẫn tương đối đầy đủ, tránh ghép nhầm hai basename ở thư mục khác nhau. Bảo vệ đường dẫn khi đọc/giải nén archive.
- Đọc ảnh thật; ghi kích thước và kiểu giải mã dùng để hiểu tọa độ. Nếu xoay theo EXIF phải biến đổi khung tương ứng hoặc giữ nguyên hệ tọa độ và báo rõ.
- Kiểm tra mỗi dòng nhãn: 5 trường, ID lớp nguyên hợp lệ, số hữu hạn, rộng/cao dương, hình học trong biên với sai số số học được giải thích. Phân biệt nhãn thiếu, nhãn rỗng, nhãn lỗi và ảnh lỗi.
- Không tự sửa mọi bbox bằng clip. Chỉ sửa trường hợp có quy tắc/đối chiếu xác nhận; lưu khung trước–sau và kiểm tra lại. Khung thiếu một phần không được mặc định mất hết ảnh nếu các khung còn lại còn dùng được; mọi quyết định phải báo rõ phạm vi.
- Xem ảnh có khung và ký tự gốc trên mẫu ngẫu nhiên có seed, ký tự ít mẫu và trường hợp nghi ngờ. Khung có 5 số hợp lệ chưa chứng minh gán nhãn đúng.
- Ảnh biển có nhãn rỗng không tự coi là ảnh nền. Kiểm tra có ký tự nhưng thiếu khung hay không trước khi chấp nhận làm nền.
- Đếm bbox trùng/chồng bất thường để gợi ý xem lại; không tự xóa hai khung gần nhau chỉ vì IoU lớn.

## 5. Trùng lặp và cách chia tập

Ưu tiên giữ split gốc đã tải xuống. Tự động phát hiện pixel trùng, biến thể cùng ảnh gốc và các gợi ý gần giống; quan hệ chưa chắc chắn cần bảng ảnh đối chiếu.

Quy tắc xử lý đề xuất thống nhất với YOLO1: với nhóm liên quan đã xác nhận đi qua nhiều tập, ưu tiên **test → val → train**, giữ nguyên tên tập của các bản được giữ. Xét cả họ biến thể ảnh gốc, không chỉ bỏ một tệp trùng. Với ảnh trùng trong cùng tập, áp dụng quy tắc chọn đại diện được ghi rõ; không loại toàn bộ biến thể train chỉ vì cùng cha nếu không có lý do đánh giá/nhãn.

Nếu nhãn của hai bản pixel giống nhau mâu thuẫn, giữ riêng để đối chiếu, không hợp nhất hay bỏ phiếu nhãn tự động. Dấu dHash không đủ kết luận. Lưu cạnh quan hệ kèm loại bằng chứng và mã quyết định.

Kiểm tra không còn nhóm **đã xác nhận** xuất hiện đồng thời trong các tập bộ xuất. Cách diễn đạt này không bảo đảm mọi biển/xe ngoài phạm vi kiểm tra đã độc lập.

## 6. Bảng và biểu đồ cần có

| Bảng/hình | Nội dung cần phân biệt |
|---|---|
| Quy mô theo split | Số ảnh, số ảnh đọc được, tổng khung ký tự; không dùng lẫn hai đơn vị |
| Kích thước ảnh phổ biến | Top kích thước và phần còn lại; tỷ lệ trong đúng phạm vi mẫu/toàn nguồn |
| Phân bố số khung/ảnh | Nhãn rỗng, một/nhiều khung, nhóm đuôi phân bố |
| Kích thước khung ký tự | Rộng/cao, tỷ lệ diện tích, tỷ lệ hình dạng; không tự chọn ngưỡng loại từ biểu đồ |
| Tần suất ký tự nguồn | Lấy từ ID gốc đã xác minh, trước chuyển detector sang lớp 0 |
| Lỗi và trùng | Số ảnh duy nhất ảnh hưởng; số nhóm là bảng riêng, không cộng lẫn |
| Trước–sau xử lý | Theo split, số ảnh/khung và lý do; chỉ ghi “đã xuất” khi có bộ xuất thật |

Mỗi hình có bảng CSV đi kèm và nhận xét ngắn. Không thể hiện chữ nguồn bằng ID đoán. Có thể dành thêm hình chất lượng cho phụ lục nếu thực sự giúp giải thích quyết định.

## 7. Ảnh đại diện

Theo từng bản dữ liệu và nguồn Roboflow, chọn tối đa **5 kích thước ảnh phổ biến, 3 ảnh/kích thước**. Các số này có cấu hình, không phải hạn ngạch bắt buộc khi thiếu ảnh.

Lưu vào `04_Anh_dai_dien/YOLO2/Roboflow/<Ban>/<rong>x<cao>/`. Ghi rõ chọn từ nguồn ban đầu hay bộ giữ lại; hai phạm vi không ghi chung tên. Ưu tiên khác pixel/nhóm biển. Ảnh nộp giữ byte gốc nếu không có biến đổi ảnh; hình vẽ khung nằm riêng trong báo cáo. CSV mẫu ghi nguồn, split, kích thước, nhóm và đường dẫn gốc. Không chọn ảnh sau resize hàng loạt để báo rằng nguồn có một kích thước duy nhất.

## 8. Bộ huấn luyện đầu ra

Tại `02_Du_lieu_huan_luyen/YOLO2/<Ban>/yolo2_char_num/`:

| Đường dẫn | Nội dung |
|---|---|
| `images/train/`, `images/val/`, `images/test/` | Ảnh được giữ, tên không va chạm |
| `labels/train/`, `labels/val/`, `labels/test/` | TXT cùng stem với ảnh, bbox chuẩn hóa, lớp 0 |
| `data.yaml` | Tên lớp `char_num`, đường dẫn nạp đúng sau giải nén |
| `manifest.csv` | Ảnh/nhãn đầu ra → ảnh nguồn, split gốc/cuối, nhóm, trạng thái |
| `README.md` | Vai trò một lớp, cấu trúc, số lượng, cách nạp và giới hạn kiểm tra |

Không giả định file YAML đúng chỉ vì ghi được. Thử nạp dữ liệu sau giải nén ở thư mục khác với đường dẫn tạo ban đầu, xác minh các đường dẫn và cặp nhãn. Không cần huấn luyện để chứng minh việc nạp.

## 9. Bắt buộc bàn giao riêng cho OCR

Đặt trong `03_Bao_cao/03_YOLO2/<Ban>/Ban_giao_OCR/`:

- `bang_lop_goc.json`: ID nguồn → ký tự nguồn, nguồn chứng cứ và cách xác minh.
- `anh_bien_goc.csv`: thông tin chung của mọi ảnh nguồn, cả ảnh tạm giữ ngoài.
- `ky_tu_goc.csv`: **một dòng/một khung nguồn**, không dùng nhãn lớp 0 đã chuyển đổi.
- `quan_he_nhom.csv`: cạnh nối nhóm có bằng chứng, quyết định đã xác nhận/chưa rõ.
- `quyet_dinh_chat_luong.csv`: lỗi/đề xuất sửa/giữ ngoài, không ép OCR dùng cùng quyết định giữ toàn ảnh nếu chỉ một ký tự lỗi.
- `thong_tin_phien_ban.json`, `README.md`: schema, hash nguồn, quy tắc và đơn vị tọa độ.

`ky_tu_goc.csv` ngoài trường chung phải có: `source_box_id`, `source_line_index` (đánh số dòng từ 0), `original_class_id`, `character`, `x1_px`, `y1_px`, `x2_px`, `y2_px`, `bbox_status`, `reason_codes`. Tọa độ XYXY theo ảnh gốc, cạnh phải/dưới theo quy ước nửa mở; nếu nhãn gốc dạng khác thì lưu thêm dạng gốc và cách chuyển. `character` để rỗng nếu chưa giải mã đáng tin cậy, kèm lỗi, không đoán.

`parent_id`/`group_id` phải ổn định giữa hai người. OCR đọc toàn bảng nguồn hợp lệ để thực hiện chính sách riêng, không chỉ đọc ảnh được xuất YOLO2 hoặc mặc định bỏ Roboflow test khỏi gộp OCR. OCR chỉ kế thừa quan hệ đã xác nhận, không kế thừa bừa kết luận từ hash gần giống.

## 10. Mốc thực hiện và nghiệm thu

1. **Mốc A — Đọc đúng nguồn:** danh tính, số tệp, bảng lớp và vài trang ảnh có khung/ký tự thật. Chưa chạy đầy đủ nếu mapping chưa rõ.
2. **Mốc B — Kiểm tra mẫu:** mẫu nhỏ cố định, kiểm chứng nhãn chuyển về lớp 0 không mất bảng ký tự gốc; kiểm tra chạy tiếp và chế độ xem.
3. **Mốc C — Phân tích đầy đủ:** mọi ảnh được xét hoặc có lỗi ghi riêng; bảng/biểu đồ, nhóm trùng, phương án xử lý.
4. **Mốc D — Xuất và kiểm tra:** cặp ảnh–nhãn, số lượng, class/bounds, nhóm qua tập, tệp nén và cách nạp sau giải nén.
5. **Mốc E — Bàn giao:** notebook, mã, báo cáo, bộ dữ liệu, ảnh đại diện và bộ metadata OCR. Chạy lại notebook chế độ xem, mở được bảng/hình mà không scan nguồn.

Các kiểm tra quan trọng: nguồn đổi làm cache cũ không được dùng; tiếp tục sau ngắt không thêm/bỏ mẫu; chỉ đổi I/O thì các bản ghi tương đương; nhãn gốc còn nguyên dù nhãn detector là 0. Ghi rõ kiểm tra giả lập và kiểm tra trên dữ liệu thật.
