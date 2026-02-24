# Eksplorasi Data

## 1. Pendahuluan

Dataset yang digunakan adalah dataset Iris yang berisi 150 data bunga
dengan 4 fitur numerik (sepal_length, sepal_width, petal_length,
petal_width) dan 1 fitur kategorikal (species). Dataset ini sering
digunakan dalam analisis data dan machine learning untuk klasifikasi.

## 2. Eksplorasi Data (Exploratory Data Analysis)

### 2.1 Struktur Data

-   Jumlah Baris: 150
-   Jumlah Kolom: 5
-   Tidak terdapat missing value pada dataset.
-   Tipe Data:
    -   4 kolom bertipe float (numerik)
    -   1 kolom bertipe object (kategori/species)

### 2.2 Lima Data Pertama

|sepal_length|sepal_width|petal_length|petal_width|species|
|:-----------|:---------:|:----------:|:---------:|------:|
|0|5.1|3.5|1.4|0.2 Iris-setosa|
|1|4.9|3  |1.4|0.2 Iris-setosa|
|2|4.7|3.2|1.3|0.2 Iris-setosa|
|3|4.6|3.1|1.5|0.2 Iris-setosa|
|4|5  |3.6|1.4|0.2 Iris-setosa|

## 3. Statistik Deskriptif

Statistik deskriptif digunakan untuk mengetahui gambaran umum data
seperti mean, minimum, maksimum, dan standar deviasi.

||sepal_length|sepal_width|petal_length|petal_width|
|:----|:------:|:------:|:-----:|-------:|
|count|   150  |  150   |  150  |  150   |
|mean |5.84333 |3.054   |3.75867|1.19867 |
|std  |0.828066|0.433594|1.76442|0.763161|
|min  |   4.3  |   2    |   1   |   0.1  |
|25%  |5.1|2.8|1.6|0.3|
|50%  |5.8|3|4.35|1.3|
|75%  |6.4|3.3|5.1|1.8|
|max  |7.9|4.4|6.9|2.5|

## 4. Analisis Awal

-   Rata-rata sepal_length: 5.84
-   Rata-rata sepal_width: 3.05
-   Rata-rata petal_length: 3.76
-   Rata-rata petal_width: 1.20

Dari hasil tersebut terlihat bahwa petal_length memiliki variasi nilai
yang cukup besar dibandingkan fitur lainnya, sehingga fitur ini
berpotensi baik untuk membedakan spesies bunga.

## 5. Kesimpulan

1.  Dataset Iris memiliki 150 data tanpa missing value.
2.  Semua fitur numerik memiliki distribusi yang cukup baik.
3.  Petal length dan petal width memiliki variasi paling besar dan berpotensi menjadi fitur penting dalam proses klasifikasi.