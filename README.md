# Fujitsu Dotnet
Berikut adalah jawaban dari assestment untuk fujitsu dengan role .NET

## Number 1
Solusi untuk menanggulangi dan mengantisipasi dalam pengecekan barang yang kadaluarsa yaitu:
1. Menerapkan sistem FIFO (First In First Out) pada pergudangan

Dengan menerapkan sistem FIFO kita sudah melakukan penanggulangan untuk mencegah barang yang kadaluarsa dikarenakan tidak teratur nya barang yang keluar dari gudang.

2. Memaksimalkan Design dan Sistem dari Database

Pada awal design database kita bisa memberikan `column` untuk `expired_date` sehingga kita bisa mengetahui barang yang terindikasi kadaluarsa tanpa mengeceknya satu per satu. Kemudian kita bisa menerapkan sistem alert apabila ada barang yang terindikasi akan mengalami kadaluarsa dengan membuat trigger. 

3. Melakukan scanning manual

Melakukan scanning manual tetap diperlukan agar kita bisa mengetahui apakah ada barang yang terindikasi kadaluarsa sebelum waktunya. Hal seperti ini bisa saja terjadi karena terdapat human error yang terjadi di dalam gudang, dan menyebabkan kemasan rusak atau hal lain yang dapat menyebabkan barang kadaluarsa sebelum waktunya.

## Number 2
Berikut query yang diperlukan untuk permasalahan yang dihadapi:
1. Buat 2 Table (Table Gudang & Table Barang), berikan foreignKey dan Index

```sql
CREATE TABLE Warehouse (
    WarehouseId INT PRIMARY KEY,
    WarehouseName VARCHAR(100) NOT NULL
);

CREATE TABLE Goods (
    GoodsId INT PRIMARY KEY,
    GoodsName VARCHAR(100) NOT NULL,
    GoodsPrice DECIMAL(10, 2) NOT NULL,
    GoodsQty INT NOT NULL,
    ExpiredDate DATE NOT NULL,
    WarehouseId INT,
    FOREIGN KEY (WarehouseId) REFERENCES Warehouse(WarehouseId),
);

CREATE INDEX Idx_ExpiredDate ON Goods (ExpiredDate);
```

2. Buat Store Prosedure tampilkan list data Kode Gudang, Nama Gudang, Kode Barang, Nama Barang, Harga Barang, Jumlah Barang, Expired Barang mengunakan Dynamic Query dan Paging

```sql
CREATE PROCEDURE WarehouseView (
    @PageNumber INT,
    @PageSize INT
)
AS
BEGIN
    DECLARE @Offset INT;
    SET @Offset = (@PageNumber - 1) * @PageSize;

    DECLARE @SQL NVARCHAR(MAX);
    SET @SQL = '
    SELECT
        w.WarehouseId,
        w.WarehouseName,
        g.GoodsId,
        g.GoodsName,
        g.GoodsPrice,
        g.GoodsQty,
        g.ExpiredDate
    FROM
        Warehouse w
        JOIN Goods g ON g.WarehouseId = w.WarehouseId
    ORDER BY
        w.WarehouseId, g.GoodsId
    OFFSET ' + CAST(@Offset AS NVARCHAR(MAX)) + ' ROWS
    FETCH NEXT ' + CAST(@PageSize AS NVARCHAR(MAX)) + ' ROWS ONLY;';

    EXEC sp_executesql @SQL;
END;
```

untuk melakukan pengecekan apakah `procedure` berhasil disimpan oleh sql, kita bisa menjalankan syntax berikut

```sql
DECLARE @PageNumber INT = 1;
DECLARE @PageSize INT = 10;

EXEC WarehouseView @PageNumber, @PageSize;
```

3. Buat Trigger ketika Input Barang di salah satu gudang muncul kan barang yang kadaluarsa

```sql
CREATE TRIGGER ExpiredInputGoods
ON Goods
AFTER INSERT, UPDATE
AS
BEGIN
    DECLARE @CurrentDate DATE;
    SET @CurrentDate = GETDATE();

    IF EXISTS (SELECT 1 FROM inserted WHERE ExpiredDate < @CurrentDate)
    BEGIN
        PRINT 'Warning: Barang yang dimasukkan sudah kadaluarsa!';
	ROLLBACK TRANSACTION;
    END
END;
```

fungsi dari trigger diatas adalah untuk memberitahu penginput bahwa barang yang diinput sudah memasuki masa kadaluarsa, dan tidak akan bisa diinput karena dilakukan `rollback`. kita bisa melakukan demo berikut

```sql
INSERT INTO DB_WAREHOUSE.dbo.Warehouse (WarehouseId, WarehouseName)
VALUES (1, 'Gudang Utama');

INSERT INTO  DB_WAREHOUSE.dbo.Goods (GoodsId, GoodsName, GoodsPrice, GoodsQty, ExpiredDate, WarehouseId)
VALUES (1, 'Barang Kadaluarsa', 1000.00, 10, '2023-01-01', 1);
```