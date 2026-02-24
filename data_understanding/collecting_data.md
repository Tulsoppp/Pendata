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

# Collecting Data

Collecting Data adalah proses mengumpulkan data atau informasi yang relevan untuk menjawab suatu pertanyaan, melakukan analisis, atau mendukung pengambilan keputusan. Tahap ini merupakan langkah awal dalam penelitian maupun penambangan data, di mana informasi dikumpulkan, fakta dicatat, angka dihimpun, serta berbagai kejadian didokumentasikan secara sistematis. Proses pengumpulan data yang baik akan sangat menentukan kualitas analisis pada tahap selanjutnya.

## Tujuan Collecting Data

Tujuan utama dari pengumpulan data antara lain untuk mendapatkan informasi yang akurat dan dapat dipercaya, menjawab pertanyaan penelitian, menguji hipotesis yang telah dirumuskan, serta mendukung proses pengambilan keputusan berbasis data. Tanpa data yang relevan dan berkualitas, hasil analisis bisa menjadi tidak valid atau menyesatkan.

## Contoh dalam Penambangan Data

Dalam konteks penambangan data, proses collecting data dapat dilakukan dengan berbagai cara, seperti mengambil data penjualan dari database perusahaan, mengunduh dataset dari internet, mengumpulkan data pelanggan melalui survei, atau melakukan scraping data dari website tertentu. Sumber data dapat berasal dari sistem internal maupun sumber eksternal.

## Studi Kasus

Pada studi kasus ini digunakan Iris Flower Dataset yang diperoleh dari platform Kaggle. 

https://www.kaggle.com/datasets/arshid/iris-flower-dataset

Dataset ini merupakan salah satu dataset klasik yang sering digunakan dalam pembelajaran data mining dan machine learning untuk klasifikasi.

```{code-cell}
import pandas as pd
df = pd.read_csv("../IRIS.csv")
df.head(150)
```

Dataset tersebut memiliki total 150 data (records) dengan 5 fitur (atribut), yaitu:

1. Sepal Length
2. Sepal Width
3. Petal Length
4. Petal Width
5. Species (kelas bunga)

Dataset ini terdiri dari tiga jenis bunga iris, yaitu:

- Iris setosa
- Iris versicolor
- Iris virginica

```{note}
Masing-masing jenis memiliki 50 data, sehingga total keseluruhan menjadi 150 data. Dataset ini sangat cocok digunakan untuk studi klasifikasi karena memiliki fitur numerik yang jelas serta label kelas yang sudah tersedia.
```