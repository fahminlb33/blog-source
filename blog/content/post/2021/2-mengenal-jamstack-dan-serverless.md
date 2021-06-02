---
title: Mengenal Jamstack dan Serverless🥧
slug: mengenal-jamstack-dan-serverless
date: 2021-04-18
tags: [tips, internet, cloud]
---

*Blogging* merupakan salah satu kegiatan yang bisa dilakukan untuk mengisi waktu
luang dan bahkan bisa menjadi sumber penghasilan pasif bagi banyak orang yang
menekuni bidang ini. Khususnya untuk *developer*, *blogging* merupakan salah
satu kegiatan wajib untuk melatih dan mengasah kemampuan ngoding. Hmm, menarik.

Sekarang ini ada banyak platform untuk *blogging* mulai dari pemain lama seperti
Blogger dan WordPress, hingga pendatang baru seperti Hugo dan Gatsby. Munculnya
banyak *framework* dan *library* baru untuk membuat *static site* ini merupakan
salah satu bentuk kemajuan teknologi khususnya untuk para pecinta **web
frontend**.

Kembali ke tahun 2012, sebagian besar web masih menggunakan *server-side
processing/rendering (SSR)* yang artinya proses yang dilakukan pada web kita
akan ditangani oleh server atau *backend*. Beberapa tahun terakhir ini banyak
terjadi perkembangan di bidang UI/UX, khususnya perkembangan **Jamstack**.

![](https://source.unsplash.com/v9FQR4tbIq8)

## Apa itu Jamstack?

> JAM merupakan singkatan dari JavaScript, API, & Markup.

Pada Jamstack website dibangun oleh dua komponen utama, yaitu *frontend* yang
merupakan web interaktif seperti SPA atau PWA yang menggunakan teknologi lain
yang berjalan di sisi klien seperti JavaScript dan WebAssembly yang tidak
memerlukan server khusus untuk mengolah setiap permintaan dari klien. Untuk
memenuhi kebutuhan *user*, aplikasi *frontend* akan melakukan *request* ke API
yang ada pada server sehingga mengurangi saling ketergantungan (*dependency*)
antara *frontend* dan *backend*.

Pada sebuah web Jamstack, biasanya kita akan menggunakan *library* atau
*framework* JavaScript seperti React dan Vue atau bisa juga dengan menggunakan
*static site generator* seperti Jekyll dan Hugo. Selain itu, kita juga bisa
menggunakan hampir semua bahasa pemrograman selain JavaScript seperti C#
menggunakan Blazor (WASM).

Beberapa keuntungan menggunakan Jamstack yaitu:

1. Performa aplikasi yang lebih cepat, karena *user* bisa menggunakan web kita
   seperti halnya aplikasi *native* yang bahkan bisa di *cache* dan di *load*
   dalam sekejap.
2. Keamanan yang lebih tinggi karena penggunaan API sekarang tidak terikat
   dengan endpoint *backend* dan bisa menggunakan keamanan API yang lebih tinggi
   karena aksesnya diatur oleh klien.
3. Biaya server yang lebih murah karena tidak membutuhkan banyak CPU dan RAM,
   kita bahkan bisa menggunakan CDN untuk meng-host website Jamstack. Ingat web
   Jamstack tidak memiliki pemrosesan pada server sehingga bisa disajikan
   melalui CDN murni.
4. Lebih mudah untuk dikembangkan oleh developer karena sekarang *frontend*
   tidak terikat dengan *backend*, keduanya bisa saling bekerja mengikuti
   kontrak API tanpa perlu memikirkan format data antara kedua sistem.

Wah ternyata banyak ya manfaat dari Jamstack! Blog Kodesiana.com juga saat ini
sudah menggunakan Jamstack, lho! Sejak awal Maret 2021 penulis mengubah website
Kodesiana.com menjadi Jamstack menggunakan stack Hugo dan tema Fuji. Meskipun
penulis bukan *frontend developer*, menggunakan Hugo bukanlah suatu proses yang
membutuhkan waktu banyak untuk belajar, cukup ikuti tutorial dan template dan
cling... web kamu bisa langsung jadi dalam lima menit.

## Di mana tempat hosting web Jamstack?

Di mana aja!

Karena web Jamstack pada dasarnya adalah web statis yang tidak memerlukan
pengolahan di servernya, kamu bahkan bisa menggunakan CDN atau layanan *static
site hosting* seperti GitHub Pages, Vercel, dan Netlify. Misalnya kamu mau host
di cloud service seperti Amazon EC2 atau Azure AppService, itu bisa banget, kamu
tinggal pilih service dengan harga paling murah karena server kamu tidak akan
melakukan proses apapun selain menyajikan file HTML/CSS/JS untuk web kamu. Kamu
juga bisa lho host web kamu di Amazon S3 dan Azure Blob Storage kalau kamu masih
belum yakin kalau Jamstack itu engga perlu server dedicated untuk mengolah data.

Tapi tau ga, Azure sekarang punya juga layanan khusus untuk web Jamstack,
mengingat tidak perlu adanya proses di server dedicated, sekarang Azure
menghadirkan **Azure Static Web Apps**, tempat kamu bisa host web Jamstack kamu.
Ini cocok banget buat kamu yang sudah punya sistem di Azure dan ingin lebih
menghemat biaya bulanan kamu, karena saat ini layanan Azure Static Web Apps
masih dalam tahap *Preview* sehingga penggunaannya masih gratis tanpa SLA.

![](https://source.unsplash.com/7r7-7RLdwCU)

## Gimana cara bikin web Jamstack?

Caranya sama seperti kita membuat web pada umumnya, tetapi di sini kita akan
menggunakan banyak tool seperti SCM dan CI/CD untuk mengotomatisasi proses
pengembangan web dan juga men-deploy sistem kita. Pada umumnya untuk membuat web
Jamstack kita perlu melakukan:

1. Buat SCM misalnya git.
2. Buat *pipeline* CI/CD misalnya Jenkins/Travis.
3. Buat template web menggunakan *static site generator* seperti Hugo/Jekyll
   atau *library* seperti React/Vue.
4. Commit ke git, tunggu proses *deploy* oleh CI/CD.
5. Selesai.

Nah selain itu, kalau kamu pakai Azure Static Web Apps, kamu juga bisa
integrasikan dengan **Azure Functions** untuk membuat backend yang *serverless*.
Waduh apa lagi tuh *serverless?*

## Apa itu serverless?

Serverless merupakan model *cloud service* yang menyediakan jasa komputasi bagi
kliennya saat dibutuhkan saja (*on-demand*), sehingga proses komputasi yang
dilakukan lebih efisien dan lebih murah. Model ini banyak digemari oleh
developer karena harganya yang ekonomis dan lebih mudah untuk dikembangkan
karena sifatnya yang berdiri sendiri/*standalone*.

Misalnya kamu ingin membuat sebuah API untuk menerima isian form, daripada harus
membuat *full blown backend app* "backend beneran yang isinya gede dengan
MVC/CRUD/dll." akan lebih mudah kalau kamu cukup bikin sebuah API yang menerima
data JSON kemudian menyimpannya ke database, tidak perlu bikin full backend
misalnya menggunakan Laravel untuk menyimpan data dari sebuah form, lebih banyak
waktu terbuang membuat backend dibandingkan membuat satu fungsi saja.

Nah kalau kamu punya banyak API yang bersifat *standalone*, kamu mungkin ingin
mencoba untuk beralih ke layanan *serverless* seperti Azure Functions untuk API
kamu karena harganya yang lebih murah dan juga lebih cepat.

## Wah menarik ya, gimana caranya tuh?

Penulis akan lanjutkan tulisan ini di pekan depan atau mungkin dua pekan lagi
karena penulis akan melaksanakan UTS. Nanti kita akan belajar cara menggunakan
tool Hugo, GitHub, dan Azure untuk membuat website Jamstack.

Stay tuned ya teman-teman!

## Referensi

1. Netlify. 2021. Jamstack (https://jamstack.org/). Diakses 18 April 2021.
2. Duarte, Pedro. 2019. Jamstack (https://jamstack.wtf/). Diakses 18 April 2021.
