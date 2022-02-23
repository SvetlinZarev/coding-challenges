# [184. Department Highest Salary](https://leetcode.com/problems/department-highest-salary/)

## Problem

### MySQL Schema

```sql
Create table If Not Exists Employee (id int, name varchar(255), salary int, departmentId int)
Create table If Not Exists Department (id int, name varchar(255))
Truncate table Employee
insert into Employee (id, name, salary, departmentId) values ('1', 'Joe', '70000', '1')
insert into Employee (id, name, salary, departmentId) values ('2', 'Jim', '90000', '1')
insert into Employee (id, name, salary, departmentId) values ('3', 'Henry', '80000', '2')
insert into Employee (id, name, salary, departmentId) values ('4', 'Sam', '60000', '2')
insert into Employee (id, name, salary, departmentId) values ('5', 'Max', '90000', '1')
Truncate table Department
insert into Department (id, name) values ('1', 'IT')
insert into Department (id, name) values ('2', 'Sales')
```

### Tables

##### Employee

```text
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |
+--------------+---------+
```

* `id` is the primary key column for this table.
* `departmentId` is a foreign key of the ID from the Department table.
* Each row of this table indicates the `ID`, `name`, and `salary` of 
  an employee. It also contains the `ID` of their department.

 
##### Department

```text
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+
```

* `id` is the primary key column for this table.
* Each row of this table indicates the `ID` of a department and its 
  name.


### Task
 
Write an SQL query to find employees who have the highest salary in 
each of the departments.

Return the result table in any order.

The query result format is in the following example.


#### Examples

Employee table:

```text
+----+-------+--------+--------------+
| id | name  | salary | departmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |
+----+-------+--------+--------------+
```

Department table:

```text
+----+-------+
| id | name  |
+----+-------+
| 1  | IT    |
| 2  | Sales |
+----+-------+
```

Output: 

```text
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
| IT         | Max      | 90000  |
+------------+----------+--------+
```
Explanation: Max and Jim both have the highest salary in the 
IT department and Henry has the highest salary in the Sales 
department.


## Solutions

### MySQL with JOIN

```sql
SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary FROM 
(
    SELECT max(t.salary) AS salary, t.departmentId
    FROM employee t
    GROUP BY t.departmentId
) AS m
JOIN department d ON d.id = m.departmentId
JOIN employee e ON e.salary = m.salary AND e.departmentId = m.departmentId
```

### MySQL with IN

```sql
SELECT d.name AS Department, e.name AS Employee, e.salary AS Salary 
FROM department d 
JOIN employee e on d.id = e.departmentId
WHERE (e.departmentId, e.salary) IN
(
    SELECT departmentId, MAX(salary)
    FROM employee
    GROUP BY departmentId
)
```
