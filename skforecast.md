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

# skforecast

Sumber: [skforecast.org/0.15.1/user_guides/explainability.html](https://skforecast.org/0.15.1/user_guides/explainability.html)

---

## Import Library

```{code-cell}
# Libraries
# ==============================================================================
import pandas as pd
import matplotlib.pyplot as plt
import shap
from sklearn.inspection import permutation_importance
from sklearn.inspection import PartialDependenceDisplay
from lightgbm import LGBMRegressor
from skforecast.datasets import fetch_dataset
from skforecast.recursive import ForecasterRecursive

shap.initjs()
```

Bagian ini memuat seluruh dependensi yang dibutuhkan sepanjang analisis. `pandas` dipakai untuk manipulasi data tabular, sedangkan `matplotlib` menyediakan fondasi untuk visualisasi statis. Library `shap` adalah inti dari analisis eksplainabilitas — ia menyediakan alat untuk mengurai "alasan di balik prediksi" secara matematis. `sklearn.inspection` menyediakan dua metode diagnostik model: `permutation_importance` untuk mengukur kontribusi fitur melalui pengacakan, dan `PartialDependenceDisplay` untuk memvisualisasikan hubungan rata-rata antara fitur dan target.

`LGBMRegressor` adalah implementasi *gradient boosting* berbasis histogram dari LightGBM yang terkenal cepat dan efisien secara memori. `ForecasterRecursive` dari skforecast adalah wrapper yang mengubah model regresi biasa menjadi model forecasting time series multi-step dengan pendekatan rekursif.

Pemanggilan `shap.initjs()` di awal notebook bukan sekadar formalitas — ia menyuntikkan JavaScript yang diperlukan ke dalam kernel Jupyter sehingga visualisasi interaktif seperti *force plot* dapat di-render langsung di dalam sel output. Tanpa pemanggilan ini, force plot akan gagal ditampilkan meskipun komputasinya berhasil.

---

## Load dan Siapkan Data

Data yang digunakan diperoleh dari paket R `tsibbledata`. Dataset berisi **52.608 record** dengan frekuensi setengah-jam dan 5 kolom:

| Kolom | Deskripsi |
|---|---|
| `Time` | Timestamp pencatatan (tanggal + waktu) |
| `Date` | Tanggal saja |
| `Demand` | Permintaan listrik dalam megawatt (MW) |
| `Temperature` | Suhu udara di Melbourne, ibu kota Victoria |
| `Holiday` | Penanda hari libur nasional (boolean) |

```{code-cell}
# Download data
# ==============================================================================
data = fetch_dataset(name="vic_electricity")
data.head(3)
```

```{code-cell}
# Aggregation to daily frequency
# ==============================================================================
data = data.resample('D').agg({'Demand': 'sum', 'Temperature': 'mean'})
data.head(3)
```

Karena granularitas setengah jam terlalu detail untuk forecasting harian, data diagregasi ke frekuensi harian. Logika agregasinya berbeda per kolom sesuai maknanya: `Demand` dijumlahkan karena secara fisik permintaan listrik harian adalah akumulasi dari seluruh interval dalam sehari. Sementara `Temperature` dirata-ratakan karena suhu adalah besaran kontinu yang lebih bermakna sebagai nilai tengah, bukan total kumulatif.

```{code-cell}
# Split train-test
# ==============================================================================
data_train = data.loc[: '2014-12-21']
data_test  = data.loc['2014-12-22':]

print(f"Tanggal train : {data_train.index.min()} — {data_train.index.max()}")
print(f"Tanggal test  : {data_test.index.min()} — {data_test.index.max()}")
print(f"Shape train   : {data_train.shape}")
print(f"Shape test    : {data_test.shape}")
```

Data dipisah menjadi dua partisi berdasarkan tanggal. Pemisahan pada time series **harus selalu menggunakan urutan kronologis** — tidak boleh diacak seperti pada data tabular biasa — karena model dilatih pada masa lalu dan diuji pada masa depan. Mencampurnya akan mengakibatkan *data leakage* yang membuat evaluasi model menjadi tidak valid.

---

## Buat dan Latih Forecaster

Model forecasting dibuat untuk memprediksi permintaan energi menggunakan 7 nilai masa lalu (seminggu terakhir) dan suhu sebagai variabel eksogen.

```{code-cell}
# Create a recursive multi-step forecaster (ForecasterRecursive)
# ==============================================================================
forecaster = ForecasterRecursive(
    estimator = LGBMRegressor(random_state=123, verbose=-1),
    lags      = 7
)

forecaster.fit(
    y    = data_train['Demand'],
    exog = data_train['Temperature']
)

forecaster
```

`ForecasterRecursive` mengadopsi strategi *recursive multi-step forecasting*: model dilatih sekali untuk memprediksi satu langkah ke depan, lalu prediksi sebelumnya dimasukkan kembali sebagai input lag untuk langkah berikutnya. Pendekatan ini berbeda dari strategi *direct* yang melatih model terpisah per horizon prediksi.

Penjelasan parameter:

- **`lags=7`** — model membangun fitur dari 7 nilai historis target (t-1 hingga t-7), setara dengan jangkauan satu minggu ke belakang, cukup untuk menangkap pola mingguan seperti perbedaan konsumsi weekday vs weekend.
- **`exog=Temperature`** — suhu dimasukkan sebagai variabel eksogen, yaitu fitur tambahan yang bukan bagian dari seri target itu sendiri. Ini merupakan informasi eksternal yang membantu model memahami pola yang dipengaruhi kondisi cuaca.
- **`LGBMRegressor`** — model gradient boosting yang mampu menangkap hubungan non-linear secara efisien. `verbose=-1` mematikan log pelatihan agar output notebook tetap bersih.

---

## Feature Importance

Feature importance adalah alat diagnostik untuk memahami fitur mana yang paling berkontribusi pada kemampuan prediksi model, membantu memilih kumpulan fitur terbaik, dan mengidentifikasi perilaku model. Penting untuk dipahami batasannya: **feature importance tidak mengimplikasikan kausalitas** — pentingnya sebuah fitur secara statistik tidak berarti fitur tersebut secara sebab-akibat mempengaruhi target.

### a. Model-Specific Feature Importance

Cara menghitung feature importance bergantung pada jenis model:
- **Decision tree-based** (Random Forest, Gradient Boosting): menggunakan *mean decrease impurity (MDI)* — seberapa besar rata-rata penurunan ketidakmurnian di setiap node split yang melibatkan fitur tersebut.
- **Linear model** (Ridge, Lasso): menggunakan koefisien atau koefisien yang dinormalisasi sebagai ukuran kepentingan.

Metode `get_feature_importances()` adalah shortcut yang mengakses atribut `feature_importances_` dari `LGBMRegressor` yang tersimpan di dalam forecaster, lalu mengemasnya dalam format DataFrame.

```{code-cell}
# Feature importances
# ==============================================================================
feature_importances = forecaster.get_feature_importances()
feature_importances
```

```{code-cell}
# Plot feature importances
# ==============================================================================
fig, ax = plt.subplots(figsize=(5, 4))
feature_importances.sort_values('importance', ascending=True).plot(
    x      = 'feature',
    y      = 'importance',
    kind   = 'barh',
    ax     = ax,
    legend = False
)
ax.set_title('Feature Importance (LightGBM — mean decrease impurity)')
ax.set_xlabel('Importance')
ax.set_ylabel('Feature')
plt.tight_layout()
plt.show()
```

Visualisasi bar horizontal memudahkan perbandingan antar fitur. Dengan sorting ascending dan orientasi horizontal, fitur terpenting akan muncul di atas grafik secara natural.

### b. Permutation Importance

Berbeda dari MDI yang dihitung selama proses pelatihan, permutation importance dihitung **setelah** model dilatih dengan cara mengacak nilai satu fitur pada saat evaluasi. Logikanya: jika mengacak nilai fitur X menyebabkan performa model turun drastis, maka fitur X sangat penting bagi model. Sebaliknya, jika performa tidak berubah, fitur tersebut bisa dianggap redundan. Teknik ini sangat berguna untuk model non-linear atau *opaque* karena bersifat model-agnostic.

Untuk menerapkan permutation importance pada skforecast, diperlukan **matriks training** yang sama dengan yang digunakan model saat dilatih. Matriks ini diperoleh dengan metode `create_train_X_y()`.

```{code-cell}
# Training matrices used by the forecaster to fit the internal regressor
# ==============================================================================
X_train, y_train = forecaster.create_train_X_y(
    y    = data_train['Demand'],
    exog = data_train['Temperature']
)

display(X_train.head(3))  # Features (lag_1 ... lag_7, Temperature)
display(y_train.head(3))  # Target
```

```{code-cell}
# Permutation importance
# ==============================================================================
result = permutation_importance(
    estimator    = forecaster.estimator,
    X            = X_train,
    y            = y_train,
    n_repeats    = 10,
    random_state = 123,
    n_jobs       = -1
)

permutation_imp_df = pd.DataFrame({
    'feature'         : X_train.columns,
    'importance_mean' : result.importances_mean,
    'importance_std'  : result.importances_std
}).sort_values('importance_mean', ascending=False)

permutation_imp_df
```

Parameter `n_repeats=10` berarti pengacakan dilakukan 10 kali untuk setiap fitur, menghasilkan distribusi penurunan performa — bukan hanya satu nilai tunggal. Inilah yang kemudian divisualisasikan sebagai **boxplot**, bukan bar chart biasa, sehingga kita bisa melihat stabilitas dan konsistensi kepentingan tiap fitur.

```{code-cell}
# Plot permutation importance
# ==============================================================================
sorted_idx = result.importances_mean.argsort()

fig, ax = plt.subplots(figsize=(6, 5))
ax.boxplot(
    result.importances[sorted_idx].T,
    vert   = False,
    labels = X_train.columns[sorted_idx]
)
ax.set_title('Permutation Feature Importance')
ax.set_xlabel('Penurunan performa (skor model)')
ax.axvline(x=0, color='grey', linestyle='--')
plt.tight_layout()
plt.show()
```

---

## Partial Dependence Plot (PDP)

PDP menjawab pertanyaan yang berbeda dari feature importance: bukan *seberapa penting* sebuah fitur, melainkan ***bagaimana bentuk pengaruhnya*** terhadap prediksi. PDP menghitung rata-rata prediksi model ketika satu fitur divariasikan di seluruh rentangnya, sementara semua fitur lain dibekukan pada nilai rata-ratanya (dimarginalkan melalui ekspektasi).

```{code-cell}
# Partial Dependence Plots
# ==============================================================================
features_to_plot = ['lag_1', 'lag_2', 'lag_3', 'lag_4',
                    'lag_5', 'lag_6', 'lag_7', 'Temperature']

fig, axes = plt.subplots(
    nrows   = 2,
    ncols   = 4,
    figsize = (14, 7),
    sharey  = False
)
axes = axes.flatten()

for i, feature in enumerate(features_to_plot):
    PartialDependenceDisplay.from_estimator(
        estimator  = forecaster.estimator,
        X          = X_train,
        features   = [feature],
        kind       = 'average',
        ax         = axes[i]
    )
    axes[i].set_title(f'PDP — {feature}')

plt.suptitle('Partial Dependence Plots', fontsize=14, y=1.02)
plt.tight_layout()
plt.show()
```

Cara membaca kurva PDP:

| Bentuk Kurva | Interpretasi |
|---|---|
| Naik monoton | Nilai fitur lebih tinggi mendorong prediksi Demand ke atas |
| Turun monoton | Nilai fitur lebih tinggi mendorong prediksi ke bawah |
| Hampir datar | Fitur tidak terlalu menggerakkan prediksi pada rentang tersebut |
| Non-linear (U, lengkung) | Terdapat hubungan kompleks yang tidak bisa ditangkap regresi linear |

Kelemahan PDP adalah ia menampilkan efek *rata-rata* dan bisa menyembunyikan heterogenitas — misalnya, untuk subgrup tertentu pengaruh fitur bisa berlawanan arah. Inilah salah satu motivasi menggunakan SHAP yang bekerja di level observasi individual.

---

## SHAP Values

SHAP (SHapley Additive exPlanations) berakar dari teori permainan kooperatif dalam matematika. Ide dasarnya adalah membagi "kredit" prediksi secara adil kepada seluruh fitur, sebagaimana pembagian hasil dalam sebuah koalisi pemain. Nilai SHAP untuk fitur ke-i pada observasi tertentu menyatakan: **berapa kontribusi fitur tersebut terhadap selisih antara prediksi observasi ini dan rata-rata prediksi model secara keseluruhan**.

SHAP melayani dua tujuan utama:

- **Global Interpretability** — mengidentifikasi fitur mana yang paling berpengaruh pada model secara keseluruhan dengan merata-ratakan SHAP values di seluruh dataset
- **Local Interpretability** — menjelaskan prediksi individual dengan menunjukkan seberapa besar kontribusi tiap fitur terhadap output spesifik satu observasi

Implementasi Python SHAP menyediakan beberapa *explainer* yang disesuaikan dengan arsitektur model:

| Explainer | Cocok Untuk | Catatan |
|---|---|---|
| `TreeExplainer` | LightGBM, XGBoost, Random Forest | Sangat cepat, hasil eksak |
| `LinearExplainer` | Ridge, Lasso, Logistic Regression | Efisien untuk model linear |
| `KernelExplainer` | Semua model | Paling lambat, berbasis sampling |

### a. SHAP pada Data Training

```{code-cell}
# Training matrices used by the forecaster to fit the internal regressor
# ==============================================================================
X_train, y_train = forecaster.create_train_X_y(
    y    = data_train['Demand'],
    exog = data_train['Temperature']
)

display(X_train.head(3))
display(y_train.head(3))
```

```{code-cell}
# Create SHAP explainer
# ==============================================================================
explainer   = shap.TreeExplainer(forecaster.estimator)
shap_values = explainer.shap_values(X_train)

print(f"Shape SHAP values  : {shap_values.shape}")
print(f"Expected value     : {explainer.expected_value:.2f}")
print(f"Shape X_train      : {X_train.shape}")
```

`TreeExplainer` mengeksploitasi struktur pohon keputusan untuk menghitung SHAP values secara eksak dan efisien — jauh lebih cepat dibanding `KernelExplainer` yang berbasis sampling. Output `shap_values` adalah matriks berukuran sama dengan X_train, di mana setiap elemen merepresentasikan kontribusi fitur j terhadap prediksi observasi i.

```{code-cell}
# SHAP summary plot — bar (global feature importance)
# ==============================================================================
shap.summary_plot(
    shap_values,
    X_train,
    plot_type = 'bar',
    show      = True
)
```

**Summary Plot (Bar)** menampilkan rata-rata absolut SHAP values per fitur. Ini setara dengan feature importance global namun lebih andal karena memperhitungkan arah dan distribusi kontribusi di seluruh observasi.

```{code-cell}
# SHAP summary plot — dot (distribusi kontribusi per observasi)
# ==============================================================================
shap.summary_plot(
    shap_values,
    X_train,
    show = True
)
```

**Summary Plot (Dot)** lebih informatif dari bar chart karena menampilkan distribusi lengkap. Setiap titik mewakili satu observasi. Posisi horizontal menunjukkan besar dan arah kontribusi SHAP — ke kanan berarti mendorong prediksi naik, ke kiri berarti mendorong prediksi turun. Warna titik menunjukkan nilai fitur (merah = tinggi, biru = rendah), sehingga kita bisa melihat sekaligus: *fitur ini punya nilai tinggi, dan ia mendorong prediksi ke arah mana*.

```{code-cell}
# SHAP dependence plot untuk fitur Temperature
# ==============================================================================
fig, ax = plt.subplots(figsize=(7, 4))
shap.dependence_plot(
    "Temperature",
    shap_values,
    X_train,
    ax = ax
)
plt.title('SHAP Dependence Plot — Temperature')
plt.tight_layout()
plt.show()
```

**SHAP Dependence Plot** memperlihatkan hubungan antara nilai satu fitur (sumbu X) dengan nilai SHAP-nya (sumbu Y). Berbeda dari PDP yang menampilkan rata-rata, di sini setiap titik adalah satu observasi individu sehingga variasi dan efek interaksi antar fitur juga terlihat. Titik-titik diwarnai berdasarkan fitur lain yang paling berinteraksi secara otomatis.

```{code-cell}
# SHAP force plot — penjelasan untuk satu observasi (observasi pertama)
# ==============================================================================
shap.force_plot(
    base_value  = explainer.expected_value,
    shap_values = shap_values[0, :],
    features    = X_train.iloc[0, :]
)
```

```{code-cell}
# ==============================================================================
shap.force_plot(explainer.expected_value, shap_values[:200, :], X_train.iloc[:200, :])
```

**SHAP Force Plot** menampilkan penjelasan interaktif untuk satu prediksi individual:

| Elemen | Makna |
|---|---|
| Angka di tengah | Nilai prediksi untuk observasi tersebut |
| `base value` | Rata-rata prediksi model di seluruh training data (`expected_value`) |
| Panah / blok merah | Fitur yang mendorong prediksi **naik** dari baseline |
| Panah / blok biru | Fitur yang mendorong prediksi **turun** dari baseline |
| Lebar blok | Besarnya kontribusi fitur tersebut |

### b. SHAP pada Data Prediksi

Selain menjelaskan perilaku model saat training, SHAP juga dapat digunakan untuk menjelaskan nilai yang di-forecast. Caranya adalah menggunakan matriks input yang dipakai secara internal oleh metode `predict()`.

```{code-cell}
# Predict
# ==============================================================================
predictions = forecaster.predict(
    steps = 10,
    exog  = data_test['Temperature']
)

predictions
```

```{code-cell}
# Create input matrix for predict method
# ==============================================================================
X_predict = forecaster.create_predict_X(
    steps = 10,
    exog  = data_test['Temperature']
)

X_predict
```

`create_predict_X()` mengembalikan matriks fitur yang persis sama dengan yang digunakan `forecaster.predict()` secara internal — berisi lag values yang diisi secara rekursif untuk setiap langkah ke depan, ditambah variabel eksogen untuk setiap langkah prediksi.

```{code-cell}
# SHAP values untuk data prediksi
# ==============================================================================
shap_values_pred = explainer.shap_values(X_predict)

print(f"Shape SHAP values prediksi : {shap_values_pred.shape}")
```

```{code-cell}
# SHAP summary plot untuk data prediksi
# ==============================================================================
shap.summary_plot(
    shap_values_pred,
    X_predict,
    show = True
)
```

```{code-cell}
# SHAP force plot untuk langkah prediksi pertama
# ==============================================================================
shap.force_plot(
    base_value  = explainer.expected_value,
    shap_values = shap_values_pred[0, :],
    features    = X_predict.iloc[0, :]
)
```

Dengan SHAP pada data prediksi, kita dapat menjawab pertanyaan konkret: **"Mengapa model memprediksi nilai X pada tanggal Y?"** Setiap lag dan variabel eksogen yang digunakan forecaster untuk menghasilkan prediksi dapat dijelaskan kontribusinya secara individual — memberikan transparansi penuh terhadap output model.

---

## Kesimpulan

### 1. Analisis Prediksi

Model memprediksi total permintaan listrik harian (Demand) di Victoria, Australia, dalam satuan megawatt (MW). Target prediksi adalah satu nilai agregat per hari yang merepresentasikan keseluruhan konsumsi di jaringan listrik negara bagian tersebut.

### 2. Struktur Data Training

Setelah proses `resample('D')` dan `create_train_X_y()`, setiap baris dalam matriks training merepresentasikan satu hari prediksi:

| lag_1 | lag_2 | lag_3 | lag_4 | lag_5 | lag_6 | lag_7 | Temperature | Demand |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 208000 | 210000 | 198000 | 205000 | 215000 | 200000 | 212000 | 22.1 | 195000 |
| 195000 | 208000 | 210000 | 198000 | 205000 | 215000 | 200000 | 18.5 | 221000 |

**Fitur Input (X):**
- `lag_1` hingga `lag_7` — nilai Demand dari 1 hingga 7 hari sebelumnya, memberi model "memori" jangka pendek tentang pola konsumsi terkini
- `Temperature` — suhu rata-rata pada hari yang sedang diprediksi, berfungsi sebagai konteks eksternal (eksogen)

**Target Output (y):**
- `Demand` — total permintaan listrik hari tersebut yang ingin diperkirakan oleh model

### 3. Konsep Lag

Lag adalah mekanisme di mana nilai historis dari variabel target itu sendiri dijadikan fitur prediktor. Teknik ini merupakan inti dari *autoregressive feature engineering* dalam forecasting time series. Dengan `lags=7`, model memiliki "jendela pandang" ke 7 hari terakhir — cukup untuk menangkap pola mingguan seperti perbedaan konsumsi hari kerja vs akhir pekan. Semakin panjang lag yang digunakan, semakin jauh ke belakang model bisa "melihat", namun juga semakin besar dimensi fitur yang harus dikelola, yang berpotensi meningkatkan kompleksitas dan risiko overfitting.