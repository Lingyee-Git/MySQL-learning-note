[题目网址](https://www.nowcoder.com/ta/sql)

```sql
-- Q1
SELECT *
FROM employees
WHERE hire_date = (
    SELECT max(hire_date)
    FROM employees
)
```

```sql
-- Q2
SELECT emp_no, birth_date, first_name, last_name, gender, hire_date
FROM (
    SELECT *, DENSE_RANK()OVER(ORDER BY hire_date desc) hire_rk
    FROM employees
) AS rk
WHERE rk.hire_rk = 3
ORDER BY emp_no asc
```

```SQL
-- Q3
SELECT d.emp_no, salary, from_date, d.to_date, dept_no
FROM salaries s RIGHT JOIN dept_manager d ON s.emp_no = d.emp_no
```
