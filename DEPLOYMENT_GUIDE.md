# Panduan Deployment: GitHub dan Streamlit Community Cloud

Panduan ini untuk menggabungkan aplikasi ke **satu repository** yang sudah ada (`HaiqalShudqi/Telco-Customers`, berisi notebook, dataset, dan arsip artefak), lalu mendeploy-nya secara gratis di Streamlit Community Cloud.

Dengan satu repo, **aplikasi berada di root repo** (file `app.py` sejajar dengan notebook dan dataset). Tidak perlu membuat repo baru.

---

## Bagian 1. Siapkan folder final

1. Ekstrak `telco-customer-intelligence_stage8.zip` ke folder sementara, misalnya `C:\temp\app-final`. Di dalamnya akan ada folder `telco-customer-intelligence` berisi `app.py`, `requirements.txt`, `pages`, `utils`, dan seterusnya.
2. **Salin dua file prediksi test dari proyek lokal Anda** ke folder final (file ini tidak ada di ZIP karena dihasilkan dari Colab Anda):
   - `reports\metrics\test_predictions.csv`
   - `reports\metrics\test_predictions_meta.json`

   Tempatkan keduanya di `reports\metrics\` pada folder final. Tanpa dua file ini, aplikasi online tetap jalan, tetapi halaman Methodology hanya menampilkan grafik statis.
3. **Jangan menyalin folder `.venv`** dari proyek lokal Anda. Folder itu berisi ribuan file dan tidak boleh masuk ke GitHub. Selalu salin dari folder hasil ekstrak ZIP, bukan dari folder kerja Anda.

---

## Bagian 2. Masukkan ke repo yang sudah ada (cara termudah: GitHub Desktop)

Anda harus masuk dengan akun pemilik repo (`HaiqalShudqi`).

1. Pasang **GitHub Desktop** (desktop.github.com), lalu *Sign in to GitHub.com*.
2. Menu **File → Clone repository → tab URL**. Isi `HaiqalShudqi/Telco-Customers`. Pilih folder lokal yang singkat dan di luar proyek lama, misalnya `C:\repos\Telco-Customers`, lalu **Clone**.
3. Buka folder hasil clone di File Explorer. Salin **seluruh isi** folder final (bukan folder induknya) ke dalamnya, sehingga `app.py` berada langsung di `C:\repos\Telco-Customers\app.py`.
   - Jika ditanya soal `README.md`, pilih **Replace**. README lama hanya berisi satu baris.
   - **Jangan menghapus** `WA_Fn-UseC_-Telco-Customer-Churn.csv`, `Telco_Customer_Intelligence.ipynb`, atau folder `telco_customer_intelligence_artifacts`. Dataset di root dipakai sebagai URL cadangan aplikasi.
4. Kembali ke GitHub Desktop. Tab **Changes** menampilkan daftar file baru. Periksa:
   - Tidak ada `.venv` atau `__pycache__` (keduanya sudah diabaikan oleh `.gitignore`).
   - Ada `app.py`, `requirements.txt`, `pages/`, `utils/`, `models/`, `reports/metrics/test_predictions.csv`.
5. Isi *Summary* dengan `Tambahkan aplikasi Streamlit Customer Intelligence`, klik **Commit to main**, lalu **Push origin**.

### Alternatif: unggah lewat situs GitHub

Buka repo di github.com, **Add file → Upload files**, seret isi folder final. Batas unggah web adalah 100 file per unggahan, jadi lakukan dua kali (misalnya `pages`, `utils`, `tests`, `tools` dulu, lalu sisanya). Pastikan struktur folder tetap terjaga.

### Alternatif: baris perintah

```bat
git clone https://github.com/HaiqalShudqi/Telco-Customers.git
cd Telco-Customers
rem salin isi folder final ke sini
git add .
git commit -m "Tambahkan aplikasi Streamlit Customer Intelligence"
git push origin main
```

---

## Bagian 3. Periksa repo di GitHub

Buka `https://github.com/HaiqalShudqi/Telco-Customers` dan pastikan:

- [ ] `app.py` dan `requirements.txt` ada di root.
- [ ] Ada folder `pages`, `utils`, `models`, `data`, `metadata`, `reports`.
- [ ] `models/customer_intelligence_bundle.joblib` tampil dengan ukuran sekitar 355 KB (bukan teks aneh atau tautan LFS).
- [ ] `reports/metrics/test_predictions.csv` ada.
- [ ] Isi `requirements.txt` memuat `scikit-learn==1.6.1`.

---

## Bagian 4. Deploy di Streamlit Community Cloud

1. Buka **share.streamlit.io**, klik **Continue with GitHub**, dan izinkan akses.
2. Klik **Create app** → **Deploy a public app from GitHub**.
3. Isi:
   - **Repository:** `HaiqalShudqi/Telco-Customers`
   - **Branch:** `main`
   - **Main file path:** `app.py`
   - **App URL:** pilih nama yang mudah diingat.
4. Klik **Advanced settings** dan pilih **Python 3.12**.
   Ini penting. Library dipatok untuk Python 3.12 (`pandas==2.2.3`, `scikit-learn==1.6.1`); pada Python yang lebih baru, instalasi bisa gagal. Versi Python dipilih saat aplikasi dibuat; jika salah, biasanya cara termudah adalah menghapus aplikasi dan membuatnya ulang.
5. Tidak ada *Secrets* yang perlu diisi. Aplikasi ini tidak memakai kredensial.
6. Klik **Deploy**. Build pertama memakan waktu sekitar 3 sampai 10 menit. Log tampil di sisi kanan.

Setelah selesai, salin URL aplikasi ke bagian atas `README.md` (baris "Aplikasi online"), lalu commit dan push.

---

## Bagian 5. Daftar periksa setelah deploy

Angka berikut harus sama dengan uji lokal:

| Halaman | Yang harus terlihat |
|---|---|
| Sidebar | Model aktif: XGBoost, KMEANS k = 3, Threshold 0.39, tanpa kotak merah |
| Executive Overview | 7,043 pelanggan, churn 26.5%, tenure 32.4 bln, tagihan $64.76, risiko tinggi 1,409 |
| Customer Segmentation | S0 3,651, S1 1,165, S2 2,227 pelanggan |
| Customer Explorer | Cari `7590-VHVEG`: probabilitas 80,3%, risiko High |
| Churn Prediction | Contoh pertama lalu *Prediksi churn*: 73,2%, High, segmen S0, P2 |
| Retention Strategy | P1 434, nilai tinggi 1,771, revenue at risk 1,667,798 |
| Methodology → D | Slider threshold dan kurva ROC/PR interaktif; pada 0,39: TP 324, FN 50, FP 365, TN 670 |
| Unggah CSV | Pilih *Unggah CSV sendiri*, unggah file valid dan tidak valid |

Buka juga salah satu halaman lewat URL langsung (mis. `.../Customer_Segmentation`) lalu refresh. Tema harus tetap tampil.

---

## Bagian 6. Memperbarui aplikasi

Setiap `git push` ke branch `main` memicu deploy ulang otomatis. Untuk memulai ulang tanpa perubahan kode, buka menu aplikasi (⋮) → **Reboot app**.

Aplikasi gratis di Community Cloud akan **tidur** setelah beberapa waktu tanpa pengunjung. Saat dibuka lagi, klik tombol *Wake up* dan tunggu sekitar satu menit.

---

## Pemecahan masalah

| Gejala | Penyebab dan solusi |
|---|---|
| Build gagal saat memasang `pandas` atau `scikit-learn` | Python bukan 3.12. Hapus aplikasi, buat ulang, dan pilih Python 3.12 di Advanced settings. |
| `ModuleNotFoundError: utils` | *Main file path* salah. Harus `app.py` dan berada di root repo (bukan di dalam subfolder). |
| Kotak merah "Model gagal dimuat" | Versi library tidak sama dengan saat training. Periksa `requirements.txt` (`scikit-learn==1.6.1`) dan file `models/customer_intelligence_bundle.joblib` di GitHub. Aplikasi otomatis mencoba bundle cadangan portabel. |
| `FileNotFoundError` untuk data atau reports | Ada folder yang belum ter-push. Bandingkan struktur repo dengan README. Nama file **peka huruf besar/kecil** di Linux. |
| Methodology hanya menampilkan gambar statis | `test_predictions.csv` dan `test_predictions_meta.json` belum ada di `reports/metrics/` di GitHub, atau tidak lolos validasi (peringatan kuning di halaman menjelaskan alasannya). |
| Aplikasi lambat saat pertama dibuka | Normal: model dimuat sekali per proses server. Setelah itu di-cache. |
| `Resource limits exceeded` | Hindari mengunggah CSV besar. Batas aplikasi 20.000 baris. Jika perlu, *Reboot app*. |
| Tidak bisa memilih repo di share.streamlit.io | Beri Streamlit akses ke repo di pengaturan GitHub (*Settings → Applications → Streamlit*). |

---

## Kebersihan repo (opsional)

- Folder `telco_customer_intelligence_artifacts/models/__pycache__/` berisi berkas `.pyc` yang tidak berguna. Aman dihapus.
- Folder `telco_customer_intelligence_artifacts/` dan notebook boleh tetap ada sebagai arsip. Aplikasi tidak memakainya.
- Tambahkan berkas `LICENSE` jika Anda ingin menentukan lisensi untuk kode aplikasi.
