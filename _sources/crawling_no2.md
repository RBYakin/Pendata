# Prediksi Konsentrasi Nitrogen Dioksida (NO₂) Menggunakan Metode K-Nearest Neighbor (KNN)

## 1. Latar Belakang

Pencemaran udara merupakan salah satu permasalahan lingkungan yang berdampak langsung terhadap kesehatan manusia dan kualitas lingkungan. Salah satu gas pencemar yang sering dijadikan indikator kualitas udara adalah Nitrogen Dioksida (NO₂). Gas ini umumnya berasal dari aktivitas kendaraan bermotor, industri, serta proses pembakaran bahan bakar fosil.

Perkembangan teknologi penginderaan jauh memungkinkan pemantauan konsentrasi NO₂ secara berkala menggunakan data satelit. Salah satu satelit yang menyediakan informasi tersebut adalah Sentinel-5P yang dikelola oleh European Space Agency (ESA). Data dari satelit ini dapat dimanfaatkan untuk melakukan analisis tren maupun prediksi konsentrasi NO₂ di masa mendatang.

Pada penelitian ini digunakan metode K-Nearest Neighbor (KNN) Regression untuk memprediksi nilai konsentrasi NO₂ berdasarkan data historis yang diperoleh dari Sentinel-5P melalui platform OpenEO.

---

# 2. Pengumpulan Data

## 2.1 Pengertian OpenEO

OpenEO merupakan Application Programming Interface (API) yang menyediakan akses terpadu ke berbagai sumber data penginderaan jauh. OpenEO memudahkan pengguna dalam mengambil, memproses, dan menganalisis data satelit tanpa harus mengunduh keseluruhan dataset secara lokal.

Melalui OpenEO, pengguna dapat melakukan:

- Pengambilan data satelit
- Pemotongan wilayah penelitian
- Agregasi temporal
- Agregasi spasial
- Ekspor hasil ke berbagai format data

### Import Library OpenEO

```python
import openeo
```

Kode tersebut digunakan untuk memanggil library OpenEO ke dalam lingkungan Python.

---

## 2.2 Koneksi ke Server OpenEO

Sebelum mengakses data satelit, pengguna harus melakukan autentikasi ke server OpenEO.

```python
connection = openeo.connect(
    "openeo.dataspace.copernicus.eu"
).authenticate_oidc()
```

### Penjelasan

Kode di atas berfungsi untuk:

1. Membuat koneksi ke server Copernicus Data Space.
2. Melakukan login menggunakan OpenID Connect (OIDC).
3. Memperoleh hak akses terhadap dataset satelit yang tersedia.

---

## 2.3 Menentukan Area Penelitian

Area penelitian didefinisikan dalam bentuk polygon geografis.

```python
spatial_extent = {
    "type": "Polygon",
    ...
}
```

### Penjelasan

Polygon digunakan untuk membatasi wilayah yang akan dianalisis sehingga data yang diproses hanya berasal dari area penelitian.

---

## 2.4 Mengambil Data Sentinel-5P

### Pengertian Sentinel-5P

Sentinel-5P merupakan satelit pengamatan atmosfer yang menyediakan berbagai parameter kualitas udara, salah satunya adalah Nitrogen Dioksida (NO₂).

Dataset yang digunakan:

- Produk : Sentinel-5P Level 2
- Parameter : NO₂
- Periode : Oktober 2023 – Mei 2026

### Pengambilan Data

```python
s5p = connection.load_collection(
    "SENTINEL_5P_L2",
    spatial_extent=spatial_extent,
    temporal_extent=["2023-10-01","2026-05-01"],
    bands=["NO2"]
)
```

### Penjelasan

Kode tersebut digunakan untuk mengambil data NO₂ sesuai:

- Lokasi penelitian
- Rentang waktu pengamatan
- Band yang dibutuhkan

---

## 2.5 Agregasi Data

### Pengertian Agregasi

Agregasi merupakan proses penyederhanaan data dengan menghitung nilai representatif seperti rata-rata.

Pada penelitian ini dilakukan:

### Agregasi Temporal

```python
aggregate_temporal_period("day", reducer="mean")
```

Digunakan untuk menghitung rata-rata NO₂ setiap hari.

### Agregasi Spasial

```python
aggregate_spatial(...)
```

Digunakan untuk menghitung rata-rata seluruh piksel pada wilayah penelitian.

Hasil akhir berupa satu nilai NO₂ per hari.

---

## 2.6 Menyimpan Data

```python
job = s5post.execute_batch(
    title="NO2",
    outputfile="NO2.csv"
)
```

### Penjelasan

Kode digunakan untuk menjalankan proses pada server OpenEO dan menyimpan hasil ke file.

---

# 3. Persiapan Data

## 3.1 Pengertian Format NetCDF

NetCDF (Network Common Data Form) merupakan format penyimpanan data multidimensi yang umum digunakan dalam bidang meteorologi, klimatologi, dan penginderaan jauh.

### Membaca File NetCDF

```python
from netCDF4 import Dataset

dataset = Dataset("openEO.nc")
```

### Penjelasan

Kode digunakan untuk membuka dan membaca file hasil ekspor OpenEO.

---

## 3.2 Eksplorasi Data

```python
print(dataset.variables.keys())
```

### Penjelasan

Digunakan untuk melihat variabel yang tersedia pada file NetCDF.

Contoh variabel:

- time
- x
- y
- NO2

---

## 3.3 Mengatasi Missing Value

### Pengertian Missing Value

Missing value adalah data yang tidak memiliki nilai akibat gangguan sensor, proses akuisisi, atau proses transformasi data.

### Interpolasi Linear

```python
series.interpolate(method='linear')
```

### Penjelasan

Interpolasi linear digunakan untuk memperkirakan nilai yang hilang berdasarkan nilai sebelum dan sesudahnya.

---

## 3.4 Menghitung Rata-rata Harian

```python
new_no2 = np.nanmean(no2_filled, axis=(1,2))
```

### Penjelasan

Kode menghitung rata-rata seluruh grid spasial pada setiap waktu pengamatan.

Output:

| Tanggal | NO₂ |
|----------|------|
| 2023-10-01 | 0.00014 |
| 2023-10-02 | 0.00015 |

---

# 4. Pembersihan Data

## 4.1 Pemeriksaan Tanggal Hilang

### Pengertian Missing Timestamp

Missing timestamp merupakan tanggal yang tidak memiliki data pengamatan.

### Pemeriksaan

```python
missing_dates = full_range.difference(df.index)
```

### Penjelasan

Digunakan untuk mendeteksi tanggal yang hilang dari rentang pengamatan.

---

## 4.2 Pengisian Data Hilang

```python
df.interpolate(method='time')
```

### Penjelasan

Interpolasi berbasis waktu digunakan untuk mengisi tanggal yang tidak memiliki data.

---

## 4.3 Deteksi Outlier

### Pengertian Outlier

Outlier merupakan nilai yang berbeda jauh dari pola umum data.

### Metode IQR

```python
Q1 = df["NO2"].quantile(0.25)
Q3 = df["NO2"].quantile(0.75)

IQR = Q3 - Q1
```

### Rumus

Batas bawah:

Lower Bound = Q1 − 1.5 × IQR

Batas atas:

Upper Bound = Q3 + 1.5 × IQR

Data di luar rentang tersebut dianggap outlier.

---

## 4.4 Penanganan Outlier

```python
df_clean["NO2"] = df_clean["NO2"].interpolate()
```

### Penjelasan

Nilai outlier dihapus dan digantikan menggunakan interpolasi linear agar tidak memengaruhi model prediksi.

---

# 5. Transformasi Data

## 5.1 Konsep Time Series Forecasting

Pada prediksi deret waktu, nilai masa lalu digunakan untuk memprediksi nilai masa depan.

Contoh:

| Hari-1 | Hari-2 | Hari-3 | Hari-4 | Target |
|----------|----------|----------|----------|----------|
| 0.12 | 0.13 | 0.15 | 0.14 | 0.16 |

---

## 5.2 Membentuk Dataset Supervised

```python
def create_dataset(data, lag):
```

### Penjelasan

Fungsi ini digunakan untuk mengubah data time series menjadi data supervised learning.

---

## 5.3 Normalisasi Data

### Pengertian Min-Max Scaling

Normalisasi dilakukan agar seluruh data berada pada rentang yang sama.

### Rumus

X' = (X − Xmin) / (Xmax − Xmin)

### Implementasi

```python
scaler = MinMaxScaler()
```

---

# 6. Pemodelan KNN

## 6.1 Pengertian K-Nearest Neighbor Regression

K-Nearest Neighbor Regression merupakan metode machine learning yang melakukan prediksi berdasarkan rata-rata nilai dari sejumlah tetangga terdekat.

Prinsip kerjanya:

1. Menghitung jarak data baru terhadap seluruh data pelatihan.
2. Memilih K data terdekat.
3. Menghitung rata-rata nilai target dari K tetangga tersebut.

---

## 6.2 Pembagian Data

```python
train_test_split(
    X, y,
    test_size=0.2
)
```

### Penjelasan

Data dibagi menjadi:

- 80% data latih
- 20% data uji

---

## 6.3 Pelatihan Model

```python
model = KNeighborsRegressor(
    n_neighbors=5
)
```

### Penjelasan

Model menggunakan 5 tetangga terdekat untuk melakukan prediksi.

---

## 6.4 Prediksi

```python
y_pred = model.predict(X_test)
```

### Penjelasan

Digunakan untuk menghasilkan prediksi konsentrasi NO₂ berdasarkan data sebelumnya.

---

# 7. Evaluasi Model

## 7.1 Root Mean Square Error (RMSE)

### Pengertian

Mengukur besar kesalahan prediksi model.

### Rumus

RMSE = √(Σ(y - ŷ)² / n)

Semakin kecil RMSE maka model semakin baik.

---

## 7.2 Mean Absolute Percentage Error (MAPE)

### Pengertian

Mengukur persentase kesalahan prediksi.

### Rumus

MAPE = (1/n) Σ |(y-ŷ)/y| × 100%

Semakin kecil MAPE maka model semakin baik.

---

## 7.3 R-Square (R²)

### Pengertian

Mengukur kemampuan model menjelaskan variasi data.

### Rumus

R² = 1 - (SSres / SStot)

Nilai mendekati 1 menunjukkan model semakin baik.

---

# 8. Visualisasi Hasil

Grafik digunakan untuk membandingkan:

- Nilai aktual
- Nilai prediksi

Pada beberapa skenario lag:

- Lag 4 hari
- Lag 10 hari
- Lag 30 hari

Tujuannya untuk mengetahui jumlah data historis yang memberikan performa prediksi terbaik.

---

# 9. Kesimpulan

Penelitian ini memanfaatkan data Sentinel-5P yang diperoleh melalui platform OpenEO untuk memprediksi konsentrasi Nitrogen Dioksida (NO₂). Data yang diperoleh terlebih dahulu melalui proses agregasi, pembersihan data, interpolasi missing value, serta penanganan outlier menggunakan metode IQR.

Setelah data bersih, dilakukan transformasi time series menjadi bentuk supervised learning dan normalisasi menggunakan Min-Max Scaling. Model K-Nearest Neighbor Regression kemudian dilatih menggunakan beberapa variasi lag, yaitu 4 hari, 10 hari, dan 30 hari.

Hasil evaluasi menggunakan RMSE, MAPE, dan R² menunjukkan bahwa metode KNN mampu memanfaatkan pola historis data NO₂ untuk melakukan prediksi. Perbandingan beberapa nilai lag juga memberikan informasi mengenai jumlah data historis yang paling efektif dalam menghasilkan prediksi yang akurat.

Secara keseluruhan, kombinasi data Sentinel-5P dan metode KNN Regression dapat digunakan sebagai pendekatan sederhana namun efektif dalam melakukan prediksi kualitas udara berdasarkan konsentrasi Nitrogen Dioksida.