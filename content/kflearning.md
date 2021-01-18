---
title: "KFlearning"
date: 2020-01-18
showLicense: false
showToc: false
---

KFlearning merupakan aplikasi e-learning terpadu untuk laboratorium komputer
Universitas Pakuan.

KFlearning v1.3.4 "Hardbass" dalam status **stabil.** Berikan kritik dan saran
melalui asisten praktikum Anda.

## KFlearning

KFlearning merupakan aplikasi e-learning yang digunakan untuk membantu kegiatan
praktikum di laboratorium komputer. Aplikasi ini bersifat open-source dan
dilisensikan oleh [Kodesiana.com](https://kodesiana.com) kepada LABKOM-MAROON di
bawah lisensi
[MIT](https://github.com/fahminlb33/KFlearning/blob/master/LICENSE.md). Anda
dapat melihat source code KFlearning pada repositori [GitHub
KFlearning](https://github.com/fahminlb33/KFlearning).

Aplikasi ini menggunakan **Visual Studio Code** sebagai code editor dan
bertujuan sebagai pengganti DEV C++, CodeBlocks, Sublime Text, Notepad++, dan
program editor sejenis. KFlearning sudah diintegrasikan dengan Visual Studio
Code agar dapat digunakan secara maksimal untuk melakukan segala jenis
pemrograman.

### Development Support

* Full Stack Web Development (PHP, HTML, CSS, JS)
* Desktop C++ MinGW-w64 Compiler (Console, GUI)
* Data Science (Python, Jupyter Notebook)

### Kebutuhan Sistem

* Windows 7 SP1 32-bit/64-bit.
* .NET Framework 4.5
* Disk space 500 MB (+Compiler)

### Unduh

File | Versi | Download
-----|-------|---------
KFlearning         | v1.3.4 | [Download](https://github.com/fahminlb33/KFlearning/releases/download/1.3.4/KFlearning.Setup.msi)
KFmaintenance      | v1.0.3 | [Download](https://github.com/fahminlb33/KFlearning/releases/download/1.3.4/KFmaintenance.Setup.msi)
KF-MinGW           | v1.0.1 | [Download](https://drive.google.com/open?id=1_0uEFDbsjZJY3rvjVOkYaxEqFJoQc0P3)
Visual Studio Code | Latest | [Download](https://code.visualstudio.com/docs/?dv=win)
Changelog          |        | [Download](https://github.com/fahminlb33/KFlearning/releases/tag/1.3.4)

## Cara Instalasi

Jika Anda pernah menggunakan KFlearning v1.0, v1.1, atau v1.2, Anda harus
mengikuti tutorial upgrade di bagian bawah di akhir halaman ini.

Instalasi KFlearning sedikit lebih rumit dibandingkan dengan aplikasi IDE lain,
karena KFlearning terdiri atas tiga komponen aplikasi yang saling terpisah. Hal
ini ditujukan untuk memudahkan maintenance versi aplikasi dan meningkatkan
efisiensi aplikasi.

1. Download KFlearning, KF-MinGW, dan Visual Studio Code dengan klik tombol di
   atas.
2. Install KF-MinGW, ikuti petunjuk instalasi aplikasi yang tertera.
3. Install KFlearning, ikuti petunjuk instalasi aplikasi yang tertera.
4. Install Visual Studio Code, ikuti petunjuk instalasi aplikasi yang tertera.
    Jika sudah punya Visual Studio Code, lewati langah ini.
5. Buka Visual Studio Code, kemudian klik tab Extensions.
6. Cari dan install ekstension berikut.
    * C++
    * Python
    * Bracket Pair Colorizer 2
    * Beautify
    * PHP Extension Pack
    * IntelliSense for CSS class names
7. Tutup Visual Studio Code setelah semua extension diinstall.
8. Instalasi selesai.

## Petunjuk Upgrade

### Upgrade dari v1.1 dan v1.2

1. Buka Control Panel.
2. Klik Program and Features.
3. Cari KFlearning, kemudian klik Uninstall.
4. Ikuti proses uninstall sesuai petunjuk.
5. Restart PC untuk menerapkan perubahan.
6. Lanjutkan proses instalasi dengan mengikuti petunjuk install di atas.

### Upgrade dari v1.0

1. Hapus folder berikut.
   * C:\MinGW
   * C:\kflearning
2. Lanjutkan proses instalasi dengan mengikuti petunjuk install di atas.