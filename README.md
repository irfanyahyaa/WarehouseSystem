# Fujitsu Dotnet

## Number 1
Solusi untuk menanggulangi dan mengantisipasi dalam pengecekan barang yang kadaluarsa yaitu:
1. Menerapkan sistem FIFO (First In First Out) pada pergudangan
Dengan menerapkan sistem FIFO kita sudah melakukan penanggulangan untuk mencegah barang yang kadaluarsa dikarenakan tidak teratur nya barang yang keluar dari gudang.
2. Memaksimalkan Design dan Sistem dari Database
Pada awal design database kita bisa memberikan `column` untuk `expired_date` sehingga kita bisa mengetahui barang yang terindikasi kadaluarsa tanpa mengeceknya satu per satu. Kemudian kita bisa menerapkan sistem alert apabila ada barang yang terindikasi akan mengalami kadaluarsa dengan membuat trigger. 
3. Melakukan scanning manual
Melakukan scanning manual tetap diperlukan agar kita bisa mengetahui apakah ada barang yang terindikasi kadaluarsa sebelum waktunya. Hal seperti ini bisa saja terjadi karena terdapat human error yang terjadi di dalam gudang, dan menyebabkan kemasan rusak atau hal lain yang dapat menyebabkan barang kadaluarsa sebelum waktunya.