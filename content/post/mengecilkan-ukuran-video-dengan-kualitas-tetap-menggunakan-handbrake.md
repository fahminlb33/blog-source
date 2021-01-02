---
title: "Mengecilkan Ukuran Video dengan Kualitas Tetap menggunakan Handbrake🍹"
date: "2018-05-28"
tags: [multimedia, tutorial, tips-trik]
---

Mengecilkan ukuran video/film lazim dilakukan untuk menghemat ruang pada penyimpanan. Mengoleksi banyak video/film merupakan salah satu kebiasaan banyak orang, terlebih lagi dengan mudahnya akses ke berbagai macam konten video/film yang mudah. Selain itu, kapasitas penyimpanan seperti harddisk saat ini sudah sangat beragam dan beredar di pasaran dengan harga yang terjangkau, menjadikan koleksi video/film ini semakin mudah. Tetapi, ada kalanya jumlah video/film sudah sangat banyak dan perlu ruang untuk menyimpan video/film baru.

Salah satu cara untuk mengurangi ukuran video/film adalah dengan cara _reencoding_ menggunakan _encoder_ yang dapat melakukan kompresi _lossy_ untuk mengurangi ukuran video/film. Ada banyak _encoder_ yang dapat digunakan seperti H.264 (AVC), H.265 (HEVC), dan VP9. Konsekuensi dari penggunaan kompresi ini adalah kualitas video yang berkurang, tetapi dengan konfigurasi tertentu kualitas dapat dipertahankan dengan ukuran video/film yang lebih kecil.

## Mengecilkan Ukuran Video dengan Kodek H.265

Sebelum memulai mengecilkan ukuran video, saya ingin sedikit memaparkan mengapa saya menggunakan H.265 (HEVC). H.265 (HEVC) atau MPEG-H merupakan suksesor dari H.264 (AVC) yang dapat mengompresi data hingga 2 kali lipat dari H.264 (AVC) pada kualitas yang sama\[1\]. Pada Handbrake, kodek HEVC ini terdapat pada _preset_ dengan kontainer Matroska (MKV).

- HEVC memakan waktu _encode_ **lebih lama** daripada AVC.
- Sebagai alternatif, Anda dapat menggunakan VP9 jika ingin mempercepat proses _encode_.

### Cara menggunakan Handbrake

Unduh dan pasang Handbrake, Anda dapat mengunduh Handbrake dari [tautan ini](https://handbrake.fr). Setelah selesai dipasang, buka Handbrake dan input video/film yang ingin dikecilkan. Pilih _preset_ dari panel sebelah kanan.

Anda dapat memilih salah satu pilihan sesuai dengan kualitas awal video/film Anda. Pada gambar di atas penulis menggunakan video dengan resolusi 1280x720. Setelah preset dipilih, klik **Start Encode** untuk memulai proses _re-encoding_. Sampel video yang saya gunakan adalah video rekaman gawai Xiaomi Redmi A5 dengan resolusi 720p, 30 fps, bitrate 7000 mbps, ukuran **1,2 GB** MP4. Setelah di-_encode_, hasil video MKV berukuran **520 MB**. Tidak ada cacat yang nampak pada video hasil _encode_.

## Tips Membuat Ukuran Video Kecil

1. Unduh film dari laman yang menyediakan kompresi seperti YTS/YIFY. Film dari sumber ini tidak perlu dikompres ulang (sudah paling kecil).
2. Gunakan HEVC atau VP9. Banyak tutorial di internet yang menyarankan untuk menggunakan AVC, tetapi HEVC dan VP9 saat ini sudah jauh lebih bagus dan lebih kecil ukuran video-nya.

## Referensi

1. Wikipedia. 2018. High Efficiency Video Coding ([_https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding_](https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding)). Diakses 28 Mei 2018.
