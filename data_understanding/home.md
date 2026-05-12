---
jupytext:
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Data-Understanding

Data Understanding merupakan tahap penting dalam metodologi CRISP-DM yang berfokus pada pengumpulan, eksplorasi, dan pemeriksaan kualitas data sebelum dilakukan proses pemodelan. Pada tahap ini, data tidak langsung digunakan untuk membuat model, tetapi dipahami terlebih dahulu karakteristik, struktur, tipe, serta potensi permasalahannya. Tujuan utamanya adalah memastikan bahwa data yang digunakan benar-benar relevan dan mampu menjawab permasalahan bisnis yang telah ditentukan pada tahap sebelumnya.

Tahapan dalam Data Understanding meliputi beberapa langkah utama. Pertama, mengumpulkan data awal, yaitu memperoleh data dari berbagai sumber seperti database, file Excel, sistem internal, atau API, serta memahami konteks data tersebut. Kedua, mendeskripsikan data, dengan memeriksa jumlah data, format, tipe data (numerik, kategorikal, tanggal), serta struktur tabel atau atributnya. Ketiga, mengeksplorasi data, menggunakan statistik deskriptif (mean, median, standar deviasi) dan visualisasi seperti grafik atau diagram untuk menemukan pola awal. Keempat, memverifikasi kualitas data, yaitu memeriksa adanya missing value, duplikasi, inkonsistensi, maupun outlier yang dapat memengaruhi hasil analisis.

Tujuan akhir dari tahap ini adalah memastikan data yang digunakan berkualitas tinggi, lengkap, konsisten, dan sesuai untuk proses analisis lebih lanjut. Data Understanding bertindak sebagai landasan analitik agar proses pemodelan tidak dilakukan secara terburu-buru, sehingga hasil yang diperoleh lebih akurat dan dapat dipercaya.

## Pentingnya Memahami Data

Memahami data sangat penting karena membantu dalam memilih teknik data mining yang tepat, meningkatkan akurasi model prediksi, serta menghindari kesalahan interpretasi. Tanpa pemahaman yang baik terhadap data, hasil analisis bisa menyesatkan dan sulit untuk ditindaklanjuti dalam pengambilan keputusan bisnis.

## Macam-Macam Data

Dalam proses data mining, terdapat berbagai jenis data yang perlu dipahami karakteristiknya, antara lain:

1. Data Terstruktur (Structured Data)
Data yang memiliki format tetap dan tersusun rapi dalam tabel, seperti database relasional.

2. Data Tidak Terstruktur (Unstructured Data)
Data yang tidak memiliki format tetap, seperti dokumen teks, email, atau posting media sosial.

3. Data Bahasa Alami (Natural Language Data)
Data dalam bentuk teks atau percakapan manusia yang biasanya dianalisis menggunakan teknik NLP (Natural Language Processing).

4. Data yang Dibangkitkan oleh Mesin (Machine-Generated Data)
Data yang dihasilkan secara otomatis oleh sistem atau perangkat, seperti log server atau sensor IoT.

5. Data Audio, Video, dan Citra
Data berbentuk suara, rekaman video, atau gambar yang memerlukan teknik khusus seperti computer vision atau speech recognition.

6. Data Streaming
Data yang mengalir secara real-time dan terus diperbarui, seperti data transaksi online atau data sensor waktu nyata.

7. Data Berbasis Graph (Graph-Based Data)
Data yang merepresentasikan hubungan antar entitas dalam bentuk node dan edge, seperti jaringan sosial atau relasi antar pengguna.

```{note}
Memahami jenis-jenis data ini membantu dalam menentukan metode analisis dan teknologi yang paling sesuai untuk digunakan.
```