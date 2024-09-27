# SQL Project: Data Cleaning
### PROJECT OVERVIEW
This is a project based on the cleaning of a bookstore dataset with the use of SQL server specifically MySQL

### Dataset Source
[Download here](https://www.kaggle.com/datasets/armanmanteghi/bookstore-sales-cleaning-and-preprocessing-data?select=Initial_Data.csv)

### Tools
- MySQL Server

### Data Analysis
``` sql
-- Checking for duplicates in the dataset
-- There are no duplicates

SELECT customerID, COUNT(*)
FROM bookstore
GROUP BY customerID
HAVING COUNT(*) > 1;

SELECT *
FROM bookstore
WHERE customerID IN (
  SELECT customerID
  FROM bookstore
  GROUP BY customerID
  HAVING COUNT(*) > 1
);

-- Creating a duplicate table to work on to keep the original data
DROP TABLE IF EXISTS bookstore_dup;
CREATE TABLE bookstore_dup AS 
SELECT * FROM bookstore;

SELECT * FROM bookstore_dup;

-- STANDARDIZATION
-- removing leading and trailing spaces
SELECT Name,  Email, TRIM(Name), TRIM(Email), TRIM(PhoneNumber) FROM bookstore_dup;

UPDATE bookstore_dup
SET Name = TRIM(Name), Email = TRIM(Email), PhoneNumber = TRIM(PhoneNumber) ;

-- removing leading and trailing spaces and having case consistency
SELECT Address, UPPER(TRIM(Address)) FROM bookstore_dup;
UPDATE bookstore_dup
SET Address = UPPER(TRIM(Address));

-- Checking for invalid email

SELECT Email FROM bookstore_dup
WHERE Email NOT LIKE '%@%';

-- removing the period from the book_title column to make the dataset look standardized
SELECT BookTitle, TRIM(TRAILING '.' FROM BookTitle)
FROM bookstore_dup;

UPDATE bookstore_dup
SET BookTitle = TRIM(TRAILING '.' FROM BookTitle);

-- Standardizing the phone numbers
-- removing the numbers which come after the 'x'

SELECT PhoneNumber, REPLACE(PhoneNumber, 
	SUBSTRING(PhoneNumber, 
		LOCATE('x', PhoneNumber)), '')
FROM bookstore_dup;

UPDATE bookstore_dup
SET PhoneNumber = REPLACE(PhoneNumber, 
	SUBSTRING(PhoneNumber, 
		LOCATE('x', PhoneNumber)), '');

-- Replacing periods with hyphens 

SELECT PhoneNumber, REPLACE(PhoneNumber, '.', '-')
FROM bookstore_dup;

UPDATE bookstore_dup
SET PhoneNumber = REPLACE(PhoneNumber, '.', '-');

-- Replacing 00 with +

SELECT PhoneNumber AS original,
CASE
	WHEN SUBSTR(PhoneNumber,1,2) = '00'
    THEN CONCAT('+', SUBSTR(PhoneNumber,3))
    ELSE PhoneNumber
END AS formatted
FROM bookstore_dup;

UPDATE bookstore_dup
SET PhoneNumber = CASE
	WHEN SUBSTR(PhoneNumber,1,2) = '00'
    THEN CONCAT('+', SUBSTR(PhoneNumber,3))
    ELSE PhoneNumber
END ;

-- removing Parenthesis and inserting hyphen
SELECT PhoneNumber, REPLACE(REPLACE(PhoneNumber,'(', ''), ')','')
FROM bookstore_dup;

UPDATE bookstore_dup
SET PhoneNumber = REPLACE(REPLACE(PhoneNumber,'(', ''), ')','') ;

SELECT PhoneNumber AS original,
CASE
WHEN LOCATE('-', SUBSTR(PhoneNumber,1,4)) = 0
			THEN CONCAT(SUBSTR(PhoneNumber,1,3), '-',
            SUBSTRING(PhoneNumber, 4))
		ELSE PhoneNumber
END AS formatted
FROM bookstore_dup;

UPDATE bookstore_dup
SET PhoneNumber = CASE
WHEN LOCATE('-', SUBSTR(PhoneNumber,1,4)) = 0
			THEN CONCAT(SUBSTR(PhoneNumber,1,3), '-',
            SUBSTRING(PhoneNumber, 4))
		ELSE PhoneNumber
END ;

SELECT PhoneNumber AS original,
	CASE 
		WHEN LOCATE('-', PhoneNumber) = 0
        AND LENGTH(PhoneNumber) = 10
			THEN CONCAT(SUBSTR(PhoneNumber,1,3), '-',
            SUBSTR(PhoneNumber,4,3), '-',
            SUBSTR(PhoneNumber, 7))
		ELSE PhoneNumber
	END AS formatted
    FROM bookstore_dup;
    
    UPDATE bookstore_dup
    SET PhoneNumber = CASE 
		WHEN LOCATE('-', PhoneNumber) = 0
        AND LENGTH(PhoneNumber) = 10
			THEN CONCAT(SUBSTR(PhoneNumber,1,3), '-',
            SUBSTR(PhoneNumber,4,3), '-',
            SUBSTR(PhoneNumber, 7))
		ELSE PhoneNumber
	END;
    
    -- adding +1 to numbers without the country code
    
    SELECT PhoneNumber AS original,
    CASE 
		WHEN NOT PhoneNumber LIKE '+1-%' 
		THEN CONCAT('+1-', PhoneNumber)
		ELSE PhoneNumber
    END AS formatted
    FROM bookstore_dup;
    
    UPDATE bookstore_dup
    SET PhoneNumber = CASE 
		WHEN NOT PhoneNumber LIKE '+1-%' 
		THEN CONCAT('+1-', PhoneNumber)
		ELSE PhoneNumber
    END;
    
    -- removing short and invalid numbers
    SELECT PhoneNumber
    FROM bookstore_dup
    WHERE LENGTH(REPLACE(REPLACE(REPLACE(REPLACE(PhoneNumber, '-',''),')',''), '(',''), '+','')) < 10;
    
    UPDATE bookstore_dup
    SET PhoneNumber = NULL
    WHERE LENGTH(REPLACE(REPLACE(REPLACE(REPLACE(PhoneNumber, '-',''),')',''), '(',''), '+','')) < 10;
    
    SELECT * FROM bookstore_dup;
    
-- conversion of PurchaseDate from string to date

SELECT PurchaseDate ,
STR_TO_DATE(PurchaseDate, '%m/%d/%Y' )
FROM bookstore_dup;

UPDATE bookstore_dup
SET PurchaseDate = STR_TO_DATE(PurchaseDate, '%m/%d/%Y');

SELECT * 
FROM bookstore_dup;

ALTER TABLE bookstore_dup
MODIFY COLUMN PurchaseDate DATE;

-- Dealing with null and blank values
-- Finding the columns that are empty and null, then setting the empty values with Null 

SELECT * FROM bookstore_dup
WHERE Email IS NULL OR Email = '';

UPDATE bookstore_dup
SET Email = NULL
WHERE Email = '';
 
 SELECT * FROM bookstore_dup
 WHERE Address = '';
 
UPDATE bookstore_dup
SET Address = NULL
WHERE Address = '';

SELECT * FROM bookstore_dup
WHERE BookTitle IS NULL AND BookTitle = '';

SELECT * FROM bookstore_dup

```
