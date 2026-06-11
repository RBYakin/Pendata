# ANALISIS EXPLAINABILITY TIME SERIES FORECASTING

---

## 1. Topik Analisis Prediksi
Analisis prediksi dalam studi kasus ini menggunakan dataset `vic_electricity` dari pustaka **Skforecast (Versi 0.15.1)**. Fokus utama dari analisis ini adalah **meramal total permintaan energi listrik harian (Daily Electricity Demand)** di wilayah Victoria, Australia. 

Selain memprediksi nilai konsumsi listrik di masa mendatang, tujuan inti dari eksperimen ini adalah menerapkan aspek ***Explainable AI* (XAI)** menggunakan metode **SHAP** dan **Partial Dependence Plot (PDP)**. Melalui pendekatan ini, kita dapat membongkar model "kotak hitam" (*black box*) untuk memahami secara logis alasan di balik keputusan model: fitur masa lalu mana yang paling dominan serta bagaimana dampak fluktuasi suhu udara harian dalam memengaruhi lonjakan beban listrik masyarakat.

---

## 2. Struktur Data Training (Input dan Output)
Sebelum data latih dimasukkan ke dalam algoritma regresi pohon keputusan `LGBMRegressor` (LightGBM), data deret waktu (*time series*) diubah terlebih dahulu menjadi format matriks pembelajaran terpantau (*supervised learning*) melalui fungsi internal `create_train_X_y()`.

### A. Input (Features / Prediktor)
* **Lags (`lag_1` sampai `lag_7`):** Nilai historis akumulasi permintaan listrik dari 1 hingga 7 hari sebelumnya.
* **Exogenous Variable (`Temperature`):** Faktor eksternal berupa rata-rata suhu udara harian pada hari yang bersangkutan.

### B. Output (Target)
* **Demand ($t+1$):** Total nilai permintaan energi listrik harian pada langkah waktu berikutnya yang menjadi target prediksi model.

![alt text](3_data.png)

---

## 3. Konsep Lag dalam Time Series Forecasting
Dalam pemodelan deret waktu, **Lag** adalah representasi nilai variabel target pada langkah waktu sebelumnya (kemunduran waktu). Karena pemrosesan data di-resample ke tingkat harian (`'D'`), maka definisinya adalah:
* **Lag 1:** Total penggunaan listrik 1 hari yang lalu.
* **Lag 2:** Total penggunaan listrik 2 hari yang lalu.
* **Lag 7:** Total penggunaan listrik 7 hari yang lalu (pola mingguan pada hari yang sama).

> 💡 **Analogi:** Jika kita ingin memprediksi omset warung pada hari ini, salah satu prediktor paling kuat adalah mengetahui berapa omset warung tersebut kemarin (Lag 1) dan bagaimana tren omset pada hari yang sama di minggu lalu (Lag 7). Nilai masa lalu inilah yang menuntun arah prediksi masa depan.

---

## 4. Alur Proses Analisis Kasus
Proses analisis dilakukan melalui tahapan komparatif terstruktur sebagai berikut:

### Tahap 1: Pra-pemrosesan & Pemisahan Data
Data mentah di-resample ke skala harian, di mana nilai `Demand` diakumulasikan (`sum`) dan `Temperature` dirata-rata (`mean`). Data dibagi secara kronologis: `data_train` (sampai 21 Desember 2014) dan `data_test` (mulai 22 Desember 2014).

### Tahap 2: Inisialisasi dan Pelatihan Model
Menggunakan kelas `ForecasterRecursive` dengan model dasar `LGBMRegressor` dari LightGBM. Parameter auto-regresif diatur sejauh `lags=7` dengan menyertakan suhu harian sebagai fitur eksogen (`exog`).

### Tahap 3: Ekstraksi Feature Importance Global
Pentingnya fitur dievaluasi menggunakan dua cara:
1. **Gini Importance (`get_feature_importances()`):** Menghitung seberapa sering suatu fitur dipilih untuk memisahkan cabang pohon keputusan.
2. **Permutation Importance:** Mengukur penurunan skor performa model secara acak pada fitur tertentu. Fitur yang paling merusak akurasi saat nilainya diacak dinilai sebagai fitur paling penting.

![alt text](tabel_ringkasan.png)

### Tahap 4: Analisis Nilai SHAP (Shapley Additive exPlanations)
Menerapkan `shap.TreeExplainer` untuk melihat kontribusi linear maupun non-linear setiap fitur secara aditif:
* **SHAP Summary Plot:** Menampilkan sebaran titik kontribusi. Warna merah menunjukkan nilai fitur tinggi, biru menunjukkan nilai fitur rendah. Dari grafik ini, kontribusi fitur `Temperature` dan `lag_1` terlihat paling mendominasi.

![alt text](summary_plot.png)

* **SHAP Local Force Plot:** Membedah kontribusi individual pada satu sampel baris data (observasi pertama) untuk melihat fitur mana yang mendorong nilai prediksi ke atas (zona merah) atau menurunkannya ke bawah (zona biru).

![alt text](force_plot.png)

* **SHAP Bar Plot (200 Observasi):** Menghitung nilai absolut rata-rata kontribusi SHAP pada 200 data pertama guna memvalidasi kepentingan fitur secara masal tanpa merusak batas rendering matplotlib.

![alt text](observasi.png)

### Tahap 5: Partial Dependence Plot (PDP)
Menggunakan `PartialDependenceDisplay` untuk melihat hubungan marjinal antara target dengan fitur `Temperature` dan `lag_1`. Pada fitur suhu, grafik akan menunjukkan karakteristik lengkungan non-linear berbentuk huruf **"U"**. Hal ini membuktikan interpretasi fisik bahwa beban permintaan listrik akan melonjak tinggi baik pada saat suhu udara sangat dingin (penggunaan pemanas) maupun saat suhu udara sangat panas (penggunaan AC).

![alt text](dependence.png)

---

## 5. Kode Program Utuh (Bebas Error)
Berikut adalah kode Python final yang digunakan untuk mereproduksi seluruh analisis di atas:

```python
import pandas as pd
import matplotlib.pyplot as plt
import shap
from sklearn.inspection import permutation_importance
from sklearn.inspection import PartialDependenceDisplay
from lightgbm import LGBMRegressor
from skforecast.datasets import fetch_dataset
from skforecast.recursive import ForecasterRecursive

# 1. Unduh Data
data = fetch_dataset(name="vic_electricity")
print("--- Data Mentah (3 Baris Pertama) ---")
display(data.head(3))

# 2. Agregasi ke Frekuensi Harian ('D')
data = data.resample('D').agg({'Demand': 'sum', 'Temperature': 'mean'})
print("\n--- Data Harian Setelah Agregasi ---")
display(data.head(3))

# 3. Pisahkan Data Latih (Train) dan Data Uji (Test)
data_train = data.loc[:'2014-12-21']
data_test = data.loc['2014-12-22':]

# 4. Buat Peramalan Multi-Langkah Rekursif (ForecasterRecursive)
forecaster = ForecasterRecursive(
    estimator = LGBMRegressor(random_state=123, verbose=-1),
    lags      = 7
)
forecaster.fit(
    y    = data_train['Demand'], 
    exog = data_train['Temperature']
)
print("\n--- Struktur Objek Forecaster ---")
display(forecaster)

# 5. Prediktor Penting (Gini Importance Bawaan Model)
print("\n--- Feature Importances Bawaan Model ---")
display(forecaster.get_feature_importances())

# 6. Buat Matriks Pelatihan Supervised Learning
X_train, y_train = forecaster.create_train_X_y(
    y    = data_train['Demand'], 
    exog = data_train['Temperature']
)
print("\n--- Matriks Input Training (X_train) ---")
display(X_train.head(3))
print("\n--- Matriks Target Training (y_train) ---")
display(y_train.head(3))

# 7. Analisis SHAP (Global & Local Explainability)
shap.initjs()
explainer = shap.TreeExplainer(forecaster.estimator)
shap_values = explainer.shap_values(X_train)

# Plot ringkasan dampak fitur global (Beeswarm plot)
plt.figure(figsize=(8, 5))
shap.summary_plot(shap_values, X_train, show=False)
plt.title("SHAP Summary Plot", fontsize=14)
plt.tight_layout()
plt.show()

# Force Plot untuk SATU observasi pertama (lokal)
plt.figure(figsize=(12, 3))
shap.force_plot(explainer.expected_value, shap_values[0, :], X_train.iloc[0, :], matplotlib=True, show=False)
plt.title("SHAP Force Plot - First Observation", fontsize=12, pad=20)
plt.show()

# Plot tipe 'bar' untuk melihat kontribusi rata-rata dari 200 observasi pertama secara masal
plt.figure(figsize=(10, 5))
shap.summary_plot(shap_values[:200, :], X_train.iloc[:200, :], plot_type="bar", show=False)
plt.title("SHAP Feature Importance - First 200 Observations", fontsize=12)
plt.tight_layout()
plt.show()

# Dependence Plot khusus untuk fitur Suhu (Temperature)
fig, ax = plt.subplots(figsize=(7, 4))
shap.dependence_plot("Temperature", shap_values, X_train, ax=ax, show=False)
plt.title("SHAP Dependence Plot for Temperature", fontsize=12)
plt.show()

# 8. Melakukan Prediksi / Peramalan (Forecasting)
prediksi = forecaster.predict(steps=10, exog=data_test['Temperature'].iloc[:10])
print("\n--- Hasil Prediksi 10 Langkah ke Depan ---")
display(prediksi)

# 9. Buat Matriks Input untuk Metode Prediksi (X_predict)
X_predict = forecaster.create_predict_X(steps=10, exog=data_test['Temperature'].iloc[:10])
print("\n--- Matriks Fitur Input untuk Prediksi (X_predict) ---")
display(X_predict)

# 10. SHAP Force Plot untuk Prediksi Tanggal Tertentu
predicted_date = '2014-12-22'
iloc_predicted_date = X_predict.index.get_loc(predicted_date)
shap_values_predict = explainer.shap_values(X_predict)

plt.figure(figsize=(12, 3))
shap.force_plot(
    explainer.expected_value, 
    shap_values_predict[iloc_predicted_date, :], 
    X_predict.iloc[iloc_predicted_date, :],
    matplotlib=True,
    show=False
)
plt.title(f"SHAP Force Plot for Predicted Date: {predicted_date}", fontsize=12, pad=20)
plt.show()

# 11. Analisis Permutation Importance (Global)
r = permutation_importance(
    estimator    = forecaster.estimator,
    X            = X_train,
    y            = y_train,
    n_repeats    = 3,
    max_samples  = 0.5,
    random_state = 123
)
importances = pd.DataFrame({
    'feature': X_train.columns,
    'mean_importance': r.importances_mean,
    'std_importance': r.importances_std
}).sort_values('mean_importance', ascending=False)
print("\n--- Hasil Tabel Permutation Importance ---")
display(importances)

# 12. Scikit-learn Partial Dependence Plots (PDP)
fig, ax = plt.subplots(figsize=(9, 4))
display_pdp = PartialDependenceDisplay.from_estimator(
    estimator = forecaster.estimator,
    X         = X_train,
    features  = ["Temperature", "lag_1"],
    kind      = 'both',
    ax        = ax
)
ax.set_title("Partial Dependence Plot (Temperature & Lag 1)")
fig.tight_layout()
plt.show()

```

---
## 6. Kesimpulan dan Interpretasi Model

Berdasarkan seluruh rangkaian analisis *Explainable AI* (XAI) yang telah dilakukan terhadap model peramalan permintaan listrik (`LGBMRegressor` melalui `Skforecast`), beberapa poin kritis yang dapat disimpulkan adalah:

### A. Pengertian Metode Interpretabilitas yang Digunakan
* **Gini Importance & Permutation Importance:** Digunakan untuk mengukur tingkat kepentingan fitur secara global. Bedanya, Gini melihat struktur internal pohon keputusan, sedangkan Permutation menguji seberapa bergantung model pada suatu fitur dengan cara mengacak nilainya secara paksa dan melihat dampak penurunan performanya.
* **SHAP (Shapley Additive exPlanations):** Pendekatan berbasis teori permainan (*game theory*) yang mengukur kontribusi spesifik setiap fitur terhadap arah prediksi. SHAP mampu bekerja secara global (melihat pola seluruh dataset) sekaligus lokal (membedah alasan model memprediksi angka tertentu pada hari tertentu).
* **Partial Dependence Plot (PDP):** Grafik yang memperlihatkan pengaruh marjinal dari satu atau dua fitur tunggal terhadap nilai prediksi target, dengan cara mengisolasi dan mengasumsikan fitur-fitur lainnya konstan.

### B. Temuan Utama Analisis Data (*Key Insights*)
* **Dominasi Pola Historis (Efek Autoregresif):** Fitur `lag_1` (penggunaan listrik kemarin) dan `lag_7` (penggunaan listrik pada hari yang sama di minggu lalu) secara konsisten menempati peringkat teratas dalam *Feature Importance* dan nilai SHAP. Hal ini menunjukkan bahwa konsumsi listrik harian sangat dipengaruhi oleh rutinitas atau kebiasaan masyarakat yang berulang dalam siklus mingguan.
* **Karakteristik Non-Linear Suhu Udara (Kurva U):** Melalui grafik PDP dan SHAP *Dependence Plot*, terbukti secara empiris adanya hubungan non-linear berbentuk huruf **"U"** antara `Temperature` dan `Demand`.
  * Ketika suhu udara berada di titik ekstrem **sangat dingin**, permintaan listrik melonjak tajam akibat penggunaan sistem pemanas ruangan (*heater*).
  * Ketika suhu udara berada di titik ekstrem **sangat panas**, permintaan listrik juga melonjak drastis akibat penggunaan pendingin ruangan (*AC*).
  * Permintaan listrik berada di titik terendah ketika suhu udara berada di zona nyaman/moderat (sekitar **15°C** hingga **20°C**).
* **Transparansi Model Box-Hitam:** Melalui *Local Force Plot*, model tidak lagi bersifat misterius. Kita dapat melacak secara presisi bahwa pada tanggal tertentu (misalnya 22 Desember 2014), lonjakan prediksi listrik dipicu secara aditif oleh suhu udara panas ekstrem hari itu yang bersinergi dengan tingginya angka konsumsi listrik dari hari sebelumnya.