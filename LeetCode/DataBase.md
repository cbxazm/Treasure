

# 组合两张表

https://leetcode-cn.com/problems/combine-two-tables/solution/zu-he-liang-ge-biao-by-leetcode/

```
select FirstName,LastName,City,State from Person left join Address 
on Address.PersonId=Person.Perso
```



# 第二高的薪水

https://leetcode-cn.com/problems/second-highest-salary/solution/di-er-gao-de-xin-shui-by-leetcode/

```
select ifnull((select DISTINCT Salary from Employee order by Salary Desc limit 1,1),null) as SecondHighestSalary
```

# 第N高的薪水

https://leetcode-cn.com/problems/nth-highest-salary/solution/mysql-zi-ding-yi-bian-liang-by-luanz/

```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
 SET N := N-1;
  RETURN (
      # Write your MySQL query statement below.
         select ifnull((select Distinct Salary from Employee order by Salary desc limit N,1),null)

  );
END
```

# 分数排名

https://leetcode-cn.com/problems/rank-scores/solution/fen-cheng-liang-ge-bu-fen-xie-hui-rong-yi-hen-duo-/

```
select a.Score as Score,
 (select count(distinct b.Score) from Scores b where b.Score >= a.Score) as 'Rank'
from Scores a
order by a.Score DESC
```

# 查找重复的邮箱

https://leetcode-cn.com/problems/duplicate-emails/

```
select Email from
(
select Email ,count(*) as num from Person //Group by一般和聚合函数一起使用，不然前面只会显示一条记录
GROUP BY Email
) as a where num >1
```

# 超过经理收入的员工

https://leetcode-cn.com/problems/employees-earning-more-than-their-managers/

```
select a.Name as Employee from Employee a,Employee b where a.ManagerId=b.Id and a.Salary>b.Salary
```

# 找出从不订购的客户

https://leetcode-cn.com/problems/customers-who-never-order/

## 方法1 子查询

```
select customers.name as 'Customers'
from customers 
where customers.id not in 
(select CustomerId from orders)
```

## 方法2 连接查询

```
select * from Customers c left join Orders o on o.CustomerId = c.Id where o.id is null
```

# 部门工资最高的员工

https://leetcode-cn.com/problems/department-highest-salary/solution/bu-men-gong-zi-zui-gao-de-yuan-gong-by-leetcode/

```
SELECT
    Department.name AS 'Department',
    Employee.name AS 'Employee',
    Salary
FROM
    Employee
        JOIN
    Department ON Employee.DepartmentId = Department.Id
WHERE
    (Employee.DepartmentId , Salary) IN
    (   SELECT
            DepartmentId, MAX(Salary)
        FROM
            Employee
        GROUP BY DepartmentId
	)
;

```

# 找出部门工资前三的员工

https://leetcode-cn.com/problems/department-top-three-salaries/solution/

```
# Write your MySQL query statement below
SELECT
    d.Name AS 'Department', e1.Name AS 'Employee', e1.Salary
FROM
    Employee e1
        JOIN
    Department d ON e1.DepartmentId = d.Id
WHERE
    3 > (SELECT
            COUNT(DISTINCT e2.Salary)
        FROM
            Employee e2
        WHERE
            e2.Salary > e1.Salary
                AND e1.DepartmentId = e2.DepartmentId
        )
;                 
```

