# Business-Understanding

## 1. Macam macam Data

Dalam data data mining dan maha datar, Anda akan menemukan banyak jenis data yang berbeda, dan masing-masing cenderung membutuhkan alat dan teknik yang berbeda. Macam macam data dikelompokkan sebagai berikut:

- Data Terstruktur (Structured): Data yang mengikuti model tertentu, biasanya dalam bentuk tabel (baris dan kolom) seperti database SQL atau Excel. Mudah disimpan dan di-query.

- Data Tidak Terstruktur (Unstructured): Data yang isinya bervariasi dan tidak mudah dimasukkan ke model tabel, contohnya isi email atau teks bebas.

- Bahasa Alami (Natural Language): Bahasa manusia (seperti Bahasa Indonesia atau Inggris) yang memerlukan pemrosesan khusus (NLP).

- Data Bangkitan Mesin (Machine-Generated): Data otomatis tanpa campur tangan manusia, seperti log server atau data sensor IoT.

- Data Graph/Jaringan: Menunjukkan hubungan antar objek menggunakan node dan edge, contohnya jaringan pertemanan di media sosial.

- Data Multimedia: Berupa Audio, Video, dan Citra yang digunakan untuk pengenalan objek atau suara.

- Data Streaming: Data kecil yang dikirim terus-menerus secara real-time dari ribuan sumber (misal: transaksi e-commerce atau log aktivitas klik).

## 2. Atribut dan Tipe Data

Atribut (disebut juga fitur atau variabel) adalah karakteristik yang mewakili objek data. Berdasarkan nilainya, tipe data atribut dibagi menjadi:

Kualitatif (Kategorikal):

- Nominal: Hanya berupa simbol atau nama tanpa peringkat (contoh: warna rambut, status pernikahan).

- Biner: Atribut nominal dengan hanya dua status (0/1 atau True/False). Ada yang simetris (gender) dan asimetris (hasil tes medis).

- Ordinal: Memiliki urutan atau peringkat, tetapi jarak antar nilainya tidak dapat diukur secara pasti (contoh: tingkat kepuasan, ukuran minuman Small/Medium/Large).

Kuantitatif (Numerik):

- Interval: Memiliki skala unit yang sama dan urutan, tetapi tidak punya titik nol absolut (contoh: suhu Celsius, tahun kalender).

- Rasio: Memiliki titik nol absolut, sehingga perbandingan (rasio) antar nilai bisa dihitung (contoh: berat badan, tinggi badan, jumlah kata).

## 3. Distribusi Data dan Statistik Deskriptif

Untuk memahami pola data, digunakan metode statistik:

- Distribusi Data: Paling umum adalah Distribusi Normal (Gaussian) yang berbentuk lonceng, ditentukan oleh Mean (pusat) dan Standar Deviasi (lebar sebaran).

- Kecenderungan Terpusat: Mengukur pusat data menggunakan Mean (rata-rata), Median (nilai tengah), dan Modus (nilai paling sering muncul).

- Sebaran Data: Mengukur seberapa jauh data tersebar menggunakan Rentang (Range), Kuartil, Variansi, dan Standar Deviasi.

- Skewness: Mengukur ketidaksimetrisan (kemencengan) distribusi data.

## 4. Mengukur Jarak Data (Similarity/Dissimilarity)

Pengukuran jarak sangat penting untuk algoritma clustering (pengelompokan). Beberapa metrik jarak untuk data numerik meliputi:

- Minkowski Distance: Formula umum untuk mengukur jarak.

- Manhattan Distance: Kasus khusus Minkowski (m=1), menghitung jarak berdasarkan lintasan tegak lurus (seperti blok kota).

- Euclidean Distance: Kasus khusus Minkowski (m=2), menghitung jarak garis lurus "burung terbang" antar dua titik.

- Cosine Similarity: Sering digunakan untuk mengukur kemiripan antar dokumen atau teks.

- Selain numerik, terdapat teknik khusus untuk mengukur jarak pada data bertipe Biner, Kategorikal, Ordinal, maupun Campuran.
