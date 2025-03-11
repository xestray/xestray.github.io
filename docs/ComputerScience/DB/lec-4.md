---
    comments: true
    statistics: true
---

# 进阶 SQL

## SQL Data Types and Schemas

SQL 中的类型包括内置类型和用户自定义类型，用户自定义类型可以通过 `CREATE TYPE` 语句创建。

???+ example

    ```sql
    Create type person_name as varchar (20);
    Create table student 
        (sno char(10) primary key, 
        sname person_name, 
        ssex char(1), 
        birthday date);
    Drop type person_name;
    ```

我们还可以创建新的定义域（domain），domain 和 type 的区别在于 domain 可以包含约束条件。

???+ example

    ```sql
    Create domain person_name as varchar(20) not null;
    Create table student 
        (sno char(10) primary key, 
        sname person_name, 
        ssex char(1), 
        birthday date);
    Drop domain person_name;
    ```
    
SQL 中还有专门用于存储大对象的类型（large-object type）

- blob: binary large object -- object is a large collection of uninterpreted binary data
- clob: character large object -- object is a large collection of character data

!!! info
    <figure>87
    <img src="../assets/数据库实例和方法.png" width="65%"/>
    </figure>

    - instance 指内存中可供操作的一个数据库实例，是外界与数据库系统交互的接口。
    - schema 是指数据库中的所有表、视图、索引等对象的集合，包含了数据的组织方式和约束条件。
    - table、view 是数据库中存储数据的基本单位。

## Integrity Constraints

Integrity constraints guard against accidental damage to the database, by ensuring that authorized changes to the database do not result in a loss of data consistency. 

一致性约束保证了数据库不会已授权的修改导致数据一致性的丢失。

- 一致性约束包括实体完整性、参照完整性和用户定义的完整性约束。
- 完整性约束由 DBMS 维护，数据库实例（instance）的状态必须满足完整性约束。

我们常见的在单个 relation 上的完整性约束有：

- not null
- primary key
- unique
- check(P)，其中 P 是一个谓词，用于检查插入或更新的数据是否满足某种条件。

!!! example "domain constraints"

    ```sql
    create domain hourly_wage numeric(5, 2) 
    constraint value_test check(value > = 4.00)
    ```

    这里的 `constraint value-test` 是可选的，但是可以帮助我们明确约束的名称。

### Referential Integrity

!!! note "参照完整性的形式化定义"

    Let $r_1(R_1)$ and $r_2(R_2)$ be the relations with primary keys $K_1$ and $K_2$, respectively. 

    The subset $\alpha$ of $R_2$ is a **foreign key** referencing $K_1$ in relation $r_1$, if for every $t_2$ in $r_2$ there must be a tuple $t_1$ in $r_1$ such that $t_1[K_1] = t_2[\alpha]$. 

    即 $R_2$ 中属性 $\alpha$ 的所有值必须在 $R_1$ 中的 $K_1$ 属性中有对应的值。

    Referential integrity constraint also called **subset dependency**, since its can be written as 
    $$ \Pi_{\alpha}(r_2) \subseteq \Pi_{K_1}(r_1) $$

    - 参照关系中外码的值必须在被参照关系中实际存在，或这个外码的值为 null。

当我们对一个表进行修改时，必须始终保证参照一致完整性
$ \Pi_{\alpha}(r_2) \subseteq \Pi_{K_1}(r_1) $，where $\alpha$ in $r_2$ is a Foreign Key，$K_1$ 是主码。

- Insert: 

    If a tuple $t_2$ is inserted into $r_2$, the system must ensure that there is a tuple $t_1$ in $r_1$ such that $t_1[K] = t_2[\alpha]$, i.e., 
    $$ t_2[\alpha] \in \Pi_{K_1}(r_1) $$

- Delete: 

    当删除 $r_1$ 中的一个 tuple $t_1$ 时，系统必须查找 $r_2$ 中是否有存在 tuple 的的一个集合，使得它们的 $\alpha$ 属性值等于 $t_1[K_1]$，即 $\sigma_{\alpha = t_1[K_1]}(r_2)$。

    如果找到的这个元组集合不为空，那么

    - 要么这个删除命令被当作一个错误而拒绝执行
    - 要么这些指向 $t_1$ 的元组也一起被删除（级联删除）

- Update:

    如果关系 $r_2$ 中的元组 $t_2$ 被更新了，并且这个更新改变了外键 $\alpha$ 的值，那么会经历一个类似于 insert 操作的检查，系统必须保证 $t_2[\alpha]$ 的新值在 $r_1$ 中有对应的值，即 $t_2'[\alpha] \in \Pi_{K_1}(r_1)$。

    如果关系 $r_1$ 中的元组 $t_1$ 被更新了，并且这个更新改变了主键 $K_1$ 的值，那么会经历一个类似于 delete 操作的检查，

    - 要么这个更新被当作一个错误而拒绝执行
    - 要么这些指向 $t_1$ 的元组也一起被更新（级联更新）

### Cascading Actions in SQL 

我们在设置外键时还可以使用 `on delete cascade` 和 `on update cascade` 语句来指定删除和更新时的操作。例如

```sql
CREATE TABLE account(
    ...
    foreign key (branch_name) references branch 
    [on delete cascade]
    [on update cascade]
);
```

- 如果一个外键依赖链横跨多个表，当我们指定 `cascade` 时，删除或更新操作会沿着依赖链一直传递下去
- 但是，如果级联更新或删除会导致违反完整性约束，并且它不能被更深的级联操作解决，那么系统会拒绝这个操作。

由于 null 值不会违反参照完整性，所以我们也可以在外键上指定 `on delete set null` 或 `on update set null` 来设置外键的值为 null。

**参照一致性约束只在 transaction 的末尾检查**，这意味着级联操作的中间步骤允许违反参照完整性，只要这一系列操作执行完毕后，数据库的状态满足参照完整性约束即可。

### Assertions

An **assertion** is a predicate expressing a condition that we wish the database always to satisfy. --- **for complex check condition** on several relations! 

SQL 中 assertion 语句的格式如下：
    
```sql
CREATE ASSERTION assertion_name 
    check (condition);
```

当一个 assertion 被创建后，DBMS 会在每次对数据库进行修改时检查这个 assertion 是否被满足。

- 由于反复检查会带来很大的额外开销，因此使用 assertion 时应该谨慎。

!!! example
    if we require *“the sum of all loan amounts for each branch must be less than the sum of all account balances at the branch”.* 

    但是 SQL 并不支持类似于 for all X，P(X) 的语法，所以我们需要把它转变为 not exists X, such that not P(X) 的形式。因为
    $$ \forall X, P(X) \equiv \neg \exists X, \neg P(X) $$

    于是上面这个 assertion 可以写成

    ```sql
    CREATE ASSERTION sum-constraint CHECK 
        (not exists 
        (select * from branch B 
        where (select sum(amount) from loan 
                where loan.branch-name = B.branch-name) 
                > (select sum(balance) from account 
                where account.branch-name = B.branch-name)))
    ```

    当 `check()` 中的条件为 false 时，DBMS 会拒绝这个修改操作。

### Triggers

A trigger is a statement that is executed **automatically** by the system **as a side-effect of a modification** to the database.

To design a trigger mechanism, we must: 

- Specify the *conditions* under which the trigger is to be executed. 
- Specify the *actions* to be taken when the trigger executes. 

```sql
CREATE TRIGGER trigger_name
    {BEFORE | AFTER} {INSERT | DELETE | UPDATE [OF column_name]}
    ON table_name
    FOR EACH {ROW | STATEMENT}
    EXECUTE PROCEDURE function_name;
```

???+ example

    <figure>
        <img src="../assets/trigger例子1.png" width="70%"/>
    </figure>

    <figure>
        <img src="../assets/trigger例子1.png" width="70%"/>
    </figure>

触发 trigger 的事件可以是插入、删除和更新，由 update 语句触发的 trigger 可以指定由某个特定属性的更新触发。

```sql
Create trigger overdraft-trigger 
    after update of balance on account …
```

!!! note "Statement Level Triggers"
    Instead of executing a separate action for each affected row, a single action can be executed for all rows affected by a transaction. 

    - Use **for each statement** instead of *for each row* 
    - Use **referencing old table** or *referencing new table* to refer to temporary tables  (called transition tables) containing the affected rows 
    - Can be more efficient when dealing with SQL statements that update a large number of rows 

    我们可以考虑下面这个使用语句级触发器的例子。假设我们有 account 和 account_log 两个表，我们希望当账户余额变化时相应的记录日志也会更新。

    两个表的结构如下：

    ```sql
    CREATE TABLE account (
        account_number INTEGER PRIMARY KEY,
        balance DECIMAL(10, 2)
    );

    CREATE TABLE account_log (
        account_number INTEGER,
        old_balance DECIMAL(10, 2),
        new_balance DECIMAL(10, 2),
        update_time TIMESTAMP
    );
    ```

    我们可以设置一个语句级触发器，每次 account 表被更新时，account_log 表也会被更新。

    ```sql
    CREATE TRIGGER account_update_trigger
        AFTER UPDATE OF balance ON account    
        REFERENCING OLD TABLE AS old_account
                    NEW TABLE AS new_account
        FOR EACH STATEMENT
        BEGIN
            INSERT INTO account_log(account_number, old_balance, new_balance, update_time)
            SELECT old.account_number, old.balance, new.balance, CURRENT_TIMESTAMP
            FROM old_account old, new_account new
            WHERE old.account_number = new.account_number;
        END;
    ```

    这个触发器会在 account 表的 balance 属性被更新后，会把所有被修改的 account 记录插入到 account_log 表中。这样就可以一次性记录批量的更新，而不需要为每一个更新操作都执行一次插入操作。

    - Statement Level Triggers 在一次性对多个账户修改余额时，只会触发一次更新日志的操作
    - Row Level Triggers 在一次性对多个账户修改余额时，每一个账户都会单独触发一次更新日志的操作

触发器在早年被用于以下任务：

- 维持汇总数据（如各个部门的总工资）
- 通过记录对特定关系的改变（称为 change 或 deltta relation）来复制数据库，并且具有一个单独的进程来把更改应用到副本中

There are better ways of doing these now: 

- Databases today provide built in  materialized view  facilities to maintain summary data; 
- Databases provide built-in support for replication. 

!!! tip "比较 check、assertion 和 trigger"
    - Check: 用于检查单个 relation 的完整性约束
    
        - 局限于单个表的单个列或多个列，不能执行复杂的逻辑检查，执行速度较快

    - Assertion: 用于检查多个 relation 的完整性约束

        - 可以在数据库层面定义更复杂的跨表约束，适用于较复杂的业务规则
        - 但大多数数据库系统不完全支持 ASSERTION，并且性能开销较大，因为它需要对整个数据库的状态进行检查。

    - Trigger: 在对数据库执行插入、更新或删除操作时自动触发的操作

        - 允许我们在特定条件下执行一段SQL代码，例如，验证数据、修改数据或记录日志。
        - 灵活性极高，可以在任何数据库操作时运行自定义的逻辑
        - 但性能开销较大，因为它需要在每次数据库操作时执行额外的代码。在某些场景下，触发器可能导致递归调用（例如触发器本身触发其他触发器），需要小心管理。

















