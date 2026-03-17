# Normalisasi Data (Preprocessing)

## Pengertian Normalisasi Data
Normalisasi data adalah proses mengubah nilai data ke dalam skala tertentu agar memiliki rentang yang seragam. Tujuannya adalah untuk menghindari dominasi atribut tertentu dan mempermudah proses perhitungan dalam data mining.

---

## Data Awal

| No | IPK | PO | JML |
|----|-----|--------|-----|
| 1 | 2 | 2000000 | 2 |
| 2 | 3 | 3000000 | 3 |
| 3 | 4 | 2000000 | 2 |
| 4 | 2 | 2000000 | 3 |
| 5 | 3 | 3000000 | 2 |
| 6 | 4 | 4000000 | 3 |
| 7 | 2 | 3000000 | - |


---

# METODE NORMALISASI DATA

---

## 1. Min-Max Normalization

### Rumus
X' = (X - Xmin) / (Xmax - Xmin)

---

### Nilai Minimum dan Maksimum
- IPK: min = 2, max = 4  
- PO: min = 2000000, max = 4000000  
- JML: min = 2, max = 3  

---

### Hasil Normalisasi Min-Max

| No | IPK | PO | JML |
|----|-----|-----|-----|
| 1 | 0   | 0   | 0 |
| 2 | 0.5 | 0.5 | 1 |
| 3 | 1   | 0   | 0 |
| 4 | 0   | 0   | 1 |
| 5 | 0.5 | 0.5 | 0 |
| 6 | 1   | 1   | 1 |
| 7 | 0   | 0.5 | - |

---

## 2. Z-Score Normalization

### Rumus
Z = (X - μ) / σ

---

### Perhitungan
- Rata-rata (μ):
  - IPK = 2.86  
  - PO = 2.57  
  - JML = 2.5  

- Standar deviasi (σ):
  - IPK ≈ 0.83  
  - PO ≈ 0.83  
  - JML ≈ 0.5  

---

### Hasil Normalisasi Z-Score

| No | IPK | PO | JML |
|----|------|------|------|
| 1 | -1.04 | -1.04 | -1 |
| 2 | 0.17  | 0.17  | 1 |
| 3 | 1.37  | -1.04 | -1 |
| 4 | -1.04 | -1.04 | 1 |
| 5 | 0.17  | 0.17  | -1 |
| 6 | 1.37  | 1.37  | 1 |
| 7 | -1.04 | 0.17  | - |

---

## 3. Decimal Scaling

### Rumus
X' = X / (10^j)

---

### Penentuan Nilai j
- IPK → j = 1  
- PO → j = 7  
- JML → j = 1  

---

### Hasil Normalisasi Decimal Scaling

| No | IPK | PO | JML |
|----|-----|---------|-----|
| 1 | 0.2 | 0.2 | 0.2 |
| 2 | 0.3 | 0.3 | 0.3 |
| 3 | 0.4 | 0.2 | 0.2 |
| 4 | 0.2 | 0.2 | 0.3 |
| 5 | 0.3 | 0.3 | 0.2 |
| 6 | 0.4 | 0.4 | 0.3 |
| 7 | 0.2 | 0.3 | - |

---

# Handling Missing Value Menggunakan WKNN

## Tujuan
Menentukan nilai JML pada data ke-7 yang masih kosong.

---

## 1. Menggunakan hasil normalisasi Min-Max

Data ke-7:
- IPK = 0  
- PO = 0.5  

---

## 2. Menghitung jarak

Rumus:
d = √((x1-x2)² + (y1-y2)²)

---

## 3. Perhitungan jarak

| Data | IPK | PO | JML | Jarak |
|------|-----|-----|-----|-------|
| Data ke-2 | 0.5 | 0.5 | 1 | 0.5 |
| Data ke-5 | 0.5 | 0.5 | 0 | 0.5 |

---

## 4. Menghitung bobot

w = 1 / d  

- Bobot data ke-2 = 2  
- Bobot data ke-5 = 2  

---

## 5. Menghitung nilai JML (hasil normalisasi)

JML = ((2×1) + (2×0)) / (2+2)  
JML = 0.5  

---

## 6. Denormalisasi

Karena Min-Max:
X = X' × (max - min) + min  

JML = (0.5 × (3-2)) + 2  
JML = 2.5 → dibulatkan menjadi **3**

---

# Hasil Akhir

| No | IPK | PO | JML |
|----|-----|--------|-----|
| 7 | 2 | 3000000 | 3 |

---

## Kesimpulan
Normalisasi dilakukan menggunakan metode Min-Max, Z-Score, dan Decimal Scaling untuk menyamakan skala seluruh atribut 
Missing value pada JML dihitung menggunakan metode WKNN berdasarkan jarak dan bobot data terdekat, kemudian dilakukan denormalisasi untuk mendapatkan nilai akhir.