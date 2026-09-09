# 📈 Project: Exploratory Data Analysis (EDA)

## 📋 Overview
Proyek komprehensif ini bertujuan untuk membersihkan dataset `layoffs.csv` dan mengeksplorasi tren pemutusan hubungan kerja (layoffs) secara global menggunakan **MySQL 8**. Proyek ini dibagi menjadi dua tahap utama: **Data Cleaning** dan **Exploratory Data Analysis (EDA)**.

---

## 🧹 PART 1: DATA CLEANING (`layoffs_staging2`)

### 1 Melihat Keseluruhan Data Mentah
```sql
SELECT * 
FROM layoffs_staging2;
```
### 2 Skala Maksimum Krisis PHK
```SQL
SELECT MAX(total_laid_off), MAX(percentage_laid_off) 
FROM layoffs_staging2;
```
### 3 Perusahaan yang Tutup Total (100% Layoffs) dengan Pendanaan Terbesar
```SQL
SELECT * FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;
```
Penjelasan:

### 4 Total PHK Berdasarkan Perusahaan (Top 10)
```SQL
SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
### 5 Rentang Waktu Dataset
```SQL
SELECT MIN(`date`), MAX(`date`)
FROM layoffs_staging2;
```
### 6 Total PHK Berdasarkan Negara (Top 10)
```SQL
SELECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```
### 7 Tren PHK Berdasarkan Tahun
```SQL
SELECT YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR(`date`)
ORDER BY 1 DESC;
```
### 8 Dampak PHK Berdasarkan Tahap Pendanaan (Stage)
```SQL
SELECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;
```
### 9 Rata-rata Jumlah PHK per Perusahaan
```SQL
SELECT company, AVG(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
### 10 Tren PHK Bulanan (Time Series)
```SQL
SELECT SUBSTRING(`date`, 1, 7) AS `MONTH`, SUM(total_laid_off)
FROM layoffs_staging2
WHERE SUBSTRING(`date`, 1, 7) IS NOT NULL
GROUP BY `MONTH`
ORDER BY 1 ASC;
```
### 11 Akumulasi Krisis dengan Rolling Total
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
### 12 Total PHK per Perusahaan per Tahun
```SQL
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC;
```
### 13 Top 5 Perusahaan dengan PHK Terbanyak Setiap Tahun (DENSE_RANK)
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
