---
title: "Membuat Installer Aplikasi menggunakan WiX Toolset📦"
date: "2018-05-29"
tags: [programming, tutorial, tips-trik, windows]
---

Tutorial installer ini membahas cara membuat installer aplikasi menggunakan WiX Toolset. Membuat installer merupakan tahap akhir dari siklus pembuatan aplikasi. Setelah program aplikasi selesai dibuat dan diuji, tahap selanjutnya adalah membuat paket instalasi untuk didistribusikan kepada pengguna akhir/konsumen.

Source code:  
[https://github.com/Kodesiana/Post-Samples/tree/master/Wix-Installer-Example](https://github.com/Kodesiana/Post-Samples/tree/master/Wix-Installer-Example).

## Persiapan Membuat Installer

Pada artikel ini, penulis akan menggunakan project **Wiyata Bhakti**, aplikasi _point-of-sales_ buatan Kodesiana.com. Aplikasi POS ini menggunakan .NET Framework 4.5, SQL Server 2014 LocalDB sebagai basis data, dan Crystal Report untuk membuat laporan.

Sebelum memulai membuat installer, kita akan mempersiapkan beberapa hal, yaitu:

1. Visual Studio 2017 Community Edition, IDE untuk membuat WiX project.
2. WiX Toolset, untuk membuat installer.
3. Project untuk dibuat installer (Wiyata Bhakti).

Berdasarkan kebutuhan project Wiyata Bhakti, penulis ingin agar proses instalasi aplikasi Wiyata Bhakti dapat dilakukan dengan mudah bagi pengguna. Penulis ingin .NET Framework, SQL Server Local DB dan Crystal Report sudah dipaket dalam sebuah installer agar pengguna dapat memasang aplikasi Wiyata Bhakti dengan mudah.

Hasil akhir setup:

1. Install aplikasi utama yaitu Wiyata Bhakti.
2. Install .NET Framework 4.5 apabila belum diinstal.
3. Install SQL Server 2014 LocalDB apabila belum diinstal.
4. Install Crystal Report apabila belum diinstal.
5. Buat _shortcut_ pada _start menu_ dan _Desktop_.
6. Setup bisa digunakan pada Windows 32-bit dan 64-bit.

### WiX Toolset

WiX Toolset (kependekan dari Windows Installer XML Toolset) merupakan program untuk membuat installer Windows menggunakan XML. WiX merupakan program berbasis _command line_ tapi memiliki integrasi dengan Visual Studio untuk mempermudah proses pembuatan installer.

### Instalasi WiX Toolset v3

Buka halaman unduh WiX Toolset: [http://wixtoolset.org/releases](http://wixtoolset.org/releases/). Klik **Download v3.\*** kemudian pada halaman GitHub, klik **wix3\*\*.exe**. Pada contoh ini versi WiX yang digunakan adalah versi 3.11.1, jadi file yang diunduh adalah **wix311.exe**. Tutup Visual Studio apabila dibuka dan pasang WiX Toolset.

![Tampilan Installer WiX Toolset](/posts/2018-05-29/installer-wix.png)

Klik Install untuk memasang WiX Toolset. Setelah proses instalasi selesai, buka Visual Studio dan buat project baru. Pilih **Setup Project for WiX v3**. Setelah itu, project baru akan muncul dengan file default _Product.wxs_.

![Membuat Setup Project WiX](/posts/2018-05-29/project-baru-wix.png)

Pada tahap ini, Anda telah berhasil membuat sebuah project setup kosong.

### Pengenalan WiX Toolset

Sebelum melanjutkan pada artikel yang akan datang, Anda perlu mengetahui sedikit mengenai struktur WiX. WiX menggunakan ekstensi file _wxs_ dan secara otomatis akan membuat file _Product.wxs_ setelah membuat project baru.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <Product Id="*" Name="ContohSetup" Language="1033" Version="1.0.0.0" Manufacturer="" UpgradeCode="1600e328-9361-4403-850c-58c04d6bc3c4">
    <Package InstallerVersion="200" Compressed="yes" InstallScope="perMachine" />

    <MajorUpgrade DowngradeErrorMessage="A newer version of [ProductName] is already installed.">
    <MediaTemplate />

    <Feature Id="ProductFeature" Title="ContohSetup" Level="1" />
      <ComponentGroupRef Id="ProductComponents">
    </Feature>
  </Product>

  <Fragment>
    <Directory Id="TARGETDIR" Name="SourceDir">
      <Directory Id="ProgramFilesFolder">
        <Directory Id="INSTALLFOLDER" Name="ContohSetup" /></Directory>
      </Directory>
    </Fragment>

  <Fragment>
    <ComponentGroup Id="ProductComponents" Directory="INSTALLFOLDER">
    <!-- TODO: Remove the comments around this Component element and the ComponentRef below in order to add resources to this installer. -->
    <!-- <Component Id="ProductComponent" /> -->
    <!-- TODO: Insert files, registry keys, and other resources here. -->
    <!-- </Component> -->
    </ComponentGroup>
  </Fragment>
</Wix>
```

Selain itu, WiX juga menggunakan elemen `<Fragment>` untuk memisahkan beberapa bagian installer kedalam beberapa file. Saat membuat file _wxs_ baru, secara otomatis WiX akan membuat file dengan isi sebagai berikut.

Isi file wxs kosong:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Wix xmlns="http://wixtoolset.org/schemas/v4/wxs">
  <Fragment>
  </Fragment>
</Wix>
```

## Membuat Installer Aplikasi

Sebelum membuat installer aplikasi, pertama kita harus menentukan file apa yang akan diinstal dan bagaimana struktur instalasinya. Pada contoh ini, penulis membuat folder **E:\\installer** kemudian menyalin semua file **.exe, .dll, .xml**, dan lainnya ke folder tersebut.

![Folder Aplikasi Wiyata Bhakti](/posts/2018-05-29/dir-file-aplikasi.png)

Folder di atas berisi file yang akan diinstal. Satu file _WiyataBhakti.App.exe_ yang merupakan program utama, file _WiyataBhakti.mdf_ dan _WiyataBhakti.ldf_ merupakan basis data SQL Server, dan file lain seperti DLL dan XML yang dibutuhkan oleh aplikasi Wiyata Bhakti.

### Membuat Include File untuk Path

**Include File** digunakan untuk menampung variabel yang dapat digunakan pada keseluruhan setup project. Misalkan penggunaan variabel untuk menyimpan _path_/lokasi file untuk digunakan pada setup. Daripada menulis path secara lengkap (misalnya D:\\installer\\test.exe) kita dapat menggunakan variabel untuk menggantikan path (misalnya _$(var.Source)\\test.exe_).

Buka project setup pada Visual Studio dan buat item **Installer file**. Klik kanan pada project di Solution Explorer kemudian klik **Add new item...** dan pilih **Installer File**. Beri nama file _RelativePath.wxs_ kemudian salin rekat kode di bawah ini.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Include>
  <?define SourceDir="E:\\installer" ?>
</Include>
```

Penjelasan:

1. Baris 3, membuat variabel _$(var.SourceDir)_ dengan path _E:\\installer_.

### Menambahkan Struktur Direktori ke Installer

Buat **Installer File** bari dengan nama file _Directories.wxs_. Ubah isi file tersebut seperti berikut.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <Fragment>
    <Directory Id="TARGETDIR" Name="SourceDir">
      <!-- AppDir -->
      <Directory Id="ProgramFilesFolder">
        <Directory Id="INSTALLFOLDER" Name="Wiyata Bhakti" />
      </Directory>

      <!-- Database Folder -->
      <Directory Id="AppDataFolder">
        <Directory Id="DATAFOLDER" Name="Wiyata Bhakti" />
      </Directory>

      <!-- Shortut start menu -->
      <Directory Id="ProgramMenuFolder">
        <Directory Id="ApplicationProgramsFolder" Name="Wiyata Bhakti" />
      </Directory>

      <!-- Shortut start menu -->
      <Directory Id="DesktopFolder" />
    </Directory>
  </Fragment>
</Wix>
```

Penjelasan:

1. Baris 6-8, membuat folder **Wiyata Bhakti** pada target komputer di lokasi _Program Files_. Ini target lokasi instalasi.
2. Baris 11-13, membuat folder **Wiyata Bhakti** pada target komputer di lokasi _%USERPROFILE%\\AppData\\Roaming_. Folder ini akan bersisi file basis data (MDF dan LDF). Folder _AppData_ digunakan karena folder _AppData_ dapat read/write, sedangkan folder _ProgramFiles_ bersifat _read-only._
3. Baris 16-18, membuat folder **Wiyata Bhakti** pada _start menu_.
4. Baris 21, referensi ke folder _Desktop._

### Menambahkan File ke Installer

WiX menggunakan elemen `<File>` untuk menambahkan file. Setiap elemen `<File>` harus berada dalam elemen `<Component>` yang menandakan bahwa elemen `<File>` tersebut merupakan bagian yang akan diinstal. Contoh:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
    <Fragment>
        <DirectoryRef Id="TARGETDIR">
            <Component Id="contoh.exe" Guid="*">
                <File Id="contoh.exe" KeyPath="yes" Source="$(var.SourceDir)\contoh.exe" />
            </Component>
        </DirectoryRef>
    </Fragment>
</Wix>
```

Penjelasan:

1. Baris 4, menunjukkan bahwa semua komponen yang ada pada induk direktori ini terdapat pada direktori _INSTALLFOLDER._
2. Baris 5, membuat sebuah komponen baru dengan Id _contoh.exe_ dan GUID otomatis.
3. Baris 6, mebuat elemen file dengan Id _contoh.exe_ dengan lokasi "$(var.SourceDir)\\contoh.exe".

Tentunya proses ini dilakukan hingga semua file yang perlu diinstal dientri pada project. Proses ini akan memakan waktu yang lama jika dilakukan secara manual. Untuk mempermudah mengentri file, kita dapat menggunakan tool **heat.exe** (Harvest Tool). **Heat** digunakan untuk meng-_generate_ file yang akan diinstal dalam satu folder menjadi file _wxs._

Cara menggunakan **heat**:

1. Buka _Command Prompt_.
2. Ubah _working directory_ ke folder **E:\\installer** dengan cara ketik "_cd E:\\installer_" kemudian tekan Enter.
3. Ketik "_heat dir "." -ag -sfrag -sreg -scom -suid -var var.SourceDir -template fragment -out target.wxs_" kemudian tekan Enter.
4. Buka folder **E:\\installer** kemudian buka file **target.wxs**.

![Penggunaan Harvest Tool](/posts/2018-05-29/wix-harvest-tool.png)

Anda mungkin perlu mengonfigurasi _environment variable_ `PATH` untuk dapat menggunakan **heat**. Contoh file **target.wxs** hasil dari tool heat:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
    <Fragment>
        <DirectoryRef Id="TARGETDIR">
            <Directory Id="installer" Name="installer">
                <Component Id="C__Kiosk.txt" Guid="*">
                    <File Id="C__Kiosk.txt" KeyPath="yes" Source="$(var.SourceDir)\C# Kiosk.txt" />
                </Component>
                .
                .
            </Directory>
        </DirectoryRef>
    </Fragment>
</Wix>
```

Untuk menggunakan file hasil **heat** ini, buat file baru dengan nama _Files.wxs._ Ubah isi _Files.wxs_ seperti berikut:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://wixtoolset.org/schemas/v4/wxs">
    <Fragment>
        <!-- Binaries -->
        <ComponentGroup Id="AppFiles" Directory="INSTALLFOLDER">
            <Component Id="WiyataBhakti.App.exe" Guid="*">
                <File Id="WiyataBhakti.App.exe" KeyPath="yes" Source="$(var.SourceDir)\WiyataBhakti.App.exe" />
            </Component>
            .
            .
        </ComponentGroup>

        <!-- Database -->
        <ComponentGroup Id="DatabaseFiles" Directory="DATAFOLDER">
            <Component Id="WiyataBhakti.mdf" Guid="{9D888C4D-8063-41A5-8BE2-7E66574F870C}">
                <File Id="WiyataBhakti.mdf" Source="$(var.SourceDir)\WiyataBhakti.mdf" />
                <RemoveFile Id="RemoveDb" Name="WiyataBhakti.mdf" On="uninstall"/>
                <RegistryValue Key="Software\Wiyata Bhakti" Root="HKCU" Type="integer" Name="db_main" Value="1" KeyPath="yes" />
            </Component>
            .
            .
        </ComponentGroup>
    </Fragment>
</Wix>
```

Penjelasan:

1. Baris 5, menandakan kumpulan komponen dengan induk direktori pada _INSTALLFOLDER._
2. Baris 6, menandakan komponen baru dengan Id _WiyataBhakti.App.exe_ dan GUID otomatis.
3. Baris 7, menandakan file dengan Id _WiyataBhakti.App.exe_ dan lokasi "_$(var.SourceDir)\\WiyataBhakti.App.exe_".
4. Baris 13, menandakan kumpulan komponen dengan induk direktori pada _DATAFOLDER._
5. Baris 14, menandakan komponen baru dengan Id _WiyataBhakti.mdf_ dan GUID otomatis.
6. Baris 15, menandakan file dengan Id _WiyataBhakti.mdf_ dan lokasi
7. "_$(var.SourceDir)\\WiyataBhakti.mdf_".
8. Baris 17, menandakan installer untuk menghapus file _WiyataBhakti.mdf_ saat aplikasi di uninstall.
9. Baris 18, membuat registry untuk menandakan bahwa file ini diinstal pada komputer.

Baris 5-7 terus berulang hingga semua file dientri. Khusus beberapa file untuk basis data, penggunaan `<ComponentGroup>` agak sedikit berbeda dengan menggunakan direktori lain dan tambahan registry. Harap lihat proyek ini pada GitHub.

### Membuat Shortcut Aplikasi

Buat **Installer File** baru dengan nama _Shortcut.wxs_. Salin rekat kode di bawah ini.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <Fragment>
    <!-- Start Menu Shortcut -->
    <ComponentGroup Id="AppShortcuts">
      <Component Id="POSShortcut" Guid="*" Directory="ApplicationProgramsFolder">
        <!-- POS -->
        <Shortcut Id="POSStartMenuShortcut" Name="Wiyata Bhakti POS" Description="Aplikasi POS Wiyata Bhakti" Target="[#WiyataBhakti.App.exe]" WorkingDirectory="INSTALLFOLDER" />

        <!-- Uninstall -->
        <Shortcut Id="UninstallProduct" Name="Uninstall Wiyata Bhakti" Description="Uninstalls Wiyata Bhakti" Target="[SystemFolder]msiexec.exe" Arguments="/x [ProductCode]" />

        <RemoveFolder Id="CleanUpShortcut" Directory="ApplicationProgramsFolder" On="uninstall" />
        <RegistryValue Root="HKCU" Key="Software\Wiyata Bhakti" Name="installed_startmenu" Type="integer" Value="1" KeyPath="yes" />
      </Component>

      <!-- Desktop Shortcut -->
      <Component Id="DesktopShortcut" Guid="*" Directory="DesktopFolder">
        <!-- POS -->
        <Shortcut Id="POSDesktopShortcut" Name="Wiyata Bhakti POS" Description="Aplikasi POS Wiyata Bhakti" Target="[#WiyataBhakti.App.exe]" WorkingDirectory="INSTALLFOLDER" />

        <RemoveFile Id="RemovePosPOSDesktopShortcut" Name="Wiyata Bhakti POS.lnk" On="uninstall"/>
        <RegistryValue Root="HKCU" Key="Software\Wiyata Bhakti" Name="installed_dekstop" Type="integer" Value="1" KeyPath="yes" />
      </Component>
    </ComponentGroup>
  </Fragment>
</Wix>
```

Penjelasan:

1. Baris 6, membuat komponen _shortcut_ pada direktori _ApplicationProgramsFolder_ atau _start menu_.
2. Baris 8 , membuat _shortcut_ ke file _WiyataBhakti.App.exe_.
3. Baris 11, membuat _shortcut_ untuk uninstall aplikasi.
4. Baris 13, membuat perintah untuk menghapus folder _shortcut_ aplikasi saat uninstall.
5. Baris 14, membuat _value_ pada registry untuk menandakan bahwa _shortcut_ diinstal.

Baris selanjutnya memiliki fungsi yang sama tetapi memiliki lokasi direktori yang berbeda.

### Membuat Definisi Produk Aplikasi

Setelah Anda menambahkan struktur direktori, file, dan shortcut, tahap akhir adalah membuat definisi produk aplikasi Anda agar dapat di install pada komputer. Buka file _Product.wxs_ kemudian salin rekat kode di bawah ini.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <?include "RelativePath.wxi" ?>

  <Product Id="*" Name="Wiyata Bhakti" Language="1033" Version="1.0.0.0" Manufacturer="Fahmi Noor Fiqri" UpgradeCode="{B3559FC8-4392-4CC4-A74D-E39A19B2E827}">
    <!-- Package -->
    <Package InstallerVersion="200" Compressed="yes" InstallScope="perMachine" />
    <Media Id="1" Cabinet="app.cab" EmbedCab="yes" />

    <!-- Install Conditions -->
    <MajorUpgrade DowngradeErrorMessage="A newer version of [ProductName] is already installed." />

    <!-- Features to install -->
    <Feature Id="ProductFeature" Title="Wiyata Bhakti Installer" Level="1">
      <ComponentGroupRef Id="AppFiles" />
      <ComponentGroupRef Id="DatabaseFiles" />
      <ComponentGroupRef Id="AppShortcuts" />
    </Feature>
  </Product>
</Wix>
```

Penjelasan:

1. Baris 5, definisi produk aplikasi. Anda dapat mengubah atribut _Name, Version, Manufacturer,_ dan _UpgradeCode_ dengan nilai selain yang dicontohkan. Khusus untuk atribut _UpgradeCode_, Anda tidak boleh menggunakan GUID yang sama dengan contoh ini. Harap ganti dengan GUID baru.
2. Baris 7-8, membuat _package_ CAB baru untuk menampung file instalasi dengan _scope_ _perMachine_.
3. Baris 11, membuat kondisi bahwa aplikasi tidak boleh ditimpa dengan versi yang lama.
4. Baris 14, membuat fitur utama aplikasi. Fitur ini adalah produk yang akan diinstal.
5. Baris 15-17, mereferensikan komponen direktori, file, dan _shortcut_ yang telah dibuat agar diinstall pada komputer.

### Build Setup

Sampai tahap ini, Anda sudah bisa membuat file setup MSI yang dapat diinstall. Klik **Build > Build ContohSetup** untuk membuat setup installer. Hasil akhir file MSI dapat Anda buka pada folder project Anda, biasanya terdapat pada folder **bin**.

![Build Project ContohSetup](/posts/2018-05-29/build-project1.png)

Build Project ContohSetup

## Kustomisasi UI Installer

Pada tutorial kedua, Anda telah berhasil membuat setup yang dapat menginstall aplikasi ke komputer, tetapi masih belum dapat melakukan pengecekan apakah program tertentu seperti .NET Framework, SQL Server, dan Crystal Report telah terpasang atau belum. Selain itu, Anda mungkin ingin menampilkan lisensi aplikasi, menambahkan _checkbox_ untuk memulai aplikasi setelah aplikasi berhasil di install, atau mengubah ikon aplikasi pada _Control Panel_.

### Menambahkan Perjanjian Lisensi pada Installer

Untuk menambahkan tampilan persetujuan lisensi pada installer, Anda harus membuat file teks lisensi dalam format RTF. Penulis menyarankan untuk menggunakan aplikasi bawaan Windows WordPad untuk membuat dokumen RTF. Contoh dokumen lisensi menggunakan WordPad:

![Edit Lisensi Menggunakan WordPad](/posts/2018-05-29/edit-lisensi-wordpad.png)

Buka file _Product.wxs_ kemudian tambahkan kode berikut.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <?include "RelativePath.wxi" ?>
  <Product Id="*" Name="Wiyata Bhakti" Language="1033" Version="1.0.0.0" Manufacturer="Fahmi Noor Fiqri" UpgradeCode="{B3559FC8-4392-4CC4-A74D-E39A19B2E827}">
    <!-- Package -->
    <Package InstallerVersion="200" Compressed="yes" InstallScope="perMachine" />
    <Media Id="1" Cabinet="app.cab" EmbedCab="yes" />

    <!-- Properties -->
    <WixVariable Id="WixUILicenseRtf" Value="$(var.SourceDir)\license.rtf" />

    <!-- Install Conditions -->
    <MajorUpgrade DowngradeErrorMessage="A newer version of [ProductName] is already installed." />

    <!-- Features to install -->
    <Feature Id="ProductFeature" Title="Wiyata Bhakti Installer" Level="1">
      <ComponentGroupRef Id="AppFiles" />
      <ComponentGroupRef Id="DatabaseFiles" />
      <ComponentGroupRef Id="AppShortcuts" />
    </Feature>

    <!-- UI -->
    <UI>
      <UIRef Id="WixUI_Minimal" />
    </UI>
  </Product>
</Wix>
```

Penjelasan:

1. Baris 10, menandakan lokasi file _license.rtf_ yang akan digunakan.
2. Baris 23-25, menandakan WiX untuk menggunakan dialog _Minimal_.

Untuk menggunakan kode di atas, tambahkan referensi ke **WixUIExtension.dll** dengan cara klik kanan pada project kemudian klik **Add > Reference...**. Pada tab _Browse_, pindah ke folder _C:\\Program Files (x86)\\WiX Toolset v3.11\\bin_. Pilih **WixUIExtension.dll** kemudian klik _Add_ dan klik _OK._

![Add Reference ke Project WiX](/posts/2018-05-29/add-reference-wix.png)

### Mengubah Ikon Program pada Control Panel

Untuk menambahkan ikon pada Control Panel, caranya mirip dengan cara untuk menambahkan lisensi pada setup. Buka file _Product.wxs_ kemudian tambahkan kode berikut.

```xml
<Icon Id="icon.ico" SourceFile="$(var.SourceDir)\WiyataBhakti-icon.ico" />
<Property Id="ARPPRODUCTICON" Value="icon.ico" />
```

Penjelasan:

1. Baris 1, menandakan lokasi ikon yang akan digunakan dengan Id _icon.ico_ yang berlokasi pada _SourceDir\\WiyataBhakti-icon.ico_.
2. Baris 2, mengubah properti _APPRODUCTION_ menjadi _icon.ico_.

### CheckBox untuk Membuka Program Setelah Install

Untuk menambahkan checkbox untuk membuka aplikasi setelah instalasi, buka file _Product.wxs_ kemudian tambahkan kode berikut.

```xml
<Property Id="WIXUI_EXITDIALOGOPTIONALCHECKBOXTEXT" Value="Buka Wiyata Bhakti" />
<Property Id="WixShellExecTarget" Value="[#WiyataBhakti.App.exe]" />

<!-- UI -->
<UI>
  <UIRef Id="WixUI_Minimal" />
  <Publish Dialog="ExitDialog" Control="Finish" Event="DoAction" Value="LaunchApplication">
    WIXUI_EXITDIALOGOPTIONALCHECKBOX = 1 AND NOT Installed
  </Publish>
</UI>

<!-- Custom Actions -->
<CustomAction Id="LaunchApplication" BinaryKey="WixCA" DllEntry="WixShellExec" Impersonate="yes" />
```

Penjelasan:

1. Baris 1, mengubah teks pada checkbox untuk membuka aplikasi setelah install.
2. Baris 2, menandakan target program yang akan dibuka setelah instalasi, pada contoh ini file _WiyataBhakti.App.exe_.
3. Baris 6-8, menambahkan checkbox melalui _predefined template_ dari _WixUI_Minimal_.
4. Baris 13, menandakan _custom action_ untuk membuka target aplikasi melalui Wix extension.

## Kustomisasi Install Conditions

**Install Conditions** merupakan definisi beberapa kondisi yang harus dipenuhi sebelum aplikasi dapat di install pada komputer. Misalkan program Anda membutuhkan .NET Framework dengan versi tertentu untuk dapat digunakan, maka installer akan mengecek apakah versi .NET Framework tersebut sudah ada pada komputer atau belum. Apabila .NET Framework dengan versi tersebut belum dipasang, maka instalasi dibatalkan.

Untuk melakukan pengecekan, penulis biasanya menggunakan elemen `<RegistrySearch>` untuk menentukan apakah suatu program sudah terpasang atau belum. Dengan menggunakan gabungan `<Property>` dan `<RegistrySearch>` kita dapat menentukan apakah suatu program sudah di install atau belum sebelum melanjutkan proses instalasi.

### Mengecek .NET Framework 4.5

Untuk mengecek versi .NET Framework, kita dapat mengunakan ekstensi **WixNetFxExtension.dll**. Dengan menggunakan ekstensi ini kita tidak perlu membuat `<RegistrySearch>` untuk menentukan apakah .NET Framework sudah terpasang atau belum. Kita hanya perlu mereferensikan properti dari **WixNetFxExtension.dll**.

Tambahkan referensi **WixNetFxExtension.dll** kemudian, bah elemen `<Wix>` pada file _Product.wxs_ seperti contoh di bawah ini.

```xml
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi" xmlns:netfx="http://schemas.microsoft.com/wix/NetFxExtension">
```

Penjelasan: menambahkan _namespace prefix_ ke extension WixNetFxExtension.dll. Setelah Anda mengubah elemen `<Wix>`, tambahkan kode di bawah ini.

```xml
<!-- Properties -->
<PropertyRef Id="NETFRAMEWORK45" />

<!-- Install Conditions -->
<Condition Message="This application requires .NET Framework 4.5. Please install the .NET Framework then run this installer again.">
  <![CDATA[Installed OR NETFRAMEWORK45]]>
</Condition>
```

Penjelasan:

1. Baris 2, membuat referensi ke properti _NETFRAMEWORK45_ dari library _WixNetFxExtension.dll_.
2. Baris 5-7, membuat kondisi untuk mengecek .NET Framework berdasarkan properti _NETFRAMEWORK45._

### Mengecek SQL Server LocalDB 2014

Sama halnya dengan cara untuk mengecek .NET Framework, untuk mengecek program lain sudah terpasang atau belum pada komputer dapat dilakukan menggunakan elemen `<Property>` dan `<RegistrySearch>`. Bedanya adalah untuk program selain yang didukung oleh WiX, kita harus membuat sendiri sistem pendeteksianya.

Buat **Installer File** baru dengan nama _Registry.wxs_. Ubah isi file tersebut dengan kode di bawah ini.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://wixtoolset.org/schemas/v4/wxs">
  <Fragment>
    <!-- Prerequiretes Search -->
    <Property Id="MSSQLSERVERLOCALDB32">
      <RegistrySearch Id="MSSQLSERVERLOCALDB32" Key="SOFTWARE\Microsoft\Microsoft SQL Server 2014 Redist\SqlLocalDB\CurrentVersion" Name="Version" Root="HKLM" Type="raw" Win64="no" />
    </Property>
    <Property Id="MSSQLSERVERLOCALDB64">
      <RegistrySearch Id="MSSQLSERVERLOCALDB64" Key="SOFTWARE\Microsoft\Microsoft SQL Server 2014 Redist\SqlLocalDB\CurrentVersion" Name="Version" Root="HKLM" Type="raw" Win64="yes" />
    </Property>
  </Fragment>
</Wix>
```

Penjelasan:

1. Baris 5 dan 8, membuat properti untuk menampung _value_ hasil pencarian registry.
2. Baris 6, mencari registry pada Key dan Root yang sudah ditentukan dengan versi registry 32-bit.
3. Baris 9, mencari registry pada Key dan Root yang sudah ditentukan dengan versi registry 64-bit. Dua versi pencarian registry ini dilakukan karena MSI yang dibuat merupakan 32-bit dan Windows memiliki versi 32-bit dan 64-bit. Untuk menghindari [_Registry Virtualization_](https://msdn.microsoft.com/en-us/library/windows/desktop/ms724072%28v=vs.85%29.aspx), maka menggunakan dua definisi pencarian harus dilakukan.

Buka file _Product.wxs_ kemudian tambahkan kode berikut.

```xml
<Condition Message="This application requires SQL Server 2014 LocalDB. Please install the SQL Server 2014 LocalDB then run this installer again.">
   <![CDATA[Installed OR MSSQLSERVERLOCALDB32 OR MSSQLSERVERLOCALDB64]]>
</Condition>
```

Penjelasan: Membuat kondisi untuk mengecek SQL Server LocalDB 2014 berdasarkan properti _MSSQLSERVER32_ dan _MSSQLSERVER64_.

### Mengecek SAP Crystal Report Runtime

Cara mengecek Crystal Report juga sama dengan cara untuk mengecek SQL Server LocalDB 2014. Perbedaannya adalah _path_ registry yang dicari. Buka kembali file _Registry.wxs_, kemudian tambahkan kode berikut.

```xml
<Property Id="SAPBUSINESSOBJECT32">
    <RegistrySearch Id="SAPBUSINESSOBJECT32" Key="SOFTWARE\SAP BusinessObjects\Crystal Reports for .NET Framework 4.0\Shared" Name="BINDIR" Root="HKLM" Type="raw" Win64="no" />
</Property>
<Property Id="SAPBUSINESSOBJECT64">
    <RegistrySearch Id="SAPBUSINESSOBJECT64" Key="SOFTWARE\SAP BusinessObjects\Crystal Reports for .NET Framework 4.0\Shared" Name="BINDIR" Root="HKLM" Type="raw" Win64="yes" />
</Property>
```

Penjelasan:

1. Baris 1 dan 4, membuat property untuk menampung _value_ hasil pencarian registry.
2. Baris 2, mencari registry pada Key dan Root yang sudah ditentukan dengan versi registry 32-bit.
3. Baris 5, mencari registry pada Key dan Root yang sudah ditentukan dengan versi registry 64-bit.

Buka file _Product.wxs_ kemudian tambahkan kode berikut.

```xml
<Condition Message="This application requires SAP Crystal Report Shared Runtime. Please install the SAP Crystal Report Shared Runtime then run this installer again.">
   <![CDATA[Installed OR SAPBUSINESSOBJECT32 OR SAPBUSINESSOBJECT64]]>
</Condition>
```

Penjelasan: Membuat kondisi untuk mengecek SAP Crystal Report Runtime berdasarkan properti _SAPBUSINESSOBJECT32_ dan _SAPBUSINESSOBJECT64_.

## Membuat Bootstrapper Installer

Pada artikel sebelumnya sudah di bahas bagaimana cara membuat installer MSI yang dapat menginstall aplikasi dan melakukan pengecekan terhadap beberapa program lain. Pada artikel Kustomisasi dan Install Conditions, telah dibuat kondisi pengcekan untuk .NET Framework, SQL Server LocalDB 2014, dan Crystal Report tetapi tidak dibahas bagaimana cara memasang program tersebut apabila belum dipasang.

Paket installer MSI memang tidak dapat menginstall program lain, maka dari itu dibutuhkan tipe installer lain yang dapat memasang program lain dalam satu paket instalasi. _Bootstrapper_ merupakan jenis installer yang dapat “menggabungkan” beberapa installer lain agar dapat dijalankan secara berurutan (_chain_).

### Membuat Project Bootstrapper

Klik kanan pada _solution_ **ContohSetup**, kemudian klik **Add > New Project...** dan pilih **Bootstrapper Project for  WiX v3**. Beri nama project _bootstrapper_ dengan nama **ContohBootstrapper**. Saat project selesai dibuat, secara otomatis file _Bundle.wxs_ akan dibuat. Berikut adalah isi file tersebut.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi">
  <Bundle Name="ContohBootstrapper" Version="1.0.0.0" Manufacturer="" UpgradeCode="e26ad856-c2d5-44c6-b9bf-38e6ea66eeb0">
    <BootstrapperApplicationRef Id="WixStandardBootstrapperApplication.RtfLicense" />

    <Chain>
      <!-- TODO: Define the list of chained packages. -->
      <!-- <MsiPackage SourceFile="path\to\your.msi" /> -->
     </Chain>
  </Bundle>
</Wix>
```

Sebelum memulai membuat _bootstrapper_, tambahkan referensi ke **WixUtilExtension.dll** dan **WixNetFxExtension.dll** untuk melakukan _registry search_. Sama seperti setup project sebelumnya, kita perlu menggunakan variabel agar _path_ installer yang akan digunakan lebih mudah digunakan. Buat **Installer File** dengan nama _RelativePath.wxs_ kemudian salin rekat kode di bawah ini.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Include>
  <?define NetFx451Release = 461308 ?>
  <?define SourceDir="E:\\installer_jadi" ?>
</Include>
```

Penjelasan:

1. Baris 3, deklarasi variabel _NetFx451Release_ dengan nilai 461308.
2. Baris 4, deklarasi variabel _SourceDir_ dengan nilai _E:\\installer_jadi_.

Untuk _bootstrapper_ ini, letakkan semua setup yang akan digabung dalam 1 folder, pada contoh ini penulis menggunakan folder _E:\\installer_jadi_.

![Folder Setup Bootstrapper](/posts/2018-05-29/msi-bootstrapper.png)

Pada gambar di atas dapat terlihat setup instalasi untuk .NET Framework 4.5.1 (NDP451-xxx.exe), SQL Server LocalDB 2014 (SqlLocalDB-2014-xx.msi), Crystal Report Runtime (CRRuntime\_xx.msi), dan Visual C++ 2015 Redistributable (vcredist-2015\_xx.exe). Keempat program tersebut akan di install menggunakan _bootstrapper_.

### Menambahkan Logo dan Lisensi pada Bootstrapper

Sama halnya dengan installer MSI, pada _bootstrapper_ juga dapat menampilkan lisensi dan logo. Untuk menambahkan lisensi dan logo, tambahkan kode di bawah ini.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi" xmlns:bal="http://schemas.microsoft.com/wix/BalExtension">
  <?include "RelativePath.wxi" ?>

  <Bundle Name="Wiyata Bhakti POS" Version="1.0.0.0" Manufacturer="Kodesiana.com" UpgradeCode="35e2cf60-5f88-4ce3-a6e9-0d4e09d5a449">
    <!-- Bootstrapper -->
    <BootstrapperApplicationRef Id="WixStandardBootstrapperApplication.RtfLicense">
      <bal:WixStandardBootstrapperApplication LicenseFile="$(var.SourceDir)\license.rtf" LogoFile="$(var.SourceDir)\logo.png" ShowVersion="yes" />
    </BootstrapperApplicationRef>

    <!-- Packages -->
    <Chain>
       <!-- TODO: Define the list of chained packages. -->
       <!-- <MsiPackage SourceFile="path\to\your.msi" /> -->
    </Chain>
  </Bundle>
</Wix>
```

Penjelasan:

1. Baris 4, menambahkan referensi ke _wxs_.
2. Baris 6, definisi _bootstrapper_, ubah sesuai kebutuhan.
3. Baris 9, definisi _bootstrapper_ dengan tampilan lisensi.
4. Baris 11, menentukan lokasi file lisensi (RTF).
5. Baris 12, menentukan lokasi file logo (PNG/BMP, 32x32).

### Menambahkan .NET Framework 4.5

Tambahkan **Installer File** baru dengan nama _Packages.wxs_ kemudian tambahkan kode di bawah ini.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Wix xmlns="http://schemas.microsoft.com/wix/2006/wi" xmlns:util="http://schemas.microsoft.com/wix/UtilExtension">
  <?include "RelativePath.wxi" ?>

  <Fragment>
    <util:RegistrySearchRef Id="NETFRAMEWORK45"/>

    <!-- NET Framework -->
    <PackageGroup Id="NetFx451FullRedist">
      <ExePackage Id="NetFx451FullRedist" Cache="no" Compressed="yes" PerMachine="yes" Vital="yes" Permanent="yes" Protocol="netfx4" Name="Redist\NDP451-KB2858728-x86-x64-AllOS-ENU.exe" DetectCondition="NETFRAMEWORK45" SourceFile="$(var.SourceDir)\NDP451-KB2858728-x86-x64-AllOS-ENU.exe" InstallCommand="/q /norestart /ChainingPackage &quot;[WixBundleName]&quot;" RepairCommand="/q /norestart /repair /ChainingPackage &quot;[WixBundleName]&quot;" UninstallCommand="/uninstall /q /norestart /ChainingPackage &quot;[WixBundleName]&quot;" />
    </PackageGroup>
  </Fragment>
</Wix>
```

Penjelasan:

1. Baris 2, deklarasi _bootstrapper_.
2. Baris 4, menambahkan referensi ke file _wxs_.
3. Baris 7, menambahkan referensi ke properti _NETFRAMEWORK45_.
4. Baris 9, menandakan kumpulan paket instalasi dengan Id _NetFx451FullRedist_.
5. Baris 10, definisi setup yang akan dieksekusi dengan Id _NetFx451FullRedist_. Pada contoh ini adalah setup .NET Framework 4.5.1 dengan perintah _silent_. Atribut _DetectCondition_ memastikan bahwa setup ini hanya di install apabila belum ada pada komputer.

Buka kembali file _Bundle.wxs_ dan tambahkan kode berikut.

```xml
<!-- Packages -->
<Chain>
  <PackageGroupRef Id="NetFx451FullRedist"/>
</Chain>
```

Penjelasan:

1. Baris 3, menambahkan referensi ke _NetFx451FullRedist_ agar setup tersebut ikut dieksekusi saat instalasi.

### Menambahkan SQL Server LocalDB 2014

Untuk menambahkan setup lain selain .NET Framework, atribut _DetectCondition_ harus dibuat secara manual menggunakan **<util:RegistrySearch>**. Hal ini karena WiX Toolset tidak memiliki informasi apa pun mengenai program yang akan di install. Buka file _Packages.wxs_ kemudian tambahkan kode berikut.

```xml
<util:RegistrySearch Id="SQLSERVERLOCALDB" Variable="SQLSERVERLOCALDB" Root="HKLM" Value="ParentInstance" Result="exists" Win64="no" Key="SOFTWARE\Microsoft\Microsoft SQL Server Local DB\Installed Versions\12.0" />

 <!-- SQL Server LocalDB -->
 <PackageGroup Id="SQLServerLocalDB">
  <MsiPackage SourceFile="$(var.SourceDir)\SqlLocalDB-2014-32.msi" InstallCondition="(NOT VersionNT64) AND (NOT SQLSERVERLOCALDB)">
    <MsiProperty Name="IACCEPTSQLLOCALDBLICENSETERMS" Value="YES"/>
  </MsiPackage>
  <MsiPackage SourceFile="$(var.SourceDir)\SqlLocalDB-2014-64.msi" InstallCondition="(VersionNT64) AND (NOT SQLSERVERLOCALDB)">
    <MsiProperty Name="IACCEPTSQLLOCALDBLICENSETERMS" Value="YES"/>
  </MsiPackage>
</PackageGroup>
```

Penjelasan:

1. Baris 1-3, definisi pencarian registry untuk mengetahui apakah _value_ _ParentInstance_ ada pada registry. Jika Ada, berarti SQL Server LocalDB 2014 sudah di install di komputer.
2. Baris 6, definisi paket instalasi _SQLServerLocalDB_.
3. Baris 7-10, definisi setup installer untuk SQL Server LocalDB 2014 versi 32-bit.
4. Baris 11-14, definisi setup installer untuk SQL Server LocalDB 2014 versi 64-bit.
5. Baris 9 dan 13, properti MSI untuk menyetujui lisensi SQL Server LocalDB 2014 (_silent install_).

Buka kembali file _Bundle.wxs_ dan tambahkan kode berikut.

```xml
<!-- Packages -->
<Chain>
  <PackageGroupRef Id="SQLServerLocalDB"/>
</Chain>
```

Penjelasan:

1. Baris 3, menambahkan referensi ke _SQLServerLocalDB_.

### Menambahkan SAP Crystal Report Runtime

Menginstall Crystal Report Runtime sama seperti menginstall SQL Server LocalDB 2014, tetapi untuk menginstall CRRuntime, diperlukan Visual C++ 2015 Redistributable. Jadi, pada tahap ini kita akan menambahkan dua setup installer pada _bootstrapper_.

Tambahkan kode berikut pada _Packages.wxs_.

```xml
<util:RegistrySearch Id="SAPCR" Variable="SAPCR" Root="HKLM" Value="BINDIR" Result="exists" Win64="no" Key="SOFTWARE\SAP BusinessObjects\Crystal Reports for .NET Framework 4.0\Shared" />

<!-- Visual C++ Redistributable 2005 -->
<PackageGroup Id="VCRedist">
  <ExePackage Id="VCRedist32" Cache="no" Compressed="yes" PerMachine="yes" Vital="yes" Permanent="yes" Name="Redist\vcredist-2015_x86.exe" InstallCondition="NOT VersionNT64" SourceFile="$(var.SourceDir)\vcredist-2015_x86.exe" InstallCommand="/q" />
  <ExePackage Id="VCRedist64" Cache="no" Compressed="yes" PerMachine="yes" Vital="yes" Permanent="yes" Name="Redist\vcredist-2015_x64.exe" InstallCondition="VersionNT64" SourceFile="$(var.SourceDir)\vcredist-2015_x64.exe" InstallCommand="/q" />
</PackageGroup>

<!-- SAP CrystalReport -->
<PackageGroup Id="SAPCrystalReport">
  <MsiPackage SourceFile="$(var.SourceDir)\CRRuntime_32bit_13_0_22.msi" InstallCondition="(NOT VersionNT64) AND (NOT SAPCR)" />
  <MsiPackage SourceFile="$(var.SourceDir)\CRRuntime_64bit_13_0_22.msi" InstallCondition="(VersionNT64) AND (NOT SAPCR)" />
</PackageGroup>
```

Penjelasan: kode di atas sangat mirip dengan kode pada tahap **_Menambahkan SQL Server LocalDB 2014_**. Konsep dan cara kerja sama seperti kode sebelumnya.

Buka kembali file _Bundle.wxs_ dan tambahkan kode berikut.

```xml
<!-- Packages -->
<Chain>
  <PackageGroupRef Id="VCRedist"/>
  <PackageGroupRef Id="SAPCrystalReport"/>
</Chain>
```

Penjelasan:

1. Baris 3, menambahkan referensi ke _VCRedist_ dan _SAPCrystalReport_.

### Menambahkan Wiyata Bhakti Installer

Akhirnya, installer utama ditambahkan ke dalam bootstrapper. Installer utama aplikasi diletakkan di akhir karena program utama ini membutuhkan program lain agar dapat beroperasi, jadi harus menunggu program lain yang sebelumnya telah di susun terinstall.

Buka file _Packages.wxs_ kemudian tambahkan kode di bawah ini.

```xml
<!-- Wiyata Bhakti Full -->
<PackageGroup Id="WiyataBhaktiFull">
  <MsiPackage SourceFile="$(var.SourceDir)\WiyataBhakti.Installer.msi" />
</PackageGroup>
```

Penjelasan: kode di atas digunakan untuk memasang Wiyata Bhakti.

Buka kembali file _Bundle.wxs_ dan tambahkan kode berikut.

```xml
<!-- Packages -->
<Chain>
  <PackageGroupRef Id="WiyataBhaktiFull"/>
</Chain>
```

## Finalisasi Setup Installer Project

Pada artikel sebelumnya telah dibahas bagaimana cara membuat _installer_ dan _boostrapper_ yang dapat menginstall program pada komputer lengkap dengan .NET Framework, SQL Server LocalDB, dan Crystal Report Runtime. Tahap akhir adalah melakukan uji coba setup tersebut pada _virtual machine_.

### Uji Coba pada Virtual Machine

Sebelum setup dapat digunakan pada komputer klien, setup harus diuji coba terlebih dahulu. Pada kasus testing setup installer, kita tidak bisa menguji setup secara langsung pada komputer yang digunakan untuk developing, melainkan harus menggunakan _test server_ atau menggunakan _virtual machine_. Alasannya karena setup installer akan memasang program pada sistem dan apabila terdapat _error_ pada setup kemungkinan besar program yang telah di install tidak akan dapat di uninstall.

Penulis biasanya mengunakan **VirtualBox** untuk membuat _virtual machine_ dan sistem operasi **Windows 7**. Penulis juga biasa membuat _snapshot_ saat VM pertama kali dibuat, untuk menandai _clean install_. Jadi tidak perlu melakukan install ulang saat perlu melakukan tes ulang.

![VirtualBox Manager](/posts/2018-05-29/vbox-man.png)

## Studi Kasus Lainnya

Penulis mendapatkan banyak permintaan dari pembaca blog Kodesiana.com untuk membuat tutorial dengan menggunakan basis data MySQL. Pada waktu dekat ini, penulis mungkin tidak bisa membuat tutorial lengkap cara membuat _bootstrapper_ dengan program tambahan MySQL.

Jika Anda ingin bereksperimen untuk membuat _bootstrapper_ MySQL, penulis menyarankan untuk mengunduh [**MySQL for Windows**](https://dev.mysql.com/downloads/installer) untuk memulai membuat _bootstrapper_. Saran dari penulis:

1. Gunakan `<RegistrySearch>` untuk mendeteksi program MySQL.
2. Install MySQL for Windows menggunakan `<ExePackage>`.
3. Jalankan `<CusomAction>` pada _event_ _InstallFiles_ pada setup untuk menjalankan program _mysql_ untuk mengeksekusi perintah SQL untuk membuat basis data.

## Referensi

1. Indonesia. 2014. How Can I Run an SQL Script in MySQL ([_https://stackoverflow.com/questions/8940230/how-can-i-run-an-sql-script-in-mysql_](https://stackoverflow.com/questions/8940230/how-can-i-run-an-sql-script-in-mysql)). Diakses 3 Juni 2018.
