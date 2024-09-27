# EXPLORATORY DATA ANALYSIS
### PROJECT OVERVIEW
This is the exploratory data analysis of bookstore dataset using MySQL server.
Here I am just going to explore the data, to find trends and patterns of the dataset

### Dataset Source
[Download here](https://www.kaggle.com/datasets/swaptr/layoffs-2022)

### Tools
- MySQL Server

### Data Analysis

```sql
SELECT * FROM bookstore_dup;

-- Looking for the book with the highest and lowest price
SELECT BookTitle, MAX(PurchaseAmount) highest_price
FROM bookstore_dup
GROUP BY BookTitle
ORDER BY highest_price DESC
LIMIT 1;

SELECT BookTitle, MIN(PurchaseAmount) lowest_price
FROM bookstore_dup
GROUP BY BookTitle
ORDER BY lowest_price ASC
LIMIT 1;

-- Total Sum of the books purchased

SELECT ROUND(SUM(PurchaseAmount), 2)
FROM bookstore_dup;

-- Looking for books with highest purchase

SELECT BookTitle, COUNT(BookTitle) as no_of_purchases
FROM bookstore_dup
GROUP BY BookTitle
ORDER BY no_of_purchases DESC;

-- Looking for total sum of books purchased per month
SELECT SUBSTRING(PurchaseDate, 1, 7) AS MONTH, ROUND(SUM(PurchaseAmount), 2)
FROM bookstore_dup
GROUP BY MONTH
ORDER BY MONTH ASC
```
