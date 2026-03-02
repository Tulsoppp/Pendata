# Distance
## Pengertian Jarak (Distance)
Dalam konteks data mining dan pembelajaran mesin, jarak (distance) adalah ukuran numerik yang menyatakan seberapa mirip atau berbeda dua data/objek. Semakin kecil nilai jaraknya, semakin mirip objek tersebut. Jarak merupakan komponen penting terutama dalam algoritma berbasis jarak seperti klastering (clustering) dan nearest neighbor.

### 1. Mengukur Jarak untuk Data Numerik (Kontinu)

Data numerik adalah data yang dinyatakan dengan angka yang memiliki urutan dan jarak antar nilai bermakna.

**Minkowski Distance**

Rumusan umum untuk menghitung perbedaan antara dua vektor v₁ dan v₂ adalah:

$$
d_{min}(x,y) = \left( \sum_{i=1}^{n} |x_i - y_i|^m \right)^{\frac{1}{m}}, \quad m \geq 1
$$

Parameter m menentukan jenis jarak; semakin besar m, semakin kuat kontribusi selisih besar pada hasil akhir.

**Manhattan Distance (L₁)**

Kasus khusus dari Minkowski dengan m = 1:

$$
d_{man}(x,y) = \sum_{i=1}^{n} |x_i - y_i|
$$

Karakteristik:

- Mengukur jumlah selisih atribut.

- Sensitif terhadap outlier.

- Memberikan bentuk cluster berbentuk hyper-rectangle.

**Euclidean Distance (L₂)**

Kasus umum paling sering digunakan:

$$
d_{euc}(x,y) = \sqrt{\sum_{i=1}^{n} (x_i - y_i)^2}
$$

Karakteristik:

- Mengukur jarak “lurus” dua titik dalam ruang n-dimensi.

- Performa baik untuk cluster yang kompak dan terpisah jelas.

- Kelemahan: sensitif terhadap skala fitur dan korelasi antar atribut.

**Average Distance**

Versi modifikasi Euclidean yang menghitung kuadrat rata-rata:

$$
d_{avg}(x,y) = \left( \frac{1}{n} \sum_{i=1}^{n} (x_i - y_i)^2 \right)^{\frac{1}{2}}
$$

Menyeimbangkan perbedaan skala antar dimensi.

**Weighted Euclidean Distance**

Memberikan bobot pada setiap atribut:

$$
d_{we}(x,y) = \left( \sum_{i=1}^{n} w_i (x_i - y_i)^2 \right)^{\frac{1}{2}}
$$

Cocok jika beberapa fitur lebih penting daripada yang lain.

**Chord Distance**

Merupakan variasi Euclidean yang mengatasi perbedaan skala:

$$
d_{chord}(x,y) = \left( 2 - 2 \frac{\sum_{i=1}^{n} x_i y_i}{\|x\|_2 \|y\|_2} \right)^{\frac{1}{2}}
$$

Digunakan terutama ketika data belum dinormalisasi.

**Mahalanobis Distance**

Mengukur jarak dengan mempertimbangkan covariance antar atribut:

$$
d_{mah}(x,y) = \sqrt{(x - y) S^{-1} (x - y)^T}
$$

Karakteristik:

- Mengatasi skala yang berbeda dan korelasi antar variabel.

- S⁻¹ adalah invers dari matriks kovarians fitur.