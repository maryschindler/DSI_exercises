# Summarizing Data with SQL

## Summary Statistics
32) How many rows are in the `pets` table?
pd.read_sql("SELECT COUNT(*) FROM pets;", conn)

33) How many female pets are in the `pets` table?
pd.read_sql("SELECT COUNT(*) FROM pets WHERE pets.sex = 'F';", conn)

34) How many female cats are in the `pets` table?
pd.read_sql("SELECT COUNT(*) FROM pets WHERE pets.species = 'cat' AND pets.sex = 'F';", conn)

35) What's the mean age of pets in the `pets` table?
pd.read_sql("SELECT AVG(age) FROM pets;", conn)

36) What's the mean age of dogs in the `pets` table?
pd.read_sql("SELECT AVG(age) FROM pets WHERE pets.species = 'dog';", conn)

37) What's the mean age of male dogs in the `pets` table?
pd.read_sql("SELECT AVG(age) FROM pets WHERE pets.species = 'dog' AND pets.sex = 'M';", conn)

38) What's the count, mean, minimum, and maximum of pet ages in the `pets` table?
    * _NOTE:_ SQLite doesn't have built-in formulas for standard deviation or median!
pd.read_sql("SELECT COUNT(age) from PETS;", conn)
pd.read_sql("SELECT AVG(age) from PETS;", conn)
pd.read_sql("SELECT MAX(age) from PETS;", conn)
pd.read_sql("SELECT MIN(age) from PETS;", conn)

pd.read_sql("SELECT COUNT(age), AVG(age), MAX(age), MIN(age) from PETS;", conn)
    
39) Repeat the previous problem with the following stipulations:
    * Round the average to one decimal place.
    * Give each column a human-readable column name (for example, "Average Age")
pd.read_sql("SELECT COUNT(age) AS 'Rows in Table',\
            ROUND(AVG(age), 1) AS 'Average Age',\
            MAX(age) AS 'Max Age',\
            MIN(age) AS 'Min Age' from PETS;", conn)

40) How many rows in `employees_null` have missing salaries?
pd.read_sql("SELECT COUNT(*) FROM employees_null WHERE salary IS NULL;", conn)

41) How many salespeople in `employees_null` having _nonmissing_ salaries?
pd.read_sql("SELECT COUNT(*) FROM employees_null WHERE job is 'Sales' AND salary IS NOT NULL;", conn)

42) What's the mean salary of employees who joined the company after 2010? Go back to the usual `employees` table for this one.
    * _Hint:_ You may need to use the `CAST()` function for this. To cast a string as a float, you can do `CAST(x AS REAL)`
pd.read_sql("SELECT AVG(salary) FROM employees WHERE startdate > '2010-01-01';", conn)
    
43) What's the mean salary of employees in Swiss Francs?
    * _Hint:_ Swiss Francs are abbreviated "CHF" and 1 USD = 0.97 CHF.
pd.read_sql("SELECT (AVG(salary) * 0.97) AS salary_CHF FROM employees WHERE startdate > '2010-01-01';", conn)

44) Create a query that computes the mean salary in USD as well as CHF. Give the columns human-readable names (for example "Mean Salary in USD"). Also, format them with comma delimiters and currency symbols.
    * _NOTE:_ Comma-delimiting numbers is only available for integers in SQLite, so rounding (down) to the nearest dollar or franc will be done for us.
    * _NOTE2:_ The symbols for francs is simply `Fr.` or `fr.`. So an example output will look like `100,000 Fr.`.
    
NEED TO ANSWER NEED TO ANSWER NEED TO ANSWER
NEED TO ANSWER NEED TO ANSWER NEED TO ANSWER
NEED TO ANSWER NEED TO ANSWER NEED TO ANSWER

## Aggregating Statistics with GROUP BY
45) What is the average age of `pets` by species?
pd.read_sql("SELECT species, AVG(age) FROM pets GROUP BY species;", conn)

46) Repeat the previous problem but make sure the species label is also displayed! Assume this behavior is always being asked of you any time you use `GROUP BY`.
#I did this anyways:
pd.read_sql("SELECT species, AVG(age) FROM pets GROUP BY species;", conn)

47) What is the count, mean, minimum, and maximum age by species in `pets`?
pd.read_sql("SELECT species, COUNT(age), round(AVG(age), 2), MIN(age), MAX(age) FROM pets GROUP BY species;", conn)

48) Show the mean salaries of each job title in `employees`.
pd.read_sql("SELECT job, round(AVG(salary), 2) FROM employees GROUP BY job;", conn)

49) Show the mean salaries in New Zealand dollars of each job title in `employees`.
    * _NOTE:_ 1 USD = 1.65 NZD.
pd.read_sql("SELECT job, ROUND(AVG(salary * 1.65), 2) AS kiwi_dollahs FROM employees GROUP BY job;", conn)

50) Show the mean, min, and max salaries of each job title in `employees`, as well as the numbers of employees in each category.
pd.read_sql("SELECT job, ROUND(AVG(salary), 2) AS salary,\
            MIN(salary) AS min_salary, MAX(salary) AS max_salary,\
            COUNT(job) AS number_of_emp FROM employees GROUP BY job;", conn)
            
51) Show the mean salaries of each job title in `employees` sorted descending by salary.
pd.read_sql("SELECT job, ROUND(AVG(salary), 2) AS salary FROM employees GROUP BY job ORDER BY salary DESC;", conn)

52) What are the top 5 most common first names among `employees`?
pd.read_sql("SELECT firstname, COUNT(firstname) AS frequency FROM employees GROUP BY firstname ORDER BY COUNT(firstname) DESC;", conn)

53) Show all first names which have exactly 2 occurrences in `employees`.
#I probably was not supposed to solve the question like this buttttt:
pd.read_sql("SELECT firstname, COUNT(firstname) AS frequency FROM employees GROUP BY firstname ORDER BY frequency = 2 DESC LIMIT 9;", conn)

54) Take a look at the `transactions` table to get a idea of what it contains. Note that a transaction may span multiple rows if different items are purchased as part of the same order. The employee who made the order is also given by their ID.
pd.read_sql("SELECT * FROM transactions;", conn)

55) Show the top 5 largest orders (and their respective customer) in terms of the numbers of items purchased in that order.
pd.read_sql("SELECT DISTINCT order_id, customer, quantity FROM transactions ORDER BY quantity DESC LIMIT 5;", conn)

56) Show the total cost of each transaction.
    * _Hint:_ The `unit_price` column is the price of _one_ item. The customer may have purchased multiple.
    * _Hint2:_ Note that transactions here span multiple rows if different items are purchased.
pd.read_sql("SELECT customer,\
                    SUM(unit_price * quantity) AS order_total \
            FROM transactions \
            GROUP BY order_id \
            ORDER BY order_total DESC;", conn)    
    
57) Show the top 5 transactions in terms of total cost.
pd.read_sql("SELECT customer,\
                    SUM(unit_price * quantity) AS order_total \
            FROM transactions \
            GROUP BY order_id \
            ORDER BY order_total DESC \
            LIMIT 5;", conn)

58) Show the top 5 customers in terms of total revenue (ie, which customers have we done the most business with in terms of money?)
pd.read_sql("SELECT customer,\
                    SUM(unit_price * quantity) AS order_total \
            FROM transactions \
            GROUP BY customer \
            ORDER BY order_total DESC \
            LIMIT 5;", conn)

59) Show the top 5 employees in terms of revenue generated (ie, which employees made the most in sales?)
pd.read_sql("SELECT t.employee_id, \
                    employees.firstname, \
                    employees.lastname, \
                    SUM(t.unit_price * t.quantity) AS order_total \
            FROM transactions AS t \
            INNER JOIN employees ON t.employee_id = employees.ID \
            GROUP BY employee_id \
            ORDER BY order_total DESC \
            LIMIT 5;", conn)

60) Which customer worked with the largest number of employees?
    * _Hint:_ This is a tough one! Check out the `DISTINCT` keyword.
pd.read_sql("SELECT DISTINCT customer, COUNT(DISTINCT(employee_id)) AS count FROM transactions GROUP BY customer ORDER BY count DESC;", conn)    
    
61) Show all customers who've done more than $80,000 worth of business with us.

pd.read_sql("SELECT customer,\
                    SUM(unit_price * quantity) AS order_total \
            FROM transactions \
            GROUP BY customer \
            ORDER BY order_total DESC \
            LIMIT 8;", conn)

#I tried to solve this by setting a WHERE condition but kept getting an error.

Another approach:

pd.read_sql("SELECT customer,\
                    SUM(unit_price * quantity) AS order_total \
            FROM transactions \
            GROUP BY customer \
            ORDER BY order_total;", conn)[(
    pd.read_sql("SELECT customer,\
                    SUM(unit_price * quantity) AS order_total \
            FROM transactions \
            GROUP BY customer \
            ORDER BY order_total;", conn)['order_total'] > 80_000
)]