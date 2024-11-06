# employee-dataset

Please see attached for the dataset.csv file downloaded from kaggle and the .sql file code below:

-- Create table for employee dataset.csv to upload data from kaggle to pgadmin. 

CREATE TABLE employees (
		Education VARCHAR(255), 
		JoiningYear INT, 
		City VARCHAR(100),
		PaymentTier INT, 
		Age INT,
		Gender VARCHAR(10),
		EverBenched BOOLEAN, 
		ExperienceInCurrentDomain FLOAT, 
		LeaveOrNot BOOLEAN
);

-- After importing employee dataset.csv to the table, let's see what kind of data we're working with. 

SELECT * FROM employees LIMIT 10;

-- Things look good so far. Let's check for missing values. 

SELECT 
	SUM (CASE WHEN Education IS NULL THEN 1 ELSE 0 END) AS Education_Missing,
	SUM (CASE WHEN JoiningYear IS NULL THEN 1 ELSE 0 END) AS JoiningYear_Missing,
	SUM (CASE WHEN City IS NULL THEN 1 ELSE 0 END) AS City_Missing,
	SUM (CASE WHEN PaymentTier IS NULL THEN 1 ELSE 0 END) AS PaymentTier_Missing,
	SUM (CASE WHEN Age IS NULL THEN 1 ELSE 0 END) AS Age_Missing,
	SUM (CASE WHEN Gender IS NULL THEN 1 ELSE 0 END) AS Gender_Missing,
	SUM (CASE WHEN EverBenched IS NULL THEN 1 ELSE 0 END) AS EverBenched_Missing,
	SUM (CASE WHEN ExperienceInCurrentDomain IS NULL THEN 1 ELSE 0 END) AS ExperienceInCurrentDomain_Missing,
	SUM (CASE WHEN LeaveOrNot IS NULL THEN 1 ELSE 0 END) AS LeaveOrNot_Missing		 
FROM employees;

-- No missing values. Let's also look for duplicates.

SELECT *, COUNT (*)
FROM employees
GROUP BY Education, JoiningYear, City, PaymentTier, Age, Gender, EverBenched, ExperienceInCurrentDomain, LeaveOrNot
HAVING COUNT (*) > 1;

-- It seems there are a few duplicates, for testing purposes I'm interested in unique values. Let's create a unique values table.  

CREATE TABLE cleaned_employees AS SELECT DISTINCT * FROM employees;

-- Let's see how it looks.

SELECT * FROM cleaned_employees LIMIT 10;

-- Looks good, let's do some exploratory data analysis in a 3 step approach.  

-- 1. Descriptive Analytics 

-- Total Number of Employees 

SELECT COUNT(*) AS Total_Employees 
FROM cleaned_employees;

-- Gender Breakdown 

SELECT Gender, COUNT(*) AS gender_count,
	ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM cleaned_employees)),2) AS percentage
FROM cleaned_employees 
GROUP BY Gender;

-- Average Age of Employees 

SELECT AVG(Age) AS Average_Age 
FROM cleaned_employees; 

--Employee Tenure (Experience)

SELECT AVG(ExperienceInCurrentDomain) AS Average_Experience 
FROM cleaned_employees; 

--City-Wise Employee Distribution

SELECT City, COUNT(*) AS Count
FROM cleaned_employees 
GROUP BY City
ORDER BY Count DESC; 

--Employees Ever Benched 

SELECT EverBenched, COUNT(*) AS Count,
	(COUNT(*) * 100 / (SELECT COUNT(*) FROM cleaned_employees)) AS Percentage
FROM cleaned_employees
GROUP BY EverBenched;

-- Attrition Rate 

SELECT (SUM(CASE WHEN LeaveOrNot = TRUE THEN 1 ELSE 0 END) * 100.0 / COUNT(*)) AS Attrition_Rate
FROM cleaned_employees; 

-- Payment Tier Distribution 

SELECT PaymentTier, COUNT(*) AS Count
FROM cleaned_employees
GROUP BY PaymentTier; 

-- Joining Year Breakdown

SELECT JoiningYear, COUNT(*) AS Count
FROM cleaned_employees
GROUP BY JoiningYear
ORDER BY JoiningYear;

-- Education Level Breakdown	

SELECT Education, COUNT(*) AS Count, 
	(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM cleaned_employees)) AS percentage
FROM cleaned_employees
GROUP BY Education;

-- 2. Employee Retention Analysis.

-- Average Age of Retained Employees 

SELECT AVG(Age) AS Avg_Age_Of_Retained
FROM cleaned_employees
WHERE LeaveOrNot = FALSE;

-- Education Distribution Among Retained Employees

SELECT Education, COUNT(*) AS Count_Of_Retained 
FROM cleaned_employees 
WHERE LeaveOrNot = FALSE 
GROUP BY Education;

-- Average Experience in Current Domain for Retained Employees

SELECT AVG(ExperienceInCurrentDomain) AS Avg_Experience_Of_Retained
FROM cleaned_employees
WHERE LeaveOrNot = FALSE;

-- Gender Distribution Among Retained Employees: 

SELECT Gender, COUNT(*) AS Count_Of_Retained
FROM cleaned_employees
WHERE LeaveOrNot = FALSE 
GROUP BY Gender; 

-- City-Wise Retention Distribution 

SELECT City, COUNT(*) AS Count_Of_Retained
FROM cleaned_employees
WHERE LeaveOrNot = FALSE 
GROUP BY City; 

-- Retention by Payment Tier 

SELECT PaymentTier, COUNT(*) AS Count_Of_Retained
FROM cleaned_employees
WHERE LeaveOrNot = FALSE
GROUP BY PaymentTier;

-- 3. Employee Attrition Analysis 

-- Overall Attrition Rate (Employee Churn) 

SELECT ROUND((COUNT(*) * 100.0 / (SELECT COUNT(*) FROM cleaned_employees)), 2) AS Attrition_Rate
FROM cleaned_employees
WHERE LeaveOrNot = TRUE;

-- Attrition by Education Level 

SELECT Education, COUNT(*) AS Education_Count_Of_Attrition
FROM cleaned_employees 
WHERE LeaveOrNot = TRUE
GROUP BY Education; 

-- Average Age of Employees Who Left

SELECT AVG(Age) AS Avg_Age_Of_Attrition
FROM cleaned_employees
WHERE LeaveOrNot = TRUE; 

-- Average Experience of Attrition

SELECT AVG(ExperienceInCurrentDomain) AS Avg_Experience_Of_Attrition
FROM cleaned_employees 
WHERE LeaveOrNot = TRUE; 

-- Gender Distribution Among Employees Who Left

SELECT Gender, COUNT(*) AS Gender_Count_Of_Attrition
FROM cleaned_employees
WHERE LeaveOrNot = TRUE
GROUP BY City; 

-- Attrition by Payment Tier

SELECT PaymentTier, COUNT(*) AS PaymentTier_Count_Of_Attrition
FROM cleaned_employees
WHERE LeaveOrNot = TRUE 
GROUP BY PaymentTier;	
