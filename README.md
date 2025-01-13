# SQL Data Cleaning and Exploratory Data Analysis (EDA) Project

# Project Overview

This project focuses on cleaning and analyzing a dataset of global layoffs using SQL. The dataset is sourced from Kaggle and includes information about layoffs across various industries, countries, and companies.

# Data Cleaning Steps

The data cleaning process involved several steps to ensure the dataset was ready for analysis:

# Creation of a Staging Table:

A staging table (layoffs_staging) was created to work on a duplicate of the raw data.

# Removing Duplicates:

Identified duplicate rows using ROW_NUMBER() and removed them.

# Standardizing Data:

Converted blank fields to NULL.

Standardized industry names (e.g., "Crypto Currency" and "CryptoCurrency" were unified as "Crypto").

Corrected inconsistent country names (e.g., "United States." to "United States").

Converted the date field to a proper DATE type using STR_TO_DATE().

# Handling Null Values:

Retained null values for numeric fields (total_laid_off, percentage_laid_off, funds_raised_millions) for easier calculations during analysis.

# Dropping Unnecessary Rows and Columns:

Removed rows where total_laid_off and percentage_laid_off were both NULL.

Dropped helper columns like row_num after their use.

# Exploratory Data Analysis (EDA)

The EDA phase explored trends, patterns, and potential outliers in the dataset. Queries were designed to extract insights across different dimensions.

# 1 Key Insights and Queries

Easier Queries

Maximum Layoffs:

SELECT MAX(total_laid_off)  

FROM world_layoffs.layoffs_staging2;

# 2 Percentage Layoffs:

SELECT MAX(percentage_laid_off), MIN(percentage_laid_off)

FROM world_layoffs.layoffs_staging2

WHERE percentage_laid_off IS NOT NULL;

# 3 Companies with 100% Layoffs:

SELECT *

FROM world_layoffs.layoffs_staging2

WHERE percentage_laid_off = 1;

# Grouped Analysis

# 1 Companies with the Most Layoffs (Single Day):

SELECT company, total_laid_off

FROM world_layoffs.layoffs_staging

ORDER BY 2 DESC

LIMIT 5;

# 2 Total Layoffs by Location:

SELECT location, SUM(total_laid_off)

FROM world_layoffs.layoffs_staging2

GROUP BY location

ORDER BY 2 DESC

LIMIT 10;

# 3 Layoffs by Year:

SELECT YEAR(date), SUM(total_laid_off)

FROM world_layoffs.layoffs_staging2

GROUP BY YEAR(date)

ORDER BY 1 ASC;

Advanced Analysis

# 4 Top Companies by Layoffs per Year:

WITH Company_Year AS 
(
  SELECT company, YEAR(date) AS years, SUM(total_laid_off) AS total_laid_off

  FROM layoffs_staging2
  
  GROUP BY company, YEAR(date)
)

, Company_Year_Rank AS (

  SELECT company, years, total_laid_off, DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS ranking
  
  FROM Company_Year
)

SELECT company, years, total_laid_off, ranking

FROM Company_Year_Rank

WHERE ranking <= 3

AND years IS NOT NULL

ORDER BY years ASC, total_laid_off DESC;

# 5 Rolling Total of Layoffs Per Month:

WITH DATE_CTE AS 

(
  
  SELECT SUBSTRING(date,1,7) as dates, SUM(total_laid_off) AS total_laid_off

  FROM layoffs_staging2
  
  GROUP BY dates
  
  ORDER BY dates ASC

)

SELECT dates, SUM(total_laid_off) OVER (ORDER BY dates ASC) as rolling_total_layoffs

FROM DATE_CTE

ORDER BY dates ASC;

# Future Work

Visualize trends in layoffs using tools like Tableau or Power BI.

Analyze correlations between layoffs and external economic factors.

Identify industries most affected by layoffs and study recovery trends.

 # Files Included

SQL Scripts: Contains the SQL queries for data cleaning and EDA.

README.md: Documentation of the project.

Dataset: Source data downloaded from Kaggle.

# Acknowledgments

Special thanks to Kaggle for providing the dataset.
