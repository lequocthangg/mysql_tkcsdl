# Cai dat DTB len DBMS

## 1. Muc tieu

Phan nay trien khai truc tiep co so du lieu `QuanLyRapChieuPhimDB` len DBMS dua tren luoc do cuoi cung sau chuan hoa BCNF.  
Mo hinh cuoi duoc lay theo `CHUAN HOA.docx`, nghia la:

- Giu 7 bang: `MOVIE`, `ROOM`, `SEAT`, `SHOWTIME`, `CUSTOMER`, `TICKET`, `PAYMENT`
- Khong dung `PAYMENT_DETAIL`
- Bang `PAYMENT` lien ket 1-1 voi `TICKET` thong qua `MaVe` va rang buoc `UNIQUE(MaVe)`

## 2. File da tao

- `quan_ly_rap_chieu_phim_mysql.sql`
- `quan_ly_rap_chieu_phim_sqlserver.sql`

Hai file tren deu gom:

- Tao database
- Tao bang, khoa chinh, khoa ngoai, unique, check
- Tao index co ban
- Them trigger kiem tra ghe phai thuoc dung phong cua suat chieu
- Chen du lieu mau
- Chay truy van kiem tra so dong cua tung bang

## 3. Cach nop bai

Neu lop dung `MySQL`:

1. Mo `MySQL Workbench` hoac `phpMyAdmin`
2. Mo file `quan_ly_rap_chieu_phim_mysql.sql`
3. Run toan bo script
4. Ket qua thu duoc database `QuanLyRapChieuPhimDB`

Neu lop dung `SQL Server`:

1. Mo `SQL Server Management Studio`
2. Mo file `quan_ly_rap_chieu_phim_sqlserver.sql`
3. Execute toan bo script
4. Ket qua thu duoc database `QuanLyRapChieuPhimDB`

## 4. Ket qua mong doi sau khi cai dat

Sau khi chay script, DBMS se co 7 bang:

- `MOVIE`
- `ROOM`
- `SEAT`
- `SHOWTIME`
- `CUSTOMER`
- `TICKET`
- `PAYMENT`

Du lieu mau mac dinh:

- 3 phim
- 2 phong
- 7 ghe
- 3 suat chieu
- 3 khach hang
- 3 ve
- 3 thanh toan

## 5. Truy van kiem tra nhanh

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

## 6. Ghi chu de bao cao

Co the ghi ngan gon trong bai nop:

> Em da cai dat co so du lieu tren DBMS bang script SQL, tao day du bang, khoa chinh, khoa ngoai, rang buoc `UNIQUE`, `CHECK`, du lieu mau va truy van kiem tra. Mo hinh trien khai su dung luoc do cuoi cung sau chuan hoa BCNF, trong do bang `PAYMENT_DETAIL` duoc loai bo va quan he `TICKET - PAYMENT` duoc the hien bang `MaVe` duy nhat trong bang `PAYMENT`.
