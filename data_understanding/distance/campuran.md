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

## Campuran

Mengukur Jarak pada Data Tipe Campuran

Dalam analisis data, sering kali kita tidak hanya berhadapan dengan data numerik saja, tetapi juga kombinasi antara numerik dan kategorikal. Dataset Insurance merupakan contoh nyata data campuran karena memuat informasi:

| Fitur    | Tipe Data   |
| -------- | ----------- |
| age      | Numerik     |
| bmi      | Numerik     |
| children | Numerik     |
| charges  | Numerik     |
| sex      | Kategorikal |
| smoker   | Kategorikal |
| region   | Kategorikal |

Jika kita ingin menghitung jarak antara dua nasabah, kita tidak dapat langsung menggunakan Euclidean atau Manhattan secara keseluruhan, karena atribut kategorikal tidak bisa dikurangi atau dipangkatkan.

### Rumus Umum Jarak Data Campuran

Jarak antara dua objek 𝑖 dan 𝑗 dengan 𝑝 atribut dihitung sebagai:

$$
d(i,j) =
\frac{
\sum_{f=1}^{p}
\delta_{ij}^{(f)} \, d_{ij}^{(f)}
}{
\sum_{f=1}^{p}
\delta_{ij}^{(f)}
}
$$

Rumus ini menghitung rata-rata kontribusi jarak setiap atribut.

### Perhitungan Atribut Numerik
Untuk atribut numerik, jarak dinormalisasi agar berada pada rentang [0,1]:
$$
d_{ij}^{(f)} =
\frac{
\left| x_{if} - x_{jf} \right|
}{
\max(x_f) - \min(x_f)
}
$$

Tujuan normalisasi:

- Mencegah atribut dengan nilai besar (misalnya charges) mendominasi atribut lain seperti age.

### Perhitungan Atribut Kategorikal

Untuk atribut kategorikal:

$$
d_{ij}^{(f)} =
\begin{cases}
0 & \text{jika } x_{if} = x_{jf} \\
1 & \text{jika } x_{if} \neq x_{jf}
\end{cases}
$$

Artinya:

- Jika kategori sama → tidak ada jarak (0)

- Jika berbeda → jarak maksimum (1)

### Contoh Perhitungan Dua Data Pertama

Misalkan dua data pertama:

| Fitur    | Data 1    | Data 2    |
| -------- | --------- | --------- |
| age      | 19        | 18        |
| bmi      | 27.9      | 33.77     |
| children | 0         | 1         |
| charges  | 16884.92  | 1725.55   |
| sex      | female    | male      |
| smoker   | yes       | no        |
| region   | southwest | southeast |

#### Contoh Perhitungan Numerik

Range age:

$$
\text{range}_{age} = 64 - 18 = 46
$$

Jarak age:

$$
d_{age} =
\frac{|19 - 18|}{46}
=
0.0217
$$

Contoh charges:

$$
d_{charges} =
\frac{|16884.92 - 1725.55|}
{62649}
=
0.241
$$

## Menghitung Jarak Total

Jika seluruh atribut berjumlah 7, maka jarak total:

$$
d(i,j) =
\frac{
d_{age} +
d_{bmi} +
d_{children} +
d_{charges} +
d_{sex} +
d_{smoker} +
d_{region}
}{7}
$$


### Implementasi Python

```{code-cell}
import pandas as pd
import numpy as np

df = pd.read_csv("../../insurance.csv")

data1 = df.iloc[0]
data2 = df.iloc[1]

num_cols = df.select_dtypes(include=[np.number]).columns
cat_cols = df.select_dtypes(exclude=[np.number]).columns

num_range = df[num_cols].max() - df[num_cols].min()
num_distance = np.sum(np.abs(data1[num_cols] - data2[num_cols]) / num_range)

cat_distance = np.sum(data1[cat_cols] != data2[cat_cols])

p = len(num_cols) + len(cat_cols)

mixed_distance = (num_distance + cat_distance) / p

print("Jarak Mixed:", mixed_distance)
```