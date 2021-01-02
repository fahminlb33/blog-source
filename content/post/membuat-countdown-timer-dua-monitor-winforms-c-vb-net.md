---
title: "Membuat Countdown Timer Dua Monitor WinForms C#/VB.NET⏳"
date: "2018-05-24"
tags: [programming, tutorial, tips-trik, winforms]
---

_Countdown timer_ lazim dijumpai di berbagai tempat misalnya di rumah sakit, bank, kantor servis, dan tempat lain yang menggunakan sistem antrean. Program ini biasanya menunjukkan berapa lama waktu sebelum antrean berikutnya dipanggil. Timer ini biasanya ditampilkan menggunakan dua monitor yaitu satu monitor untuk mengontrol timer dan satu monitor untuk menampilkan timer.

Pada artikel ini, saya akan membahas sedikit cara membuat aplikasi _countdown timer_ yang dapat disesuaikan dengan mudah dan dapat menampilkan timer pada layar berbeda (_multi-screen_).

## Membuat Form Countdown

Pertama buat project baru, saya menggunakan nama **CountdownScreen**. Tambahkan dua form baru kemudian tambahkan kontrol sesuai dengan gambar berikut.

![Desain Form Setting Timer](/posts/2018-05-24/form-utama.png)

![Desain Form Timer](/posts/2018-05-24/form-countdown.png)

Untuk form **CountdownForm.cs**, set _FormBorderStyle_ ke _None_ dan pada label timer set _AutoSize_ ke _False_ dan _Dock_ ke _Fill_. Tambahkan sebuah **Timer** dengan nama _tmrCount_ dengan _Interval_ 1000.Sesuaikan tampilan dan font yang digunakan agar mudah dilihat.

### Source Code CountdownForm.cs

Salin rekat kode berikut pada file **CountdownForm.cs.**

```csharp
namespace CountdownScreen {
    public partial class CountdownForm : Form {
        private TimeSpan _currentCount;

        public CountdownForm(Rectangle bounds, TimeSpan value) {
            InitializeComponent();
            SetBounds(bounds.X, bounds.Y, bounds.Width, bounds.Height, BoundsSpecified.Location);
            WindowState = FormWindowState.Maximized;
            _currentCount = value; label1.Text = $"{_currentCount.Hours:D2}:{_currentCount.Minutes:D2}:{_currentCount.Seconds:D2}";
            tmrCount.Start(); 
        }

        private void tmrCount_Tick(object sender, EventArgs e) // timer, interval 1000
        {
            _currentCount = _currentCount - TimeSpan.FromSeconds(1);
            label1.Text = "{_currentCount.Hours:D2}:{_currentCount.Minutes:D2}:{_currentCount.Seconds:D2}";

            if (_currentCount.TotalSeconds == 0) return;
            label1.Text = "Selesai"; tmrCount.Stop();
        }
    }
}
```

Penjelasan Kode

- Baris 7 saat form akan dibuat ("constructed"), lokasi tampilan form _(bounds)_ dan lama waktu _(value)_ dimuat dalam _constructor_.
- Baris 11 dan 12 men-set lokasi form dan membuat form menjadi **Maximized.**
- Baris 14-16 menyimpan nilai _value,_ menampilkan berapa lama waktu timer, dan memulai _tmrCount_.

Setiap kali interval pada _tmrCount_ dilewati, _event handler_ pada baris 19-27 akan dieksekusi.

- Baris 21 mengurangi waktu sebanyak 1 detik dari variabel.
- Baris 22 menampilkan sisa waktu.
- Baris 24-26 apabila jumlah detik pada variabel lebih kecil daripada satu, berarti countdown telah selesai dan kemudian menghentikan _tmrCount_.

### Source Code MainForm.cs

Salin rekat kode berikut pada file **MainForm.cs**.

```csharp
namespace CountdownScreen {
    public partial class MainForm : Form {
        private CountdownForm _countdownForm; 

        public MainForm() {
            InitializeComponent();
            cboScreen.DataSource = Screen.AllScreens.Select(x => $"{x.DeviceName} (Primary: {x.Primary})").ToList();
        }

        private void cmdShow_Click(object sender, EventArgs e) // event handler
        {
            var screenBounds = Screen.AllScreens[cboScreen.SelectedIndex].Bounds;
            var time = new TimeSpan(dtCount.Value.Hour, dtCount.Value.Minute, dtCount.Value.Second);
            _countdownForm = new CountdownForm(screenBounds, time);
            _countdownForm.Show();
        }

        private void button1_Click(object sender, EventArgs e) // event handler 
        {
            _countdownForm?.Close();
        }
    }
}
```

- Baris 5 variabel yang menampung referensi ke objek **CountdownForm**.
- Baris 11 me-load array _monitor/screen_ yang ada pada PC ke **ComboBox.**
- Baris 16-17 me-load ukuran _(bounds)_ layar berdasarkan _index_ dari **ComboBox** dan membuat objek **TimeSpan** yang berisi durasi waktu timer.
- Baris 19-20 menampilkan form **CountdownForm.**
- Baris 25 menutup form **CountdownForm.**

Setelah selesai, jalankan program dengan cara tekan **F5**. Form **MainForm** akan muncul. Pastikan komputer/laptop Anda terhubung dengan dua monitor dan _project mode_ pada **Extend**.

![Project Mode Extend](/posts/2018-05-24/monitor-extend.jpg)

Masukkan berapa lama timer dan pilihan monitor, kemudian klik **Mulai**. Countdown akan muncul pada layar yang dipilih. Klik **Berhenti** untuk menutup form countdown.

![Tampilan Aplikasi Timer](/posts/2018-05-24/pilihan-display.png)

## Download

Source code: [https://github.com/Kodesiana/Post-Samples/tree/master/Countdown-Timer](https://github.com/Kodesiana/Post-Samples/tree/master/Countdown-Timer)
