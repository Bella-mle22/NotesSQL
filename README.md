# NotesSQL
PostgresSQL


SELECT *
FROM employees
WHERE department = 'Sport'

SELECT * 
FROM employees
WHERE salary > 20000
AND (department = 'Tools' OR department = 'Funiture')

SELECT * 
FROM employees
WHERE NOT department = 'Tools'

SELECT * 
FROM employess
WHERE department <> 'Tools'

SELECT *
FROM employees
WHERE NOT department <> 'Tools'
//(WHERE department = 'Tools') 

NULL is Never gonna equals to NULL
NULL = NULL ---False
NULL != NULL --False--Reason: NULL cannot be compare to NULL(no values)

/* WRONG
SELECT *
FROM employees
WHERE email = NULL
*/

/*RIGHT
SELECT *
FROM employees
WHERE email IS NULL
*/

WHERE NOT email IS NULL
WHERE email IS NOT NULL

SELECT *
FROM employees
WHERE department = 'Sports'
OR department = 'First Aid'
OR department = 'Toys'
OR department = 'Garden'

SELECT *
FROM employees
WHERE department IN ('Sport', 'First Aid', 'Toys', 'Garden')

SELECT *
FROM employees
WHERE salary BETWEEN 80000 AND 100000

SELECT first_name, hire_date
FROM employees
WHERE salary > 165000
OR (department = 'Sports' AND gender = 'M')

SELECT first_name, hire_date
FROM employees
WHERE hire_date BETWEEN '2002-01-01' AND '2004-02-02'

SELECT * 
FROM employees
WHERE salary > 40000
AND salary < 100000
AND department = 'Automotive'
AND gender = 'M'
OR (gender = 'F' AND department = 'Toys')

SELECT *
FROM employees
ORDER BY employee_id DESC
--ASC is by default

SELECT department
FROM employees
ORDER BY 1
LIMIT 10

SELECT DISTINCT department
FROM employees
Order by 1
FERCH FIRST 10 ROWS ONLY

SELECT UPPER(first_name), LOWER(department)  //It just displays Upper/Lower, it does not change the real data in database.
FROM employees

SELECT LENGTH(first_name), LOWER(department)
FROM employees

SELECT TRIM('  HELLO THERE  ')

SELECT first_name || last_name
FROM employees

SELECT first_name ||' '|| last_name full_name, (salary > 140000) is_highly_paid
FROM employees
ORDER BY salary desc

SELECT department, ('Curwood' in (first_name, last_name))
FROM employees

SELECT department, (department like '%oth%')
FROM employees

SELECT 'This is test data' test_data
SELECT SUBSTRING('This is test data' FROM 1 FOR 4) test_data_extracted
SUBSTRING('This is test data' FROM 9 FOR 4) test_data_extracted

SELECT department, REPLACE(department, 'Clothing', 'Attire') modified_data
FROM departments

SELECT department,
REPLACE(department, 'Clothing', 'Attire') modified_data,
department || ' department' as "Complete Department Name"
FROM departments

SELECT email, SUBSTRING(email, POSITION('@' IN email) + 1) formated_text
FROM employees

SELECT COALESCE(email, 'NONE') as email
FROM employees

SELECT MAX(salary)
FROM employees

SELECT SUM(*)
FROM employees
WHERE department = 'Toys'

SELECT COUNT(*), make
FROM cars
GROUP BY make

SELECT make
FROM cars
GROUP BY make

SELECT make, count(*)
FROM cars
GROUP BY make

SELECT SUM(salary) 
FROM employees
WHERE 1 = 1
GROUP BY department

SELECT department, COUNT(*)
FROM employees
GROUP BY department

SELECT department, count(*), AVG(salary), MIN(salary), MAX(salary)
FROM employees
GROUP BY department

SELECT department, count(*) total_number_employees,
ROUND(AVG(salary)) avg_sal, MIN(salary) min_sal,
MAX(salary) max_sal
FROM employees 
GROUP BY department

/* Wrong
SELECT department, count(*)
FROM employees
WHERE count(*) < 35
GROUP BY department
ORDER BY department

WHERE is not used to filter aggregated data to filter aggregated data. Using "having"
*/

SELECT department, count(*)
FROM employees
GROUP BY department
HAVING count(*) > 35
ORDER BY department

SELECT first_name, count(*)
FROM employees
GROUP BY first_name
HAVING count(*) > 2

SELECT DISTINCT department
FROM employees

SELECT department
FROM employees
GROUP BY department

SELECT department, count(*)
FROM employees
GROUP BY department

SELECT SUBSTRING(email, position('@' IN email) + 1) as email_domain,
count(*)
FROM employees
GROUP BY SUBSTRING(email, position('@' IN email) + 1)

/* SUBSTRING is not aggregate columns and is not using functions like count or sum and so on. It is needed be use in GROUP BY*/

SELECT *
FROM employees
WHERE department NOT IN (SELECT department FROM departments)

SELECT * 
FROM employees
WHERE region_id IN 
(SELECT region_id 
FROM regions 
WHERE country = 'United States')

SELECT *
FROM employees
WHERE region_id > ALL 
(SELECT region_id 
 FROM region 
 WHERE country = 'Inited States')
 
 SELECT *
FROM employees
WHERE department = ANY (
						SELECT department 	
						FROM departments
						WHERE division = 'Kids')
AND hire_date > ALL (
					SELECT hire_date
					FROM employees
					WHERE department = 'Maintenance')


SELECT salary, count(*)
FROM employees

SELECT salary, count(*)
FROM employees
GROUP BY salary

SELECT salary, count(*)
FROM employees
GROUP BY salary
ORDER BY count(*)

SELECT salary, count(*)
FROM employees
GROUP BY salary
ORDER BY count(*), salary desc

SELECT salary, count(*)
FROM employees
GROUP BY salary
ORDER BY count(*), salary desc

SELECT salary, count(*)
FROM employees
GROUP BY salary
ORDER BY count(*), salary desc
LIMIT 1

SELECT salary
FROM (
	SELECT salary, count(*)
	FROM employees
	GROUP BY salary
	ORDER BY count(*), salary desc
	LIMIT 1
) a

CREATE table dupes (id integer, name varchar(10));
											 
INSERT INTO dupes VALUES (1, 'FRANK');
INSERT INTO dupes VALUES (2, 'FRANK');
INSERT INTO dupes VALUES (3, 'ROBERT');
INSERT INTO dupes VALUES (4, 'ROBERT');
INSERT INTO dupes VALUES (5, 'SAM');
INSERT INTO dupes VALUES (6, 'FRANK');
INSERT INTO dupes VALUES (7, 'PEPTER');

SELECT min(id), name
FROM dupes
GROUP BY name

/* Wrong -- We expect a single column with multiple values using IN
SELECT * FROM dupes
WHERE id IN (
	SELECT min(id), name
	FROM dupes
	GROUP BY name
)
*/

SELECT * FROM dupes
WHERE id IN (
	SELECT min(id)
	FROM dupes
	GROUP BY name
)

DELETE FROM dupes
WHERE id NOT IN (
	SELECT min(id)
	FROM dupes
	GROUP BY name
)

DROP table dupes

SELECT ROUNG(AVG(salary))
FROM employees
WHERE salary NOT IN (
(SELECT MIN(salary) FROM employees),
(SELECT MAX(salary) FROM employees)
)

SELECT first_name, salary,
CASE
	WHEN salary < 100000 THEN 'UNDER PAID'
	WHEN salary > 100000 THEN 'PAID WELL'
END
FROM employees
ORDER BY salary desc

SELECT first_name, salary,
CASE
	WHEN salary < 100000 THEN 'UNDER PAID'
	WHEN salary > 100000 AND salary < 160000 THEN 'PAID WELL'
	ELSE 'EXECUTIVE'
END
FROM employees
ORDER BY salary DESC

SELECT first_name, salary,
CASE
	WHEN salary < 100000 THEN 'UNDER PAID'
	WHEN salary > 100000 AND salary < 160000 THEN 'PAID WELL'
	WHEN salary > 160000 THEN 'EXECUTIVE'
	ELSE 'UNPAID'
END as category
FROM employees
ORDER BY salary DESC

/* This is not gonna work
SELECT a.category, COUNT(*) FROM(
SELECT first_name, salary,
CASE
	WHEN salary < 100000 THEN 0
	WHEN salary > 100000 AND salary < 160000 THEN 1
	WHEN salary > 160000 THEN 2
	ELSE 'UNPAID'
END as category
FROM employees
ORDER BY salary desc
) a
GROUP BY a.category
*/

SELECT a.category, COUNT(*) FROM
(SELECT first_name, salary,
CASE
	WHEN salary < 100000 THEN 0
	WHEN salary > 100000 AND salary < 160000 THEN 1
	WHEN salary > 160000 THEN 2
	ELSE 3
END as category
FROM employees
ORDER BY salary desc) a
GROUP BY a.category

SELECT SUM(CASE WHEN salary < 100000 THEN 1 ELSE 0 END) as under_paid,
SUM(CASE WHEN salary > 100000 AND salary < 150000 THEN 1 ELSE 0 END) as paid_well,
SUM(CASE WHEN salary > 150000 THEN 1 ELSE 0 END) as executive
FROM employees

SELECT department, count(*)
FROM employees
WHERE department IN ('Sports', 'Tools', 'Clothing', 'Computers')
GROUP BY department

SELECT SUM(CASE WHEN department = 'Sports' THEN 1 ELSE 0 END) as Sports_department,
SUM(CASE WHEN department = 'Tools' THEN 1 ELSE 0 END) as Tools_department,
SUM(CASE WHEN department = 'Clothing' THEN 1 ELSE 0 END) as Clothing_department,
SUM(CASE WHEN department = 'Computers' THEN 1 ELSE 0 END) as Computers_department
FROM employees

SELECT * FROM regions

SELECT first_name,
CASE WHEN region_id = 1 
THEN (SELECT country FROM regions WHERE region_id = 1) END region_1,
CASE WHEN region_id = 2
THEN (SELECT country FROM regions WHERE region_id = 2) END region_2,
CASE WHEN region_id = 3
THEN (SELECT country FROM regions WHERE region_id = 3) END region_3,
CASE WHEN region_id = 4
THEN (SELECT country FROM regions WHERE region_id = 4) END region_4,
CASE WHEN region_id = 5
THEN (SELECT country FROM regions WHERE region_id = 5) END region_5,
CASE WHEN region_id = 6
THEN (SELECT country FROM regions WHERE region_id = 6) END region_6,
CASE WHEN region_id = 7
THEN (SELECT country FROM regions WHERE region_id = 7) END region_7
FROM employees


SELECT 2 + 3 added_numbers

SELECT * FROM (
SELECT first_name,
CASE WHEN region_id = 1 
THEN (SELECT country FROM regions WHERE region_id = 1) END region_1,
CASE WHEN region_id = 2
THEN (SELECT country FROM regions WHERE region_id = 2) END region_2,
CASE WHEN region_id = 3
THEN (SELECT country FROM regions WHERE region_id = 3) END region_3,
CASE WHEN region_id = 4
THEN (SELECT country FROM regions WHERE region_id = 4) END region_4,
CASE WHEN region_id = 5
THEN (SELECT country FROM regions WHERE region_id = 5) END region_5,
CASE WHEN region_id = 6
THEN (SELECT country FROM regions WHERE region_id = 6) END region_6,
CASE WHEN region_id = 7
THEN (SELECT country FROM regions WHERE region_id = 7) END region_7
FROM employees
) a 

SELECT COUNT(a.region_1) + COUNT(a.region_2) + COUNT(region_3) as United_States,
COUNT(a.region_4) + COUNT(a.region_5) as Asia, 
COUNT(a.region_6) + COUNT(a.region_7) as Canada
 FROM (
SELECT first_name,
CASE WHEN region_id = 1 
THEN (SELECT country FROM regions WHERE region_id = 1) END region_1,
CASE WHEN region_id = 2
THEN (SELECT country FROM regions WHERE region_id = 2) END region_2,
CASE WHEN region_id = 3
THEN (SELECT country FROM regions WHERE region_id = 3) END region_3,
CASE WHEN region_id = 4
THEN (SELECT country FROM regions WHERE region_id = 4) END region_4,
CASE WHEN region_id = 5
THEN (SELECT country FROM regions WHERE region_id = 5) END region_5,
CASE WHEN region_id = 6
THEN (SELECT country FROM regions WHERE region_id = 6) END region_6,
CASE WHEN region_id = 7
THEN (SELECT country FROM regions WHERE region_id = 7) END region_7
FROM employees
) a 


Understanding Correlated Subquery

SELECT first_name, salary 
FROM employees e1
WHERE salary > (SELECT round(AVG(salary)) FROM employees)


Let s get all the employees whose salary is above the average department salary
SELECT first_name, salary
FROM employees e1
WHERE salary > (SELECT round(AVG(salary))
				FROM employees e2 WHERE e1.department = e2.department)

SELECT department
FROM department
WHERE 38 < (SELECT COUNT(*)
		   FROM employees
			WHERE e.department = departments.department
		   )


SELECT department, first_name, salary,
		(SELECT max(salary) FROM employees e2
		WHERE e1.department = e2.department)
FROM employees e1

SELECT department, first_name, salary,
		(SELECT max(salary) FROM employees e2
		WHERE e1.department = e2.department) as max_by_department,
		(SELECT min(salary) FROM employees e3
		WHERE e1.department = e3.department) as min_by_department
FROM employees e1
ORDER BY department


SELECT department, first_name, salary FROM (
SELECT department, first_name, salary,
	(SELECT max(salary) FROM employees e2
	WHERE e1.department = e2.department) as max_by_department,
	(SELECT min(salary) FROM employees e2
	WHERE e1.department = e2.department) as min_by_department
FROM employees e1
ORDER BY department
) a
WHERE salary = max_by_department OR salary = min_by_department

SELECT department, first_name, salary,
CASE WHEN salary = max_by_department THEN 'HIGHEST SALARY'
	 WHEN salary = min_by_department THEN 'LOWEST SALARY'
END as salary_in_department
FROM (
SELECT department, first_name, salary,
	(SELECT max(salary) FROM employees e2
	WHERE e1.department = e2.department) as max_by_department,
	(SELECT min(salary) FROM employees e2
	WHERE e1.department = e2.department) as min_by_department
FROM employees e1
ORDER BY department
) a
WHERE salary = max_by_department OR salary = min_by_department

Introduction Table Joins

SELECT first_name, country
FROM employees, regions
WHERE employees.region_id = regions.region_id

SELECT first_name, email, division, country
FROM employees e, departments d, regions r
WHERE e.department = d.department
AND e.region_id = r.region_id
AND email IS NOT NULL

SELECT first_name, country
FROM employees e, regions r
WHERE e.region_id = r.region_id

SELECT country, count(employee_id)
FROM employees e, regions r
WHERE e.region_id = r.region_id
GROUP BY country

SELECT first_name, email, division
FROM employees INNER JOIN departments
ON employees.department = departments.department
WHERE email IS NOT NULL

SELECT distinct department FROM employees
-- 27 departments

SELECT distinct department FROM departments
-- 24 departments

SELECT distinct employees.department, departments.department
FROM employees INNER JOIN departments ON employees.department = departments.department
-- 23 departments

SELECT distinct employees.department, departments.department
FROM employees LEFT JOIN departments ON employees.department = departments.department


SELECT distinct employees.department, departments.department
FROM employees LEFT JOIN departments ON employees.department = departments.department
WHERE departments.department IS NULL

SELECT distinct employees.department, departments.department
FROM employees FULL OUTER JOIN departments ON employees.department = departments.department

UNION, UNION ALL, EXCEPT

SELECT DISTINCT department
FROM employees
UNION
SELECT department
FROM departments
ORDER BY department
-- 27 distinct department in employees table and 24 distinct department in departments table
--Total 32 records by UNION


SELECT DISTINCT department
FROM employees
UNION ALL
SELECT department
FROM departments
-- Total 51 records

/*UNION eliminates duplicates while stacking the data on top of each other, 
whereas UNION ALL does not eliminates duplicates.
The query can not anything they could be sub queries and inside of sub-queries for the top one, and the bottom query could be just as complex.

The key thing to remember is that the columns must match. each UNION query must have the same number of columns. Also the data type should also be the same.
*/

SELECT DISTINCT department
FROM employees
UNION ALL
SELECT department
FROM departments
--This is a whole query. UNION ALL is part of the whole query.

SELECT DISTINCT department
FROM employees
ORDER BY department
UNION ALL 
SELECT department
FROM departments
--This does not work because ORDER BY always at the end of the query.


SELECT DISTINCT department
FROM employees
EXCEPT
SELECT department
FROM departments
--EXCEPT operator takes the first result set and removes from it all of the rows that are found in the second result.


SELECT department
FROM departments
EXCEPT
SELECT DISTINCT department
FROM employees
--EXCEPT operator takes the first result set and removes from it all of the rows that are found in the second result.
--Minus in Oracle does the same thing as EXCEPT in postgres 

SELECT department, COUNT(*)
FROM employees
GROUP BY department
UNION ALL
SELECT 'TOTAL', COUNT(*)
FROM employees


Cartesian

SELECT COUNT(*)
FROM employees, departments

Cartesian
SELECT COUNT(*)
FROM employees, departments

SELECT *
FROM employees, employees

SELECT *
FROM employees a, employees b

SELECT COUNT(*) FROM (
	SELECT *
	FROM employees a, employees b
) sub


SELECT COUNT(*) FROM (
	SELECT *
	FROM employees a, employees b, departments
) sub
--1000 * 1000 * 24

SELECT *
FROM employees a CROSS JOIN department b
--2400 records.

Assignment: Write me a query that returns 
the first name, department, the hire date 
and the country of the first employee 
that we hired in the company.
As well as, the last employees that we hired in the company.

SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MIN(hire_date) FROM employees e2)


SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MAX(hire_date) FROM employees e3)


SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MIN(hire_date) FROM employees e2)
UNION ALL
SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MAX(hire_date) FROM employees e3)

SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MIN(hire_date) FROM employees e2)
UNION ALL
SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MAX(hire_date) FROM employees e3)
ORDER BY hire_date

/* Wrong-- Union does not allow order by or limit before it.

SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MIN(hire_date) FROM employees e2)
LIMIT 1
UNION ALL
SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MAX(hire_date) FROM employees e3)
ORDER BY hire_date
*/

/* If you want to force limit one as part of the first query, you can put parentheses around it
(SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MIN(hire_date) FROM employees e2)
LIMIT 1)
UNION ALL
SELECT first_name, department, hire_date, country
FROM employees e INNER JOIN regions r
ON e.region_id = r.region_id
WHERE hire_date = (SELECT MAX(hire_date) FROM employees e3)
ORDER BY hire_date
*/

SELECT first_name, hire_date, hire_date - 90
FROM employees

SELECT first_name, hire_date, hire_date + 90
FROM employees

use particular date to check whether it exists between this date.
WHERE hire_date BETWEEN hire_date AND hire_date - 90

SELECT hire_date, salary, 
(SELECT SUM(salary) 
FROM employees e2
WHERE e2.hire_date BETWEEN e.hire_date - 90 AND e.hire_date) as spending_pattern
FROM employees e
ORDER BY hire_date
-- The oldest date is 2003-01-01, and check 2003-04-09. It is 90 days. Check the 90 days window, it s changing.

Creating Views vs. Inline Views

SELECT first_name, email, e.department, salary, division, regions, country
FROM employees e, departments d, regions r
WHERE e.department = d.department
AND e.region_id = r.region_id


How to turn this query into a view?

CREATE VIEW v_employees_information as
SELECT first_name, email, e.department, salary, division, regions, country
FROM employees e, departments d, regions r
WHERE e.department = d.department
AND e.region_id = r.region_id

SELECT *
FROM v_employee_information
--This is not a table, it is a view, you cannot insert/delete date from it.

Inline view--inside FROM clause, but v_employee_information is not inline view, it is hard view that has created in the database.

WINDOWS FUNCTION - OVER() 

SELECT * FROM employees

SELECT department, count(*)
FROM employees

SELECT department, count(*)
FROM employees
GROUP BY department

/* This is not what i want.
SELECT first_name, department, count(*)
FROM employees
GROUP BY department, first_name
*/

SELECT first_name, department, 
(SELECT COUNT(*) FROM employees e1 WHERE e1.department = e2.department)
FROM employees e2
GROUP BY department, first_name

SELECT first_name, department, 
COUNT(*) OVER()
FROM employees e2

SELECT first_name, department,
COUNT(*) OVER(PARTITION BY department)
FROM employees e2

Please memories both sql statement can return the same result!

SELECT first_name, department, 
(SELECT COUNT(*) FROM employees e1 WHERE e1.department = e2.department)
FROM employees e2
GROUP BY department, first_name

SELECT first_name, department,
COUNT(*) OVER(PARTITION BY department)
FROM employees e2

SELECT first_name, department, 
(SELECT COUNT(*) FROM employees e1 WHERE e1.department = e2.department)
FROM employees e2
GROUP BY department, first_name
EXCEPT
SELECT first_name, department,
COUNT(*) OVER(PARTITION BY department)
FROM employees e2
-- we cannot do ORDER BY Clause before UNION or EXCEPT

(SELECT first_name, department, 
(SELECT COUNT(*) FROM employees e1 WHERE e1.department = e2.department)
FROM employees e2
GROUP BY department, first_name)
EXCEPT
SELECT first_name, department,
COUNT(*) OVER(PARTITION BY department)
FROM employees e2

-- All the results that are being returned by the second query are being subtracted from the first one.

SELECT first_name, department,
SUM(salary) OVER (PARTITION BY department)
FROM employees e2

SELECT first_name, department,
SUM(salary) OVER()
FROM employees e2

SELECT first_name, department,
COUNT(*) OVER (PARTITION BY department) dept_count,
COUNT(*) OVER(PARTITION BY region_id) region_count
FROM employees e2

SELECT first_name, department, count(*) over()
FROM employees
WHERE region_id = 3


SELECT first_name, department, count(*) over()
FROM employees
WHERE region_id = 3

SELECT first_name, department, count(*) over(PARTITION BY department)
FROM employees
WHERE region_id = 3

Ordering data from Windows Frame

SELECT * FROM employees

SELECT first_name, hire_date, salary FROM employees

SELECT first_name, hire_date, salary 
FROM employees
ORDER BY hire_date

SELECT first_name, hire_date, salary,
SUM(salary) OVER (ORDER BY hire_date RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)
as running_total_of_salaries
FROM employees

-- By default it RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
SELECT first_name, hire_date, salary,
SUM(salary) OVER (ORDER BY hire_date)
as running_total_of_salaries
FROM employees

SELECT first_name, hire_date, department, salary, 
SUM(salary) OVER (PARTITION BY department ORDER BY hire_date) as running_total_of_salary
FROM employees

SELECT first_name, hire_date, department, salary, 
SUM(salary) OVER (ORDER BY hire_date ROWS BETWEEN 1 PRECEDING AND CURRENT ROW
				 )
FROM employees

SELECT first_name, hire_date, department, salary,
SUM(salary) OVER(ORDER BY hire_date ROWS BETWEEN 3 PRECEDING AND CURRENT ROW)
FROM employees

RANK, FIRST_VALUE and NTILE function

SELECT first_name, email, department, salary,
RANK() OVER(PARTITION BY department ORDER BY salary DESC)
FROM employees

SELECT * FROM (
SELECT first_name, email, department, salary,
RANK() OVER(PARTITION BY department ORDER BY salary desc)
FROM employees ) a
WHERE rank = 8


SELECT first_name, email, department, salary,
RANK() OVER(PARTITION BY department ORDER BY salary DESC)
FROM employees
WHERE rank = 8
-- the reason is because this entire thing(Rank line) is the actuall call
--This is processed pretty much at the end of the query after WHERE clause.
--The where clause is used to filter records from the employees table.
--and that becomes a universe of data on which this window function is applied.

Lets say you wanted to consider the first five highest employees as rank 1,
and then the next five highest employees as rank 2, and the next
five highest paid employees rank 3 and so on.

--NTILE() tell this function how many buckets of data we want created.
- NTITLE() it is going to split buckets of rows and theres only five buckets in each department 
SELECT first_name, email, department, salary,
NTILE(5) OVER(PARTITION BY department ORDER BY salary DESC)
FROM employees
--What if theres a department that has a number of employees that is not divisible by five then 
--it will try to match it as close as possible.

SELECT first_name, email, department, salary,
FIRST_VALUE(salary) OVER(PARTITION BY department ORDER BY salary DESC) first_value
FROM employees
--same as MAX().
SELECT first_name, email, department, salary,
MAX(salary) OVER(PARTITION BY department ORDER BY salary DESC) max_salary
FROM employees

--compare those two queries
SELECT first_name, email, department, salary,
FIRST_VALUE(salary) OVER(PARTITION BY department ORDER BY salary DESC) first_value
FROM employees
EXCEPT
SELECT first_name, email, department, salary,
MAX(salary) OVER(PARTITION BY department ORDER BY salary DESC) max_salary
FROM employees

SELECT first_name, email, department, salary,
NTH_VALUE(salary, 5) OVER(PARTITION BY department ORDER BY first_name asc) nth_value
FROM employees

LEAD and LAG functions

SELECT first_name, last_name, salary
FROM employees

SELECT first_name, last_name, salary,
LEAD(salary) OVER() next_salary
FROM employees

SELECT first_name, last_name, salary,
LAG(salary) OVER() previous_salary
FROM employees

SELECT department, last_name, salary,
LAG(salary) OVER(ORDER BY salary DESC) closest_higher_salry
FROM employees

SELECT department, last_name, salary,
LEAD(salary) OVER(ORDER BY salary DESC) closest_lower_salary
FROM employees

SELECT department, last_name, salary,
LEAD(salary) OVER(PARTITION BY department ORDER BY salary DESC)
FROM employees

CREATE TABLE sales (
	continent varchar(20),
	country varchar(20),
	city varchar(20),
	units_sold integer
);

INSERT INTO sales VALUES ('North America', 'Canada', 'Toronto', 10000);
INSERT INTO sales VALUES ('North America', 'Canada', 'Montreal', 5000);
INSERT INTO sales VALUES ('North America', 'Canada', 'Vancouver', 15000);
INSERT INTO sales VALUES ('Asia', 'China', 'Hong Kong', 7000);
INSERT INTO sales VALUES ('Asia', 'China', 'Shanghai', 3000);
INSERT INTO sales VALUES ('Asia', 'Japan', 'Tokyo', 5000);
INSERT INTO sales VALUES ('Europe', 'UK', 'London', 6000);
INSERT INTO sales VALUES ('Europe', 'UK', 'Manchester', 12000);
INSERT INTO sales VALUES ('Europe', 'France', 'Paris', 5000);

SELECT * FROM sales

SELECT * FROM sales
ORDER BY continent, country, city

SELECT continent, SUM(units_sold)
FROM sales
GROUP BY continent

SELECT country, SUM(units_sold)
FROM sales
GROUP BY country

SELECT city, SUM(units_sold)
FROM sales
GROUP BY city

SELECT continent, country, city, SUM(units_sold)
FROM sales
GROUP BY GROUPING SETS(continent, country, city)

SELECT continent, country, city, SUM(units_sold)
FROM sales
GROUP BY GROUPING SETS(continent, country, city, ())

SELECT continent, country, city, SUM(units_sold)
FROM sales
GROUP BY ROLLUP(continent, country, city)
					   
SELECT continent, country, city, SUM(units_sold)
FROM sales
GROUP BY CUBE(continent, country, city)

