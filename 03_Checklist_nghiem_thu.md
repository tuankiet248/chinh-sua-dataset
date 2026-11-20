# Checklist triển khai và nghiệm thu YOLO2/OCR

Ngày 20/11/2026 · Tất cả mục triển khai dưới đây bắt đầu ở trạng thái **chưa hoàn thành**. Đây không phải checklist chứng nhận công việc đã làm. Mỗi thành viên sao chép phần của mình, ghi bằng chứng và ngày thực hiện.

## 1. Cách ghi tiến độ

Mỗi mốc ghi: người phụ trách; phiên bản nguồn/quy tắc; phạm vi mẫu/toàn bộ; số mẫu; thư mục kết quả; kiểm tra đã chạy; phần chưa rõ. Chỉ đánh dấu `[x]` khi có tệp hoặc output kiểm chứng. “Đã viết code”, “đã chạy trên mẫu” và “đã xuất đầy đủ” là ba trạng thái khác nhau.

| Mốc | YOLO2 | OCR | Bằng chứng cần dẫn |
|---|---|---|---|
| A. Đọc đúng nguồn/mapping | Chưa làm | Chưa làm | Bảng nguồn, lớp, ảnh đối chiếu |
| B. Mẫu thử và kiểm chứng | Chưa làm | Chưa làm | Mẫu có seed, ảnh giữ/loại, log kiểm tra |
| C. Thống kê phạm vi đầy đủ | Chưa làm | Chưa làm | Bảng đếm, cấu hình phạm vi, thời gian |
| D. Làm sạch/chia tập | Chưa làm | Chưa làm | Quy tắc, quan hệ nhóm, số trước–sau |
| E. Xuất và kiểm tra | Chưa làm | Chưa làm | Manifest, bộ xuất, kết quả kiểm tra |
| F. Mở lại và bàn giao | Chưa làm | Chưa làm | Notebook xem báo cáo, README, file bàn giao |

## 2. Checklist chung

- [ ] Đọc tài liệu chung và tài liệu nhiệm vụ, nêu lại đúng nguồn/vai trò/split.
- [ ] Có quyền đọc đúng nguồn, cấu hình được đường dẫn trên tài khoản thành viên.
- [ ] Tạo notebook riêng; không sửa đè notebook tổng, YOLO1 hoặc nguồn dùng chung.
- [ ] Lưu danh tính nguồn, cấu trúc và bằng chứng mapping, không suy từ tên ZIP.
- [ ] Phân biệt tệp ảnh, ảnh đọc được, khung ký tự, biển và crop; ghi đơn vị/mẫu số.
- [ ] Tạo ID ổn định có namespace; lưu split gốc và split cuối, parent/group.
- [ ] Chạy thử có seed và xem đối chiếu trước khi chạy lớn.
- [ ] Tách tiến độ mẫu thử và toàn nguồn, không dùng cache sai phạm vi.
- [ ] Tiến độ khớp nguồn/quy tắc/thư viện; đổi cấu hình liên quan không dùng kết quả cũ.
- [ ] Kiểm tra gián đoạn/chạy tiếp: số lượng và bản ghi không mất/nhân đôi.
- [ ] Tối ưu trên cùng mẫu, có đối chiếu tính đúng đắn; ghi thời gian từng bước.
- [ ] `xem_bao_cao` không quét nguồn/crop lại/CRC toàn bộ tự động khi thiếu kết quả.
- [ ] Bảng/hình lưu lại và có nhận xét dễ hiểu; biểu đồ trùng/lỗi nêu khả năng chồng nhóm.
- [ ] Không tự xóa nguồn, không tự gán lại nhãn mâu thuẫn hoặc coi ảnh nhãn rỗng là nền.
- [ ] Dữ liệu/báo cáo có phiên bản, cấu hình, trạng thái và giới hạn.

## 3. Checklist YOLO2

- [ ] Chỉ đọc Roboflow OCR v1; kiểm tra lại mốc kiểm kê 20.262 ảnh và giải thích nếu khác.
- [ ] Đối chiếu ID–ký tự gốc trên ảnh; giữ bảng mapping và nhãn nguyên bản.
- [ ] Kiểm tra ảnh–nhãn, thiếu/rỗng/lỗi, finite/class/bounds/diện tích và nhãn mồ côi.
- [ ] Nhãn detector xuất là lớp 0 `char_num`; không đánh mất ký tự gốc phục vụ OCR.
- [ ] Không loại khung detector chỉ vì ký tự ngoài 32 lớp nhận dạng.
- [ ] Có bảng/hình quy mô split, kích thước ảnh, số khung/ảnh, kích thước khung và ký tự nguồn.
- [ ] Kiểm tra pixel trùng, biến thể/ảnh cha và ứng viên gần giống giữa các tập.
- [ ] Quyết định dựa trên bằng chứng; giữ split gốc khi không có lý do xử lý; ghi mọi thay đổi.
- [ ] Không còn nhóm xác nhận qua nhiều tập trong bộ giữ lại; nêu giới hạn của kiểm tra.
- [ ] Có số ảnh/khung trước–sau và lý do theo split, đếm ảnh duy nhất.
- [ ] Có ảnh đại diện theo nguồn/kích thước, manifest; ảnh nộp và ảnh vẽ khung tách riêng.
- [ ] Xuất đúng `yolo2_char_num/images/<split>` và `labels/<split>`, YAML, manifest, README.
- [ ] Đọc lại toàn bộ nhãn xuất và kiểm tra cặp tệp/số lượng; kiểm tra ảnh xuất không hỏng theo phạm vi ghi rõ.
- [ ] Giải nén ở vị trí khác và xác nhận cấu hình nạp dữ liệu trỏ đúng.
- [ ] Bàn giao bộ bảng gốc cho OCR theo schema trong tài liệu, gồm mọi ảnh nguồn và quyết định.

## 4. Checklist OCR

- [ ] Roboflow/Winter/CCPD là pool train/val, VNLP chỉ test; không dùng nguồn khác.
- [ ] Bảng lớp Roboflow và Winter được xác minh riêng; không dùng chung ID theo phỏng đoán.
- [ ] CCPD giải mã nhãn, thứ tự đỉnh, vị trí và slot bằng chứng thật; không coi transcript là bbox.
- [ ] VNLP xác định kiểu ground truth và khả năng crop; không lấy bộ 4.063 crop bổ sung cũ làm test đầy đủ.
- [ ] Tập lớp đúng chuỗi 32 ký tự; giữ nhãn số 0 dưới dạng chuỗi; không tự đổi I/O thành 1/0.
- [ ] Một ký tự đạt vẫn được giữ nếu vị trí–nhãn đáng tin, không có ngưỡng cả biển tối thiểu 4/6–5/6.
- [ ] Thiếu một slot không dồn crop rồi ghép lệch nhãn; kiểm tra ca lỗi giữa chuỗi.
- [ ] Lỗi nền tảng toàn biển và lỗi từng slot có nhật ký riêng, đơn vị riêng.
- [ ] Có mẫu ngẫu nhiên, sát ngưỡng, bị loại, lớp hiếm; có nhóm kiểm chứng khác nhóm hiệu chỉnh.
- [ ] Không dùng VNLP test để chọn ngưỡng hoặc mục tiêu cân bằng; nếu có tập hiệu chỉnh VNLP thì loại khỏi test và ghi rõ.
- [ ] Thống kê ký tự theo từng nguồn trước gộp/cân bằng, đủ 32 lớp kể cả hàng 0.
- [ ] Kiểm tra quan hệ ảnh/biển qua nguồn và pixel crop trùng; nhãn mâu thuẫn được giữ ngoài.
- [ ] Không nối biển khác nhau chỉ vì ký tự crop giống nhau; xử lý giao crop theo quy tắc có ghi nhận.
- [ ] VNLP test được bảo vệ khi có trùng; không chuyển mẫu VNLP sang train.
- [ ] Chia theo nhóm khoảng 80/20 theo crop, seed cố định; không phá nhóm để đủ tỷ lệ/lớp.
- [ ] Báo tỷ lệ thực tế, số nhóm, lớp vắng và phần chưa thể độc lập hóa.
- [ ] Có bảng nguồn × split, ký tự × nguồn × split, tỷ trọng nguồn đúng mẫu số.
- [ ] Chưa áp 3k/10k cũ; nếu đề xuất cân bằng thì có bản thống kê trước cân bằng.
- [ ] Mọi lấy mẫu/augmentation/cân bằng chỉ train, không tăng số mẫu độc lập bằng cách đếm bản sao.
- [ ] Bộ xuất đủ ảnh/nhãn một ký tự, manifest, mapping 32 lớp, README; nguồn test đúng VNLP.
- [ ] Mọi crop truy về nguồn, cha/nhóm, box/slot khi áp dụng; mapping giữa nguồn và đầu ra nhất quán.
- [ ] Ảnh đại diện theo nguồn/kích thước crop trước resize, kèm ký tự và cha.
- [ ] Đọc lại bộ xuất: số tệp/cặp/manifest/mapping khớp, ảnh mở được, TXT đúng một ký tự.
- [ ] Kiểm tra giao nhau nhóm và pixel trên bộ xuất cuối, không chỉ trên phương án trước crop.

## 5. Tệp bàn giao tối thiểu

| Nhóm tệp | YOLO2 | OCR |
|---|---|---|
| Notebook | Bản riêng mở lại được | Bản riêng mở lại được |
| Mã | Hàm đọc, kiểm tra, nhóm, xuất, báo cáo | Hàm nguồn/crop/lọc, nhóm/chia, xuất, báo cáo |
| Cấu hình | Nguồn, phiên bản, seed, quyết định | Thêm mapping, ngưỡng, quy tắc test/lấy mẫu |
| Báo cáo | CSV, hình, kết luận, log thời gian | CSV, hình, kết luận, nhật ký biển/slot, thời gian |
| Dữ liệu | Thư mục chuẩn hoặc ZIP đã kiểm tra | Thư mục chuẩn hoặc ZIP đã kiểm tra |
| Ảnh đại diện | Nguồn/kích thước + bảng nguồn | Nguồn/kích thước crop + bảng nhãn/cha |
| Metadata phối hợp | Bảng lớp/khung/ảnh gốc cho OCR | Manifest và quan hệ nhóm để tích hợp |
| Nghiệm thu | Checklist có bằng chứng | Checklist có bằng chứng |

Không cần nén thêm toàn bộ nguồn vào gói báo cáo. Bộ dữ liệu lớn có thể bàn giao bằng đường dẫn Drive đã cấp quyền, kèm dấu kiểm tra và thông tin phiên bản.

## 6. Điều kiện chưa được ghi “hoàn thành”

Chưa nghiệm thu nếu mapping còn đoán; chỉ chạy vài ảnh nhưng gọi toàn nguồn; output không truy về nguồn; split chỉ là random từng crop; thiếu log mẫu loại; dùng VNLP trong train; chỉ thử cú pháp nhãn mà nói crop đúng; chưa kiểm tra bộ xuất; hoặc notebook xem báo cáo tự chạy lại pipeline nặng.

Nếu cần giới hạn phạm vi để hoàn thành trong thời gian nhóm có, ghi đây là **bản mẫu có giới hạn**, báo đúng số đã xét và lý do; không thay tên thành kết quả đầy đủ.

## 7. Kiểm tra khi tích hợp toàn nhóm

- [ ] Hai notebook dùng đúng đường dẫn/phiên bản nguồn và schema trao đổi.
- [ ] OCR nhận nhãn ký tự gốc, không nhận lớp detector 0 như nhãn nhận dạng.
- [ ] Không nhầm split riêng YOLO2 với split OCR; lưu được cả hai.
- [ ] Các bảng tổng hợp khớp bộ dữ liệu thật, không trộn kết quả 3k/10k cũ với bản mới.
- [ ] Nếu báo cáo đánh giá đầu-cuối, đã xét nhóm test đầu-cuối so với train của mọi công đoạn; nếu chưa, nêu giới hạn.
- [ ] Người tích hợp xem kết quả và đưa phần đạt vào notebook tổng; không ghi đè output có thật bằng số giả.

## 8. Mẫu báo tiến độ ngắn để thành viên gửi lại

> **Phần việc:** YOLO2/OCR — phiên bản …  
> **Đã chạy:** mẫu/toàn phạm vi …; … ảnh biển, … khung/slot, … crop.  
> **Đã kiểm chứng:** …; bằng chứng tại …  
> **Đã xuất:** chưa/có, số lượng và đường dẫn …  
> **Còn thiếu hoặc chưa rõ:** …  
> **Bước tiếp theo:** …  
> **Quyết định cần nhóm chốt thêm:** chỉ nêu quyết định mới, không hỏi lại các quy tắc trong bàn giao.
