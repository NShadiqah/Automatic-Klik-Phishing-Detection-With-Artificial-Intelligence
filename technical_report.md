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

### 4.1 System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                     USER / CLIENT LAYER                         │
│  ┌──────────────────┐    ┌──────────────────────────────────┐  │
│  │ Browser User     │    │ Desktop Application              │  │
│  │ (Chrome/Firefox) │    │ (System Tray Monitor)            │  │
│  └────────┬─────────┘    └──────────────┬───────────────────┘  │
└───────────┼─────────────────────────────┼────────────────────────┘
            │                             │
            ▼                             ▼
    ┌───────────────────────────────────────────────────┐
    │      BROWSER EXTENSION LAYER                      │
    │  ┌─────────────────────────────────────────────┐  │
    │  │  background.js (Event Listener)             │  │
    │  │  - Detect link clicks                       │  │
    │  │  - Extract URL                             │  │
    │  │  - Send to backend                         │  │
    │  └────────────────┬────────────────────────────┘  │
    │                   │                              │
    │  ┌────────────────┴───────────┐                  │
    │  │ popup.js (UI Display)      │                  │
    │  │ - Show warning/safe        │                  │
    │  │ - Continue/Block options   │                  │
    │  └────────────────────────────┘                  │
    └──────────────────┬──────────────────────────────┘
                       │
                       ▼
        ┌──────────────────────────────────┐
        │  BACKEND API (Flask)             │
        │  http://localhost:5001           │
        │  ┌──────────────────────────┐    │
        │  │ POST /api/check-url      │    │
        │  │ {url: "..."}             │    │
        │  └────────────┬─────────────┘    │
        │               │                  │
        │  ┌────────────┴──────────────┐   │
        │  ▼                           ▼   │
        │ Layer 1:              Layer 2:   │
        │ Check Whitelist         ML Model │
        │ (118 domains)           (V3)     │
        │ ├─ YES → SAFE          Extract   │
        │ │ 99%                   71 features
        │ └─ NO ↓                          │
        │    Run Model 1           ├─ Phishing
        │    → PHISHING?           └─ Legitimate
        │                                  │
        └──────────────┬───────────────────┘
                       │
        ┌──────────────┴──────────────────────┐
        │  RESPONSE                           │
        │  {                                  │
        │    "is_phishing": bool,             │
        │    "confidence": 95.5,              │
        │    "risk_level": "HIGH",            │
        │    "protection_type": "whitelist"   │
        │  }                                  │
        └──────────────┬──────────────────────┘
                       │
        ┌──────────────┴──────────────────────┐
        │  Show Result to User                │
        │  ├─ If PHISHING → Show warning     │
        │  └─ If SAFE → Allow access        │
        └─────────────────────────────────────┘
```

### Penjelasan

Diagram di atas menggambarkan arsitektur sistem deteksi phishing secara menyeluruh yang terdiri dari beberapa lapisan utama.

Pada lapisan pertama, yaitu **User/Client Layer**, terdapat dua jenis pengguna, yaitu pengguna browser (Chrome/Firefox) dan aplikasi desktop (system tray). Keduanya berperan sebagai sumber input URL yang akan dianalisis oleh sistem.

Selanjutnya, pada **Browser Extension Layer**, extension berfungsi sebagai komponen utama untuk mendeteksi aktivitas pengguna. File `background.js` bertugas sebagai event listener yang akan menangkap setiap klik link, mengekstrak URL, dan mengirimkannya ke backend API. Sementara itu, `popup.js` bertanggung jawab untuk menampilkan hasil analisis kepada pengguna dalam bentuk peringatan atau notifikasi aman, serta menyediakan opsi untuk melanjutkan atau membatalkan akses.

Pada **Backend API Layer**, sistem berbasis Flask menerima request berupa URL melalui endpoint `/api/check-url`. Setelah itu, URL akan diproses melalui dua lapisan analisis.

Lapisan pertama adalah **Whitelist Checking**, di mana sistem memeriksa apakah URL termasuk dalam daftar domain terpercaya (sekitar 118 domain). Jika URL ditemukan dalam whitelist, maka langsung dikategorikan sebagai aman dengan tingkat kepercayaan tinggi.

Jika URL tidak termasuk dalam whitelist, maka akan diproses pada lapisan kedua yaitu **Machine Learning Model (V3)**. Pada tahap ini, sistem melakukan ekstraksi sekitar 71 fitur dari URL dan menggunakan model Random Forest untuk menentukan apakah URL tersebut termasuk phishing atau legitimate.

Hasil dari proses ini dikembalikan dalam bentuk response yang berisi informasi seperti status phishing, tingkat kepercayaan (confidence), tingkat risiko (risk level), serta jenis proteksi yang digunakan.

Terakhir, hasil analisis akan ditampilkan kembali kepada pengguna. Jika URL terdeteksi sebagai phishing, maka sistem akan menampilkan peringatan. Sebaliknya, jika URL dinyatakan aman, pengguna dapat melanjutkan akses tanpa hambatan.

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
