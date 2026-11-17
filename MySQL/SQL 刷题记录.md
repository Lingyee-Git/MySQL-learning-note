[题目网址](https://www.nowcoder.com/ta/sql)

```sql
-- SQL200
SELECT *
FROM employees
WHERE hire_date = (
    SELECT max(hire_date)
    FROM employees
)
```

```sql
-- SQL201
SELECT emp_no, birth_date, first_name, last_name, gender, hire_date
FROM (
    SELECT *, DENSE_RANK()OVER(ORDER BY hire_date desc) hire_rk
    FROM employees
) AS rk
WHERE rk.hire_rk = 3
ORDER BY emp_no asc
```

```sql
-- SQL202
SELECT d.emp_no, salary, from_date, d.to_date, dept_no
FROM salaries s RIGHT JOIN dept_manager d ON s.emp_no = d.emp_no
```

```sql
-- SQL203
SELECT last_name, first_name, dept_no
FROM employees e JOIN dept_emp d ON e.emp_no = d.emp_no
```

```SQL
-- SQL204
SELECT last_name, first_name, dept_no
FROM employees e LEFT JOIN dept_emp d ON e.emp_no = d.emp_no
```

```SQL
-- SQL206
SELECT  emp_no, COUNT(salary) t
FROM salaries
GROUP BY emp_no
HAVING COUNT(salary) > 15
```

```SQL
-- SQL207
SELECT salary
FROM salaries
GROUP BY salary
ORDER BY salary DESC
```

```SQL
-- SQL209
SELECT e.emp_no
FROM employees e LEFT JOIN dept_manager d ON e.emp_no = d.emp_no
WHERE d.emp_no is NULL
```

```SQL
-- SQL210
SELECT e.emp_no, m.emp_no manager
FROM dept_emp e LEFT JOIN dept_manager m ON e.dept_no = m.dept_no
WHERE e.emp_no != m.emp_no
```

```SQL
-- SQL211
SELECT dept_no, emp_no, salary
FROM (
SELECT dept_no, d.emp_no, salary, 
RANK()OVER(PARTITION BY dept_no ORDER BY salary desc) s
FROM dept_emp d JOIN salaries s ON d.emp_no = s.emp_no
) r
WHERE r.s = 1
ORDER BY dept_no asc
```