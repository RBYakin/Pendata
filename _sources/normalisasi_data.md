# Normalisasi Data (Preprocessing)

## Pengertian Normalisasi Data
Normalisasi data adalah proses mengubah nilai data ke dalam skala tertentu agar memiliki rentang yang seragam sehingga mempermudah proses analisis dan perhitungan.

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
IPK:
- Xmin = 2  
- Xmax = 4  

PO:
- Xmin = 2000000  
- Xmax = 4000000  

JML:
- Xmin = 2  
- Xmax = 3  

---

### Contoh Perhitungan

Data ke-1:

IPK:
X' = (2 - 2) / (4 - 2) = 0 / 2 = 0  

PO:
X' = (2000000 - 2000000) / (4000000 - 2000000) = 0 / 2000000 = 0  

JML:
X' = (2 - 2) / (3 - 2) = 0 / 1 = 0  

---

Data ke-2:

IPK:
X' = (3 - 2) / (4 - 2) = 1 / 2 = 0.5  

PO:
X' = (3000000 - 2000000) / (4000000 - 2000000) = 1000000 / 2000000 = 0.5  

JML:
X' = (3 - 2) / (3 - 2) = 1 / 1 = 1  

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

### Perhitungan Rata-rata (μ)

IPK:
μ = (2+3+4+2+3+4+2) / 7 = 20 / 7 = 2.86  

PO:
μ = (2+3+2+2+3+4+3) / 7 = 19 / 7 = 2.71  

JML:
μ = (2+3+2+3+2+3) / 6 = 15 / 6 = 2.5  

---

### Contoh Perhitungan

Data ke-1 (IPK):

Z = (2 - 2.86) / 0.83 = -0.86 / 0.83 = -1.04  

Data ke-2 (JML):

Z = (3 - 2.5) / 0.5 = 0.5 / 0.5 = 1  

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

IPK:
nilai maksimum = 4 → j = 1  

PO:
nilai maksimum = 4000000 → j = 7  

JML:
nilai maksimum = 3 → j = 1  

---

### Contoh Perhitungan

Data ke-1:

IPK:
X' = 2 / 10 = 0.2  

PO:
X' = 2000000 / 10^7 = 0.2  

JML:
X' = 2 / 10 = 0.2  

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

## Langkah 1: Data yang digunakan
Data ke-7:
IPK = 0  
PO = 0.5  

---

## Langkah 2: Menghitung jarak

Rumus:
d = √((x1-x2)² + (y1-y2)²)

---

### Perhitungan

Ke data ke-2:

d = √((0 - 0.5)² + (0.5 - 0.5)²)  
d = √(0.25 + 0)  
d = 0.5  

Ke data ke-5:

d = √((0 - 0.5)² + (0.5 - 0.5)²)  
d = √(0.25 + 0)  
d = 0.5  

---

## Langkah 3: Menghitung bobot

Rumus:
w = 1 / d  

w2 = 1 / 0.5 = 2  
w5 = 1 / 0.5 = 2  

---

## Langkah 4: Menghitung nilai JML (normalisasi)

JML = ((2×1) + (2×0)) / (2+2)  
JML = 2 / 4 = 0.5  

---

## Langkah 5: Denormalisasi

Rumus:
X = X' × (max - min) + min  

JML = 0.5 × (3 - 2) + 2  
JML = 0.5 + 2 = 2.5  

Dibulatkan menjadi 3

---

# Hasil Akhir

| No | IPK | PO | JML |
|----|-----|--------|-----|
| 7 | 2 | 3000000 | 3 |

## Kesimpulan
Normalisasi dilakukan menggunakan metode Min-Max, Z-Score, dan Decimal Scaling untuk menyamakan skala seluruh atribut 
Missing value pada JML dihitung menggunakan metode WKNN berdasarkan jarak dan bobot data terdekat, kemudian dilakukan denormalisasi untuk mendapatkan nilai akhir.