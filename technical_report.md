---
"LAPORAN TEKNIS: SISTEM DETEKSI PHISHING OTOMATIS BERBASIS AI"

---

# Automatic Phishing Detection with AI

## 1. Pendahuluan
Phishing merupakan salah satu ancaman keamanan siber yang bertujuan mencuri informasi pengguna melalui website palsu. URL biasanya dibuat menyerupai situs asli sehingga sulit dibedakan oleh pengguna.

Proyek ini mengembangkan sistem deteksi phishing otomatis berbasis machine learning yang mampu bekerja secara real-time melalui browser.

---

## 2. Rumusan Masalah
Permasalahan yang ditemukan pada sistem sebelumnya:
- Domain terpercaya sering salah terdeteksi sebagai phishing  
- Dataset memiliki bias dan kesalahan label  
- Akurasi model masih rendah (~85%)  
- Tingkat false positive cukup tinggi  

---

## 3. Tujuan
Tujuan dari sistem ini:
- Meningkatkan akurasi deteksi hingga sekitar 97%  
- Mengurangi false positive  
- Mendeteksi phishing secara real-time  
- Menghasilkan sistem yang siap digunakan  

---

## 4. Arsitektur Sistem

### Diagram Arsitektur Sistem

[ USER / CLIENT ]
| Browser / Desktop
v
[ BROWSER EXTENSION ]

Detect click
Extract URL
Send to backend
|
v
[ BACKEND API (Flask) ]
http://localhost:5001

|
+------------------------+
| |
v v
[ WHITELIST ] [ ML MODEL ]
(Trusted) (RandomForest)
| |
+-----------+------------+
|
v
[ RESULT ]
SAFE / PHISHING
|
v
[ BROWSER POPUP ]
Warning / Continue


### Penjelasan

Diagram di atas menunjukkan alur kerja sistem deteksi phishing secara sederhana namun menyeluruh. Proses dimulai dari pengguna (user) yang mengakses sebuah link melalui browser atau aplikasi.

Ketika pengguna mengklik link, browser extension akan secara otomatis mendeteksi aktivitas tersebut, mengambil URL, dan mengirimkannya ke backend API untuk diproses.

Di sisi backend, sistem melakukan dua tahap pengecekan. Pertama, URL akan dibandingkan dengan daftar whitelist (domain terpercaya). Jika URL termasuk dalam whitelist, maka langsung dikategorikan sebagai aman (safe).

Jika URL tidak ditemukan dalam whitelist, maka akan diproses oleh model machine learning (Random Forest). Model ini menganalisis berbagai fitur dari URL untuk menentukan apakah termasuk phishing atau legitimate.

Hasil analisis kemudian dikembalikan dalam bentuk status (safe atau phishing). Informasi ini ditampilkan kepada pengguna melalui browser popup, berupa peringatan jika terdeteksi phishing atau akses normal jika URL aman.

Dengan alur ini, sistem mampu memberikan perlindungan secara real-time tanpa memerlukan intervensi manual dari pengguna.

---

## 5. Metodologi
Tahapan pengembangan sistem:
1. Pengumpulan dataset phishing dan legitimate  
2. Pembersihan data (data cleaning)  
3. Ekstraksi fitur dari URL  
4. Pelatihan model machine learning  
5. Integrasi dengan backend dan extension  
6. Pengujian sistem  

---

## 6. Dataset & Fitur
Dataset terdiri dari 11.430 URL yang seimbang antara phishing dan legitimate.

Fitur yang digunakan meliputi:
- Panjang URL  
- Karakter khusus  
- Struktur domain  
- Subdomain dan TLD  
- Kata kunci mencurigakan  

Total fitur yang digunakan sebanyak 71 fitur.

---

## 7. Model Machine Learning
Model yang digunakan adalah Random Forest karena mampu menangani pola kompleks dan memberikan performa yang stabil.

Konfigurasi utama:
- 400 decision trees  
- Depth terbatas untuk menghindari overfitting  
- Menggunakan probabilitas (calibration)  

---

## 8. Hasil & Temuan
Hasil pengujian menunjukkan:
- Akurasi meningkat dari ~85% menjadi 97%  
- False positive menurun menjadi sekitar 2.9%  
- Domain terpercaya tidak lagi salah terdeteksi  

---

## 9. Analisis & Kesimpulan
Kualitas dataset sangat berpengaruh terhadap performa model. Penggunaan whitelist membantu mengurangi kesalahan deteksi, dan kombinasi machine learning serta rule-based menghasilkan sistem yang lebih efektif.

Sistem berhasil mendeteksi phishing secara real-time dengan tingkat akurasi yang tinggi.

---

## 10. Rencana Pengembangan
Pengembangan selanjutnya meliputi:
- Analisis konten halaman website  
- Pembaruan dataset secara berkala  
- Peningkatan tampilan antarmuka  
- Pengembangan versi mobile  

---

## 11. Panduan Operasional

Menjalankan backend:
pip install -r requirements.txt  
python backend_api/api.py  

Mengaktifkan extension:
1. Buka chrome://extensions/  
2. Aktifkan Developer Mode  
3. Klik "Load unpacked"  
4. Pilih folder browser_extension  

---

## 12. Ringkasan
Sistem ini menyediakan deteksi phishing otomatis berbasis AI yang bekerja secara real-time dan membantu meningkatkan keamanan pengguna saat mengakses internet.
