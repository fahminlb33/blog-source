---
title: "Teknik Pagination dan Styling untuk DataGridView C#/VB.NET📑"
date: "2018-06-09"
tags: ["c#", "tutorial"]
---

**Pagination** dan **styling** merupakan dua hal yang umum dilakukan untuk meningkatkan _user experience_ pada aplikasi. Pagination digunakan untuk memisahkan beberapa baris data kedalam halaman (*page*), tujuannya untuk memudahkan user melihat data dengan hanya menampilkan beberapa data per halaman.

Styling digunakan untuk "memperindah" tampilan DataGridView kepada user, biasanya berupa kombinasi warna untuk meningkatkan _look-and-feel_ dari data yang ditampilkan. Trik utama untuk membuat _style_ adalah pemilihan warna. Bagaimana penggunaan warna dapat mengubah _look-and-feel_. Tanpa perlu menggunakan _library_ pihak ketiga seperti Telerik dan DevExpress, Anda dapat membuat tampilan DataGridView yang menarik dengan kombinasi warna yang tepat.

[![enhanced-dgv-demo-min](/posts/2018-06-09/42081976985_c793f5c1c1_o.png)](https://www.flickr.com/photos/158825251@N05/42081976985/in/dateposted-public/)

## Pagination

Untuk melakukan pagination, penulis menggunakan kueri SQL berikut (kueri merupakan hasil implementasi dari Aram Koukia\[1\]).

```sql
SELECT ROW_NUMBER() OVER(ORDER BY ProductId), * FROM [Product] ORDER BY [ProductId] OFFSET 10 ROWS FETCH NEXT 50 ROWS ONLY;
```

Kueri di atas dapat dirombak menjadi beberapa bagian, yaitu:

1. Menambah kolom baru untuk menampung nomor baris (`ROW_NUMBER()`).
2. Mengambil baris dari tabel Product dengan include semua kolom.
3. Urutan baris yang diambil diurutkan sesuai kolom ProductId.
4. Baris yang diambil dimulai dari baris nomor 11 (`OFFSET 10`).
5. Jumlah baris yang diambil adalah 50 baris.

Untuk maju/mundur halaman (*page*), Anda bisa mengubah `OFFSET` menjadi nomor baris yang akan diambil, sedangkan untuk mengubah jumlah baris yang ditampilkan dalam satu halaman, Anda dapat mengubah jumlah baris pada kueri `FETCH NEXT`.

## Styling

Untuk melakukan styling, DataGridView bawaan .NET Framework memiliki banyak kustomisasi yang dapat diubah. Penulis biasanya menggunakan tiga kustomisasi bawaan DataGridView, yaitu:

1. **DefautCellStyle**, properti ini akan menerapkan tema pada semua sel (secara umum).
2. **AlternatingCellStyle**, properti ini akan menerapkan tema pada semua sel ganjil.
3. **HeaderCellStyle**, properti ini akan menerapkan tema pada sel bagian _header_.

Selain penggunaan **CellStyle**, properti bawaan lain seperti `Font`, `BackColor` dan `ForeColor` memainkan peranan penting untuk mempercantik tampilan DataGridView. Untuk membuat tampilan DataGridView yang menarik, Anda dapat bereksperimen dengan kombinasi warna dengan melihat referensi palet warna seperti [Material Color System](https://material.io/design/color/the-color-system.html).

## Demo Aplikasi

Source code:  
[https://github.com/Kodesiana/Post-Samples/tree/master/DataGridEnhanced](https://github.com/Kodesiana/Post-Samples/tree/master/DataGridEnhanced)

Clone repositori source code, kemudian buka **KF.DataGridEnhanced.sln**. Buat database baru dan buat tabel baru beserta isinya. Pada contoh ini, penulis menggunakan tabel _Product_ dengan kolom sebagai berikut.

![Contoh Tabel untuk Pagination](/posts/2018-06-09/42265141214_37dcf83fae_o_d.png)

Contoh Tabel untuk Pagination

Setelah Anda memiliki project **KF.DataGridEnhanced** dan databasenya, Anda dapat mengubah beberapa _source code_ pada project demo **KF.DataGridEnhanced**.

```vb
Private _datagridEnhanced As EnhancedDataGridView

Sub New()
    ' This call is required by the designer.
    InitializeComponent()

    ' Buat instance baru untuk setiap services 
    _datagridEnhanced = New EnhancedDataGridView
    
    ' Set datagridview 
    _datagridEnhanced.SetDataGridView(dgv)

    ' Set connectionstring dan tabel 
    _datagridEnhanced.ConnectionString = "Data Source=localhost\\SQLEXPRESS;Initial Catalog=WiyataBhakti;Integrated Security=True"
    _datagridEnhanced.TableName = "Product" ' nama tabel
    _datagridEnhanced.OrderColumn = "ProductId" ' primary key atau kolom lain untuk ordering
    _datagridEnhanced.PageSize = 20 ' jumlah entri per halaman 
    _datagridEnhanced.ReloadData() ' reload data, hanya panggil 1 kali sebelum ambil data

    AddHandler _datagridEnhanced.PageSwitched, AddressOf Pagination_PageSwitched ' event handler halaman

    ' Set style
    cboTheme.SelectedIndex = 0

    ' tampilkan data pada halaman pertama 
    lblPageTotal.Text = "dari " & _datagridEnhanced.GetPageCount()
    _datagridEnhanced.GotoPage(1)
End Sub
```

## Referensi

1. Koukia, Aram. 2016. Let SQL Server Query Do the Pagination for You (_[https://koukia.ca/let-sql-server-query-do-the-pagination-for-you-offset-fetch-clause-in-sql-2012-a52ca6e5beb2](https://koukia.ca/let-sql-server-query-do-the-pagination-for-you-offset-fetch-clause-in-sql-2012-a52ca6e5beb2)_). Diakses 9 Juni 2018.
2. Microsoft. 2007. Tutorial 25: Efficiently Paging Through Large Amounts of Data ([_https://msdn.microsoft.com/en-us/library/bb445504.aspx_](https://msdn.microsoft.com/en-us/library/bb445504.aspx)). Diakses 9 Juni 2018.
3. Phan, Chesiung. 2014. Kosmetik DataGridView ([_https://www.facebook.com/groups/programervbnetindonesia/permalink/10152465359328621/_](https://www.facebook.com/groups/programervbnetindonesia/permalink/10152465359328621/)). Diakses 10 Juni 2018.
