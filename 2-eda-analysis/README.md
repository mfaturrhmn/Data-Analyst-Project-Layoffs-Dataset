# 📈 Project: Exploratory Data Analysis (EDA)

## 📋 Overview

Proyek ini bertujuan untuk menganalisis tren Pemutusan Hubungan Kerja (PHK) global secara masif yang terjadi di berbagai perusahaan dunia menggunakan MySQL. Analisis ini dirancang untuk menggali data mentah, membersihkannya, dan menjawab berbagai pertanyaan bisnis krusial guna memahami pola krisis ekonomi di sektor teknologi dan korporasi.
---

## 🛠️ 1. Data Preparation & Sanity Check

### 1. Melihat Keseluruhan Data Mentah
```sql
SELECT * 
FROM layoffs_staging2;
```
Penjelasan: Menampilkan seluruh baris dan kolom untuk memahami isi dataset secara keseluruhan.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/03ae7b0ae1c2d02a7f01179a04e5311fda3f5952/2-eda-analysis/image/Query%201%20Melihat%20Keseluruhan%20Data%20Mentah.png)

### 2. Skala Maksimum Krisis PHK
```SQL
SELECT MAX(total_laid_off), MAX(percentage_laid_off) 
FROM layoffs_staging2;
```
Penjelasan: Mencari jumlah maksimum karyawan yang di-PHK dalam satu waktu serta persentase maksimum (1 merepresentasikan perusahaan yang tutup total)
Output:

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%202%20Skala%20Maksimum%20Krisis%20PHK.png)

Insight: Skala pemutusan hubungan kerja terbesar dalam satu insiden tunggal mencapai 12.000 karyawan, dengan rasio persentase 1 menunjukkan adanya perusahaan yang terpaksa gulung tikar sepenuhnya.

### 3. Perusahaan yang Tutup Total (100% Layoffs) dengan Pendanaan Terbesar
```SQL
SELECT * FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;
```
Penjelasan: Menganalisis perusahaan mana saja yang melakukan PHK 100% karyawannya meskipun memiliki pendanaan besar untuk melihat anomali burn rate startup.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%203%20Perusahaan%20yang%20Tutup%20Total%20(100%25%20Layoffs)%20dengan%20Pendanaan%20Terbesar.png)

## 🌍 2. Macro Analysis: Tren Berdasarkan Perusahaan, Negara, dan Waktu

### 4. Total PHK Berdasarkan Perusahaan (Top 10)
```SQL
SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
Penjelasan: Menunjukkan perusahaan-perusahaan raksasa mana saja yang menyumbang angka PHK kumulatif terbesar selama periode berjalan.

Output:

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%204%20Total%20PHK%20Berdasarkan%20Perusahaan%20(Top%2010).png)

Insight: Perusahaan teknologi raksasa seperti Amazon (18.150 total PHK) dan Google (12.000 total PHK) menjadi penyumbang kumulatif korban PHK terbesar di level korporasi.

### 5. Rentang Waktu Dataset
```SQL
SELECT MIN(`date`), MAX(`date`)
FROM layoffs_staging2;
```
Penjelasan: Mengetahui batas awal dan akhir rentang waktu perekaman data agar konteks waktu analisis jelas.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%205%20Rentang%20Waktu%20Dataset.png)

### 6. Total PHK Berdasarkan Negara (Top 10)
```SQL
SELECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```
Penjelasan: Mengidentifikasi negara mana yang paling parah terdampak gelombang PHK.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%206%20Total%20PHK%20Berdasarkan%20Negara%20(Top%2010).png)

Insight: Amerika Serikat (US) mendominasi skala krisis secara masif dengan total korban mencapai 256.559 jiwa, jauh melampaui negara-negara lain di urutan berikutnya seperti India.

### 7. Tren PHK Berdasarkan Tahun
```SQL
SELECT YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR(`date`)
ORDER BY 1 DESC;
```
Penjelasan: Melihat perbandingan skala krisis dari tahun ke tahun untuk mendeteksi kapan puncak gelombang PHK terjadi.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%207%20Tren%20PHK%20Berdasarkan%20Tahun.png)

Insight: Krisis global ini mencapai puncaknya pada tahun 2022 (~160 ribu korban) dan 2023 (~125 ribu korban meski baru terekam hingga Maret).

### 8. Dampak PHK Berdasarkan Tahap Pendanaan (Stage)
```SQL
SELECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;
```
Penjelasan: Menganalisis tahap perusahaan mana yang paling rentan terhadap efisiensi.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%208%20Dampak%20PHK%20Berdasarkan%20Tahap%20Pendanaan%20(Stage).png)

Insight: Perusahaan dengan kategori Post-IPO menyumbang angka absolut terbanyak (204.132 PHK) karena skala operasional dan jumlah karyawan mereka yang sudah sangat masif. 

### 9. Rata-rata Jumlah PHK per Perusahaan
```SQL
SELECT company, AVG(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
Penjelasan: Melihat rata-rata skala gelombang PHK yang dilakukan oleh suatu perusahaan setiap kali mereka melakukan efisiensi.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%209%20Rata-rata%20Jumlah%20PHK%20per%20Perusahaan.png)

## 📈 3. Advanced Analysis: Tren Bulanan & Ranking (Window Functions & CTEs)

### 10. Tren PHK Bulanan (Time Series)
```SQL
SELECT SUBSTRING(`date`, 1, 7) AS `MONTH`, SUM(total_laid_off)
FROM layoffs_staging2
WHERE SUBSTRING(`date`, 1, 7) IS NOT NULL
GROUP BY `MONTH`
ORDER BY 1 ASC;
```
Penjelasan: Menggunakan ekstraksi string tanggal untuk melihat fluktuasi angka PHK dari bulan ke bulan secara kronologis.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%2010%20Tren%20PHK%20Bulanan%20(Time%20Series).png)

### 11. Akumulasi Krisis dengan Rolling Total
```SQL
WITH Rolling_Total AS
(
SELECT SUBSTRING(`date`, 1, 7) AS `MONTH`, SUM(total_laid_off) AS total_off
FROM layoffs_staging2
WHERE SUBSTRING(`date`, 1, 7) IS NOT NULL
GROUP BY `MONTH`
ORDER BY 1 ASC
)
SELECT `month`, total_off,
SUM(total_off) OVER(ORDER BY `MONTH`) AS rolling_total
FROM Rolling_Total;
```
Penjelasan: Memanfaatkan CTE dan Window Function (SUM() OVER) untuk melacak bagaimana angka kumulatif korban PHK terus bertambah dari waktu ke waktu secara akumulatif.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%2011%20Akumulasi%20Krisis%20dengan%20Rolling%20Total.png)

Insight: Eskalasi krisis melompat drastis menembus angka kumulatif 383.159 korban jiwa pada Maret 2023, dengan lonjakan paling tajam terjadi pada awal tahun 2023 (Januari).

### 12. Total PHK per Perusahaan per Tahun
```SQL
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC;
```
Penjelasan: Memecah data total PHK agar spesifik melihat perusahaan mana yang melakukan efisiensi terbesar di tahun tertentu.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%2012%20Total%20PHK%20per%20Perusahaan%20per%20Tahun.png)

### 13. Top 5 Perusahaan dengan PHK Terbanyak Setiap Tahun (DENSE_RANK)
```SQL
WITH Company_Year(company, years, total_laid_off) AS
(
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
), Company_Year_Rank AS
(
SELECT * , DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS Ranking
FROM Company_Year
WHERE years IS NOT NULL
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <= 5;
```
Penjelasan: Menggunakan kombinasi CTE dan Window Function DENSE_RANK() OVER (PARTITION BY ...) untuk menyaring dan memunculkan 5 besar perusahaan penyumbang PHK tertinggi di setiap tahunnya secara otomatis.

![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%2013%20Top%205%20Perusahaan%20dengan%20PHK%20Terbanyak%20Setiap%20Tahun%20(DENSE_RANK).png)

Insight: Peta perusahaan paling agresif melakukan efisiensi bergeser dari tahun ke tahun: dipimpin oleh Bytedance pada 2021, beralih ke Meta di 2022, hingga Google dan Microsoft yang mendominasi daftar teratas pada awal 2023.

## 4. 💡 Conclusion
Analisis menggunakan MySQL ini membuktikan bahwa gelombang PHK global massal didorong oleh koreksi besar-besaran di sektor teknologi pasca-pandemi, di mana perusahaan-perusahaan besar (Post-IPO) dan berbasis di Amerika Serikat menjadi yang paling signifikan terdampak secara akumulatif. Penggunaan Window Functions dan CTE berhasil memetakan eskalasi krisis ini secara kronologis dan terstruktur.
