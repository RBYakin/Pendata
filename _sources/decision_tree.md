## Decision Tree Learner

<br>

![alt text](image-34.png)

<br>


Workflow ini digunakan untuk membangun model klasifikasi menggunakan algoritma Decision Tree, melakukan prediksi, serta menyimpan dan membaca kembali model.

---

### 1. Excel Reader
Node ini digunakan untuk membaca dataset dari file Excel (.xlsx).

Output:
- Data mentah yang akan digunakan untuk proses training dan testing.

---

### 2. Table Partitioner
Node ini berfungsi untuk membagi dataset menjadi dua bagian:
- Data training (untuk melatih model)
- Data testing (untuk menguji model)

---

### 3. Color Manager
Node ini memberikan warna pada kelas (label) tertentu.

Fungsi:
- Mempermudah visualisasi data berdasarkan kategori (misalnya Yes / No).

---

### 4. Decision Tree Learner
Node ini digunakan untuk membangun model Decision Tree berdasarkan data training.

Fungsi:
- Mempelajari pola dari data
- Menghasilkan model berupa pohon keputusan (decision tree)

Output:
- Model Decision Tree

---

### 5. Model Writer
Node ini digunakan untuk menyimpan model yang sudah dibuat ke dalam file (.model).

Fungsi:
- Menyimpan model agar bisa digunakan kembali tanpa perlu training ulang

---

### 6. Decision Tree Predictor
Node ini digunakan untuk melakukan prediksi terhadap data testing menggunakan model yang telah dibuat.

Fungsi:
- Mengklasifikasikan data baru berdasarkan model Decision Tree

Output:
- Hasil prediksi (kelas)

---

### 7. Color Appender (deprecated)
Node ini digunakan untuk menambahkan warna pada hasil prediksi.

Catatan:
- Node ini sudah deprecated (tidak direkomendasikan digunakan)

---

### 8. Scorer (deprecated)
Node ini digunakan untuk mengevaluasi hasil prediksi.

Fungsi:
- Menghitung akurasi model
- Membandingkan hasil prediksi dengan data asli

Catatan:
- Node ini sudah deprecated

---

### 9. Model Reader
Node ini digunakan untuk membaca kembali model yang telah disimpan (.model).

Fungsi:
- Memungkinkan penggunaan model tanpa training ulang

---

### 10. Decision Tree View

<br>

![alt text](image-35.png)

<br>

Node ini digunakan untuk menampilkan bentuk visual dari Decision Tree.

Fungsi:
- Menampilkan struktur pohon (tree)
- Mempermudah interpretasi model

---

### Kesimpulan
Workflow ini menunjukkan proses lengkap dalam machine learning menggunakan Decision Tree, mulai dari membaca data, membagi dataset, melatih model, melakukan prediksi, hingga menyimpan dan menampilkan model. Model yang dihasilkan dapat digunakan kembali melalui Model Reader tanpa perlu melakukan pelatihan ulang.