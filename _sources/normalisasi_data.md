# Normalisasi Data (Preprocessing)

## Pengertian Normalisasi Data
Normalisasi data adalah proses mengubah nilai data ke dalam skala tertentu agar memiliki rentang yang seragam. Tujuannya adalah untuk menghindari dominasi atribut tertentu dan meningkatkan kinerja algoritma data mining.

---

## Data Contoh

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

## Catatan Penting
Normalisasi dilakukan pada atribut IPK dan PO karena kedua atribut tersebut memiliki nilai lengkap.  
Meskipun terdapat missing value pada atribut JML (data ke-7), proses normalisasi tetap dapat dilakukan karena tidak mempengaruhi atribut lain.

---

# METODE NORMALISASI DATA

---

## 1. Min-Max Normalization

### Rumus
X' = (X - Xmin) / (Xmax - Xmin)

### Langkah Penyelesaian

#### 1. Menentukan nilai minimum dan maksimum
Data IPK: 2, 3, 4, 2, 3, 4, 2  
- Xmin = 2  
- Xmax = 4  

#### 2. Menghitung normalisasi

| No | IPK | Perhitungan | Hasil |
|----|-----|------------|-------|
| 1 | 2 | (2-2)/(4-2) | 0 |
| 2 | 3 | (3-2)/(4-2) | 0.5 |
| 3 | 4 | (4-2)/(4-2) | 1 |
| 4 | 2 | (2-2)/(4-2) | 0 |
| 5 | 3 | (3-2)/(4-2) | 0.5 |
| 6 | 4 | (4-2)/(4-2) | 1 |
| 7 | 2 | (2-2)/(4-2) | - |

---

## 2. Z-Score Normalization

### Rumus
Z = (X - μ) / σ

### Langkah Penyelesaian

#### 1. Menghitung rata-rata (μ)
μ = (2 + 3 + 4 + 2 + 3 + 4 + 2) / 7  
μ = 20 / 7 = 2.86  

#### 2. Menghitung standar deviasi (σ)
σ ≈ 0.83  

#### 3. Menghitung Z-Score

| No | IPK | Perhitungan | Hasil |
|----|-----|------------|-------|
| 1 | 2 | (2-2.86)/0.83 | -1.04 |
| 2 | 3 | (3-2.86)/0.83 | 0.17 |
| 3 | 4 | (4-2.86)/0.83 | 1.37 |
| 4 | 2 | (2-2.86)/0.83 | -1.04 |
| 5 | 3 | (3-2.86)/0.83 | 0.17 |
| 6 | 4 | (4-2.86)/0.83 | 1.37 |
| 7 | 2 | (2-2.86)/0.83 | - |

---

## 3. Decimal Scaling

### Rumus
X' = X / (10^j)

### Langkah Penyelesaian

#### 1. Menentukan nilai maksimum
Nilai maksimum IPK = 4  

#### 2. Menentukan nilai j
Karena nilai maksimum < 10, maka j = 1  

#### 3. Menghitung normalisasi

| No | IPK | Perhitungan | Hasil |
|----|-----|------------|-------|
| 1 | 2 | 2/10 | 0.2 |
| 2 | 3 | 3/10 | 0.3 |
| 3 | 4 | 4/10 | 0.4 |
| 4 | 2 | 2/10 | 0.2 |
| 5 | 3 | 3/10 | 0.3 |
| 6 | 4 | 4/10 | 0.4 |
| 7 | 2 | 2/10 | - |

---

# Handling Missing Value Menggunakan WKNN

## Tujuan
Menentukan nilai JML pada data ke-7 yang masih kosong.

---

## Langkah Penyelesaian

### 1. Menggunakan hasil normalisasi Min-Max

Data ke-7:
- IPK = 2 → 0  
- PO = 3000000 → 0.5  

---

### 2. Menghitung jarak ke data lain

Rumus:
d = √((x1-x2)² + (y1-y2)²)

#### Contoh perhitungan:

**Ke data ke-2:**
(IPK = 0.5, PO = 0.5)  
d = √((0-0.5)² + (0.5-0.5)²) = 0.5  

**Ke data ke-5:**
(IPK = 0.5, PO = 0.5)  
d = 0.5  

---

### 3. Menentukan tetangga terdekat
Dipilih:
- Data ke-2 (JML = 3)
- Data ke-5 (JML = 2)

---

### 4. Menghitung bobot

w = 1 / d  

- w1 = 2  
- w2 = 2  

---

### 5. Menghitung nilai akhir

JML = ((2×3) + (2×2)) / (2+2)  
JML = 2.5  

Dibulatkan → **JML = 3**

---

# Hasil Akhir

| No | IPK | PO | JML |
|----|-----|-----|-----|
| 7 | 2 | 3000000 | 3 |

---

## Kesimpulan
Normalisasi dilakukan pada atribut yang memiliki data lengkap yaitu IPK dan PO menggunakan metode Min-Max, Z-Score, dan Decimal Scaling. Missing value pada atribut JML diselesaikan menggunakan metode WKNN berdasarkan kedekatan jarak dan bobot data terdekat.