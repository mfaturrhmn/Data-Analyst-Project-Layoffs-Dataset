# 📈 Project: Exploratory Data Analysis (EDA)

## 📋 Overview
### Proyek ini bertujuan untuk menganalisis tren Pemutusan Hubungan Kerja (PHK) global secara masif yang terjadi di berbagai perusahaan dunia menggunakan MySQL. Analisis ini dirancang untuk menggali data mentah, membersihkannya, dan menjawab berbagai pertanyaan bisnis krusial guna memahami pola krisis ekonomi di sektor teknologi dan korporasi.
---

## 🛠️ 1. Data Preparation & Sanity Check

### 1. Melihat Keseluruhan Data Mentah
```sql
SELECT * 
FROM layoffs_staging2;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/03ae7b0ae1c2d02a7f01179a04e5311fda3f5952/2-eda-analysis/image/Query%201%20Melihat%20Keseluruhan%20Data%20Mentah.png)

### 2. Skala Maksimum Krisis PHK
```SQL
SELECT MAX(total_laid_off), MAX(percentage_laid_off) 
FROM layoffs_staging2;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%202%20Skala%20Maksimum%20Krisis%20PHK.png)
### 3. Perusahaan yang Tutup Total (100% Layoffs) dengan Pendanaan Terbesar
```SQL
SELECT * FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%203%20Perusahaan%20yang%20Tutup%20Total%20(100%25%20Layoffs)%20dengan%20Pendanaan%20Terbesar.png)
Penjelasan:

## 🌍 2. Macro Analysis: Tren Berdasarkan Perusahaan, Negara, dan Waktu
### 4. Total PHK Berdasarkan Perusahaan (Top 10)
```SQL
SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%204%20Total%20PHK%20Berdasarkan%20Perusahaan%20(Top%2010).png)
### 5. Rentang Waktu Dataset
```SQL
SELECT MIN(`date`), MAX(`date`)
FROM layoffs_staging2;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%205%20Rentang%20Waktu%20Dataset.png)
### 6. Total PHK Berdasarkan Negara (Top 10)
```SQL
SELECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%206%20Total%20PHK%20Berdasarkan%20Negara%20(Top%2010).png)
### 7. Tren PHK Berdasarkan Tahun
```SQL
SELECT YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR(`date`)
ORDER BY 1 DESC;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%207%20Tren%20PHK%20Berdasarkan%20Tahun.png)
### 8. Dampak PHK Berdasarkan Tahap Pendanaan (Stage)
```SQL
SELECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;
```
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%208%20Dampak%20PHK%20Berdasarkan%20Tahap%20Pendanaan%20(Stage).png)
### 9. Rata-rata Jumlah PHK per Perusahaan
```SQL
SELECT company, AVG(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```
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
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%2011%20Akumulasi%20Krisis%20dengan%20Rolling%20Total.png)
### 12. Total PHK per Perusahaan per Tahun
```SQL
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
ORDER BY 3 DESC;
```
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
![image alt](https://github.com/mfaturrhmn/Data-Analyst-Project-Layoffs-Dataset/blob/aa5547a9c1af0e29b60223da175eb100780e6b8b/2-eda-analysis/image/Query%2013%20Top%205%20Perusahaan%20dengan%20PHK%20Terbanyak%20Setiap%20Tahun%20(DENSE_RANK).png)
