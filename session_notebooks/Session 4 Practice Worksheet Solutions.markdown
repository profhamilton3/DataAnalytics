# Session 4 Practice

**Schema (PostgreSQL v17)**
```SQL
    -- =====================================================================
    -- Session 4: Advanced SQL for Data Extraction | SQL Fiddle schema script
    -- Works in PostgreSQL (recommended) and SQLite on sqlfiddle.com
    -- Paste this whole script into the SCHEMA panel and build/run it once.
    -- =====================================================================
    DROP TABLE IF EXISTS projects;
    DROP TABLE IF EXISTS employees;
    DROP TABLE IF EXISTS departments;
    
    CREATE TABLE departments (
        dept_id    INTEGER PRIMARY KEY,
        dept_name  VARCHAR(50) NOT NULL,
        region     VARCHAR(20) NOT NULL
    );
    
    CREATE TABLE employees (
        employee_id INTEGER PRIMARY KEY,
        name        VARCHAR(50) NOT NULL,
        dept_id     INTEGER REFERENCES departments(dept_id),   -- may be NULL (not yet assigned)
        salary      INTEGER NOT NULL
    );
    
    CREATE TABLE projects (
        project_id   INTEGER PRIMARY KEY,
        employee_id  INTEGER REFERENCES employees(employee_id), -- may be NULL (not yet staffed)
        project_name VARCHAR(60) NOT NULL,
        hours_logged INTEGER NOT NULL
    );
    
    INSERT INTO departments (dept_id, dept_name, region) VALUES
     (10, 'Engineering', 'East'),
     (20, 'Marketing',   'West'),
     (30, 'Sales',       'Central'),
     (40, 'Finance',     'East'),
     (50, 'Research',    'West');          -- a department with NO employees yet
    
    INSERT INTO employees (employee_id, name, dept_id, salary) VALUES
     (1,  'Ana Rivera',      10,  98000),
     (2,  'Ben Okafor',      20,  62000),
     (3,  'Carla Nguyen',    10, 105000),
     (4,  'Derek Schmidt',   30,  58000),
     (5,  'Elena Petrova',   20,  71000),
     (6,  'Farid Haddad',    30,  64000),
     (7,  'Grace Kim',       10,  88000),
     (8,  'Hector Alvarez',  30,  52000),
     (9,  'Imani Brooks',    40,  79000),
     (10, 'Jonas Lindqvist', NULL, 70000), -- new hire, no department assigned yet
     (11, 'Kira Tanaka',     40,  83000),
     (12, 'Luis Moreno',     20,  59000);
    
    INSERT INTO projects (project_id, employee_id, project_name, hours_logged) VALUES
     (101, 1,    'Data Lake Migration', 120),
     (102, 3,    'Data Lake Migration',  95),
     (103, 1,    'API Gateway',          40),
     (104, 2,    'Spring Campaign',      60),
     (105, 5,    'Spring Campaign',      75),
     (106, 4,    'Q3 Pipeline Review',   30),
     (107, 6,    'Q3 Pipeline Review',   45),
     (108, 9,    'Budget Forecast',      80),
     (109, 11,   'Budget Forecast',      65),
     (110, 7,    'API Gateway',          50),
     (111, 3,    'Security Audit',       35),
     (112, NULL, 'Vendor Evaluation',    20); -- a project nobody is assigned to yet
```
---
[View on DB Fiddle](https://www.db-fiddle.com/)    
---
# Part C: ✏️ Practice Prompts (progressively harder)
Write each query yourself. Use the hint only if you are stuck for more than 2 minutes. Check your result against the expected output.

## Prompt 1: Level 1: LEFT JOIN + COALESCE
List every employee and the project(s) they work on. Employees with no project should still appear, showing 'No project' and 0 hours. Order by employee name.

💡 Hint: Which table must be on the LEFT so nobody is lost? Use COALESCE twice.

-- SOLUTION ---

**Query #1**
```SQL            
    SELECt e.name as employee_name, 
    	COALESCE(p.project_name, 'No Project') AS project_name, 
    	COALESCE(p.hours_logged, 0) as hours_logged
    FROM employees e
    LEFT JOIN projects p ON e.employee_id = p.employee_id
    ORDER BY e.name ASC;
```
| employee_name   | project_name        | hours_logged |
| --------------- | ------------------- | ------------ |
| Ana Rivera      | Data Lake Migration | 120          |
| Ana Rivera      | API Gateway         | 40           |
| Ben Okafor      | Spring Campaign     | 60           |
| Carla Nguyen    | Data Lake Migration | 95           |
| Carla Nguyen    | Security Audit      | 35           |
| Derek Schmidt   | Q3 Pipeline Review  | 30           |
| Elena Petrova   | Spring Campaign     | 75           |
| Farid Haddad    | Q3 Pipeline Review  | 45           |
| Grace Kim       | API Gateway         | 50           |
| Hector Alvarez  | No Project          | 0            |
| Imani Brooks    | Budget Forecast     | 80           |
| Jonas Lindqvist | No Project          | 0            |
| Kira Tanaka     | Budget Forecast     | 65           |
| Luis Moreno     | No Project          | 0            |

---
## Prompt 2: Level 2: Find what is missing (anti-join)
Which departments have no employees? Return the department name and region.

💡 Hint: LEFT JOIN from departments to employees, then keep only rows where the employee side IS NULL. (Remember: = NULL never works. Use IS NULL.)

** SOLUTION **

---

**Query #2**
```SQL
    SELECT d.dept_name, d.region
    FROM departments d
    LEFT JOIN employees e ON d.dept_id = e.dept_id
    WHERE e.employee_id IS NULL;
```
| dept_name | region |
| --------- | ------ |
| Research  | West   |

---
## Prompt 3: Level 3: Three-table join + GROUP BY 
Show total hours logged per region, highest first. Only count hours that belong to an employee in a department.

💡 Hint: Chain two joins: projects → employees → departments. INNER joins are fine here, because we only want hours that trace all the way back to a region.

**Query #3**
```SQL
SELECT 
    d.region,
    SUM(p.hours_logged) AS total_hours
FROM projects p
INNER JOIN employees e 
    ON p.employee_id = e.employee_id
INNER JOIN departments d 
    ON e.dept_id = d.dept_id
GROUP BY d.region
ORDER BY total_hours DESC;
```
---
## Prompt 4: Level 4: Scalar subquery
List employees who earn more than the company-wide average salary, highest salary first. Show the average as a column too.

💡 Hint: The inner query (SELECT AVG(salary) FROM employees) returns exactly one value, so you can compare with >.

**Query #4a**
```SQL
SELECT 
    name AS employee_name,
    salary,
    (SELECT AVG(salary) FROM employees) AS avg_salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees)
ORDER BY salary DESC;
```
**Query #4b**
```SQL
SELECT 
    e.name AS employee_name,
    e.salary,
    d.dept_name,
    e.salary,
    ROUND(AVG(e.salary) OVER (PARTITION BY e.dept_id), 2) AS dept_avg_salary
FROM employees e
LEFT JOIN departments d 
    ON e.dept_id = d.dept_id
WHERE salary > (SELECT AVG(salary) FROM employees)
ORDER BY e.salary DESC;
```
---
## Prompt 5: Level 5: Correlated subquery
List employees who earn more than the average salary of their own department. Show their department's average next to them.

💡 Hint: Inside the subquery, use a second alias (e2) and match e2.dept_id = e.dept_id. The subquery re-runs for each outer row.

**Query #5**
```SQL
SELECT 
    e.name AS employee_name,
    d.dept_name,
    e.salary,
    (SELECT ROUND(AVG(e2.salary), 2) 
     FROM employees e2 
     WHERE e2.dept_id = e.dept_id) AS dept_avg_salary
FROM employees e
LEFT JOIN departments d 
    ON e.dept_id = d.dept_id;
```
---
# Part D: 🐞 Debug Lab
Each query below has a bug. Run it, read the error (or study the output), explain the problem in one sentence, and fix it.

## Bug 1: Missing GROUP BY column
```SQL
SELECT d.dept_name, d.region, COUNT(*) AS headcount
FROM employees e
JOIN departments d ON e.dept_id = d.dept_id
GROUP BY d.dept_name;
```
What went wrong? Query Error: column "d.region" must appear in the GROUP BY clause or be used in an aggregate function
Your fixed query: Add d.region to GROUP BY clause so every non-aggregated column in SELECT is accounted for.


## Bug 2: Ambiguous column name
```SQL
SELECT employee_id, name, project_name, hours_logged
FROM employees e
JOIN projects p ON e.employee_id = p.employee_id;
```
What went wrong? select has ambiguous id:
Your fixed query: specify alias. e.employee_id
```SQL
SELECT e.employee_id, e.name, p.project_name, p.hours_logged
FROM employees e
JOIN projects p ON e.employee_id = p.employee_id;
```

## Bug 3: Aggregate inside WHERE
```SQL
SELECT dept_id, AVG(salary) AS avg_salary
FROM employees
WHERE AVG(salary) > 70000
GROUP BY dept_id;
```
What went wrong? No aggregrations inside where clause
Your fixed query: use HAVING after teh Group By
```SQL
SELECT dept_id, ROUND(AVG(salary),2) AS avg_salary
FROM employees 
GROUP BY dept_id
HAVING AVG(salary) > 70000;
```

## Bug 4: = with a multi-row subquery
```SQL
SELECT name, dept_id
FROM employees
WHERE dept_id = (SELECT dept_id FROM departments WHERE region = 'East');
```
What went wrong? = in where clause must be scalar or us IN for multi-row
Your fixed query:
```SQL
SELECT name, dept_id
FROM employees
WHERE dept_id IN (SELECT dept_id FROM departments WHERE region = 'East');
```

## Bug 5 (logic bug, no error!): WHERE cancels the LEFT JOIN
-- Goal: every employee, plus any project where they logged more than 50 hours
```SQL
SELECT e.name, p.project_name, p.hours_logged
FROM employees e
LEFT JOIN projects p ON e.employee_id = p.employee_id
WHERE p.hours_logged > 50;
```
What went wrong? the where clause filters employees that should be in the report.
Your fixed query:
```SQL
    SELECT 
        e.name, 
        COALESCE(p.project_name,'Unassigned') as project, 
        COALESCE(p.hours_logged,0) as logged_hours
    FROM employees e
    LEFT JOIN projects p 
        ON e.employee_id = p.employee_id 
        AND p.hours_logged > 50;
```

| name            | project             | logged_hours |
| --------------- | ------------------- | ------------ |
| Ana Rivera      | Data Lake Migration | 120          |
| Carla Nguyen    | Data Lake Migration | 95           |
| Ben Okafor      | Spring Campaign     | 60           |
| Elena Petrova   | Spring Campaign     | 75           |
| Imani Brooks    | Budget Forecast     | 80           |
| Kira Tanaka     | Budget Forecast     | 65           |
| Luis Moreno     | Unassigned          | 0            |
| Jonas Lindqvist | Unassigned          | 0            |
| Hector Alvarez  | Unassigned          | 0            |
| Farid Haddad    | Unassigned          | 0            |
| Derek Schmidt   | Unassigned          | 0            |
| Grace Kim       | Unassigned          | 0            |

---
[View on DB Fiddle](https://www.db-fiddle.com/)

---
# Part E: 🧩 Guided Challenge: The Project Utilization Report
Scenario: the COO asks, "For every department, including people who are not assigned to one yet, how many hours does the average person log on projects? Label each department High, Medium or Low utilization."

This needs everything from tonight: a LEFT JOIN, COALESCE, GROUP BY, a subquery in the FROM clause (a derived table), and CASE WHEN. Build it in three steps, running each one before moving on.

## Step 1: Hours per employee (everyone included)
Build one row per employee with their total hours. Employees with no projects must show 0.
```SQL
SELECT e.employee_id, e.name, e.dept_id,
       COALESCE(SUM(p.hours_logged), ___) AS total_hours.  --A: 0
FROM employees e
___ JOIN projects p ON e.employee_id = p.employee_id       --A: LEFT
GROUP BY e.employee_id, e.name, ___;                       --A: e.dept_id
```

## Step 2: Wrap it as a derived table and roll up by department
Put your Step 1 query in parentheses inside FROM, give it an alias (eh = employee hours), and join it to departments. Use a LEFT JOIN and COALESCE so Jonas shows up as Unassigned.

⚠️ Why not just join all three tables and COUNT(*)? Ana has two projects, so a direct join produces two rows for her and double-counts headcount. That is called fan-out. Pre-aggregating per employee first avoids it.
```SQL
SELECT COALESCE(d.dept_name, 'Unassigned')   AS department,
       COUNT(*)                              AS headcount,
       SUM(eh.total_hours)                   AS dept_hours,
       ROUND(AVG(eh.total_hours), 1)         AS avg_hours_per_person
FROM ( /* paste your Step 1 query here, without the semicolon */ ) eh
LEFT JOIN departments d ON eh.dept_id = ___  -- A: e.dept_id
GROUP BY ___;  
```
**SOLUTION**
```SQL
SELECT COALESCE(d.dept_name, 'Unassigned')   AS department,
       COUNT(*)                              AS headcount,
       SUM(eh.total_hours)                   AS dept_hours,
       ROUND(AVG(eh.total_hours), 1)         AS avg_hours_per_person
FROM (
  SELECT e.employee_id, e.name, e.dept_id,
       COALESCE(SUM(p.hours_logged), 0) AS total_hours  
  FROM employees e
  LEFT JOIN projects p ON e.employee_id = p.employee_id 
   GROUP BY e.employee_id, e.name, e.dept_id
     ) eh
LEFT JOIN departments d ON eh.dept_id = d.dept_id
GROUP BY department;
```

## Step 3: Add a utilization tier with CASE WHEN, and sort
Label each department: High if average hours per person ≥ 90, Medium if ≥ 50, otherwise Low. Sort by avg_hours_per_person, highest first.

-- Add this column to your SELECT list from Step 2:
CASE
    WHEN AVG(eh.total_hours) >= ___ THEN 'High'.   -- A: 90
    WHEN AVG(eh.total_hours) >= ___ THEN 'Medium'   --A: 50
    ELSE ___                                        --A: 'LOW'
END AS utilization_tier
-- ...and finish with: ORDER BY ___ DESC;           --A: avg_hours_per_person

**Solution Query**
```SQL
    SELECT COALESCE(d.dept_name, 'Unassigned')   AS department,
           COUNT(*)                              AS headcount,
           SUM(eh.total_hours)                   AS dept_hours,
           ROUND(AVG(eh.total_hours), 1)         AS avg_hours_per_person,
    CASE
        WHEN AVG(eh.total_hours) >= 90 THEN 'High'
        WHEN AVG(eh.total_hours) >= 50 THEN 'Medium'
        ELSE 'LOW'
    END AS utilization_tier
    --
    FROM (
      SELECT e.employee_id, e.name, e.dept_id,
           COALESCE(SUM(p.hours_logged), 0) AS total_hours  
      FROM employees e
      LEFT JOIN projects p ON e.employee_id = p.employee_id 
       GROUP BY e.employee_id, e.name, e.dept_id
         ) eh
    LEFT JOIN departments d ON eh.dept_id = d.dept_id
    GROUP BY department
    ORDER BY avg_hours_per_person DESC;
```
| department  | headcount | dept_hours | avg_hours_per_person | utilization_tier |
| ----------- | --------- | ---------- | -------------------- | ---------------- |
| Engineering | 3         | 340        | 113.3                | High             |
| Finance     | 2         | 145        | 72.5                 | Medium           |
| Marketing   | 3         | 135        | 45.0                 | LOW              |
| Sales       | 3         | 75         | 25.0                 | LOW              |
| Unassigned  | 1         | 0          | 0.0                  | LOW              |

---

[View on DB Fiddle](https://www.db-fiddle.com/)
