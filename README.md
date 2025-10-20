# 🧠 Project: Layoffs Dataset Data Cleaning and Exploratory Data for Data Analytics


## 📋 Overview
Project ini bertujuan untuk membersihkan dataset `layoffs.csv` yang berisi informasi tentang pemutusan kerja (layoffs) di berbagai perusahaan.  
Seluruh proses dilakukan menggunakan **SQL (MySQL 8)** tanpa mengubah struktur logika asli dataset.  

Proses pembersihan mencakup:
- Pembuatan tabel staging  
- Penghapusan duplikat  
- Standarisasi teks  
- Konversi format tanggal  
- Pengisian nilai kosong  
- Pembersihan baris data yang tidak relevan  

## 💼 Project Summary
| Kategori               | Deskripsi                                                                  |
| ---------------------- | -------------------------------------------------------------------------- |
| Dataset                | layoffs.csv                                                                |
| Tools                  | MySQL 8                                                                    |
| Tujuan                 | Membersihkan dan menyiapkan data layoffs untuk analisis                    |
| Fokus Cleaning         | Deduplikasi, standardisasi teks, konversi tanggal, pengisian missing value |
| Output Akhir           | layoffs_staging1 (data siap analisis)                                      |
| Level                  | Entry-Level Data Analyst Project                                           |
| Kompetensi Ditunjukkan | SQL Data Cleaning, Window Function, Data Standardization, NULL Handling    |
---

## **STAGE 1: 🧩 Step-by-Step Cleaning Process** 

### 1️⃣ Menampilkan Data Mentah
```sql
SELECT *
FROM layoffs
LIMIT 100;

DROP TABLE IF EXISTS layoffs_staging1;
```
Tujuan: Menampilkan 100 baris pertama untuk memahami struktur dan tipe data dataset.

### 2️⃣ Membuat Tabel Staging
```sql
CREATE TABLE `layoffs_staging1` (
  `company` text,
  `location` text,
  `industry` text,
  `total_laid_off` int DEFAULT NULL,
  `percentage_laid_off` text,
  `date` text,
  `stage` text,
  `country` text,
  `funds_raised_millions` int DEFAULT NULL,
  `row_num` INT
);
```
Tujuan: Membuat salinan tabel dalam bentuk staging table agar proses cleaning tidak mengubah data mentah.

### 3️⃣ Menyalin Data ke Staging
```sql
INSERT INTO layoffs_staging1
SELECT *,
ROW_NUMBER() OVER(
PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
) AS row_num
FROM layoffs;
```
Tujuan: Menambahkan kolom row_num untuk mendeteksi duplikat.

### 4️⃣ Menghapus Data Duplikat
```sql
DELETE  
FROM layoffs_staging1
WHERE row_num > 1;
```
Tujuan: Menyisakan hanya satu baris unik untuk setiap kombinasi nilai.

### 5️⃣ Membersihkan Teks di Kolom company
```sql
SELECT company, TRIM(company)
FROM layoffs_staging1;
```
Tujuan: Menghapus spasi ekstra yang dapat menyebabkan duplikat tersembunyi.

### 6️⃣ Menstandarisasi Kategori Industri
```sql
SELECT industry FROM layoffs_staging1
WHERE industry LIKE 'Crypto%';

UPDATE layoffs_staging1	
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';
```
Tujuan: Menggabungkan variasi nama industri yang sama menjadi satu kategori standar.

### 7️⃣ Membersihkan Format Negara
```sql
SELECT DISTINCT country, TRIM(TRAILING '.' FROM country)
FROM layoffs_staging1
WHERE country LIKE 'United States%'
ORDER BY 1;

UPDATE layoffs_staging1	
SET country = TRIM(TRAILING '.' FROM country)
WHERE country LIKE 'United States%';

SELECT DISTINCT country
FROM layoffs_staging1
ORDER BY 1;
```
Tujuan: Menghapus tanda titik di akhir nama negara agar konsisten.

### 8️⃣ Mengonversi Format Tanggal
```sql
SELECT `date`
FROM layoffs_staging1;

UPDATE layoffs_staging1
SET `date` = STR_TO_DATE(`date`,'%m/%d/%Y');

ALTER TABLE layoffs_staging1
MODIFY COLUMN `date` DATE;
```
Tujuan: Mengubah kolom date dari string ke tipe DATE untuk analisis temporal.

### 9️⃣ Mengisi Nilai Kosong di Kolom industry
```sql
SELECT t1.industry, t2.industry
FROM layoffs_staging1 t1
JOIN layoffs_staging1 t2
	ON t1.company = t2.company
    AND t1.location = t2.location
WHERE (t1.industry IS NULL OR t1.industry = '')
AND t2.industry IS NOT NULL;

UPDATE layoffs_staging1 t1
JOIN layoffs_staging1 t2
	ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL
AND t2.industry IS NOT NULL;
```
Tujuan: Mengisi nilai kosong berdasarkan baris lain dengan company & location yang sama (self-join imputation).

### 🔟 Menghapus Baris yang Tidak Relevan
```sql
SELECT *
FROM layoffs_staging1
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;

DELETE
FROM layoffs_staging1
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
```
Tujuan: Menghapus baris yang tidak mengandung informasi penting.

### 1️⃣1️⃣ Menghapus Kolom Bantu
```sql
ALTER TABLE layoffs_staging1
DROP COLUMN row_num;
```
Tujuan: Membersihkan struktur tabel akhir dengan menghapus kolom sementara.

### ✅ Final Output
```sql
SELECT *
FROM layoffs_staging1;
```
Hasil: Tabel layoffs_staging1 bersih dan siap untuk analisis, visualisasi, atau pipeline machine learning.

---
