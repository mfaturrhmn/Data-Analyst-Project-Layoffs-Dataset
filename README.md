# 🌐 Global Layoffs Data Analysis Project (SQL & MySQL)

Proyek portofolio end-to-end Data Analyst ini bertujuan untuk membersihkan data mentah (*Data Cleaning*) dan melakukan analisis mendalam (*Exploratory Data Analysis*) terhadap tren pemutusan hubungan kerja (layoffs) di perusahaan-perusahaan global menggunakan **MySQL 8**.

---

## 📂 Struktur Repository
Proyek ini dibagi menjadi dua tahap utama yang terstruktur di dalam folder terpisah:

1. 🧹 **[Data Cleaning (`/1-data-cleaning`)](./1-data-cleaning)**
   - Fokus: Pembuatan tabel staging, deduplikasi data, standarisasi teks dan format tanggal, *imputation* nilai kosong (*missing values*), hingga pembersihan baris tidak relevan.
   - Output: Tabel bersih **`layoffs_staging2`** yang siap dianalisis.

2. 📈 **[Exploratory Data Analysis / EDA (`/2-eda-analysis`)](./2-eda-analysis)**
   - Fokus: Macro analysis (perusahaan, negara, tahun, tahap pendanaan), analisis deret waktu (*time series*), akumulasi krisis (*rolling total*), dan pemeringkatan menggunakan fungsi lanjutan (*Window Functions* seperti `DENSE_RANK`).
   - Output: Temuan bisnis (*key insights*) dan visualisasi kueri.

---

## 🛠️ Tools & Technologies
- **Database:** MySQL 8
- **Concepts:** CTEs, Window Functions, Data Standardization, String Manipulation, Aggregations, Business Storytelling.

---
*Dibuat sebagai bagian dari Portofolio Data Analyst.*
