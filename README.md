# MySQL TKCSDL - Quản Lý Rạp Chiếu Phim

## Giới thiệu

Đây là project cài đặt cơ sở dữ liệu **Quản Lý Rạp Chiếu Phim** trên **MySQL/MariaDB** và chạy được trên **XAMPP**.

Repo này chỉ giữ phần **MySQL**:

- script tạo database
- script tạo bảng, khóa chính, khóa ngoại
- ràng buộc `UNIQUE`, `CHECK`
- trigger kiểm tra dữ liệu
- tài liệu hướng dẫn tạo bảng và giải thích PK/FK

## Nội dung repo

- `quan_ly_rap_chieu_phim_mysql.sql`: script MySQL hoàn chỉnh
- `HUONG_DAN_TAO_TABLE_MYSQL.md`: tài liệu học và giải thích chi tiết cách tạo bảng, PK, FK, ràng buộc và phần văn nói

## Mô hình cuối

Database cuối gồm 7 bảng:

- `MOVIE`
- `ROOM`
- `SEAT`
- `SHOWTIME`
- `CUSTOMER`
- `TICKET`
- `PAYMENT`

Điểm quan trọng của mô hình cuối:

- không dùng `PAYMENT_DETAIL`
- `PAYMENT` liên kết `1-1` với `TICKET` qua `MaVe`
- `MaVe` trong `PAYMENT` được gắn `UNIQUE`

## Cài đặt trên XAMPP

### Cách 1: phpMyAdmin

1. Mở `XAMPP Control Panel`
2. Start `Apache`
3. Start `MySQL`
4. Mở `http://localhost/phpmyadmin`
5. Chọn tab `Import`
6. Chọn file `quan_ly_rap_chieu_phim_mysql.sql`
7. Bấm `Go`

### Cách 2: Command line

```powershell
Get-Content -LiteralPath '.\quan_ly_rap_chieu_phim_mysql.sql' -Raw |
& 'C:\xamp\mysql\bin\mysql.exe' -u root --default-character-set=utf8mb4
```

## Database được tạo

Tên database trong script là:

```sql
QuanLyRapChieuPhimDB
```

Trên MariaDB/XAMPP, tên thư mục dữ liệu có thể hiển thị ở dạng chữ thường:

```text
quanlyrapchieuphimdb
```

## Dữ liệu mẫu mặc định

Sau khi chạy script sẽ có:

- 3 phim
- 2 phòng
- 7 ghế
- 3 suất chiếu
- 3 khách hàng
- 3 vé
- 3 thanh toán

## Ràng buộc quan trọng

- `SEAT`: `UNIQUE(MaPhong, HangGhe, SoGhe)`
- `TICKET`: `UNIQUE(MaSuat, MaGhe)`
- `PAYMENT`: `UNIQUE(MaVe)`
- trigger kiểm tra ghế phải thuộc đúng phòng của suất chiếu

## Truy vấn kiểm tra nhanh

```sql
SELECT * FROM MOVIE;
SELECT * FROM ROOM;
SELECT * FROM SEAT;
SELECT * FROM SHOWTIME;
SELECT * FROM CUSTOMER;
SELECT * FROM TICKET;
SELECT * FROM PAYMENT;
```

Kiểm tra liên kết vé và thanh toán:

```sql
SELECT
    t.MaVe,
    c.HoTen,
    m.TenPhim,
    st.ThoiGianBatDau,
    p.PhuongThuc,
    p.ThoiGianThanhToan
FROM TICKET t
JOIN CUSTOMER c ON c.MaKH = t.MaKH
JOIN SHOWTIME st ON st.MaSuat = t.MaSuat
JOIN MOVIE m ON m.MaPhim = st.MaPhim
LEFT JOIN PAYMENT p ON p.MaVe = t.MaVe;
```

## Ghi chú

- Repo này chỉ giữ phần MySQL
- Script phù hợp để nộp bài thực hành TKCSDL trên MySQL/XAMPP
- Nếu cần học cách giải thích cấu trúc bảng, xem `HUONG_DAN_TAO_TABLE_MYSQL.md`
