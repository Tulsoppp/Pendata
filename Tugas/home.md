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


# Missing Values dan Normalisasi

Missing values atau data yang hilang merupakan kondisi ketika suatu nilai dalam dataset tidak tersedia atau tidak tercatat. Hal ini dapat terjadi karena berbagai faktor, seperti kesalahan saat proses input data, responden yang tidak memberikan jawaban pada saat pengumpulan data, ataupun gangguan teknis yang menyebabkan data tidak tersimpan dengan baik. Keberadaan missing values dalam dataset dapat mempengaruhi kualitas analisis karena dapat menimbulkan bias atau menghasilkan kesimpulan yang kurang akurat.

Untuk mengatasi masalah tersebut, missing values biasanya ditangani dengan metode **imputasi**, yaitu proses mengisi nilai yang hilang menggunakan nilai pengganti tertentu. Nilai pengganti tersebut dapat berupa **mean (rata-rata)**, **median**, **modus**, ataupun nilai hasil prediksi berdasarkan metode tertentu. Selain menggunakan imputasi, missing values juga dapat ditangani dengan cara **menghapus baris atau kolom** yang memiliki nilai kosong apabila jumlah data yang hilang tidak terlalu banyak.

Selain penanganan missing values, tahapan penting lain dalam proses pengolahan data adalah **normalisasi**. Normalisasi merupakan proses mengubah atau menskalakan nilai data ke dalam rentang tertentu sehingga setiap atribut memiliki skala yang sama atau sebanding. Proses ini penting karena dalam banyak kasus, dataset memiliki atribut dengan rentang nilai yang sangat berbeda. Jika tidak dinormalisasi, atribut dengan nilai yang lebih besar dapat memberikan pengaruh yang lebih dominan dalam proses analisis atau pemodelan.

Dengan melakukan normalisasi, setiap fitur dalam dataset akan berada dalam skala yang lebih seimbang sehingga proses analisis data atau penerapan algoritma machine learning dapat menghasilkan hasil yang lebih akurat dan stabil.

Beberapa metode yang sering digunakan untuk melakukan normalisasi data antara lain:

- **Min-Max Normalization**, yaitu metode normalisasi yang mengubah nilai data ke dalam rentang tertentu, biasanya antara 0 sampai 1.
- **Z-Score Normalization**, yaitu metode normalisasi yang menyesuaikan nilai data berdasarkan rata-rata dan standar deviasi sehingga menghasilkan distribusi data dengan mean 0 dan standar deviasi 1.
- **Decimal Scaling**, yaitu metode normalisasi yang dilakukan dengan memindahkan posisi desimal pada nilai data hingga nilai tersebut berada dalam rentang tertentu.