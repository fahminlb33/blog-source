---
title: "Mengatasi Lag DataGridView C#⌚"
date: "2018-05-15"
tags: [programming, tutorial, tips]
---

DataGridView merupakan kontrol yang sangat umum digunakan untuk menampilkan data dari _database_. Tetapi, saat menggunakan DataGridView dengan banyak _style_ dan data, aplikasi akan menjadi _lag_ atau _flickering_. Lag DataGridView ini bisa diatasi dengan beberapa cara, salah satunya adalah menggunakan **Double Buffering**.

Double Buffering merupakan teknik untuk membuat proses _render_ menjadi dua kali secara terpisah. _Render_ akan dilakukan menggunakan memori sebelum akhirnya pada layar\[2\]. Dengan demikian, tampilan aplikasi akan lebih _smooth_ karena proses _render_ telah selesai sebelumnya di memori sebelum ditampilkan pada layar.

## Mengatasi Lag DataGridView (*Flickering*)

Tutorial kali ini akan membahas cara mengaktifkan **Double Buffering** menggunakan cara **Reflection**.

Pada project yang sudah ada, buat file baru dengan nama **DoubleBufferExtension.cs** (C#). Kemudian salin rekat kode di bawah ini ke dalam file yang telah dibuat. Untuk versi VB.NET dari kode ini, Anda bisa melakukan konversi melalui berbagai konverter kode _online_ atau berikan komentar untuk mendapat _source code_ versi VB.NET.

Buka form dengan DataGridView yang akan digunakan, kemudian panggil kode berikut.

```csharp
using System.Diagnostics;
using System.Reflection;
using System.Windows.Forms;

namespace Kodesiana.Extension {
    public static class BindingExtension {
        public static void DoubleBuffered(this DataGridView dgv, bool state) {
            var type = dgv.GetType();
            var prop = type.GetProperty("DoubleBuffered", BindingFlags.Instance | BindingFlags.NonPublic);
            Trace.Assert(prop != null, "prop != null");
            prop.SetValue(dgv, state, null);
        }
    }
}
```

Sekarang Anda bisa melakukan _debugging_ dan melihat perbedaannya. Berikut adalah video perbedaan antara DataGridView dengan menggunakan Double Buffer dan tidak.  
  
Pada video di atas, dapat dilihat dengan menggunakan Double Buffer _scroll_ menggunakan tetikus lebih _smooth_ dan tidak _lag_.

## Tips Aplikasi Tidak Lag/Flickering

1. Jangan menampilkan banyak data dalam satu form, gunakan _pagination_ untuk membatasi jumlah data yang tampil pada form.
2. Kurangi jumlah kontrol pada satu form.
3. Aktifkan Double Buffering pada kontrol yang _lag/flickering_.

Anda dapat menggunakan _method_ _DoubleBuffered(bool state)_ pada SEMUA JENIS KONTROL pada Windows Forms. Ini berarti Anda dapat menghilangkan _lag__flickering_ pada semua kontrol pada form, bukan hanya DataGridView. Anda dapat menggunakan teknik Double Buffer ini pada **Form, Panel, GroupBox, ListView**, dan kontrol lain yang menyebabkan _lag/flickering_.

## Update! Library Paging dan Styling DataGridView

Penulis telah membuat sebuah library untuk memudahkan _paging_ dan _styling_ DataGridView. Pada library ini fitur **double buffering** sudah terintegrasi dan dapat langsung digunakan. Selain itu, terdapat banyak fitur lain seperti _styling_ DataGridView agar tampilannya lebih menarik.

Baca artikel lengkapnya pada artikel [Teknik Pagination dan Styling DataGridView C#/VB.NET](https://kodesiana.com/post/teknik-pagination-dan-styling-untuk-datagridview/).

Source code:  
[https://github.com/Kodesiana/Post-Samples/tree/master/DataGridEnhanced](https://github.com/Kodesiana/Post-Samples/tree/master/DataGridEnhanced)

## Referensi

1. Indonesia. 2016. How To Double buffer .NET controls on a form ([_https://stackoverflow.com/questions/76993/how-to-double-buffer-net-controls-on-a-form_](https://stackoverflow.com/questions/76993/how-to-double-buffer-net-controls-on-a-form)). Diakses 15 Mei 2018.
2. Microsoft. 2017. Double Buffered Graphics ([_https://docs.microsoft.com/en-us/dotnet/framework/winforms/advanced/double-buffered-graphics_](https://docs.microsoft.com/en-us/dotnet/framework/winforms/advanced/double-buffered-graphics)). Diakses 15 Mei 2018.
