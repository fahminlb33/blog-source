---
title: Klasifikasi Bunga Iris menggunakan KNN Python🌺
slug: klasifikasi-bunga-iris-menggunakan-knn-python
date: 2020-07-28
tags: [kuliah, programming, python, tutorial]
math: true
---

Klasifikasi merupakan salah satu kegiatan yang paling sering dilakukan
menggunakan _machine learning_. Pada artikel ini kamu akan mencoba untuk membuat
sebuah model untuk melakukan klasifikasi pada spesies bunga iris (Fisher, 1936).
Bunga iris memiliki tiga spesies, yaitu: _versicolor, virginica_, dan _setosa_.
Dataset yang digunakan berisi informasi panjang dan lebar untuk masing-masing
kelopak pada bunga (sepal dan petal). Klasifikasi yang akan kita buat ini
termasuk ke dalam jenis _multiclass classification_, karena terdapat lebih dari
dua kelas output.

Dataset ini merupakan dataset standar yang biasa digunakan untuk belajar
_machine learning_, mirip seperti _Hello World_ saat kamu pertama kali belajar
membuat program. Dataset ini berisi 150 data tanpa adanya data kosong dan
merupakan dataset yang paling banyak digunakan dalam pembelajaran _machine
learning_. Dataset ini dapat diakses pada [UCI Machine Learning
Repository](https://archive.ics.uci.edu/ml/datasets/Iris).

![](https://source.unsplash.com/gK6f8bKKic0/1600x900)

Foto oleh Olga Mandel, sumber:
[Unsplash](https://unsplash.com/photos/gK6f8bKKic0).

Selain itu, kamu juga akan belajar bagaimana melakukan _data ingestion,
exploratory data analysis, preprocessing, training,_ dan juga _evaluation_
menggunakan dataset ini. Perlu diingat bahwa tidak semua proses EDA dan
_preprocessing_ diperlukan untuk melakukan analisis, tetapi dipilih sesuai
dengan jenis data yang kamu olah.

Karakteristik dataset:

- Kelas output seimbang (banyaknya kelas seragam untuk tiga kelas output).
- Tidak terdapat data kosong.
- Data tersebar membentuk _cluster_.
- _Outlier_ tidak signifikan.

**Catatan!** Tidak semua dataset akan memberikan informasi ini. Kalau kamu punya
data mentah, kamu harus melakukan EDA untuk mengetahui distribusi dan sebaran
data. Selain itu, informasi tersebut juga akan berguna untuk melakukan
_preprocessing_. Dengan adanya informasi ini, kamu tidak perlu melakukan EDA
yang terlalu intensif.

## K-Nearest Neighbor🤼

K-Nearest Neighbor merupakan algoritma untuk melakukan klasifikasi dengan cara
membandingkan jarak ketetanggaan tiap-tiap titik data dengan data yang akan
diprediksi. Pada algoritma ini metode jarak yang digunakan biasanya adalah jarak
Euclidean. Data baru akan dibandingkan jarak terdekat dengan tetangganya,
semakin banyak tetangga dengan jarak yang dekat, maka data tersebut akan masuk
ke dalam kelas tersebut.

![](/posts/2020-07-20/MsLY6Gu.png)

Visualisasi KNN.

Banyaknya tetangga yang akan menjadi penentu kelas data yang baru, ditentukan
oleh nilai konstanta \\(k\\). Nilai ini bisa ditentukan secara bebas atau dengan
melakukan _training_ menggunakan beberapa nilai \\(k\\) hingga mendapatkan
akurasi tertinggi atau _mean absolute error (MAE)_ paling kecil.

### Contoh Klasifikasi Manual🖊

Terdapat data pengukuran berat badan dan tinggi badan sebagai berikut. Tentukan
apakah berat badan 80 kg dengan tinggi badan 150 cm termasuk normal atau
obesitas dengan menggunakan algoritma KNN dengan \\(k=3\\).

Berat Badan | Tinggi Badan | Kelas
------------|--------------|-------
70  |   200   |  Normal
68  |   173   |  Normal
90  |   150   |  Obesitas
100 |   170   |  Obesitas

**Jawab**
Buat tabel jarak antara data baru dengan data yang ada, pada contoh ini kita
akan menggunakan jarak Euclidean. Setelah jarak antar data didapatkan, urutkan
berdasarkan jarak terkecil kemudian ambil  data teratas.

Persamaan jarak Euclidean yaitu:

{{< math >}}
d(x,y) =\ sqrt{ {(x_2-x_1)}^2 + {(y_2-y_1)}^2 }
{{< /math >}}

dengan \\(x\\) dan \\(y\\) adalah dua titik pada ruang Euclidean yang akan
diukur.

**Contoh**
Jarak antara berat badan \\(x_1=70\\) dan tinggi badan \\(y_1=200\\) dengan
berat badan \\(x_2=80\\) dan tinggi badan \\(y_2=150\\).

{{< math >}}
\begin{aligned}
d(x,y) &= \sqrt{{(x_2-x_1)}^2+{(y_2-y_1) }^2 } \\
&= \sqrt{ { (80-70)}^2 + { (150-200) }^2 } \\
&= 50,99
\end{aligned}
{{< /math >}}

Lakukan proses yang sama antara semua baris pada dataset dengan data yang akan
diuji.

Berat Badan | Tinggi Badan | Kelas | Jarak (\(d\))
------------|--------------|-------|----------------
     90     |     150      |  Obsesitas  |  10,00
     100    |     170      |  Obsesitas  |  28,28
     62     |     180      |  Normal     |  34,98
     70     |     200      |  Normal     |  50,99

Setelah data jarak didapatkan, kemudian urutkan jarak berdasarkan nilai terkecil
ke terbesar. Selanjutnya hitung banyaknya data dengan kelas outputnya.

Karena data memiliki dua tetangga dengan kelas obesitas dan hanya satu tetangga
dengan data normal, maka data tersebut termasuk ke dalam kelas **obesitas**.
Jika banyaknya kelas sama, maka akan diambil kelas dengan jarak terkecil.

## Klasifikasi KNN menggunakan Python🐍

Untuk setiap kode pada artikel ini, salin kode tersebut sebagai sebuah sel pada
Jupyter Notebook atau Google Colab!

Untuk membuat sebuah model klasifikasi ini, kita akan melakukan beberapa proses,
yaitu:

1. _Data ingestion_
    1. Membaca data dari file CSV.
2. _Exploratory data analysis_
    1. Statistik deskriptif data.
    2. Sampel data teratas.
    3. _Pair plot_ distribusi data.
3. _Preprocessing_
    1. Memisahkan _features_ dan _label_.
    2. Membagi data latih dan uji.
    3. _Label encoding_.
    4. Standarisasi data.
    5. Menentukan nilai \\(k\\) berdasarkan _mean absolute error_.
4. _Training_, menggunakan algoritma KNN.
5. _Evaluation_, menggunakan _confusion matrix_.

![](/posts/2020-07-20/P1gKiep.jpg)

### Data Ingestion

Sebelum dapat membaca data, kita perlu mengimpor _library_ yang akan digunakan.
Pada artikel ini kita akan menggunakan _library_ NumPy, Pandas, Matplotlib,
Seaborn, dan Scikit-Learn. Kelima _library_ ini digunakan untuk membaca,
mengolah, dan melakukan _training_ model

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

from sklearn.preprocessing import LabelEncoder
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier
from sklearn.metrics import classification_report
from sklearn.metrics import confusion_matrix
from sklearn.metrics import ConfusionMatrixDisplay
```

Setelah mengimpor _library_ yang akan digunakan, selanjutnya kamu akan membaca
file CSV dengan memanggil fungsi **read_csv**. Data yang dibaca akan disimpan
sebagai objek DataFrame. Objek DataFrame ini akan menampung data seperti sebuah
tabel. Kamu dapat melakukan berbagai pengolahan data pada objek DataFrame ini.

```python
columns = ["sepal-length", "sepal-width", "petal-length", "petal-width", "class"]
df = pd.read-csv(r'https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data', names=columns)
```

### Exploratory Data Analysis

Setelah kamu memuat data yang akan diolah sebagai DataFrame, kammu dapat mulai
melakukan eksplorasi data. Proses eksplorasi data yang akan dilakukan yaitu
menghitung statistika deskripsi data, melihat sampel data teratas, dan melakukan
visualisasi sebaran data dan distribusinya.

#### Statistik Deskriptif Data

```python
df.describe()
```

![](/posts/2020-07-20/qoP3W9m.png)

Dapat dilihat pada output di atas masing-masing kelas terdapat 150 data. Semua
atribut memiliki _mean_ dan standar deviasi yang berbeda-beda pula. Jika kita
melihat pada _min_ dan _max_, terlihat bahwa _range_ antar atribut juga
bervariasi.

Karena kamu akan menggunakan model KNN, maka data perlu di standarisasi sebelum
digunakan untuk melakukan _training_. Hal ini karena KNN menggunakan prinsip
metrik jarak dengan Euclidean. Apabila terdapat data dengan _range_ yang jauh,
maka dengan melakukan standarisasi akan mempercepat proses _training_ dan
meningkatkan akurasi model.

#### Sampel Data

```python
df.head()
```

![](/posts/2020-07-20/Uoqa4yh.png)

Dari output di atas dapat dilihat ternyata data di dalam file CSV disusun
berurutan berdasarkan kelas. Artinya sebelum melakukan _training_ data harus
diacak dan dibagi seimbang.

#### Pair Plot

Pada tahap ini kamu akan membuat pair plot untuk membantu memvisualisasikan
hubungan antar variabel pada dataset.

```python
sns.pairplot(df, hue='class')
```

![](/posts/2020-07-20/PDwVmDh.png)

Pair Plot antar Variabel.

Berdasarkan plot yang dibuat, dapat dilihat bahwa kelas _iris-setosa_ selalu
terpisah dari kelas yang lain. Artinya saat melakukan klasifikasi terdapat
kemungkinan besar bahwa model akan selalu dapat membedakan spesies _setosa_
dengan baik. Dapat dilihat juga distribusi data untuk _petal-length_, spesies
_setosa_ terpisah dari kelas yang lain.

Selain itu, jika dilihat persebaran datanya pada diagram pencar, sebagian besar
kombinasi atribut miliki korelasi Pearson yang positif. Artinya _features_ yang
terdapat pada dataset ini baik untuk digunakan untuk membuat sebuah model.
Selain itu, dengan korelasi yang tinggi, maka model klasifikasi dapat diubah
menjadi model regresi untuk melakukan peramalan.

### Preprocessing

#### Memisahkan Features dan Label

Pada tahap ini kamu melakukan operasi _slicing_ untuk membagi data dari dalam
objek DataFrame. Pada tahap ini juga kamu akan mengambil data sebagai _array_,
bukan objek DataFrame. Hal ini dilakukan karena _library_ scikit-learn
menggunakan tipe data _NumPy array_ untuk digunakan dalam algoritmanya.

```python
X = df.iloc[:, :-1].values
y = df.iloc[:, 4].values
```

#### Membagi Data Latih dan Uji

Pada tahap ini kamu akan membagi 150 data untuk latih dan uji. Kamu akan
menggunakan fungsi **train_test_split** untuk memudahkan proses pembagian data
agar data yang dibagi seimbang.

```python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, shuffle=True, stratify=y, random_state=42)
```

#### Label Encoding

Karena kelas dari dataset ini berupa teks, maka perlu dilakukan _encoding_. Pada
kasus ini kamu akan menggunakan _label encoding_, karena internal algoritma ini
menggunakan ketetanggan, maka nilai output tidak akan terbias dari penggunaan
teknik _encoding_ ini.

```python
lb = LabelEncoder()
lb.fit(y_train)

y_train = lb.transform(y_train)
y_test = lb.transform(y_test)
```

#### Standarisasi

Algoritma KNN menggunakan metrik jarak (Euclidean), sehingga memiliki data yang
terdistribusi merata khususnya pada distribusi normal akan meningkatkan performa
model KNN ini. Maka dari itu diperlukan proses standarisasi data.

```python
scaler = StandardScaler()
scaler.fit(X_train)

X_train = scaler.transform(X_train)
X_test = scaler.transform(X_test)
```

#### Menentukan Nilai Konstanta k

Seperti yang sudah di jelaskan sebelumnya, bahwa KNN membutuhkan suatu nilai
konstanta \\(k\\) untuk menentukan berapa banyak tetangga yang akan digunakan
oleh model. Kode di bawah ini akan melakukan _training_ sebanyak 4o kali dengan
nilai \\(k\\) dari 1 hingga 40. Angka 40 ini bersifat bebas dan dapat diganti
dengan angka lain.

```python
error = []

for i in range(1, 40):
    knn = KNeighborsClassifier(n_neighbors=i)
    knn.fit(X_train, y_train)

    pred_i = knn.predict(X_test)
    error.append(np.mean(pred_i != y_test))

plt.figure()
plt.plot(range(1, 40), error, color='red', marker='o',
         markerfacecolor='blue', markersize=10)
plt.title('Rata-Rata Error terhadap nilai K')
plt.xlabel('Nilai K')
plt.ylabel('Rata-Rata Error')
plt.show()
```

![](/posts/2020-07-20/vVbJETU.png)

Dapat dilihat pada grafik yang dihasilkan, bahwa terdapat beberapa nilai \\(k\\)
yang dapat meminimalisasi nilai _error_. Contohnya nilai \\(k\\) awal yang
digunakan adalah 40, dapat dilihat pada grafik bahwa nilai \\(k=40\\) memiliki
tingkat _error_ sebesar 0,17. Ternyata nilai \\(k\\) yang kecil seperti 1, 2, 4,
dan 5 memiliki tingkat _error_ yang kecil.

Setelah mengetahui nilai \\(k\\) dengan _error_ yang rendah, kamu dapat melatih
kembali model kamu dengan mengubah nilai \\(k\\) sesuai dengan grafik yang ada.
Teknik ini merupakan salah satu teknik sederhana untuk menghasilkan model yang
lebih baik. Terdapat banyak cara lain untuk meningkatkan akurasi dan performa
suatu model yang akan di bahas pada bab selanjutnya.

### Training

Pada proses _training_ ini kamu akan menggunakan nilai konstanta \\(k = 4\\).
Kamu bisa mengubah angka ini sesuai dengan keinginanmu sesuai pada hasil
observasi pada tahap _preprocessing_ sebelumnya.

```python
classifier = KNeighborsClassifier(n_neighbors=4)
classifier.fit(X_train, y_train)
```

### Evaluation

Setelah proses _training_, kamu akan mengevaluasi apakah model yang kamu buat
memiliki akurasi yang baik atau belum.

```python
y_pred = classifier.predict(X_test)
print(classification_report(y_test, y_pred, target_names=lb.classes_))
```

![](/posts/2020-07-20/gacPCPX.png)

Berdasarkan hasil output dari **classification_report**, diketahui bahwa model
dapat membedakan semua data dengan spesies _iris-setosa_ dengan sempurna. Tetapi
pada kelas lain seperti _iris-versicolor_ dan _iris-virginica_ masih terdapat
kesalahan klasifikasi yang ditandai dengan _precision_ dan _recall_ yang tidak
mencapai angka 1.

Secara keseluruhan model ini berhasil mendapatkan akurasi sebesar 93%. Hasil ini
sudah sangat baik melihat juga pada nilai _F1 score_ yang tinggi menandakan
bahwa sebaran hasil klasifikasi yang seimbang untuk kelas-kelas lainnya.

```python
cm = confusion_matrix(y_test, y_pred)
ConfusionMatrixDisplay(cm, display_labels=lb.classes_).plot()
```

![Confusion matrix hasil klasifikasi menggunakan model machine
learning](/posts/2020-07-20/xLl7GmJ.png)

## Penutup🌌

Sampai di sini kamu sudah belajar tentang algoritma KNN dan contoh
pengaplikasiannya untuk melakukan klasifikasi. Latihan selanjutnya kamu bisa
mencoba mencari data lain dan melakukan klasifikasi menggunakan algoritma KNN
atau algoritma lain yang kamu ketahui.

Klasifikasi bukanlah satu-satunya hal yang dapat dilakukan menggunakan _machine
learning_, masih banyak lagi hal-hal yang dapat dilakukan menggunakan _machine
learning_. Jangan lupa untuk _subscribe_ ke
[YouTube](https://www.youtube.com/c/FahmiNoorFiqri) dan juga ke newsletter
Kodesiana.com ya!

Stay tuned, teman-teman!😄

## Referensi📘

1. Fisher, R. A. “THE USE OF MULTIPLE MEASUREMENTS IN TAXONOMIC PROBLEMS.”
    _Annals of Eugenics_, vol. 7, no. 2, Sept. 1936, pp. 179–88. _DOI.org
   (Crossref)_, doi:10.1111/j.1469-1809.1936.tb02137.x.
2. Hunter, J. D. (2007). Matplotlib: A 2D graphics environment. Computing in
   Science & Engineering, 9(3), 90–95.
3. Oliphant, T. E. (2006). A guide to NumPy (Vol. 1). Trelgol Publishing USA.
4. Pedregosa, F., Varoquaux, Ga"el, Gramfort, A., Michel, V., Thirion, B.,
   Grisel, O., … others. (2011). Scikit-learn: Machine learning in Python.
   Journal of Machine Learning Research, 12(Oct), 2825–2830.
5. Waskom, Michael, et al. Mwaskom/Seaborn: V0.8.1 (September 2017). Zenodo,
   2017. DOI.org (Datacite), doi:10.5281/ZENODO.883859.
