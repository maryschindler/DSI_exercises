# The Basics

## Simple SELECTs
1) Query all the data in the `pets` table.
pd.read_sql("SELECT * FROM pets;", conn)

2) Query only the first 5 rows of the `pets` table.
pd.read_sql("SELECT * FROM pets LIMIT 5;", conn)

3) Query only the names and ages of the pets in the `pets` table.
pd.read_sql("SELECT pets.name, pets.age FROM pets;", conn)

4) Query the pets in the `pets` table, sorted youngest to oldest.
pd.read_sql("SELECT * FROM pets ORDER BY pets.age;", conn)

5) Query the pets in the `pets` table alphabetically.
pd.read_sql("SELECT * FROM pets ORDER BY pets.name;", conn)

6) Query all the male pets in the `pets` table.
pd.read_sql("SELECT * FROM pets WHERE pets.sex = 'M';", conn)

7) Query all the cats in the `pets` table.
pd.read_sql("SELECT * FROM pets WHERE pets.species = 'cat';", conn)

8) Query all the pets in the `pets` table that are at least 5 years old.
pd.read_sql("SELECT * FROM pets WHERE pets.age >= '5';", conn)

9) Query all the male dogs in the `pets` table. Do not include the sex or species column, since you already know them.
pd.read_sql("SELECT pets.name, pets.age FROM pets WHERE pets.sex = 'M' AND pets.species = 'dog';", conn)

10) Get all the names of the dogs in the `pets` table that are younger than 5 years old.
pd.read_sql("SELECT pets.name, pets.age, pets.species FROM pets WHERE pets.species = 'dog' AND pets.age < '5';", conn)

11) Query all the pets in the `pets` table that are either male dogs or female cats.
pd.read_sql("SELECT * FROM pets WHERE pets.sex = 'M' AND pets.species = 'dog' OR (pets.sex = 'F' AND pets.species = 'cat');", conn)

12) Query the five oldest pets in the `pets` table.
pd.read_sql("SELECT * FROM pets ORDER BY pets.age DESC LIMIT 5;", conn)

13) Get the names and ages of all the female cats in the `pets` table sorted by age, descending.
pd.read_sql("SELECT pets.name, pets.age FROM pets WHERE pets.sex = 'F' AND pets.species = 'cat' ORDER BY pets.age DESC;", conn)

14) Get all pets from `pets` whose names start with P.
pd.read_sql("SELECT * FROM pets WHERE pets.name LIKE 'P%';", conn)

15) Select all employees from `employees_null` where the salary is missing.
pd.read_sql("SELECT * FROM employees_null AS e where e.salary IS NULL;", conn)

16) Select all employees from `employees_null` where the salary is below 35,000 or missing.
pd.read_sql("SELECT * FROM employees_null AS e WHERE e.salary IS NULL OR e.salary < '35000';", conn)

17) Select all employees from `employees_null` where the job title is missing. What do you see?
pd.read_sql("SELECT * FROM employees_null AS e WHERE e.job IS NULL;", conn)
#observing where e.job is null the other fields (apart from salary) are 'UNKNOWN'

18) Who is the newest employee in `employees`? The most senior?
pd.read_sql("SELECT * FROM employees_null AS e ORDER BY e.startdate DESC LIMIT 1;", conn)
#Roger Conner is the newest employee in 'employees_null'

pd.read_sql("SELECT * FROM employees_null AS e ORDER BY e.startdate LIMIT 1;", conn)
#Mary Nash is the most senior employee in 'employees_null'

19) Select all employees from `employees` named Thomas.
pd.read_sql("SELECT * FROM employees AS e WHERE e.firstname IS 'Thomas';", conn)

20) Select all employees from `employees` named Thomas or Shannon.
pd.read_sql("SELECT * FROM employees AS e WHERE e.firstname = 'Thomas' OR e.firstname = 'Shannon';", conn)

21) Select all employees from `employees` named Robert, Lisa, or any name that begins with a J. In addition, only show employees who are _not_ in sales. This will be a little bit of a longer query.
    * _Hint:_ There will only be 6 rows in the result.
pd.read_sql("SELECT * FROM employees AS e \
            WHERE (e.firstname = 'Robert' OR e.firstname = 'Lisa' OR e.firstname LIKE 'J%')\
            AND e.job IS NOT 'Sales';", conn)

## Column Operations
22) Query the top 5 rows of the `employees` table to get a glimpse of these new data.
pd.read_sql("SELECT * FROM employees LIMIT 5;", conn)

23) Query the `employees` table, but convert their salaries to Euros. 
    * _Hint:_ 1 Euro = 1.1 USD.
    * _Hint2:_ If you think the output is ugly, try out the `ROUND()` function.
pd.read_sql("SELECT *, ROUND((salary / 1.1), 2) AS 'salary_euros' FROM employees LIMIT 5;", conn)    

24) Repeat the previous problem, but rename the column `salary_eu`.
pd.read_sql("SELECT *, ROUND((salary / 1.1), 2) AS 'salary_eu' FROM employees LIMIT 5;", conn)

25) Query the `employees` table, but combine the `firstname` and `lastname` columns to be "Firstname, Lastname" format. Call this column `fullname`. For example, the first row should contain `Thompson, Christine` as `fullname`. Also, display the rounded `salary_eu` instead of `salary`.
    * _Hint:_ The string concatenation operator is `||`
pd.read_sql("SELECT *, lastname || ', ' || firstname AS fullname, ROUND((salary / 1.1), 2)\
            AS 'salary_eu' FROM employees LIMIT 5;", conn)
    
26) Query the `employees` table, but replace `startdate` with `startyear` using the `SUBSTR()` function. Also include `fullname` and `salary_eu`.
pd.read_sql("SELECT *, SUBSTR(startdate, 0, 5) AS startyear, lastname || ', ' || firstname AS fullname, ROUND((salary / 1.1), 2)\
            AS 'salary_eu' FROM employees LIMIT 5;", conn)

27) Repeat the above problem, but instead of using `SUBSTR()`, use `STRFTIME()`.
pd.read_sql("SELECT *, STRFTIME('%Y', startdate) AS startyear, lastname || ', ' || firstname AS fullname, ROUND((salary / 1.1), 2)\
            AS 'salary_eu' FROM employees LIMIT 5;", conn)

28) Query the `employees` table, replacing `firstname`/`lastname` with `fullname` and `startdate` with `startyear`. Print out the salary in USD again, except format it with a dollar sign, comma separators, and no decimal. For example, the first row should read `$123,696`. This column should still be named `salary`.
    * _Hint:_ Check out SQLite's `printf` function.
    * _Hint2:_ The format string you'll need is `$%,.2d`. You should read more about such formatting strings as they're useful in Python, too!
    
#I don't know what's wrong here, as far as I can tell, it should run:
pd.read_sql("SELECT ID, job, (printf("%,.2d", salary) as salary),\
            lastname || ', ' || firstname AS fullname,\
            STRFTIME('%Y', startdate) AS startyear,\
            ROUND((salary / 1.1), 2)\
            AS 'salary_eu' FROM employees LIMIT 5;", conn)

**Note:** For the next few problems, you'll probably want to use `CASE`/`WHEN` statements.

29) Last year, only salespeople were eligible for bonuses. Create a column `bonus` that is "Yes" if you're eligible for a bonus, otherwise "No".
pd.read_sql("SELECT *, CASE WHEN job == 'Sales' THEN 'Yes' ELSE 'No' END AS bonus FROM employees;", conn)

30) This year, only sales people with a salary of $100,000 or higher are eligible for bonuses. Create a `bonus` column like in the last problem for salespeople with salaries at least $100,000.
pd.read_sql("SELECT *, CASE WHEN salary >= 100000 THEN 'Yes' ELSE 'No' END AS bonus FROM employees;", conn)

31) Next year, the bonus structure will be a little more complicated. You'll create a `target_comp` column which represents an employee's target total compensation after their bonus. Here is the company's bonus structure:

* Salespeople who make more than $100,000 will be eligible for a 10% bonus.
* Salespeople who make less than $100,000 will be eligible for a 5% bonus.
* Administrators will also be eligible for a 5% bonus.
* Anyone who does not meet any of the above descriptions is not eligible for a bonus.

Create this `target_comp` column, making sure to format _both_ the `salary` and `target_comp` columns nicely (ie, with dollar signs and comma separators).

#NEED TO WORK ON #NEED TO WORK ON #NEED TO WORK ON 