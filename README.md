# MySQL TKCSDL - Quan Ly Rap Chieu Phim

## Gioi thieu

Day la project cai dat co so du lieu **Quan Ly Rap Chieu Phim** tren **MySQL/XAMPP**.

Mo hinh cuoi cung duoc lay tu file `CHUẨN HÓA.docx`, tuc la luoc do sau chuan hoa BCNF.

Project nay tap trung vao:

- tao database tren MySQL
- tao day du bang, khoa chinh, khoa ngoai, `UNIQUE`, `CHECK`
- tao trigger kiem tra ghe thuoc dung phong cua suat chieu
- chen du lieu mau
- truy van kiem tra nhanh sau khi cai dat

## Mo hinh cuoi

Database cuoi giu **7 bang**:

- `MOVIE`
- `ROOM`
- `SEAT`
- `SHOWTIME`
- `CUSTOMER`
- `TICKET`
- `PAYMENT`

Khong su dung:

- `PAYMENT_DETAIL`

Ly do:

- quan he giua `TICKET` va `PAYMENT` la `1-1`
- trong mo hinh cuoi, `PAYMENT` tham chieu `TICKET` thong qua `MaVe`
- `MaVe` trong `PAYMENT` duoc gan `UNIQUE` de dam bao moi ve chi co 1 thanh toan

## Cau truc file

- `quan_ly_rap_chieu_phim_mysql.sql`: script cai dat tren MySQL
- `quan_ly_rap_chieu_phim_sqlserver.sql`: script cai dat tren SQL Server
- `CAI_DAT_DTB_LEN_DBMS.md`: huong dan cai dat ngan gon
- `HUONG_DAN_TAO_TABLE_MYSQL.md`: ghi chu chi tiet ve PK, FK, cach tao bang va van noi
- `CHUẨN HÓA.docx`: nguon thiet ke cuoi cung de dung database
- `MÔ HÌNH QUAN HỆ.docx`: mo hinh trung gian
- `quy tắc mối quan hệ.docx`: giai thich cac moi quan he nghiep vu

## Cach cai dat tren XAMPP

### Cach 1: Dung phpMyAdmin

1. Mo XAMPP Control Panel
2. Start `Apache`
3. Start `MySQL`
4. Truy cap `http://localhost/phpmyadmin`
5. Chon tab `Import`
6. Chon file `quan_ly_rap_chieu_phim_mysql.sql`
7. Bam `Go`

### Cach 2: Dung command line

```powershell
Get-Content -LiteralPath '.\quan_ly_rap_chieu_phim_mysql.sql' -Raw |
& 'C:\xamp\mysql\bin\mysql.exe' -u root --default-character-set=utf8mb4
```

## Database duoc tao

Ten database trong script:

```sql
QuanLyRapChieuPhimDB
```

Tren MariaDB/XAMPP, ten thu muc du lieu co the hien o dang chu thuong:

```text
quanlyrapchieuphimdb
```

## Du lieu mau mac dinh

Sau khi chay script, se co san:

- 3 phim
- 2 phong
- 7 ghe
- 3 suat chieu
- 3 khach hang
- 3 ve
- 3 thanh toan

## Truy van kiem tra nhanh

```sql
SELECT * FROM MOVIE;
SELECT * FROM ROOM;
SELECT * FROM SEAT;
SELECT * FROM SHOWTIME;
SELECT * FROM CUSTOMER;
SELECT * FROM TICKET;
SELECT * FROM PAYMENT;
```

Kiem tra lien ket ve va thanh toan:

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

## Rang buoc quan trong

- `SEAT`: `UNIQUE(MaPhong, HangGhe, SoGhe)`
- `TICKET`: `UNIQUE(MaSuat, MaGhe)`
- `PAYMENT`: `UNIQUE(MaVe)`
- Trigger kiem tra `MaGhe` phai thuoc dung `MaPhong` cua `MaSuat`

## Nguon thiet ke

Project nay bam vao luoc do cuoi cung sau chuan hoa BCNF trong:

- `CHUẨN HÓA.docx`

Neu chi lam phan **database cuoi**, day la file can bam vao de:

- chot bang nao duoc giu
- chot PK va FK
- chot rang buoc `UNIQUE`
- loai bo `PAYMENT_DETAIL`

## Ghi chu

- Project phu hop de nop bai thuc hanh TKCSDL
- Script MySQL co the chay tren XAMPP/MariaDB
- Neu lop hoc dung SQL Server thi su dung file `quan_ly_rap_chieu_phim_sqlserver.sql`
