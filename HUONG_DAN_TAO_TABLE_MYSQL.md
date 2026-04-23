# Hướng Dẫn Làm Database Cuối Trên MySQL/XAMPP

## 1. Phần của tôi cần bám vào đâu

Nếu phần của bạn chỉ là làm **database cuối** để chạy trên MySQL/XAMPP, thì:

- chỉ cần bám vào `CHUẨN HÓA.docx`
- không cần bám vào các file khác để quyết định cấu trúc cuối

Lý do:

- `CHUẨN HÓA.docx` là bản cuối sau chuẩn hóa BCNF
- file này chốt chính thức bảng nào được giữ lại
- file này chốt khóa chính, khóa ngoại, ràng buộc duy nhất
- file này chốt bảng nào bị loại bỏ, cụ thể là `PAYMENT_DETAIL`

Nói ngắn gọn:

> Phần làm DB cuối chỉ bám vào `CHUẨN HÓA.docx`.  
> Các file khác là phần giải thích thêm hoặc phần trung gian.

## 2. Dựa vào chuẩn hóa để làm gì

Khi đọc `CHUẨN HÓA.docx`, bạn cần lấy ra 6 việc:

1. Xác định database cuối có bao nhiêu bảng
2. Xác định mỗi bảng có những field nào
3. Xác định cột nào là `PK`
4. Xác định cột nào là `FK`
5. Xác định các ràng buộc như `UNIQUE`, `CHECK`, `NOT NULL`
6. Xác định bảng nào bị loại bỏ

Kết quả cuối rút ra từ chuẩn hóa là có đúng 7 bảng:

- `MOVIE`
- `ROOM`
- `SEAT`
- `SHOWTIME`
- `CUSTOMER`
- `TICKET`
- `PAYMENT`

Và phải bỏ:

- `PAYMENT_DETAIL`

## 3. Làm database cuối như thế nào

Quy trình đúng là:

1. Đọc `CHUẨN HÓA.docx`
2. Ghi ra 7 bảng cuối
3. Ghi các field của từng bảng
4. Đánh dấu `PK`
5. Đánh dấu `FK`
6. Thêm `UNIQUE`, `CHECK`, `NOT NULL`
7. Viết lệnh `CREATE DATABASE`
8. Viết các lệnh `CREATE TABLE`
9. Chạy trên MySQL của XAMPP

Lưu ý:

- MySQL/XAMPP không tạo ra file `.db` kiểu SQLite
- kết quả đúng là một **database** trong MySQL
- nếu cần nộp file thì thường nộp file `.sql`

## 4. PK là gì, mình nên làm gì, lý do là gì

### PK là gì

`PK` là khóa chính của bảng.

PK dùng để:

- phân biệt duy nhất từng dòng
- không được trùng
- không được để trống

Ví dụ:

- `MaPhim` là `PK` của `MOVIE`
- `MaPhong` là `PK` của `ROOM`
- `MaVe` là `PK` của `TICKET`

### Mình nên làm gì

Khi tạo một bảng, hãy tìm cột nào đại diện duy nhất cho từng bản ghi rồi chọn cột đó làm `PK`.

Ví dụ:

```sql
CREATE TABLE MOVIE (
    MaPhim VARCHAR(10) PRIMARY KEY,
    TenPhim VARCHAR(150) NOT NULL
);
```

### Lý do

Nếu không có `PK` thì:

- không biết dòng nào là duy nhất
- dễ bị trùng dữ liệu
- bảng khác không tham chiếu ổn định vào được

### Văn nói

> PK là khóa chính. Em chọn PK là mã định danh duy nhất của mỗi thực thể để phân biệt từng dòng và làm mốc cho các bảng khác tham chiếu tới.

## 5. FK là gì, mình nên làm gì, lý do là gì

### FK là gì

`FK` là khóa ngoại.

FK là cột trong bảng này dùng để tham chiếu đến `PK` của bảng khác.

Ví dụ:

- `SEAT.MaPhong` tham chiếu `ROOM.MaPhong`
- `SHOWTIME.MaPhim` tham chiếu `MOVIE.MaPhim`
- `TICKET.MaKH` tham chiếu `CUSTOMER.MaKH`

### Mình nên làm gì

Bạn phải xác định quan hệ giữa hai bảng:

- nếu là `1-N` thì đặt `FK` ở phía `N`
- nếu là `1-1` thì đặt `FK` ở một bên và thường thêm `UNIQUE`

Ví dụ:

```sql
FOREIGN KEY (MaPhong) REFERENCES ROOM(MaPhong)
```

### Lý do

FK giúp:

- liên kết các bảng với nhau
- tránh dữ liệu mồ côi
- đảm bảo bảng con chỉ tham chiếu tới dữ liệu cha có thật

### Văn nói

> FK là khóa ngoại. Em đặt FK ở bảng con để thể hiện quan hệ giữa các bảng và giữ toàn vẹn tham chiếu.

## 6. UNIQUE là gì, mình nên làm gì, lý do là gì

### UNIQUE là gì

`UNIQUE` là ràng buộc không cho dữ liệu bị trùng.

Nó dùng khi cột đó không phải `PK` nhưng vẫn phải duy nhất theo nghiệp vụ.

Ví dụ:

- `UNIQUE(MaPhong, HangGhe, SoGhe)` trong `SEAT`
- `UNIQUE(MaSuat, MaGhe)` trong `TICKET`
- `UNIQUE(MaVe)` trong `PAYMENT`

### Mình nên làm gì

Hãy tự hỏi:

- dữ liệu này có được phép trùng không
- nếu không thì thêm `UNIQUE`

### Lý do

`UNIQUE` giúp đúng với nghiệp vụ thực tế.

Ví dụ:

- một ghế trong một suất không thể bán hai lần
- một vé chỉ có một thanh toán

### Văn nói

> UNIQUE dùng cho những cột không phải khóa chính nhưng vẫn phải duy nhất theo nghiệp vụ.

## 7. CHECK là gì, mình nên làm gì, lý do là gì

### CHECK là gì

`CHECK` là ràng buộc kiểm tra điều kiện hợp lệ của dữ liệu.

Ví dụ:

- `ThoiLuong > 0`
- `SucChua > 0`
- `GiaVe >= 0`

### Mình nên làm gì

Nếu dữ liệu có điều kiện nghiệp vụ rõ ràng thì thêm `CHECK`.

### Lý do

Nó ngăn dữ liệu vô lý đi vào database.

### Văn nói

> CHECK dùng để chặn dữ liệu sai ngay từ lúc nhập vào database, ví dụ giá vé không thể âm.

## 8. NOT NULL là gì, mình nên làm gì, lý do là gì

### NOT NULL là gì

`NOT NULL` nghĩa là cột bắt buộc phải có dữ liệu.

### Mình nên làm gì

Nếu cột đó là thông tin bắt buộc thì thêm `NOT NULL`.

### Lý do

Nếu để trống các cột quan trọng thì dữ liệu sẽ thiếu và dùng không được.

### Văn nói

> NOT NULL dùng cho các cột bắt buộc, để dữ liệu không bị thiếu những thông tin quan trọng.

## 9. CREATE DATABASE là gì, mình nên làm gì, lý do là gì

### CREATE DATABASE là gì

`CREATE DATABASE` là lệnh tạo cơ sở dữ liệu.

Database là nơi chứa toàn bộ các bảng.

### Mình nên làm gì

Bạn nên:

1. đặt tên database theo đề tài
2. chọn `utf8mb4` để lưu tiếng Việt tốt hơn
3. dùng `USE` để chuyển vào database đó

Ví dụ:

```sql
CREATE DATABASE IF NOT EXISTS QuanLyRapChieuPhimDB
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

USE QuanLyRapChieuPhimDB;
```

### Lý do

Phải có database trước thì mới có chỗ để tạo bảng.

### Văn nói

> Em tạo database trước để có nơi chứa toàn bộ các bảng của hệ thống.

## 10. CREATE TABLE là gì, mình nên làm gì, lý do là gì

### CREATE TABLE là gì

`CREATE TABLE` là lệnh tạo từng bảng trong database.

### Mình nên làm gì

Khi tạo một bảng, bạn phải làm đủ:

1. ghi tên bảng
2. ghi tên các cột
3. chọn kiểu dữ liệu
4. xác định `PK`
5. xác định `FK`
6. thêm `NOT NULL`, `UNIQUE`, `CHECK` nếu cần

### Lý do

Nếu cấu trúc bảng sai thì:

- quan hệ giữa các bảng sai
- dữ liệu nhập vào sai
- database cuối không đúng với bản chuẩn hóa

### Văn nói

> Khi tạo table, em phải xác định đúng tên bảng, cột, kiểu dữ liệu, khóa chính, khóa ngoại và các ràng buộc để cấu trúc đúng với bản chuẩn hóa.

## 11. Bảng cuối cùng và cách giải thích từng bảng

## 11.1. Bảng `MOVIE`

### Bảng này dùng để làm gì

Lưu thông tin phim.

### Các field

- `MaPhim`
- `TenPhim`
- `TheLoai`
- `ThoiLuong`
- `NgayKhoiChieu`
- `MoTa`

### PK và lý do

- `MaPhim` là `PK`
- vì mỗi phim cần một mã duy nhất để phân biệt với phim khác

### FK

- không có `FK`

### Văn nói

> Bảng MOVIE dùng để lưu thông tin phim. Em chọn MaPhim làm khóa chính vì mỗi phim phải có một mã duy nhất để định danh.

## 11.2. Bảng `ROOM`

### Bảng này dùng để làm gì

Lưu thông tin phòng chiếu.

### Các field

- `MaPhong`
- `TenPhong`
- `LoaiPhong`
- `SucChua`

### PK và lý do

- `MaPhong` là `PK`
- vì mỗi phòng cần một mã duy nhất

### FK

- không có `FK`

### Văn nói

> Bảng ROOM dùng để lưu thông tin phòng chiếu. Em chọn MaPhong làm khóa chính vì mỗi phòng chỉ có một mã riêng để phân biệt.

## 11.3. Bảng `SEAT`

### Bảng này dùng để làm gì

Lưu thông tin ghế trong từng phòng.

### Các field

- `MaGhe`
- `MaPhong`
- `HangGhe`
- `SoGhe`

### PK và lý do

- `MaGhe` là `PK`
- vì mỗi ghế cần một mã duy nhất

### FK và lý do

- `MaPhong` là `FK` tham chiếu `ROOM(MaPhong)`
- vì mỗi ghế phải thuộc về một phòng cụ thể

### Ràng buộc thêm

- `UNIQUE(MaPhong, HangGhe, SoGhe)`
- để trong cùng một phòng không có hai ghế trùng hàng và số ghế

### Văn nói

> Bảng SEAT lưu thông tin ghế. Em chọn MaGhe làm khóa chính vì mỗi ghế có một mã riêng. Em đặt MaPhong là khóa ngoại vì ghế phải thuộc một phòng cụ thể. Em thêm UNIQUE theo MaPhong, HangGhe, SoGhe để tránh trùng ghế trong cùng một phòng.

## 11.4. Bảng `SHOWTIME`

### Bảng này dùng để làm gì

Lưu thông tin suất chiếu.

### Các field

- `MaSuat`
- `MaPhim`
- `MaPhong`
- `ThoiGianBatDau`
- `ThoiGianKetThuc`

### PK và lý do

- `MaSuat` là `PK`
- vì mỗi suất chiếu cần một mã riêng

### FK và lý do

- `MaPhim` là `FK` sang `MOVIE(MaPhim)`
- vì mỗi suất chiếu thuộc về một phim
- `MaPhong` là `FK` sang `ROOM(MaPhong)`
- vì mỗi suất chiếu diễn ra ở một phòng

### Ràng buộc thêm

- `CHECK(ThoiGianKetThuc > ThoiGianBatDau)`
- để thời gian kết thúc phải lớn hơn thời gian bắt đầu

### Văn nói

> Bảng SHOWTIME lưu thông tin suất chiếu. Em chọn MaSuat làm khóa chính vì mỗi suất có một mã riêng. Em đặt MaPhim và MaPhong là khóa ngoại vì một suất phải gắn với một phim và một phòng. Em thêm CHECK để thời gian kết thúc phải sau thời gian bắt đầu.

## 11.5. Bảng `CUSTOMER`

### Bảng này dùng để làm gì

Lưu thông tin khách hàng.

### Các field

- `MaKH`
- `HoTen`
- `SoDienThoai`
- `Email`

### PK và lý do

- `MaKH` là `PK`
- vì mỗi khách hàng cần một mã riêng

### FK

- không có `FK`

### Văn nói

> Bảng CUSTOMER lưu thông tin khách hàng. Em chọn MaKH làm khóa chính vì mỗi khách hàng cần một mã duy nhất để quản lý.

## 11.6. Bảng `TICKET`

### Bảng này dùng để làm gì

Lưu thông tin vé đã đặt.

### Các field

- `MaVe`
- `MaKH`
- `MaSuat`
- `MaGhe`
- `NgayDat`
- `TrangThai`
- `GiaVe`

### PK và lý do

- `MaVe` là `PK`
- vì mỗi vé cần một mã duy nhất

### FK và lý do

- `MaKH` là `FK` sang `CUSTOMER(MaKH)`
- vì mỗi vé thuộc về một khách hàng
- `MaSuat` là `FK` sang `SHOWTIME(MaSuat)`
- vì mỗi vé thuộc về một suất chiếu
- `MaGhe` là `FK` sang `SEAT(MaGhe)`
- vì mỗi vé gắn với một ghế

### Ràng buộc thêm

- `UNIQUE(MaSuat, MaGhe)`
- để một ghế trong cùng một suất không bị bán hai lần
- `CHECK(GiaVe >= 0)`
- để giá vé không âm
- `CHECK(TrangThai IN ('DaDat', 'DaThanhToan', 'DaHuy'))`
- để trạng thái đúng theo nghiệp vụ

### Văn nói

> Bảng TICKET lưu thông tin vé. Em chọn MaVe làm khóa chính vì mỗi vé phải có mã duy nhất. Em đặt MaKH, MaSuat, MaGhe là khóa ngoại vì vé liên kết tới khách hàng, suất chiếu và ghế. Em thêm UNIQUE theo MaSuat và MaGhe để một ghế trong một suất không bị bán hai lần.

## 11.7. Bảng `PAYMENT`

### Bảng này dùng để làm gì

Lưu thông tin thanh toán của vé.

### Các field

- `MaThanhToan`
- `MaVe`
- `PhuongThuc`
- `ThoiGianThanhToan`

### PK và lý do

- `MaThanhToan` là `PK`
- vì mỗi thanh toán cần một mã riêng

### FK và lý do

- `MaVe` là `FK` sang `TICKET(MaVe)`
- vì mỗi thanh toán gắn với một vé

### Ràng buộc thêm

- `UNIQUE(MaVe)`
- để một vé chỉ có một thanh toán

### Văn nói

> Bảng PAYMENT lưu thông tin thanh toán. Em chọn MaThanhToan làm khóa chính vì mỗi lần thanh toán có một mã riêng. Em đặt MaVe là khóa ngoại vì thanh toán phải gắn với vé. Em thêm UNIQUE cho MaVe để đảm bảo một vé chỉ có một thanh toán.

## 12. Vì sao bỏ bảng `PAYMENT_DETAIL`

`TICKET` và `PAYMENT` là quan hệ `1-1`.

Nếu tạo thêm bảng `PAYMENT_DETAIL(MaThanhToan, MaVe)` thì bị dư thừa vì:

- biết `MaThanhToan` là biết được `MaVe`
- biết `MaVe` cũng chỉ ra đúng một `MaThanhToan`

Vì vậy bản cuối chỉ cần:

- bảng `PAYMENT`
- cột `MaVe` trong `PAYMENT`
- `UNIQUE(MaVe)`

### Văn nói

> Em bỏ PAYMENT_DETAIL vì quan hệ giữa vé và thanh toán là 1-1. Chỉ cần để MaVe trong PAYMENT và gắn UNIQUE là đủ, không cần thêm bảng trung gian.

## 13. Thứ tự tạo bảng bằng tay

Tạo theo thứ tự cha trước, con sau:

1. `MOVIE`
2. `ROOM`
3. `CUSTOMER`
4. `SEAT`
5. `SHOWTIME`
6. `TICKET`
7. `PAYMENT`

### Văn nói

> Em tạo bảng cha trước rồi mới tạo bảng con để tránh lỗi khóa ngoại.

## 14. Cách chọn kiểu dữ liệu

- mã như `MaPhim`, `MaVe`, `MaKH` dùng `VARCHAR(10)`
- tên dùng `VARCHAR(100)` hoặc `VARCHAR(150)`
- mô tả dài dùng `TEXT`
- số lượng, số ghế, thời lượng dùng `INT`
- tiền dùng `DECIMAL(12,2)`
- chỉ có ngày dùng `DATE`
- có ngày giờ dùng `DATETIME`

Ví dụ:

- `NgayKhoiChieu` dùng `DATE`
- `NgayDat` dùng `DATETIME`
- `GiaVe` dùng `DECIMAL(12,2)`

### Văn nói

> Em chọn kiểu dữ liệu theo bản chất của dữ liệu, ví dụ tiền dùng DECIMAL, ngày dùng DATE, còn ngày giờ dùng DATETIME.

## 15. SQL tạo database và table

```sql
CREATE DATABASE IF NOT EXISTS QuanLyRapChieuPhimDB
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

USE QuanLyRapChieuPhimDB;

CREATE TABLE MOVIE (
    MaPhim VARCHAR(10) PRIMARY KEY,
    TenPhim VARCHAR(150) NOT NULL,
    TheLoai VARCHAR(100) NOT NULL,
    ThoiLuong INT NOT NULL CHECK (ThoiLuong > 0),
    NgayKhoiChieu DATE NOT NULL,
    MoTa TEXT
);

CREATE TABLE ROOM (
    MaPhong VARCHAR(10) PRIMARY KEY,
    TenPhong VARCHAR(100) NOT NULL,
    LoaiPhong VARCHAR(30) NOT NULL,
    SucChua INT NOT NULL CHECK (SucChua > 0)
);

CREATE TABLE CUSTOMER (
    MaKH VARCHAR(10) PRIMARY KEY,
    HoTen VARCHAR(100) NOT NULL,
    SoDienThoai VARCHAR(20) NOT NULL,
    Email VARCHAR(100) NOT NULL
);

CREATE TABLE SEAT (
    MaGhe VARCHAR(10) PRIMARY KEY,
    MaPhong VARCHAR(10) NOT NULL,
    HangGhe VARCHAR(5) NOT NULL,
    SoGhe INT NOT NULL CHECK (SoGhe > 0),
    UNIQUE (MaPhong, HangGhe, SoGhe),
    FOREIGN KEY (MaPhong) REFERENCES ROOM(MaPhong)
);

CREATE TABLE SHOWTIME (
    MaSuat VARCHAR(10) PRIMARY KEY,
    MaPhim VARCHAR(10) NOT NULL,
    MaPhong VARCHAR(10) NOT NULL,
    ThoiGianBatDau DATETIME NOT NULL,
    ThoiGianKetThuc DATETIME NOT NULL,
    CHECK (ThoiGianKetThuc > ThoiGianBatDau),
    FOREIGN KEY (MaPhim) REFERENCES MOVIE(MaPhim),
    FOREIGN KEY (MaPhong) REFERENCES ROOM(MaPhong)
);

CREATE TABLE TICKET (
    MaVe VARCHAR(10) PRIMARY KEY,
    MaKH VARCHAR(10) NOT NULL,
    MaSuat VARCHAR(10) NOT NULL,
    MaGhe VARCHAR(10) NOT NULL,
    NgayDat DATETIME NOT NULL,
    TrangThai VARCHAR(30) NOT NULL,
    GiaVe DECIMAL(12,2) NOT NULL CHECK (GiaVe >= 0),
    UNIQUE (MaSuat, MaGhe),
    CHECK (TrangThai IN ('DaDat', 'DaThanhToan', 'DaHuy')),
    FOREIGN KEY (MaKH) REFERENCES CUSTOMER(MaKH),
    FOREIGN KEY (MaSuat) REFERENCES SHOWTIME(MaSuat),
    FOREIGN KEY (MaGhe) REFERENCES SEAT(MaGhe)
);

CREATE TABLE PAYMENT (
    MaThanhToan VARCHAR(10) PRIMARY KEY,
    MaVe VARCHAR(10) NOT NULL UNIQUE,
    PhuongThuc VARCHAR(30) NOT NULL,
    ThoiGianThanhToan DATETIME NOT NULL,
    CHECK (PhuongThuc IN ('TienMat', 'The', 'ChuyenKhoan', 'ViDienTu')),
    FOREIGN KEY (MaVe) REFERENCES TICKET(MaVe)
);
```

## 16. Câu nói ngắn để trả lời thầy

> Phần của em là làm database cuối nên em chỉ bám vào file chuẩn hóa cuối.  
> Từ file đó em xác định 7 bảng cuối, các khóa chính, khóa ngoại và các ràng buộc cần có.  
> Sau đó em dùng MySQL để tạo database và tạo từng table theo đúng lược đồ chuẩn hóa.

## 17. Ghi nhớ nhanh

- `MOVIE`, `ROOM`, `CUSTOMER` là bảng cha cơ bản
- `SEAT` phụ thuộc `ROOM`
- `SHOWTIME` phụ thuộc `MOVIE` và `ROOM`
- `TICKET` phụ thuộc `CUSTOMER`, `SHOWTIME`, `SEAT`
- `PAYMENT` phụ thuộc `TICKET`
- bỏ `PAYMENT_DETAIL`
- `TICKET` cần `UNIQUE(MaSuat, MaGhe)`
- `PAYMENT` cần `UNIQUE(MaVe)`
