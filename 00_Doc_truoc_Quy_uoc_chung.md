# Giao việc YOLO2 và OCR — đọc trước khi bắt đầu

Ngày bàn giao: 20/11/2026 · Phiên bản yêu cầu: 1.0.

Đây là tài liệu yêu cầu và nghiệm thu, **chưa phải kết quả triển khai YOLO2/OCR**. Có thể gửi tài liệu chung này cùng tài liệu nhiệm vụ tương ứng cho từng thành viên và AI của bạn đó. Người làm YOLO1 tiếp tục giữ notebook tổng; hai thành viên làm trên notebook riêng để tránh sửa đè.

## 1. Mục tiêu của đồ án

| Công đoạn | Đầu vào | Công việc | Nguồn đã chốt |
|---|---|---|---|
| YOLO1 | Ảnh xe/cảnh | Tìm vùng biển số | Global v13 |
| YOLO2 | Ảnh biển số trong nguồn, phải kiểm chứng thực tế | Tìm khung từng ký tự | Roboflow OCR v1 |
| OCR | Ảnh cắt chứa một ký tự | Nhận dạng ký tự thuộc 32 lớp | Train/val: Roboflow + Winter2897 + CCPD2019; test: VNLP |

YOLO1 và YOLO2 dùng nguồn riêng, không gộp thêm bộ khác. OCR mới là phần gộp ba nguồn. Thống kê phải tính lại từ đúng nguồn, không chép số lượng của pipeline gộp cũ.

## 2. Tiến độ đã xác minh

| Nội dung | Trạng thái tại lúc bàn giao |
|---|---|
| Tập trung nguồn, cấu hình và hai chế độ notebook | Đã thực hiện trên Drive/Colab của người phụ trách |
| YOLO1 Global v13 | Đã phân tích 101.866 ảnh; đã lập phương án sau rà soát |
| Bộ YOLO1 sau xử lý | Chưa xuất. 98.983 ảnh là số dự kiến, không phải số đã xuất |
| YOLO2 | Mới kiểm kê nguồn/cách chia; chưa phân tích ảnh và làm sạch theo phương án mới |
| OCR | Đã chốt hướng xử lý; chưa triển khai bộ ba nguồn train/val và VNLP test mới |
| Thêm biểu đồ YOLO1 vừa được yêu cầu | Đang tạm hoãn để soạn bàn giao; chưa bổ sung trong lượt này |

Roboflow đã được xác nhận từ nội dung README là dự án `licence-plate-vn/ocr-stypd`, phiên bản 1. Kiểm kê danh sách tệp: **16.209 train + 2.027 val + 2.026 test = 20.262 ảnh**. Đây chưa phải số ảnh hợp lệ sau giải mã, chưa phải số ký tự và chưa phải kết quả làm sạch.

Winter đã tìm thấy ZIP và `winter_classes.txt`, nhưng chưa xác minh xong sự tương ứng ID–ký tự trong lượt mới. CCPD đã kiểm tra có tệp XZ, chưa quét toàn bộ. VNLP đã quan sát cấu trúc nguồn, chưa chuẩn bị bộ test mới. Không đoán số mẫu/cấu trúc nhãn chi tiết của ba bộ này.

## 3. Nguồn và quyền truy cập

Thư mục chung trên tài khoản người phụ trách: `MyDrive/ML/Do_an_bien_so/`.

| Nguồn | Đường dẫn tương đối dưới thư mục chung |
|---|---|
| Global v13 | `01_Du_lieu_goc/Global_v13/license-plate-dataset.zip` |
| Roboflow | `01_Du_lieu_goc/Roboflow/roboflow_ocr_v1_yolov8.zip` |
| Winter2897 | `01_Du_lieu_goc/Winter2897/yolo_plate_ocr_dataset.zip` |
| Bảng lớp Winter | `01_Du_lieu_goc/Winter2897/winter_classes.txt` |
| CCPD2019 | `01_Du_lieu_goc/CCPD2019/CCPD2019.tar.xz` |
| VNLP | `01_Du_lieu_goc/VNLP/VNLP.zip` |

- [Mở thư mục dự án](https://drive.google.com/drive/folders/1nBzyrK4yzCwul_6WSiItAKXqjl21DEw6).
- [Mở các nguồn đã tập trung](https://drive.google.com/drive/folders/1Qm4crpxPTgGh5tLeGMWKAzR10inSxolC).
- [Mở notebook tổng để tham khảo cách trình bày](https://colab.research.google.com/drive/1pqbj00RtMUQSCgYblkzjoTISLk_ddhzG?hl=vi).

Các liên kết dùng quyền hiện có; tài liệu này **không tự cấp quyền**. Thành viên cần có quyền đọc nguồn và quyền ghi thư mục nhiệm vụ của mình. Không cần đặt dữ liệu công khai. Drive của thành viên có thể mount đường dẫn khác; cấu hình thư mục nguồn một lần, không giả định `MyDrive` của ai cũng giống nhau. Nếu không đọc được nguồn, báo đúng vướng mắc, không tự tải một bộ cùng tên trên Internet thay thế.

Giữ nguyên tệp nguồn. Mọi chỉnh nhãn, cắt ảnh hoặc loại mẫu chỉ áp dụng vào đầu ra phiên bản mới; mẫu loại vẫn có bảng truy vết, không xóa nguồn.

## 4. Phân công và phần có thể làm song song

| Người phụ trách | Làm riêng | Bàn giao cho người còn lại |
|---|---|---|
| Thành viên YOLO2 | Kiểm tra Roboflow; thống kê; xử lý quan hệ trùng; xuất bộ phát hiện ký tự | Bảng lớp gốc, bảng ký tự và khung gốc, mã ảnh/nhóm biển, quyết định chất lượng/trùng lặp |
| Thành viên OCR | Kiểm tra Winter/CCPD/VNLP, hiệu chỉnh lọc ký tự; thống kê; gộp và chia nhóm | Bộ OCR, ánh xạ lớp, bảng nguồn/ký tự/tập, nhóm liên quan và nhật ký lọc |
| Người tích hợp | Giữ YOLO1 và notebook tổng; rà tính nhất quán giữa các phần | Hợp nhất báo cáo sau khi nghiệm thu; không tự coi kết quả chưa chạy là hoàn tất |

OCR có thể làm Winter/CCPD/VNLP ngay. Khi chưa nhận bảng Roboflow đã kiểm chứng, phần Roboflow chỉ được đánh dấu tạm thời. Không dùng nhãn YOLO2 đã gộp về lớp 0 để đoán lại chữ.

Hai bạn trao đổi trực tiếp các tệp bàn giao trong thư mục chung. AI chỉ soạn/xử lý trong phạm vi được giao, không tự gửi tin nhắn cho người khác hoặc sửa phần việc của họ.

## 5. Tổ chức đầu ra và notebook

Giữ các thư mục cấp trên đã thống nhất:

| Thư mục | YOLO2 | OCR |
|---|---|---|
| Dữ liệu huấn luyện | `02_Du_lieu_huan_luyen/YOLO2/` | `02_Du_lieu_huan_luyen/OCR/` |
| Báo cáo | `03_Bao_cao/03_YOLO2/` | `03_Bao_cao/04_OCR/` |
| Ảnh đại diện | `04_Anh_dai_dien/YOLO2/Roboflow/` | `04_Anh_dai_dien/OCR/<Nguon>/` |
| Notebook riêng | `05_Notebook/Phan_tich_va_chuan_bi_YOLO2.ipynb` | `05_Notebook/Phan_tich_va_chuan_bi_OCR.ipynb` |

Trong mỗi thư mục nhiệm vụ có thể dùng `Ban_01`, `Ban_02` cho các bản đã xuất, kèm `thong_tin_phien_ban.json` nêu ngày, nguồn, cấu hình và trạng thái. Đây là quy ước đầu ra đề xuất cho phần chưa triển khai, không phải các tệp đã tồn tại. Không tạo một loạt thư mục tên ngẫu nhiên khó hiểu và không ghi đè bản đã được nghiệm thu.

Giữ tên bộ nạp đã dùng trong dự án: `yolo2_char_num` và `ocr_ky_tu`. Cấu trúc chi tiết có trong hai tài liệu nhiệm vụ. Đặt tên báo cáo bằng từ dễ hiểu, phần mềm vẫn có thể dùng tên cột ASCII ổn định.

## 6. Hai chế độ bắt buộc

- `CHE_DO = "xu_ly_du_lieu"`: kiểm tra/cắt/xuất theo cấu hình; lưu kết quả và tiến độ.
- `CHE_DO = "xem_bao_cao"`: chỉ mở bảng, hình và kết luận đã lưu. Thiếu báo cáo thì thông báo; không tự quét ZIP/TAR, giải mã toàn bộ ảnh hoặc đóng gói lại.

Mặc định notebook khi bàn giao là `xem_bao_cao`. Không gọi chế độ xem là “nhanh” nếu bên trong vẫn kiểm tra CRC toàn bộ ZIP hoặc nạp lại mọi ảnh. Có thể đọc một bảng dữ liệu nhỏ đã tổng hợp để vẽ; ưu tiên mở hình đã lưu.

Phân biệt **mẫu thử**, **lượt đầy đủ**, **phương án đề xuất**, **bộ đã xuất** bằng nhãn rõ ràng trên bảng/hình. Không để kết quả cũ hiển thị như lượt chạy mới khi ô mã gặp lỗi.

## 7. Giao ước dữ liệu giữa YOLO2 và OCR

CSV dùng UTF-8-SIG, một dòng tiêu đề, dấu phẩy, đúng quy tắc trích dẫn CSV. Đọc nhãn ký tự dưới dạng chuỗi để không mất `0`. Đường dẫn trong manifest tính tương đối từ bộ dữ liệu; đường dẫn nguồn trong archive được lưu nguyên dạng POSIX. JSON dùng UTF-8. Trạng thái boolean xuất `true`/`false` và bộ đọc phải phân tích tường minh.

Mỗi tệp metadata có `schema_version`; mỗi lượt có nguồn, phiên bản quy tắc, phiên bản thư viện giải mã, seed và cấu hình. Nếu không xác minh được số phiên bản nguồn, ghi “chưa xác minh”, không suy từ tên ZIP.

Các trường tối thiểu dùng chung:

| Trường | Ý nghĩa |
|---|---|
| `source` | `Roboflow`, `Winter2897`, `CCPD2019` hoặc `VNLP` |
| `source_file` | Tên tệp archive nguồn |
| `source_member` | Đường dẫn tệp ảnh trong archive |
| `source_image_id` | ID ổn định có namespace nguồn và đường dẫn, không chỉ dùng basename |
| `original_split` | Split nguồn; ghi `unknown` nếu không có, không tự gọi đó là train |
| `parent_id` | Ảnh/biển gốc trước các phép tạo biến thể, kèm namespace nguồn |
| `group_id` | Nhóm liên quan sau khi nối các quan hệ đã xác nhận, có thể qua nhiều nguồn |
| `width_px`, `height_px` | Kích thước ảnh ứng với tọa độ được ghi |
| `pixel_sha256` | Dấu toàn bộ pixel đã giải mã và kích thước theo quy tắc được tài liệu hóa |
| `status`, `reason_codes` | Giữ, tạm giữ ngoài, loại; nêu lý do có thể tra lại |

Namespace nguồn chống trùng tên ID nhưng **không chứng minh hai nguồn độc lập**. Cần đối chiếu ảnh thật để nối nhóm giữa nguồn. Hash gần giống chỉ gợi ý; ảnh cùng dòng xe/kiểu chữ không đủ kết luận là cùng ảnh/biển.

## 8. Bố cục trình bày theo notebook thực hành

Mỗi phần viết theo chuỗi: **câu hỏi cần trả lời → giải thích ngắn → ô mã gọn → bảng/hình thực tế → nhận xét và việc tiếp theo**. Hàm hỗ trợ dài để ô thu gọn. Không đưa chi tiết lập trình vào tiêu đề dành cho người đọc báo cáo.

Ví dụ nhận xét đúng: “Trong lượt đã xét, nhóm kích thước A chiếm B%; vì vậy chọn ba ảnh gốc ở kích thước này để đối chiếu.” A/B chỉ điền sau khi tính thật. Ví dụ chưa đủ: “Dataset chất lượng tốt, đã tránh mọi data leak” khi mới kiểm tra cú pháp hoặc chỉ xem vài ảnh.

Mỗi biểu đồ có đơn vị và mẫu số; nếu biểu đồ log thì ghi rõ. Nhóm nguyên nhân có thể chồng lặp phải được ghi chú; tổng số loại phải đếm theo ID ảnh/mẫu duy nhất.

## 9. Tối ưu và tính đúng đắn

1. Đọc cấu trúc, chạy mẫu nhỏ, xem đối chiếu trước khi chạy lớn. Giới hạn mẫu là cấu hình của lượt thử; không suy rộng số lượng lên toàn nguồn.
2. Với thao tác đọc nhiều, dùng nguồn trên SSD `/content` sau khi kiểm tra dung lượng. Không copy/giải nén lại khi bộ đệm vẫn đúng nguồn.
3. Tách các bước kiểm kê → kiểm tra → crop → chia tập → thống kê → đóng gói; lưu metadata đủ để sửa biểu đồ mà không đọc lại ảnh.
4. Tiến độ ghi theo phần hoàn tất, ghi tệp tạm rồi đổi tên; chạy tiếp phải không mất hoặc nhân đôi mẫu. Đổi nguồn/quy tắc ảnh hưởng bước nào thì làm lại đúng bước đó.
5. CCPD TAR.XZ là nguồn đọc tuần tự. Chọn đọc một lượt và lưu crop/chỉ mục trung gian vừa đủ; đo chi phí khi chạy tiếp. Không khẳng định có thể truy cập ngẫu nhiên ảnh nén mà chưa chứng minh.
6. Ghi thời gian và số mẫu thực sự cho từng bước; khi đo nhanh hơn, dùng cùng dữ liệu/cấu hình, phân biệt lần đầu với lần dùng cache.
7. Khi chỉ tối ưu đọc/ghi, đối chiếu bản ghi/ảnh/nhãn/split với bản tham chiếu trên cùng mẫu. Đổi quy tắc lọc phải được ghi là thay đổi phương pháp.

## 10. Ranh giới và điểm chưa chốt

- Nhiệm vụ hiện tại là chuẩn bị, phân tích và xuất dữ liệu. Chưa yêu cầu huấn luyện mô hình hoặc lựa chọn kiến trúc mới.
- OCR cố định 32 lớp: `0123456789ABCDEFGHKLMNPQRSTUVXYZ`; ID 0–31 theo đúng thứ tự chuỗi. Không có I/J/O/W. Không tự đổi I thành 1 hoặc O thành 0.
- Mục tiêu 3k/10k của pipeline cũ **không còn là mục tiêu mặc định**. Quy mô OCR sẽ chọn sau khi có thống kê nguồn/lớp.
- Chưa có ngưỡng chất lượng mới đã được xác nhận. Không tự gắn mác “đã chốt” cho một giá trị độ nét/diện tích/độ sáng.
- YOLO2 giữ các tập gốc sau kiểm tra trùng lặp; OCR gộp ba nguồn rồi chia nhóm train/val, VNLP test riêng. Vì vậy split OCR không bắt buộc trùng split YOLO2. Cần lưu cả hai và tránh tuyên bố độc lập toàn pipeline khi mới kiểm tra từng mô hình.
- Nếu cần đánh giá hệ thống đầu-cuối, người tích hợp phải đối chiếu nhóm ở **mọi dữ liệu train của các công đoạn** với nhóm test đầu-cuối. Không tự đổi nguồn hay split đã chốt để giải quyết việc này mà không báo.
- Không sửa YOLO1 hoặc notebook tổng trong phần việc giao cho hai thành viên. Không di chuyển lại nguồn dùng chung.

## 11. Cách bắt đầu

Người giao việc gửi: tài liệu này + tài liệu nhiệm vụ + checklist nghiệm thu. Thành viên dán đoạn “Yêu cầu đưa cho AI” ở đầu tài liệu nhiệm vụ, đính kèm tài liệu, cung cấp bản notebook tổng tham khảo và quyền đọc đúng nguồn.

Đầu ra đầu tiên cần nhận là **bản kiểm tra nguồn và mẫu ảnh đối chiếu**, không phải lời hứa đã chạy toàn bộ. Sau đó triển khai các mốc trong tài liệu; không cần hỏi lại các quyết định đã chốt. Khi bàn giao, nêu rõ việc đã chạy, dữ liệu đã kiểm tra và phần chưa hoàn tất.

Trong gói ZIP có `Mau_bang/`: CSV chỉ chứa tiêu đề và JSON mapping 32 lớp theo yêu cầu. Đây là mẫu giao ước, **không có dữ liệu đã xử lý**. Có thể thêm cột nhưng không tự đổi ý nghĩa cột; thay schema thì hai người cùng cập nhật. Thư mục `Tham_khao/` chứa bản notebook tổng đã lưu phiên bản 4, 31 ô, không kèm output. Dùng tham khảo cấu hình/cách trình bày, không chạy tất cả các phần để bắt đầu nhiệm vụ riêng và không hiểu đó là notebook YOLO2/OCR đã triển khai.
