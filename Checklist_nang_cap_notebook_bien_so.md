# Checklist nâng cấp notebook phân tích dữ liệu biển số

Cập nhật: 20/11/2026. Tài liệu này theo dõi công việc đã hoàn thành và việc còn phải làm. Ô chưa đánh dấu không được hiểu là đã triển khai trên Colab.

## Mục tiêu

Notebook dễ đọc theo cách trình bày của các bài thực hành trong sách Machine Learning: giải thích mục đích, mã xử lý, bảng hoặc hình, nhận xét và kết luận. Chạy hiệu quả, có thể tiếp tục sau khi gián đoạn và không làm sai số liệu khi dùng lại kết quả.

## 1. Những quyết định đã thống nhất

- [x] Chỉnh trên bản sao của notebook cũ; giữ bản cũ để đối chiếu.
- [x] YOLO1 chỉ dùng **Global v13**, theo yêu cầu được làm rõ ngày 20/11/2026; thay lựa chọn `vietnamese-license-plate-1` trước đó.
- [x] YOLO2 chỉ dùng `roboflow_ocr_v1_yolov8`.
- [x] Giữ các loại thống kê cần thiết ở YOLO1 và YOLO2; bỏ việc gộp nhiều nguồn. Số liệu phải tính lại theo nguồn được chọn.
- [x] Giữ hai chế độ: xử lý dữ liệu và xem lại kết quả đã lưu.
- [x] Dùng cách trình bày của notebook sách: tiêu đề dễ hiểu, giải thích ngắn, kết luận dựa trên kết quả thực tế.
- [x] Đưa hiệu quả chạy và tính đúng đắn thành yêu cầu kiểm tra ở từng bước.
- [x] OCR: Roboflow + Winter + CCPD làm train/validation; VNLP làm test riêng.
- [x] YOLO1 và YOLO2 ưu tiên giữ train/validation/test trong từng bộ tải xuống, sau khi kiểm tra trùng lặp; không lấy cách chia từ các bộ đã gộp cũ.
- [x] OCR chia khoảng 80% train, 20% validation theo số ảnh ký tự, nhưng giữ nguyên nhóm biển gốc trong một tập; tỷ lệ thực tế có thể chênh nhẹ. Đối chiếu ảnh trùng và các ảnh cùng biển giữa các nguồn/tập.
- [x] Hướng lọc OCR: đánh giá từng ký tự, bỏ yêu cầu cứng phải có ít nhất 4/6 hoặc 5/6 ký tự đạt mới giữ. Một ký tự đạt cũng có thể giữ nếu ảnh cắt đúng vị trí, khớp nhãn và đạt chất lượng.
- [x] Chỉ loại cả biển khi có vấn đề làm việc trích xuất/ghép nhãn không đáng tin cậy, như ảnh lỗi, vùng biển sai hoặc không xác định được tương ứng vị trí–nhãn. Số lượng ký tự đạt không phải điều kiện cứng để giữ cả biển.
- [ ] Chọn và kiểm chứng các ngưỡng chất lượng bằng ảnh thực tế; hướng lọc mới chưa được triển khai hay xác nhận đạt chất lượng.
- [ ] Xác định quy mô lấy mẫu OCR sau khi có thống kê; không tự mang mục tiêu bổ sung 3k/10k từ quy trình cũ sang.

### Thư mục chung đã thống nhất

Thư mục chính: `MyDrive/ML/Do_an_bien_so/`.

| Thư mục | Nội dung |
|---|---|
| `01_Du_lieu_goc` | Mỗi nguồn một thư mục: `Global_v13`, `Roboflow`, `Winter2897`, `CCPD2019`, `VNLP` |
| `02_Du_lieu_huan_luyen` | Dữ liệu đã xử lý, chia riêng `YOLO1`, `YOLO2`, `OCR`; giữ bố cục huấn luyện đã thống nhất |
| `03_Bao_cao` | Bảng thống kê, biểu đồ và kết luận |
| `04_Anh_dai_dien` | Chia theo nhiệm vụ/nguồn/kích thước |
| `05_Notebook` | Notebook và hướng dẫn |
| `06_Ket_qua_cu` | Kết quả trước đây để đối chiếu |

Đã tạo cấu trúc này trên Drive, chuyển năm tệp nguồn và bảng nhãn Winter vào `01_Du_lieu_goc`, kiểm tra lại đường dẫn và chuyển notebook mới vào `05_Notebook`. Roboflow dùng chung một bản nguồn cho YOLO2 và OCR. Các gói kết quả cũ vẫn ở vị trí cũ để đối chiếu; `06_Ket_qua_cu` được dành cho việc lưu trữ sau này.

## 2. Thực hiện theo thứ tự

### Bước 1 — Mở đầu, cấu hình và kiểm tra nguồn

- [x] Tạo notebook mới cho bước 1, dựa trên cấu hình và mã tham chiếu cũ; không sao chép nguyên pipeline gộp cũ sang để chạy.
- [x] Giới thiệu nhiệm vụ của YOLO1, YOLO2 và OCR bằng ngôn ngữ dễ hiểu.
- [x] Viết và chạy phần lập bảng nguồn, vai trò, đường dẫn và tình trạng có/thiếu trên Drive thật.
- [x] Xác nhận Global v13 và Roboflow v1 từ đường dẫn dự án và dòng phiên bản trong hai tệp README của mỗi ZIP trên Drive; sửa bộ kiểm tra để hỗ trợ định dạng README tách thông tin. Nguồn VN v1 cũ không nằm trong danh sách nguồn hiện dùng.
- [x] Xác định từ log trong notebook đính kèm: lần chạy cũ dùng `ML/license-plate-dataset.zip` làm nguồn `global`. Đã kiểm tra README của tệp hiện tại ở bước 1 và xác nhận Global v13.
- [x] Gom cấu hình bước 1 vào một nơi: chế độ chạy, nguồn dữ liệu, thư mục kết quả và đường dẫn notebook cũ.
- [x] Chế độ xem lại của bước 1 chỉ đọc báo cáo đã lưu; nếu thiếu kết quả, báo rõ phần còn thiếu, không tự chạy xử lý nặng. Đã kiểm tra bằng dữ liệu giả.
- [x] Chạy bước 1 trên Drive thật: tìm được đủ năm tệp nguồn, xác nhận Global/Roboflow, đếm các tập gốc và đối chiếu notebook cũ. Kiểm tra sâu nội dung Winter/CCPD và chất lượng ảnh vẫn thuộc các bước sau.
- [x] Chuyển năm tệp nguồn và `winter_classes.txt` vào thư mục chung; kiểm tra tồn tại ở đích, dung lượng giữ nguyên và đường dẫn cũ không còn tệp. Lưu lịch sử cũ → mới.
- [x] Kiểm tra chế độ xem trên Colab: chạy lại toàn bộ notebook và hiển thị báo cáo đã lưu, không quét nguồn hay di chuyển tệp. Giữ mặc định `xem_bao_cao`, `SAP_XEP_NGUON=False`.

Điều kiện hoàn thành: người đọc biết dùng nguồn nào cho từng nhiệm vụ; có thể kiểm tra cấu hình mà chưa phải giải nén hay xử lý toàn bộ ảnh.

### Bước 2 — Phân tích dữ liệu YOLO1

- [x] Triển khai mã phân tích nguồn riêng, lưu tiến độ, bảng/hình và ảnh đại diện. Đã chạy đạt 15 kiểm tra giả lập, gồm đối chiếu đọc song song, chạy tiếp sau gián đoạn và từ chối tiến độ cũ khi phiên bản giải mã thay đổi.
- [x] Chạy thử 300 ảnh thật trên Colab: 294 train, 5 val, 1 test; đọc được cả 300 ảnh, 318 khung đúng định dạng, chưa thấy ảnh lỗi hoặc nhãn thiếu/sai cú pháp trong mẫu này. Không suy rộng thành tỷ lệ lỗi toàn nguồn.
- [x] Xuất 15 ảnh nguyên bản của mẫu thử theo năm kích thước phổ biến, kèm bảng nguồn và hình đối chiếu có khung lưu riêng.
- [x] Đo cùng 256 ảnh trên ZIP ổ tạm: một luồng 2,449 giây, hai luồng 1,820 giây, bản ghi khớp hoàn toàn. Thứ tự đo có thể chịu ảnh hưởng cache; chưa coi là phép đo toàn pipeline.

- [x] Đọc riêng toàn bộ **Global v13**, không gộp với `vietnamese-license-plate-1`.
- [ ] Kiểm tra và ưu tiên giữ cách chia gốc của bộ Global v13 tải xuống, xử lý trùng lặp giữa các tập; kết quả đã gộp cũ chỉ đưa Global vào train nên không dùng nguyên cách chia đó cho phương án mới.
- [x] Thống kê toàn nguồn: 101.866 ảnh đọc được, 106.098 khung đúng định dạng, cách chia gốc, kích thước ảnh/vùng biển và đặc điểm chất lượng.
- [x] Chạy kiểm tra tự động toàn nguồn: 26 ảnh nhãn sai quy tắc, 144 ảnh nhãn rỗng, 67 nhóm trùng pixel qua nhiều tập; 72 nhóm liên quan qua nhiều tập khi nối thêm tên gốc.
- [x] Xem bảng ghép của 67 nhóm pixel, 26 khung vượt biên, 144 ảnh nhãn rỗng, 134 gợi ý dHash với 285 đại diện và năm nhóm chỉ cùng tên. Lưu ghi nhận theo mã; chưa chứng nhận mọi ảnh ở độ phân giải gốc.
- [x] Lập phương án sau rà soát: 26 khung có thể chỉnh vào biên; 28 ảnh thấy biển rõ nhưng thiếu nhãn, 56 ảnh không thấy biển trên bảng ghép, 60 ảnh cần xem thêm. Xác nhận 133 gợi ý quan hệ ảnh/cảnh/xe; giữ H123 chưa kết luận.
- [x] Kiểm tra phương án dự kiến 95.941 train, 2.023 val, 1.019 test; không còn nhóm đã xác nhận được giữ ở nhiều tập. Chưa áp dụng vào bộ huấn luyện.
- [ ] Xem lại những mẫu từng bị loại do so sánh với nguồn nay không còn sử dụng.
- [x] Xuất 15 ảnh gốc của toàn nguồn theo năm kích thước phổ biến vào nhánh YOLO1/Global_v13/Toan_bo; kèm bảng nguồn và hình có khung trong báo cáo.
- [x] Viết nhận xét và kết luận phân tích nguồn; giữ riêng mẫu thử và toàn nguồn.
- [ ] Hoàn tất quyết định làm sạch/chia tập, lập số lượng trước–sau và xuất bộ YOLO1 huấn luyện.

### Bước 3 — Phân tích dữ liệu YOLO2

- [ ] Đọc riêng bộ Roboflow, bỏ bước gộp với Winter.
- [ ] Kiểm tra và ưu tiên giữ cách chia gốc trong bộ tải xuống sau khi xử lý trùng lặp giữa các tập.
- [ ] Giữ các thống kê ảnh, nhãn ký tự, kích thước, chất lượng và trùng lặp phù hợp nhiệm vụ.
- [ ] Giữ thông tin ký tự gốc để tạo dữ liệu OCR, dù nhãn phát hiện có thể được chuẩn hóa cho YOLO2.
- [ ] Xuất ảnh đại diện theo kích thước phổ biến vào nhánh YOLO2.
- [ ] Viết nhận xét và kết luận từ kết quả mới.

### Bước 4 — Kiểm tra và chuẩn bị dữ liệu OCR

- [ ] Thống kê từng ký tự theo từng nguồn trước khi gộp hoặc bổ sung.
- [ ] Kiểm tra lại cách tách ký tự CCPD; lấy mẫu kiểm tra thủ công và ghi riêng lý do bỏ mẫu.
- [ ] Phân biệt số biển được xét với số ảnh ký tự thu được; không dùng hai đơn vị này để tính chung một tỷ lệ.
- [ ] Triển khai nhận từng ký tự độc lập, bảo đảm vị trí ảnh cắt khớp nhãn. Bỏ điều kiện tối thiểu 4/6 hoặc 5/6, cho phép giữ một ký tự đạt; không gom các vùng tìm được rồi gán nhãn tuần tự khi thiếu vị trí.
- [ ] Chạy thử trên một phần dữ liệu; tạo bảng ảnh ngẫu nhiên, sát ngưỡng, bị loại và ký tự ít mẫu để kiểm tra bằng mắt. Sau điều chỉnh, kiểm tra trên một nhóm ảnh khác trước khi chạy đầy đủ; không dò thủ công toàn bộ dataset.
- [ ] Kiểm tra ảnh trùng, nhãn mâu thuẫn và nhóm biển gốc giữa các nguồn/tập.
- [ ] Tạo train/validation từ ba nguồn theo tỷ lệ mục tiêu 80/20; các ký tự cùng nhóm biển gốc không rơi vào nhiều tập. Ghi lại tỷ lệ thực tế và độ phủ ký tự/nguồn.
- [ ] Chuẩn bị VNLP test riêng từ dữ liệu nguồn theo quy tắc cố định; không coi 4.063 ảnh bổ sung train cũ là toàn bộ tập test đại diện.
- [ ] Thống kê số lượng và tỷ trọng nguồn theo từng ký tự trong từng tập; chỉ xử lý cân bằng trên train.
- [ ] Tách rõ số liệu lịch sử 3k, lượt bổ sung 10k nếu có và phiên bản dữ liệu mới.

### Bước 5 — Báo cáo và bàn giao

- [ ] Có bảng tổng hợp nguồn → số ảnh ban đầu → số giữ lại → lý do loại → train/validation/test.
- [ ] Mỗi bảng/biểu đồ có nhận xét và ý nghĩa đối với bài toán.
- [ ] Ảnh đại diện có cấu trúc nhiệm vụ/nguồn/kích thước; giữ nguyên ảnh mẫu, kèm bảng giải thích.
- [ ] Kiểm tra bố cục thư mục xuất ra và cách đọc cho huấn luyện.
- [ ] Giữ bố cục thư mục huấn luyện; đặt tên thư mục kết quả bằng từ dễ hiểu, nhất quán, không dùng viết tắt tự đặt hoặc chuỗi mã khó đọc. Ảnh đại diện chia theo nhiệm vụ/nguồn/kích thước.
- [ ] Mở lại notebook bằng chế độ xem kết quả để xác nhận các bảng, biểu đồ có thể tái hiện từ dữ liệu đã lưu.

## 3. Checklist chạy nhanh nhưng vẫn đúng

- [ ] Ghi thời gian riêng cho đọc nguồn, kiểm tra ảnh, cắt ký tự, thống kê và xuất dữ liệu; ghi số mẫu thực sự đã xét.
- [ ] Dùng tệp nguồn trên ổ tạm Colab cho các bước đọc ảnh nhiều lần; tránh đọc/ghi hàng loạt ảnh rời trực tiếp qua Drive.
- [ ] Chỉ sao chép/giải nén phần cần thiết; kiểm tra dung lượng trước các bước lớn.
- [ ] Lưu kết quả kiểm tra ảnh và thông tin cần thống kê để việc vẽ lại biểu đồ không mở lại toàn bộ ảnh.
- [ ] Kết quả lưu phải gắn với phiên bản dữ liệu và quy tắc xử lý; nguồn hoặc quy tắc đổi thì chỉ tính lại các bước bị ảnh hưởng.
- [ ] Lưu tiến độ theo từng phần hoàn chỉnh; chạy tiếp không mất ảnh, không cộng mẫu hai lần và không bỏ qua mẫu chưa lưu.
- [ ] Hạn chế đọc lặp tệp CCPD nén lớn; lựa chọn cách lưu trung gian sau khi kiểm tra dung lượng và đo thực tế.
- [ ] Tách khâu tạo dữ liệu khỏi khâu báo cáo; chỉ đóng gói lại dữ liệu khi có thay đổi cần xuất.
- [ ] Đo trước/sau trên cùng mẫu dữ liệu và cấu hình Colab; tách lần đầu với lần dùng lại kết quả đã lưu.
- [ ] Khi chỉ thay cách đọc/lưu, đối chiếu ảnh, nhãn, số lượng và cách chia tập với bản tham chiếu. Nếu đổi quy tắc lọc/chia tập thì ghi rõ thay đổi và kiểm tra theo quy tắc mới.
- [ ] Kiểm tra một lượt nhỏ, lượt chạy tiếp sau gián đoạn và chế độ xem lại trước khi chạy đầy đủ.

Chưa đặt cam kết nhanh hơn bao nhiêu lần: cần đo trên phiên bản mã và dữ liệu thực tế.

## 4. Ghi nhận kiểm tra ban đầu đối với mục 7 cũ

Phạm vi: bản lưu `OCR32_toi_uu_10k_va_anh_dai_dien.zip`, phiên bản 1, lưu ngày 18/11/2026. Đã đối chiếu mã và các bài kiểm tra đang đọc với nội dung gói này. Chưa kiểm tra bản đang mở trên Colab; nếu AI khác sửa sau bản lưu thì các nhận xét sau chưa xác nhận bản sửa đó.

| Nội dung | Điều thấy trong mã đã lưu | Việc tiếp theo |
|---|---|---|
| Đọc/ghi dữ liệu | Có sao chép sang ổ tạm và lưu từng phần hoàn chỉnh | Giữ nguyên nguyên tắc, đo thời gian thực tế |
| Chạy tiếp | Có lưu tiến độ và kiểm tra chuỗi các phần đã lưu | Kiểm tra lại với quy trình chia nguồn mới |
| Quét dữ liệu OCR sạch | Lượt chưa hoàn tất gọi lại bước mở ảnh, kiểm tra nhãn và tạo dấu nhận diện pixel cho toàn bộ bộ sạch | Lưu kết quả kiểm tra có đối chiếu đúng phiên bản dữ liệu để tránh làm lại không cần thiết |
| CCPD | Đọc tệp TAR.XZ tuần tự; chạy tiếp vẫn đi qua phần trước vị trí đã lưu | Đo chi phí đọc lại và cân nhắc lưu trung gian phù hợp dung lượng |
| Xem trước mục 7.3 | Không gọi bước quét toàn bộ ảnh bộ sạch, nhưng đọc và kiểm tra các phần đã lưu, gồm kiểm tra toàn vẹn tệp | Để chế độ xem báo cáo thông thường chỉ dùng bảng tổng hợp; tách kiểm tra sâu thành thao tác riêng |
| Quy tắc lọc ký tự | Nhánh CCPD chính vẫn yêu cầu ít nhất 5/6 vị trí đạt điều kiện; nhánh dự phòng có quy tắc riêng | Kiểm tra chất lượng ảnh cắt bằng mẫu đối chiếu, ghi số bị bỏ theo lý do |
| Phù hợp kế hoạch mới | Quy trình bổ sung cũ vẫn lấy VNLP vào train | Không mang nguyên quy trình này sang phương án VNLP chỉ làm test |

Kiểm tra trong lượt này: 5 bài kiểm tra xuất ảnh đại diện chạy đạt. Nhóm 10 bài kiểm tra bổ sung/chạy tiếp chưa chạy được ở môi trường hiện tại do thiếu thư viện OpenCV (`cv2`); chưa thể ghi nhận nhóm này đạt trong lượt này. Chưa đo tốc độ trên Colab, chưa đánh giá thủ công chất lượng nhãn/crop trên dữ liệu thật.

## 5. Trạng thái hiện tại và việc làm tiếp

- [x] Tạo checklist và ghi phạm vi kiểm tra.
- [x] Cập nhật lựa chọn YOLO1 sang Global v13 theo xác nhận mới nhất của người dùng.
- [x] Ghi nhận cách chia tập, hướng lọc từng ký tự và thư mục chung đã thống nhất; phân biệt quyết định đã chốt với công việc chưa triển khai.
- [x] Đọc lại mã tối ưu mục 7 trong bản đã lưu, xác định các điểm cần đo và cải thiện.
- [x] Đối chiếu mục 7 trong bản notebook người dùng gửi ngày 20/11 với bản lưu 18/11. Mã 7.2 chỉ thay hàm gom ứng viên trước khi lưu; các hàm cắt/lọc/khử trùng không đổi. Chưa xác nhận thay đổi sau thời điểm gửi tệp hoặc chạy lại trên Drive.
- [x] Tạo `Phan_tich_va_chuan_bi_du_lieu_bien_so.ipynb` với phần mở đầu, cấu hình, kiểm kê nguồn, đếm các tập gốc, so sánh mã mục 7 và kết luận.
- [x] Chạy đạt 18 kiểm tra bước 1 bằng dữ liệu giả, gồm định dạng README thực tế và thao tác di chuyển; kiểm tra các ô mã và chạy notebook giả lập. Đã chạy cả chế độ xử lý và chế độ xem trên Colab thật. Những kiểm tra này độc lập với nhóm kiểm tra bổ sung OCR cũ đang thiếu OpenCV.
- [x] Trích bảng 32 lớp của lượt 10k từ output đã lưu: train 334.826, validation 19.928, test 19.306, còn thiếu 23.062. Log ghi xuất ZIP hoàn tất; chưa kiểm tra ZIP thật. Mục 8 và ảnh đại diện đang hiển thị vẫn thuộc lượt 3k.
- [x] Đưa notebook mới vào `05_Notebook` trên Drive, giữ nguyên ID/liên kết và chạy kiểm tra nguồn thật trên Colab.

Việc tiếp theo: áp dụng phương án sau rà soát để xuất YOLO1 và kiểm tra ảnh–nhãn/số lượng/nhóm qua tập. Giữ riêng ảnh thiếu nhãn/chưa rõ để bổ sung sau. Sau đó triển khai YOLO2 và OCR. Đã hoàn tất thống kê toàn Global v13. Quy mô đếm ban đầu: train 98.798, validation 2.048, test 1.020; tổng 101.866. Roboflow: train 16.209, validation 2.027, test 2.026; tổng 20.262. Đây chưa phải số ảnh sau làm sạch.

Đã truy cập trực tiếp và chỉnh notebook mới trên Colab, kết nối runtime với Drive và hoàn thành kiểm tra/sắp xếp nguồn. Đã đọc lại notebook cũ trên Drive. Notebook mới: https://colab.research.google.com/drive/1pqbj00RtMUQSCgYblkzjoTISLk_ddhzG#scrollTo=h8L7UHkNnJlQ . Thư mục nguồn đã mở và xác minh: https://drive.google.com/drive/folders/1Qm4crpxPTgGh5tLeGMWKAzR10inSxolC . Đã chạy phân tích ảnh đầy đủ YOLO1, chạy tiếp từ 20.000 ảnh và kiểm tra chế độ xem. Chưa làm sạch/xuất bộ YOLO1, chưa phân tích YOLO2 hoặc triển khai lọc OCR mới.

### Bổ sung sau phân tích YOLO1 trên Colab

- [x] Chạy lại phần xử lý từ đủ tiến độ: dùng lại 101.866 bản ghi, không giải mã lại ảnh; khoảng 28,746 giây.
- [x] Chạy toàn bộ notebook ở chế độ xem, mở lại các bảng, biểu đồ và ảnh đã lưu. Giữ mặc định `xem_bao_cao`, số ảnh YOLO1 bằng `0`.
- [x] Ghi phiên bản bộ giải mã vào tiến độ, đối chiếu bảy hàm đọc/kiểm tra/tổng hợp ảnh trước khi bổ sung thông tin này; không thay bản ghi ảnh/khung/pixel.

Ảnh toàn nguồn: https://drive.google.com/drive/folders/1OCJ3hbn2pwVTbZ54v0dTwqlRQ1uGJtju . Báo cáo: `Ket_qua_Buoc_2_YOLO1_tren_Colab.md`.


### Bổ sung sau rà soát bảng ảnh YOLO1

- [x] Bổ sung mục 2.6–2.7 trên Colab, giữ hai chế độ và chạy lại toàn notebook ở chế độ xem.
- [x] Lưu 61 trang ảnh, biên bản theo mã, trị số khung và phương án cho từng ảnh trên Drive.
- [x] Năm kiểm tra giả lập cho quy tắc nhóm/nhãn rỗng/dHash chạy đạt.
- [ ] Áp dụng chỉnh khung vào tệp xuất, đóng gói và kiểm tra bộ YOLO1.

Phương án giữ 98.983 ảnh là số dự kiến, không phải bộ đã xuất. Thư mục rà soát: https://drive.google.com/drive/folders/1H-wJ7bsZE867FVhzUDDU__EiQKDEYSDT .

### Bàn giao phần việc YOLO2 và OCR cho thành viên nhóm — 20/11/2026

- [x] Soạn quy ước chung: nguồn/đường dẫn, vai trò, trạng thái đã xác minh, hai chế độ, cấu trúc đầu ra và cách phối hợp giữa hai thành viên.
- [x] Soạn yêu cầu có thể đưa cho AI, quy trình, bảng/biểu đồ, mốc và đầu ra nghiệm thu riêng YOLO2/OCR.
- [x] Soạn schema trao đổi nhãn ký tự gốc YOLO2 → OCR; phân biệt nhãn detector lớp 0 với nhãn nhận dạng 32 lớp.
- [x] Soạn checklist nghiệm thu theo từng mốc, kèm quy tắc kiểm tra tính đúng đắn, chạy tiếp, nguồn test và báo tiến độ.
- [ ] Thành viên triển khai YOLO2 và OCR trên các notebook riêng; nhận lại và nghiệm thu kết quả thật.
- [ ] Người tích hợp bổ sung phần đã nghiệm thu vào notebook tổng.
- [ ] Bổ sung biểu đồ YOLO1 theo yêu cầu mới: quy mô ảnh/khung, kích thước phổ biến, số biển/ảnh, diện tích tương đối, tỷ lệ khung và trước–sau theo trạng thái phương án/thực xuất.

Tài liệu bàn giao là yêu cầu triển khai, không phải chứng nhận YOLO2/OCR đã chạy. Lượt thêm biểu đồ YOLO1 được tạm hoãn theo chỉ dẫn mới của người dùng trước khi có chỉnh sửa notebook. Notebook tổng vẫn ở bản 31 ô của lượt rà soát YOLO1; chưa xuất bộ YOLO1.

