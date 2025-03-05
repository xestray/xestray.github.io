---
    comments: true
    statistics: true
---

# SQL

## Data Definition Language

### Domain Types in SQL

- `char(n)`：Fixed length character string, with user-specified length n.

    定长字符串. C 语言里字符串结尾有 `\0`, 但数据库里没有，长度由定义而得。

- `varchar(n)`：Variable length character strings, with user-specified maximum length n.

    不定长字符串，长度至多为 n。不同的数据类型比较可能有问题（比如定长和不定长的字符串）

- `int`：Integer (a finite subset of the integers that is machine-dependent).
- `smallint`：Small integer (a machine-dependent subset of the integer domain type).
- `numeric(p,d)`：Fixed point number, with user-specified precision of p digits, with d digits to the right of decimal point.

    p 表示有效数字位数, d 表示小数点后多少位。 
    
    - e.g. number(3,1) allows 44.5 to be store exactly, but neither 444.5 or 0.32

- `real`, `double precision`：Floating point and double-precision floating point numbers, with machine-dependent precision.
- `float(n)`：Floating point number, with user-specified precision of at least n digits.
- `Null` values are allowed in all the domain types. 声明一个变量是 `not null` 的可以避免 attribute 的值为 `null`。

### Built-in Data Types in SQL

- date: Dates, containing a (4 digit) year, month and date

    e.g. date ‘2005-7-27’
    
- time: Time of day, in hours, minutes and seconds. 

    e.g. time ‘09:00:30’， time ‘09:00:30.75’

- timestamp: date plus time of day 

    e.g. timestamp ‘2005-7-27 09:00:30.75’

- interval: period of time 

    e.g. interval ‘1’ day

    - Subtracting a date/time/timestamp value from another gives an interval value.
    - Interval values can be added to date/time/timestamp values
    - built-in date, time functions: `current_date()`, `current_time()`, `year(x)`, `month(x)`, `day(x)`, `hour(x)`, `minute(x)`, `second(x)`

### Creat Table

An SQL relation is defined using the create table command:

```sql
CREATE TABLE r (A1 D1, A2 D2, ..., An Dn,
                (integrity constraint1),
                ...
                (integrity constraintk)) 
```

- $r$ is the name of the relation
- Each $A_i$ is an attribute name in the schema of relation $r$
- $D_i$ is the data type of values in the domain of attribute $A_i$ 

常见的完整性约束有：

- `not null`
- `primary key (A1, A2, ..., An)`
- `foreign key (A1, A2, ..., An) references r`
- `check (condition)`
- `unique (A1, A2, ..., An)`

### Drop and Alter Table

drop 命令会把表和表中的数据都删除。

```sql
DROP TABLE r
```


alter 命令可以用于在已经存在的关系（即 table）上增加或删除属性，或者增加或删除完整性约束。

```sql
ALTER TABLE r ADD A D; 
ALTER TABLE r ADD (A1 D1, …, An Dn); 
```

- 其中 `A` 是要增加的属性名，`D` 是属性的值域
- 添加新的属性后，已经存在的元组会给新属性值赋 `null`

也可以用 `alter` 命令删除属性或修改属性

```sql
ALTER TABLE r DROP A;
ALTER TABLE r MODIFY (A1 D1, …, An Dn);
```

- dropping of attributes is not supported by many databases.

### Create Index

我们可以为表中的某个属性创建索引，以提高查询效率。

```sql
CREATE INDEX <i-name> ON <table-name> (<attribute-list>);
```

- e.g. `CREATE INDEX b_index branch (branch_name);`

也可以创建唯一索引，以保证属性值的唯一性。

```sql
CREATE UNIQUE INDEX <i-name> ON <table-name> (<attribute-list>); 
```

还能用 drop 命令删除索引。

```sql
DROP INDEX <i-name>;
```

---

## Basic Structure

### Select Clause

select 语句的查询结果是一个关系，包含了满足查询条件的元组。

SQL names are case insensitive
SQL 的对大小写不敏感，属性名称、关系名称、关系名都可以用大写或小写或大小写混合，但是字符串除外（使用单引号包围）

一个典型的 select 语句如下：

```sql
SELECT A1, A2, ..., An
FROM r1, r2, ..., rm
WHERE P
```

- $A_i$ 是属性名，$r_i$ 是关系名，$P$ 是谓词
- SQL 不允许在名称中使用 `-`，但是在课本、课件中为了美观可能会这么用，在实际操作中需要用下划线 `_` 代替

SQL allows duplicates in relations as well as in query results.

- To force the elimination of duplicates, insert the keyword `distinct` after select. 

    `SELECT distinct branch_name FROM loan`

    - 与之相反的是 `all`，它允许重复元组出现在结果中。在不显式指定时，`all` 是默认的。

- An asterisk `*` in the select clause denotes all attributes. 

    `SELECT * FROM loan`

-  the select clause can contain arithmetic expressions involving the operations +, –, *, and /, as well as operating on constants or attributes of tuples,

    SQL 可以在 select 语句中使用加减乘除等运算符，也可以对常量或元组的属性进行运算。例如

    ```sql
    SELECT loan_number, branch_name, amount * 100 
    FROM loan 
    ```

### Where Clause

where 子句用于指定查询条件，只有满足条件的元组才会出现在结果中。

可以使用 AND, OR, NOT 来连接多个条件，还可以使用 BTWEEN, IN, LIKE, IS NULL 等操作符。

比如想要查询贷款金额在 90000 到 100000 之间的贷款，可以这么写：

```sql
SELECT loan_number  
FROM loan 
WHERE amount BETWEEN 90000 AND 100000 
```

### From Clause

FROM 子句用于指定查询的关系，可以是一个或多个关系，用逗号分隔。

```sql
SELECT *
FROM loan, branch
```

当两个关系中具有相同属性名时，需要使用关系名作为前缀来区分。

```SQL
SELECT customer_name, borrower.loan_number, amount
FROM borrower, loan
WHERE borrower.loan_number = loan.loan_number and 
      branch_name = 'Perryridge' 
```

### Natural Join

自然连接是一种特殊的连接，它会自动找到两个关系中具有相同属性名的属性，并将它们连接起来，并且会去掉重复的属性。

例如 borrower 和 loan 中具有相同的属性 loan_number，那么只有当两个表中 loan_number 的值相同时，这个元组才会出现在这条语句的结果中。

```sql
SELECT * FROM borrower NATURAL JOIN loan
```

下面这两条语句是等价的：

```sql
select name, course_id from instructor, teaches where instructor.ID = teaches.ID;
select name, course_id from instructor natural join teaches;
```

### Rename Operation

The SQL allows renaming relations and attributes using the `as` clause:

```sql
old_name as new_name
```

例如我们可以将 borrower 表重命名为 b，然后查询 b 表中的所有元组：

```sql
SELECT b.*
FROM borrower as b
```

- 关键字 `as` 可以省略，直接用空格代替也是可以的。

### String Operations

SQL includes a **string-matching operator** for comparisons on character strings. The operator **`like`** uses patterns that are described using two special characters.

- `%` —— matches any substring (likes `*` in the file system). 

    匹配任何长度的字符串，包括空字符串。

- `_` —— matches any character (like `?` in the file system). 

    匹配任何单个字符

!!! example
    - 寻找带有 `oe` 字符串的名字

        ```sql
        SELECT name FROM instructor WHERE name LIKE '%oe%'
        ```

    - 寻找至少有 3 个字符的名字

        ```sql
        SELECT name FROM instructor WHERE name LIKE '___%'
        ```

    - 如果我们想要匹配字符串“百分之百” `100%`，那么需要使用转义字符，并且在后面通过 `escape` 明确指出转义字符（默认为 `\`，但也可以定义为其他字符） 

        ```sql
        LIKE '100%'
        LIKE '100\%' escape '\'
        LIKE '100#%' escape '#'
        ```

!!! info
    当我们使用 `like` 操作符对汉字进行匹配操作时，可能会遇见意料之外的情况。

    例如我们想要查询名字里带 “一” 字的人，使用了 `LIKE '%一%'`，但是查询到的结果中可能有一些没有 “一” 这个字的名字。

    这与汉字的编码方式有关，例如假设 “一” 的字节编码占据两个字节，分别为 b1, b2，那么查询过程中可能会出现某些汉字的编码是 b1, b2, b3，或 b4, b1, b2, b3 等情况，这些汉字也会被匹配到，因此就会出现奇怪的匹配情况。

    - 当我们使用汉字进行匹配时，最好输入完整的汉字，而尽可能少用通配符。

SQL 还包括一些其他的字符串操作符，比如

- `concat(s1, s2)`：连接两个字符串
- concatenate operator `||`：连接两个字符串
- 将字符串转换为大写或小写：`upper(s)`, `lower(s)`
- 去除字符串两端的空格：`trim(s)` 等

### Ordering the Display of Tuples

关系内部的各个元组是无序的，但我们可以通过 `order by` 子句来指定元组在显示时的排序方式。

```sql
SELECT * FROM customer 
ORDER BY customer_city, customer_street desc, customer_name asc
``` 

- `desc` 表示降序，`asc` 表示升序。默认是升序。
- 多个属性之间用逗号分隔，优先按照第一个属性排序，如果第一个属性相同，则按照第二个属性排序，以此类推。
- 要求 `order by` 子句中的类型可以被排列，如数字、字符串、日期等

## Set Operations

SQL supports the set operations `union`, `intersect`, and `except`, which correspond to the relational algebra operations $\cup$, $\cap$, and $-$.

上述三个操作会自动去重，如果不想去重，可以使用 `union all`, `intersect all`, `except all`。

??? example 
    <figure>
        <img src="../assets/集合操作例子.png" width="70%">
    </figure>

## Aggregate Functions

下列操作符可以用于对多重集的属性进行聚合操作：

- `avg(col)`: average value 
- `min(col)`: minimum value 
- `max(col)`: maximum value 
- `sum(col)`: sum of values 
- `count(col)`: number of values 

需要注意的是，如果 select 中出现的属性并不属于聚合函数，那么它们必须出现在 group by 子句中。

=== "错误写法"

    ```sql
    SELECT branch_name, avg(balance) avg_bal
    FROM account
    WHERE branch_name = 'Perryridge'
    ```

=== "正确写法"

    ```sql
    SELECT branch_name, avg(balance) avg_bal
    FROM account
    WHERE branch_name = 'Perryridge'
    GROUP BY branch_name
    ```

我们还可以用 having 子句对分组后的组进行筛选。

```sql
select dept_name, count (*) as cnt
from instructor
where  salary >=100000
group by dept_name
having  count (*) > 10
order by cnt;
```

这条语句会找出工资大于 100000 的教师所在的部门，如果这个部门的教师人数大于 10，那么就会出现在结果中。

- where 子句出现在 group by 之前，用于筛选元组
- having 子句出现在 group by 之后，用于筛选组

???+ example "使用聚合函数分组的例子"

    <figure>
        <img src="../assets/聚合函数例子.png" width="70%"/>
    </figure>

    对应的 SQL 语句如下：

    ```sql
    SELECT dept_name, avg(salary) as avg_salary
    FROM instructor
    GROUP BY dept_name
    ```

!!! abstract

    select 语句的格式可以总结如下：

    ```sql
    SELECT <[DISTINCT] c1, c2, …> 
    FROM <r1, …> 
    [WHERE <condition>] 
        [GROUP BY <c1, c2, …> [HAVING <cond2>]] 
    [ORDER BY <c1 [DESC] [, c2 [DESC|ASC], …]>] 
    ```

    一条 select 语句执行的顺序依次为

    from -> where -> group by (aggregate) -> having -> select -> distinct -> order by

## Null Values

Null is a special marker used in SQL, and was first introduced by E.F. Codd. 

The meaning is "missing information" or "inapplicable information"

i.e., unknown value or that a value does not exist. 

- OR: 
    - (unknown or true) = true
    - (unknown or false) = unknown
    - (unknown or unknown) = unknown
- AND: 
    - (unknown and true) = unknown
    - (unknown and false) = false
    - (unknown and unknown) = unknown
- NOT:
    - (not unknown) = unknown

## Nested Subqueries

A **subquery** is a **select-from-where** expression that is nested within another query.

A common use of subqueries is to perform tests for **set membership**, **set comparisons**. 

### Set Membership

使用 `in` 和 `not in` 来判断一个元素或元组是否在另一个关系中。

!!! example
    Find all customers who have both an account and a loan at the bank. 

    ```sql
    SELECT distinct customer_name
    FROM borrower
    WHERE customer_name in (SELECT customer_name
                            FROM depositor) 
    ```

    相当于

    ```sql
    SELECT distinct customer_name
    FROM borrower B, depositor D
    WHERE B.customer_name = D.customer_name
    ```

### Set Comparisons

- `some` 或 `any`：只要嵌套查询中的某个元组满足条件，就返回 true
    - `= some` 等价于 `in`，但 `!= some` 不等价于 `not in`
- `all`：嵌套查询中的所有元组都满足条件，才返回 true
    - `!= all` 等价于 `not in`，但 `= all` 不等价于 `in`

!!! example
    Find all branches that have greater assets than some branch located in Brooklyn. 

    ```sql
    SELECT distinct branch_name 
        FROM branch 
        WHERE assets > some
            (SELECT assets 
            FROM branch 
            WHERE branch_city = 'Brooklyn') 
    ```

    相当于

    ```sql
    SELECT distinct T.branch_name 
    FROM branch as T, branch as S 
    WHERE T.assets > S.assets and S.branch_city = 'Brooklyn'
    ```

### Test for Empty Relations

The `exists` construct returns the value true if the argument subquery is **non-empty**.

- exists $r \Leftrightarrow r \neq \emptyset$
- not exists $r \Leftrightarrow r = \emptyset$

???+ example
    Find all customers who have an account at the bank. 

    ```sql
    SELECT distinct customer_name
    FROM depositor D
    WHERE exists
        (SELECT * 
        FROM account A
        WHERE D.account_number = A.account_number) 
    ```

???+ example
    找出选了所有生物系课程的学生。

    我们可以从逆向角度考虑这个问题，即寻找一些学生，要求不存在他们没选的生物系课程，即**生物系的课程是他们所选课程的子集**。或者说，可以注意到 $A \subseteq B \Leftrightarrow A - B = \emptyset$，因此可以这么写：

    ```sql
    SELECT distinct S.ID, S.name
    FROM student as S
    WHERE not exists ( /* 所有的生物系课程 */
            (SELECT course_id
            FROM course
            WHERE dept_name = 'Biology')
        EXCEPT
            (SELECT T.course_id /* 这个学生所选的课程 */
            FROM takes as T
            WHERE S.ID = T.ID));
    ```

### Test for Absence of Duplicate Tuples

The `unique` construct tests whether a subquery has any duplicate tuples in its result. 

即使用 `unique` 来判断子查询的结果是否有重复元组（是否为多重集），当结果中没有重复元组（有零个或一个）时返回 true，否则返回 false。

- 可以把 unique 理解为 at most once

!!! example

    <figure>
        <img src="../assets/unique例子.png" width="60%"/>
    </figure>

    如果没有 exists，那么没有开的课程也会被算进去，使用了 exists 相当于保证了选出的课程是开且仅开了一次的课程。

!!! example
    Find all customers who have **at least two** accounts at the Perryridge branch.

    ```sql
    SELECT distinct T.customer_name 
        FROM depositor T 
        WHERE not unique ( 
            SELECT R.customer_name 
            FROM account, depositor as R 
            WHERE T.customer_name = R.customer_name 
                and R.account_number = account.account_number 
                and ccount.branch_name = 'Perryridge') 
    ```

## views

SQL provide a mechanism to **hide certain data** from the view of certain users. 

- create a view 

    ```sql
    CREATE VIEW <v_name> AS 
        SELECT c1, c2, … From … 
        
    CREATE VIEW <v_name> (c1, c2, …) AS 
        SELECT e1, e2, … FROM … 
    ```

- drop a view 

    ```sql
    DROP VIEW <v_name>
    ```

!!! note 
    - 当我们通过插入、修改和删除等操作对视图进行修改时，实际上是对基本表进行了修改。
    - 但在一些情况下，视图是只读的，不能对其进行修改。

## Derived Relations

派生关系（导出关系）是通过查询操作得到的临时的关系，等效于局部的视图。

<figure>
    <img src="../assets/派生关系.png" width="60%"/>
</figure>

### With Clause

The `WITH` clause allows views to be defined locally for a query, rather than globally

!!! example
    <figure>
        <img src="../assets/with子句.png" width="60%"/>
    </figure>

!!! example "Complex Query Using With Clause"
    <figure>
        <img src="../assets/with子句1.png" width="60%"/>
    </figure>

## Modification of the Database

### Deletion

```sql
DELETE FROM <table | view> 
[WHERE <condition>] 
```

<figure>
    <img src="../assets/删除例子.png" width="60%"/>
</figure>

### Insertion

```sql
INSERT INTO <table|view> [(c1, c2,…)] 
VALUES (e1, e2, …) 

INSERT INTO <table|view> [(c1, c2,…)] 
SELECT e1, e2, … 
FROM … 
```

- 若插入时没有明确指出属性名，则需要保证插入的值的顺序和表中定义的属性顺序一致。
- 当明确指出属性名后，只需要保证 `insert` 语句中的值和属性名一一对应即可，不需要保证顺序和表中定义的属性顺序一致。

<figure>
    <img src="../assets/插入例子.png" width="65%"/>
</figure>

### Updates

```sql
UPDATE <table | view> 
SET <c1 = e1 [, c2 = e2, …]> [WHERE <condition>] 
```

比如想要给所有人的存款增加 100，可以这么写：

```sql
UPDATE account
SET balance = balance + 100
```

当我们需要根据不同条件对不同的属性进行更新时，可以使用 `case` 语句。

<figure>
    <img src="../assets/更新例子.png" width="70%"/>
</figure>

!!! tip
    - View 是虚表，对其进行的所有操作都将转化为对基表的操作。
    - 查询操作时，VIEW 与基表没有区别，但对 VIEW 的更新操作有严格限制，如只有行列视图，可更新数据。

## Joined Relations

`Join` operations take as input two relations and return as a result another relation

- **Join condition**: defines which tuples in the two relations match, and what attributes are present in the result of the join. 
- **Join type**: defines how tuples in each relation that **do not match any tuple** in the other relation (based on the join condition) are treated. 

<figure>
    <img src="../assets/连接类型.png" width="60%"/>
</figure>

??? example
    <figure>
        <img src="../assets/连接例子1.png" width="60%"/>
    </figure>

    <figure>
        <img src="../assets/连接例子2.png" width="50%"/>
    </figure>

- 自然连接：R natural {inner join, left join, right join, full join} S 
- 非自然连接：R {inner join, left join, right join, full join} S 
    - on <连接条件判别式>
    - using (<同名的等值连接属性名>)

关键字 inner 和 outer 是可选的

- natural join：以同名属性相等作为连接条件
- inner join：只返回两个表中满足连接条件的元组
- outer join：返回两个表中所有的元组，不满足连接条件的元组用 null 填充
- 非自然连接会保留两个表中的同名属性

### Left Outer Join

- 返回左表中所有的元组，右表中满足连接条件的元组
- 右表中不满足连接条件的元组用 null 填充

```sql
SELECT *
FROM table1 LEFT OUTER JOIN table2
ON table1.col1 = table2.col1
```

### Right Outer Join

- 返回右表中所有的元组，左表中满足连接条件的元组
- 左表中不满足连接条件的元组用 null 填充

```sql
SELECT *
FROM table1 RIGHT OUTER JOIN table2
ON table1.col1 = table2.col1
```

### Full Outer Join

- 返回两个表中所有的元组
- 不满足连接条件的元组用 null 填充

```sql
SELECT *
FROM table1 FULL OUTER JOIN table2
ON table1.col1 = table2.col1
```

### join 的更多例子


<figure>
    <img src="../assets/连接更多例子1.png" width="60%"/>
</figure>

<figure>
    <img src="../assets/连接更多例子2.png" width="60%"/>
</figure>

<figure>
    <img src="../assets/连接更多例子3.png" width="60%"/>
</figure>






