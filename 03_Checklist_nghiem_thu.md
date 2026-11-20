# Checklist triển khai và nghiệm thu YOLO2/OCR

Ngày bàn giao: 20/11/2026 · Cập nhật hoàn tất nghiệm thu toàn nguồn YOLO2 (`Ban_01`): 21/11/2026 (`00:00–01:15 +07:00`).  
Ghi chú nguyên tắc: Tất cả các mục đánh dấu `[x]` ở phần YOLO2 đều đã có **tệp đích thực tế trên Google Drive (`/content/drive/MyDrive/Do_an_bien_so/...`) và output kiểm chứng lưu trực tiếp trong `05_Notebook/Phan_tich_va_chuan_bi_YOLO2.ipynb`** trên cả mẫu thử `Thu_300_anh` (`300` ảnh) và toàn bộ nguồn `Ban_01` (`20.262` ảnh).

## 1. Cách ghi tiến độ

Mỗi mốc ghi: người phụ trách; phiên bản nguồn/quy tắc; phạm vi mẫu/toàn bộ; số mẫu; thư mục kết quả; kiểm tra đã chạy; phần chưa rõ. Chỉ đánh dấu `[x]` khi có tệp hoặc output kiểm chứng. “Đã viết code”, “đã chạy trên mẫu” và “đã xuất đầy đủ” là ba trạng thái khác nhau.

| Mốc | YOLO2 | OCR | Bằng chứng cần dẫn |
|---|---|---|---|
| A. Đọc đúng nguồn/mapping | **Hoàn thành (21/11/2026)** | Chưa làm | Output Ô 6 `Phan_tich_va_chuan_bi_YOLO2.ipynb` (`exec=17`); `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/moc_a_kiem_tra_nguon.json`, `moc_a_kiem_ke_split.csv`, `moc_a_bang_lop_goc.csv`, `moc_a_doi_chieu_lop.png`. Khớp `licence-plate-vn/ocr-stypd` v1; đủ `20.262` ảnh & `20.262` nhãn (`16.209` train, `2.027` val, `2.026` test; chênh lệch `0`); `data.yaml` có `nc = 30` lớp (`0–9`, `A–P`, `S–Z`; không có `Q`, `R`, các khung khớp ký tự trên ảnh thật). |
| B. Mẫu thử và kiểm chứng | **Hoàn thành (21/11/2026)** | Chưa làm | Đã chạy mẫu thử `Thu_300_anh` (`SEED = 42`, `300/20.262` ảnh: `236` train, `36` val, `28` test; `2.550` khung hợp lệ; lưu riêng tại `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Thu_300_anh` và `04_Anh_dai_dien/YOLO2/Roboflow/Thu_300_anh_bo_giu_lai`) + đạt 8/8 bài kiểm tra giả lập. |
| C. Thống kê phạm vi đầy đủ | **Hoàn thành toàn nguồn `Ban_01` (21/11/2026)** | Chưa làm | Output Ô 8 & Ô 10 (`exec=18, 19`); `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/`: đã quét `20.262/20.262` ảnh (`100%` đọc được, kích thước `640x640`), `172.521` khung ký tự hợp lệ (`137.930` train, `17.282` val, `17.309` test), `0` ảnh lỗi, `0` nhãn thiếu/rỗng/sai cú pháp. Đủ 7 biểu đồ PNG và các bảng CSV đi kèm. |
| D. Làm sạch/chia tập | **Hoàn thành toàn nguồn `Ban_01` (21/11/2026)** | Chưa làm | Output Ô 12 & Ô 15 (`exec=20, 22`); `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/Ra_soat/` và `phuong_an_tung_anh.csv`: phát hiện `68` nhóm trùng pixel/họ `.rf.` (`136` ảnh, `27` nhóm qua nhiều tập, `1` nhóm gồm `2` ảnh trùng pixel mâu thuẫn nhãn), `21` ảnh chồng lấn `IoU >= 0.85`, `55` nhóm gợi ý `dHash` (`192` cạnh trong `quan_he_nhom.csv`). Giữ `20.193` ảnh (`171.922` khung), tạm giữ ngoài `69` ảnh (`39` trùng pixel cùng tập, `27` nhóm ưu tiên `test → val → train`, `2` mâu thuẫn nhãn, `1` đại diện nhóm đánh giá); `0` nhóm xác nhận qua nhiều tập trong bộ giữ lại. |
| E. Xuất và kiểm tra | **Hoàn thành toàn nguồn `Ban_01` (21/11/2026)** | Chưa làm | Output Ô 17 & Ô 18 (`exec=23, 28`): đã xuất `20.193` ảnh (`16.141` train, `2.027` val, `2.025` test) và `171.922` khung lớp `0: char_num` tại `/content/drive/MyDrive/Do_an_bien_so/02_Du_lieu_huan_luyen/YOLO2/Ban_01/yolo2_char_num` & `yolo2_char_num.zip` (`743,744 MiB`), gói 7 tệp `Ban_giao_OCR/` (`ky_tu_goc.csv` `81,879 MiB`, `anh_bien_goc.csv` `8,351 MiB`) và ảnh đại diện `04_Anh_dai_dien/YOLO2/Roboflow/Ban_01/`. Kiểm chứng độc lập: giải mã `20.193/20.193` ảnh xuất (`100%`), đọc lại `20.193` nhãn (`171.922` khung lớp `0`), `0` nhóm qua tập, giải nén & nạp `data.yaml` ở thư mục khác đạt `True`, `13/13` tệp đích `DA_CO_TREN_DRIVE`. |
| F. Mở lại và bàn giao | **Hoàn thành (21/11/2026)** | Chưa làm | Đã kiểm chứng mở lại toàn bộ báo cáo ở `CHE_DO = "xem_bao_cao"` không quét ZIP nguồn; đặt mặc định `CHE_DO = "xem_bao_cao"`, `XUAT_BO_DU_LIEU = False` trong `05_Notebook/Phan_tich_va_chuan_bi_YOLO2.ipynb` kèm đầy đủ output và bảng kiểm tra 13 tệp đích trên Drive. |

## 2. Checklist chung (Phần YOLO2)

- [x] Đọc tài liệu chung và tài liệu nhiệm vụ, nêu lại đúng nguồn/vai trò/split (`01_Du_lieu_goc/Roboflow/roboflow_ocr_v1_yolov8.zip`, 1 lớp detector `0: char_num`, bảo toàn bảng nhãn gốc cho OCR, ưu tiên giữ split gốc `train`/`val`/`test`).
- [x] Có quyền đọc đúng nguồn, cấu hình được đường dẫn trên tài khoản thành viên (`/content/drive/MyDrive/Do_an_bien_so/01_Du_lieu_goc/Roboflow/roboflow_ocr_v1_yolov8.zip` — Ô 1 `exec=27`).
- [x] Tạo notebook riêng (`05_Notebook/Phan_tich_va_chuan_bi_YOLO2.ipynb`); không sửa đè notebook tổng, YOLO1 hoặc nguồn dùng chung.
- [x] Lưu danh tính nguồn, cấu trúc và bằng chứng mapping, không suy từ tên ZIP (`https://universe.roboflow.com/licence-plate-vn/ocr-stypd ; README.roboflow.txt ghi v1`, `data.yaml` 30 lớp — Ô 6 `exec=17`).
- [x] Phân biệt tệp ảnh, ảnh đọc được, khung ký tự, biển và crop; ghi đơn vị/mẫu số (bảng `quy_mo_theo_split.csv` và các biểu đồ trong Ô 10 `exec=19`).
- [x] Tạo ID ổn định có namespace (`Roboflow::<source_member>`, `Roboflow::rf::<base>`, `G::Roboflow::...`); lưu split gốc và split cuối, parent/group.
- [x] Chạy thử có seed (`SEED = 42`, `SO_ANH_THU = 300`) và xem đối chiếu trước khi chạy lớn (`03_Bao_cao/03_YOLO2/Thu_300_anh`).
- [x] Tách tiến độ mẫu thử (`Thu_300_anh`) và toàn nguồn (`Ban_01`), không dùng cache sai phạm vi.
- [x] Tiến độ khớp nguồn/quy tắc/thư viện (`Python 3.13.18`, `Pillow 11.3.0`, `libjpeg_turbo 3.1.1`, `metadata_zip`); đổi cấu hình liên quan không dùng kết quả cũ (đã kiểm chứng tự động ở Test giả lập số 6).
- [x] Kiểm tra gián đoạn/chạy tiếp: số lượng và bản ghi không mất/nhân đôi (đã kiểm chứng tự động ở Test giả lập số 2 và dùng lại `20.262` bản ghi tiến độ trên Colab ở Ô 8 `exec=18` trong `10,661` giây).
- [x] Tối ưu trên cùng mẫu, có đối chiếu tính đúng đắn; ghi thời gian từng bước (Test giả lập số 3 xác nhận bản ghi 1 luồng == 2 luồng; mỗi luồng mở handle `ZipFile` 1 lần/batch trên SSD).
- [x] `xem_bao_cao` không quét nguồn/crop lại/CRC toàn bộ tự động khi thiếu kết quả (đã kiểm chứng trên Colab và Test giả lập số 7, 8).
- [x] Bảng/hình lưu lại và có nhận xét dễ hiểu; biểu đồ trùng/lỗi nêu khả năng chồng nhóm (Ô 10, Ô 12 `exec=19, 20`).
- [x] Không tự xóa nguồn, không tự gán lại nhãn mâu thuẫn hoặc coi ảnh nhãn rỗng là nền (`2` ảnh trùng pixel mâu thuẫn nhãn được giữ riêng trong `Ra_soat/Trung_pixel_mau_thuan_nhan`).
- [x] Dữ liệu/báo cáo có phiên bản (`yolo2_1.0.0`, `schema_version: 1.0`), cấu hình, trạng thái (`da_xuat_day_du`) và giới hạn.

## 3. Checklist YOLO2

- [x] Chỉ đọc Roboflow OCR v1; kiểm tra lại mốc kiểm kê 20.262 ảnh và giải thích nếu khác (**khớp tuyệt đối 20.262 ảnh**: `16.209` train, `2.027` val, `2.026` test; chênh lệch `0`, `0` nhãn mồ côi — Ô 6 `exec=17`).
- [x] Đối chiếu ID–ký tự gốc trên ảnh; giữ bảng mapping và nhãn nguyên bản (đã xác minh `nc = 30` lớp từ `data.yaml` trên lưới ảnh thật `moc_a_doi_chieu_lop.png` và `mau_anh_va_nhan_goc.png`; phát hiện `data.yaml` không có `Q`, `R` nên ID `23:S..29:Z` lệch 2 đơn vị so với 32 lớp OCR).
- [x] Kiểm tra ảnh–nhãn, thiếu/rỗng/lỗi, finite/class/bounds/diện tích và nhãn mồ côi (**toàn nguồn `20.262` ảnh**: `20.262/20.262` ảnh đọc được, `172.521` khung hợp lệ, `0` ảnh lỗi, `0` nhãn thiếu/rỗng/sai cú pháp/vượt biên, `0` nhãn mồ côi, `21` ảnh có cặp khung chồng lấn `IoU >= 0.85` — Ô 10, Ô 12 `exec=19, 20`).
- [x] Nhãn detector xuất là lớp 0 `char_num`; không đánh mất ký tự gốc phục vụ OCR (đã xuất `171.922` khung lớp `0` trong `yolo2_char_num` và bảo toàn toàn bộ `172.521` khung gốc kèm `original_class_id`, `character` trong `Ban_giao_OCR/ky_tu_goc.csv` — Ô 18 `exec=28`).
- [x] Không loại khung detector chỉ vì ký tự ngoài 32 lớp nhận dạng (cả 30 lớp gốc trong `data.yaml` đều được giữ nguyên; quy tắc giữ lớp ngoài 32 lớp OCR đã đạt kiểm chứng ở Test giả lập số 5).
- [x] Có bảng/hình quy mô split, kích thước ảnh, số khung/ảnh, kích thước khung và ký tự nguồn (đủ các bảng CSV và biểu đồ `01`–`07` tại `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/Bieu_do/` — Ô 10, 12, 15).
- [x] Kiểm tra pixel trùng, biến thể/ảnh cha và ứng viên gần giống giữa các tập (`68` nhóm trùng pixel & họ `.rf.` với `136` ảnh, `27` nhóm qua nhiều tập, `1` nhóm mâu thuẫn nhãn; `55` nhóm gợi ý `dHash` với `111` ảnh; đã tạo 16 trang ảnh đối chiếu trong `Ra_soat/` — Ô 12 `exec=20`).
- [x] Quyết định dựa trên bằng chứng; giữ split gốc khi không có lý do xử lý; ghi mọi thay đổi (`phuong_an_tung_anh.csv`, `ly_do_tam_giu_ngoai.csv`, `Ra_soat/quyet_dinh_ra_soat.json` — Ô 15 `exec=22`).
- [x] Không còn nhóm xác nhận qua nhiều tập trong bộ giữ lại; nêu giới hạn của kiểm tra (`nhom_xac_nhan_qua_tap = 0` trên cả phương án Ô 15 và bộ xuất thực tế Ô 18, kèm ghi chú giới hạn).
- [x] Có số ảnh/khung trước–sau và lý do theo split, đếm ảnh duy nhất (`20.262` ảnh / `172.521` khung ban đầu → xuất `20.193` ảnh / `171.922` khung; tạm giữ ngoài `69` ảnh duy nhất: `39` trùng pixel cùng tập, `27` ưu tiên `test → val → train`, `2` trùng pixel mâu thuẫn nhãn, `1` đại diện nhóm đánh giá — Ô 15, Ô 18).
- [x] Có ảnh đại diện theo nguồn/kích thước, manifest; ảnh nộp và ảnh vẽ khung tách riêng (`3` ảnh nguyên byte gốc kích thước `640x640` tại `/content/drive/MyDrive/Do_an_bien_so/04_Anh_dai_dien/YOLO2/Roboflow/Ban_01/640x640/` kèm `danh_sach_anh_mau.csv` và hình vẽ khung riêng trong báo cáo — Ô 17 `exec=23`).
- [x] Xuất đúng `yolo2_char_num/images/<split>` và `labels/<split>`, YAML, manifest, README (`/content/drive/MyDrive/Do_an_bien_so/02_Du_lieu_huan_luyen/YOLO2/Ban_01/yolo2_char_num.zip` `743,744 MiB` + `yolo2_char_num/data.yaml`, `manifest.csv` `10,363 MiB`, `README.md`, `thong_tin_phien_ban.json` — Ô 18 `exec=28`).
- [x] Đọc lại toàn bộ nhãn xuất và kiểm tra cặp tệp/số lượng; kiểm tra ảnh xuất không hỏng theo phạm vi ghi rõ (đã đọc lại `20.193` tệp nhãn với `171.922` khung lớp `0` hợp lệ và **giải mã kiểm tra trực tiếp `20.193/20.193` (`100%`) ảnh xuất** — Ô 18 `exec=28`).
- [x] Giải nén ở vị trí khác và xác nhận cấu hình nạp dữ liệu trỏ đúng (`nap_sau_giai_nen_thu_muc_khac = True`, số ảnh nạp theo tập khớp `{'train': 16141, 'val': 2027, 'test': 2025}` — Ô 18 `exec=28`).
- [x] Bàn giao bộ bảng gốc cho OCR theo schema trong tài liệu, gồm mọi ảnh nguồn và quyết định (đủ 7 tệp tại `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/Ban_giao_OCR/`: `bang_lop_goc.json`, `anh_bien_goc.csv` `8,351 MiB` gồm đủ `20.262` ảnh, `ky_tu_goc.csv` `81,879 MiB` gồm đủ `172.521` khung gốc, `quan_he_nhom.csv`, `quyet_dinh_chat_luong.csv`, `thong_tin_phien_ban.json`, `README.md` — tất cả `13/13` tệp đích đạt `DA_CO_TREN_DRIVE` tại Ô 18 `exec=28`).

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

> **Phần việc:** YOLO2 — phiên bản `Ban_01` (mã `yolo2_1.0.0`, `schema_version: 1.0`, hoàn tất ngày 21/11/2026)  
> **Đã chạy:**  
> - **Mốc B (mẫu thử `Thu_300_anh`, `SEED = 42`):** `300 / 20.262` ảnh biển số, `2.550` khung ký tự hợp lệ.  
> - **Mốc A, C, D, E, F (toàn bộ nguồn `Ban_01`):** **`20.262 / 20.262` ảnh biển số** (`16.209` train, `2.027` val, `2.026` test — khớp tuyệt đối mốc kiểm kê `20.262`, chênh lệch `0`; `0` nhãn mồ côi), giải mã được `20.262/20.262` ảnh (`100%` kích thước `640x640`), **`172.521` khung ký tự hợp lệ** (`137.930` train, `17.282` val, `17.309` test).  
> **Đã kiểm chứng:**  
> - Danh tính nguồn khớp `licence-plate-vn/ocr-stypd` v1 từ `README.dataset.txt` và `README.roboflow.txt`.  
> - Bảng lớp gốc trong `data.yaml` có **`nc = 30` lớp** (`0–9`, `A, B, C, D, E, F, G, H, K, L, M, N, P, S, T, U, V, X, Y, Z`). Đã đối chiếu bằng mắt trên lưới ảnh thật (`moc_a_doi_chieu_lop.png`, `mau_anh_va_nhan_goc.png`, `Ra_soat/Ky_tu_it_mau`): các khung đều là từng ký tự biển số và khớp chính xác ký tự trong `data.yaml`. **Lưu ý quan trọng cho OCR:** `data.yaml` của Roboflow v1 không có `Q` và `R`, nên từ `original_class_id = 23` (`S`) đến `29` (`Z`) lệch 2 đơn vị so với bảng 32 lớp `class_to_idx.json`; OCR bắt buộc đọc cột `character` (hoặc `bang_lop_goc.json`), không dùng trực tiếp `original_class_id` làm `class_id` 32 lớp.  
> - Kiểm tra toàn nguồn `20.262` ảnh: `0` ảnh hỏng, `0` nhãn rỗng/thiếu/sai cú pháp/vượt biên, `77` ảnh duy nhất (`0,380%`) có vấn đề cần lưu ý gồm: `21` ảnh có cặp khung chồng lấn `IoU >= 0.85`, `2` ảnh trùng pixel nhưng **mâu thuẫn nhãn** (`1` nhóm), `54` ảnh thuộc `27` nhóm xác nhận đi qua nhiều tập (`68` nhóm trùng pixel/họ `.rf.` với `136` ảnh; `55` nhóm gợi ý `dHash` với `111` ảnh; tổng `192` cạnh lưu trong `quan_he_nhom.csv`). Đã tạo 16 trang ảnh đối chiếu trong `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/Ra_soat/`.  
> - Kiểm chứng độc lập bộ xuất (Mốc D): `dat_toan_bo_kiem_chung = True`; giải mã trực tiếp `20.193/20.193` (`100%`) ảnh xuất; đọc lại `20.193` tệp nhãn (`171.922` khung lớp `0: char_num` hợp lệ); `0` nhóm đã xác nhận rò rỉ qua nhiều tập; giải nén và nạp thử `data.yaml` ở thư mục tạm khác đạt `True` (`{'train': 16141, 'val': 2027, 'test': 2025}`).  
> - Kiểm tra trực tiếp **`13/13` tệp đích trên Google Drive** đều đạt trạng thái `DA_CO_TREN_DRIVE` (`kiem_tra_tep_dich_drive.csv`).  
> **Đã xuất:** **Có (`da_xuat_day_du` — phiên bản `Ban_01`)**, giữ lại **`20.193` ảnh (`171.922` khung lớp `0: char_num`)** gồm `16.141` train (`137.340` khung), `2.027` val (`17.282` khung), `2.025` test (`17.300` khung); tạm giữ ngoài `69` ảnh (`39` trùng pixel cùng tập, `27` ưu tiên `test → val → train`, `2` trùng pixel mâu thuẫn nhãn, `1` đại diện nhóm đánh giá):  
> - Bộ huấn luyện YOLO2: `/content/drive/MyDrive/Do_an_bien_so/02_Du_lieu_huan_luyen/YOLO2/Ban_01/yolo2_char_num.zip` (`743,744 MiB`) và `/content/drive/MyDrive/Do_an_bien_so/02_Du_lieu_huan_luyen/YOLO2/Ban_01/yolo2_char_num/` (`data.yaml`, `manifest.csv` `10,363 MiB`, `README.md`, `thong_tin_phien_ban.json`).  
> - Gói bàn giao riêng cho OCR (đủ 7 tệp): `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/Ban_giao_OCR/` (`bang_lop_goc.json`, `anh_bien_goc.csv` `8,351 MiB` gồm đủ `20.262` ảnh nguồn, `ky_tu_goc.csv` `81,879 MiB` gồm đủ `172.521` khung ký tự gốc, `quan_he_nhom.csv`, `quyet_dinh_chat_luong.csv`, `thong_tin_phien_ban.json`, `README.md`).  
> - Báo cáo & biểu đồ toàn nguồn: `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/`.  
> - Ảnh đại diện nguyên byte gốc: `/content/drive/MyDrive/Do_an_bien_so/04_Anh_dai_dien/YOLO2/Roboflow/Ban_01/` (`640x640/01_test.jpg`, `02_train.jpg`, `03_train.jpg`, `danh_sach_anh_mau.csv`).  
> - Notebook riêng kèm đầy đủ output: `05_Notebook/Phan_tich_va_chuan_bi_YOLO2.ipynb` (đặt mặc định `CHE_DO = "xem_bao_cao"`).  
> **Còn thiếu hoặc chưa rõ:** Không còn mục thiếu trong phạm vi YOLO2.  
> **Bước tiếp theo:** Thành viên OCR đọc gói `/content/drive/MyDrive/Do_an_bien_so/03_Bao_cao/03_YOLO2/Ban_01/Ban_giao_OCR/` để gộp nguồn train/val; người tích hợp đưa kết quả đã nghiệm thu vào notebook tổng.  
> **Quyết định cần nhóm chốt thêm:** Không có quyết định mới; lưu ý thành viên OCR dùng cột `character` / `bang_lop_goc.json` vì `data.yaml` của Roboflow v1 có 30 lớp (không có `Q`, `R`).
