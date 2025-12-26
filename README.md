# sql-layoffs-eda

## Exploratory Data Analysis (EDA) on Global Layoffs Dataset

---

### Project Overview

This project performs Exploratory Data Analysis (EDA) on a global layoffs dataset using SQL. The goal is to understand trends, patterns, and insights related to company layoffs across industries, countries, funding stages, and time periods.

The analysis is carried out on a cleaned and staged table named `layoffs_staging2`, which contains company-level layoff data.

---

### Dataset Source

The dataset used in this project was sourced from an external tutorial and then cleaned and staged before analysis. This README focuses specifically on explaining the EDA queries performed on the prepared dataset.

---

### Table Used

**Table Name:** `layoffs_staging2`

**Purpose:** Stores cleaned layoff records including company, industry, country, date, funding stage, funds raised, total layoffs, and percentage laid off.

---

### Step-by-Step Analysis Explanation

#### 1. Viewing the Dataset

```sql
SELECT * FROM layoffs_staging2;
```

This query is used to inspect the full dataset and understand its structure, columns, and data distribution before starting analysis.

---

#### 2. Identifying Maximum Layoffs

```sql
SELECT MAX(total_laid_off), MAX(percentage_laid_off)
FROM layoffs_staging2;
```

**Finds:**
- The highest number of employees laid off in a single event
- The maximum percentage of layoffs recorded

This helps identify extreme layoff cases.

---

#### 3. Companies with 100% Layoffs

```sql
SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY total_laid_off DESC;
```

Filters companies that laid off 100% of their workforce, sorted by the total number laid off.

---

#### 4. Funding vs Complete Layoffs

```sql
SELECT *
FROM layoffs_staging2
WHERE percentage_laid_off = 1
ORDER BY funds_raised_millions DESC;
```

Analyzes how much funding companies had raised before shutting down completely.

---

#### 5. Total Layoffs by Company

```sql
SELECT company, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company
ORDER BY 2 DESC;
```

Calculates cumulative layoffs per company to identify the most affected organizations overall.

---

#### 6. Date Range of Layoffs

```sql
SELECT MIN(`date`), MAX(`date`)
FROM layoffs_staging2;
```

Determines the time span covered by the dataset.

---

#### 7. Layoffs by Industry

```sql
SELECT industry, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY industry
ORDER BY 2 DESC;
```

Identifies which industries experienced the highest number of layoffs.

---

#### 8. Layoffs by Country

```sql
SELECT country, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY country
ORDER BY 2 DESC;
```

Shows which countries were most impacted by layoffs.

---

#### 9. Layoffs by Year

```sql
SELECT YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY YEAR(`date`)
ORDER BY 1 DESC;
```

Analyzes how layoffs changed year over year.

---

#### 10. Layoffs by Company Stage

```sql
SELECT stage, SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY stage
ORDER BY 2 DESC;
```

Examines which funding stages (Seed, Series A, Post-IPO, etc.) faced the most layoffs.

---

#### 11. Monthly Layoff Trends

```sql
SELECT SUBSTRING(`date`, 1, 7) AS `month`, SUM(total_laid_off)
FROM layoffs_staging2
WHERE SUBSTRING(`date`, 1, 7) IS NOT NULL
GROUP BY `month`
ORDER BY 1;
```

Aggregates layoffs month-wise to observe trends and spikes over time.

---

#### 12. Rolling Total of Layoffs

```sql
WITH Rolling_total AS (
  SELECT SUBSTRING(`date`, 1, 7) AS `month`,
         SUM(total_laid_off) AS total_off
  FROM layoffs_staging2
  WHERE SUBSTRING(`date`, 1, 7) IS NOT NULL
  GROUP BY `month`
)
SELECT `month`, total_off,
       SUM(total_off) OVER (ORDER BY `month`) AS rolling_total
FROM Rolling_total;
```

Uses a window function to calculate a cumulative (rolling) total of layoffs over time, helping visualize progression.

---

#### 13. Company-Year Layoff Breakdown

```sql
SELECT company, YEAR(`date`), SUM(total_laid_off)
FROM layoffs_staging2
GROUP BY company, YEAR(`date`)
ORDER BY company ASC;
```

Breaks down layoffs by company and year.

---

#### 14. Top 5 Companies by Layoffs per Year

```sql
WITH Company_year AS (
  SELECT company, YEAR(`date`) AS years, SUM(total_laid_off) AS total_laid_off
  FROM layoffs_staging2
  GROUP BY company, YEAR(`date`)
),
Company_year_rank AS (
  SELECT *,
         DENSE_RANK() OVER(PARTITION BY years ORDER BY total_laid_off DESC) AS ranking
  FROM Company_year
  WHERE years IS NOT NULL
)
SELECT *
FROM Company_year_rank
WHERE ranking <= 5;
```

Identifies the top 5 companies with the highest layoffs for each year using window functions and ranking.

---

### Key Skills Demonstrated

- SQL aggregation and grouping
- Window functions (`SUM OVER`, `DENSE_RANK`)
- Common Table Expressions (CTEs)
- Time-based analysis
- Real-world EDA workflow

---

### Conclusion

This EDA provides insights into:

- Layoff trends over time
- Most affected companies, industries, and countries
- Impact of funding stage and financial backing
- Year-wise and monthly progression of layoffs

The analysis can be extended further using visualization tools or integrated into dashboards.
