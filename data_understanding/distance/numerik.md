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

## Numerik
Menghitung Jarak Data Numerik (Insurance Dataset)

| age | sex | bmi | children | smoker | region | charges |
|-----|-----|------|---|------|--------|------|----|
| 19 | female | 27.9 | 0 | yes | southwest | 16884924 |
| 18 | male | 33.77 | 1 | no | southwest | 17255523 |
| 28 | male | 33 | 3 | no | southwest | 4449462 |
| 33 | male | 22.705 | 0 | no | northwest | 2198447061 |
| 32 | male | 28.88 | 0 | no | northwest | 2198447061 |

Pada bagian ini dilakukan perhitungan jarak data numerik menggunakan dataset Medical Cost Personal Datasets yang diperoleh dari platform Kaggle. Dataset ini berisi data biaya asuransi kesehatan individu dengan beberapa atribut numerik dan kategorikal.

#### Struktur Dataset Insurance

Beberapa atribut dalam dataset ini adalah:

- age (numerik)

- sex (kategorikal)

- bmi (numerik)

- children (numerik)

- smoker (kategorikal)

- region (kategorikal)

- charges (numerik)

Untuk perhitungan jarak numerik, hanya atribut bertipe numerik yang digunakan, yaitu:

- age

- bmi

- children

- charges

Memilih Fitur Numerik

Menggunakan Python untuk memilih fitur numerik:

```{code-cell}
import pandas as pd
import numpy as np

df = pd.read_csv("insurance.csv")
df_numeric = df.select_dtypes(include=[np.number])
print(df_numeric.dtypes)
```

Output tipe data numerik:

```{code-cell}
age           int64
bmi         float64
children      int64
charges     float64
dtype: object
```