---
    comments: true
    statistics: true
---

# Introduction

## Purpose of Database Systems 

Characteristics of DBMS（Database Management System）：

- Efficiency and scalability in data access
- Reduced application development time
- Data independence (including physical data independence and logical data independence)
- Data integrity and security
- Concurrent access and robustness (i.e., recovery)

通常的文件处理系统具有以下的问题：

- Data redundancy and inconsistency

    数据冗余与不一致性：有各种各样的文件格式，并且数据重复存储在不同的文件中

- Difficulty in accessing data

    数据访问困难：对于每一个新任务而言，都需要编写一个新的程序

- Data isolation

    数据隔离（孤立）：数据分散在不同的文件中，数据难以共享

- Integrity problems

    完整性问题：数据的完整性约束被隐藏在程序代码中（例如，检查余额是否为非负数），很难为现有的程序和文件添加新的约束

- Atomicity problems

    原子性问题：在文件系统中，很难确保所有相关的操作都能成功完成

    Failures may leave database in an inconsistent state with partial updates carried out

- Concurrent-access anomalies

    并发访问异常：在文件系统中，很难确保多个用户同时访问数据时不会发生冲突

- Security problems

    安全问题：在文件系统中，很难确保只有授权的用户才能访问数据（i.e., Right person uses right data）

而在数据库系统为以上这些问题都提供了解决方案。

## View of Data 

<figure>
    <img src="../assets/数据库系统架构.png" width="70%">
</figure>

Three-level abstraction of databases

- **Physical level**：描述数据在磁盘上的存储方式
- **Logical level**：描述在数据库中储存的数据，以及数据之间的关系
- **View level**：不同类型用户所看到的数据不同

### Schemas and Instances

- **Schema**（模式）– the structure of the database on different level
- **Instance**（实例）– the actual content of the database at a particular point in time

它们的关系类比于编程语言中的 type 和 variable
 
- type $\leftrightarrow$ schema, variable $\leftrightarrow$ instance

### Data Independence

Ability to modify a schema definition at one level without affecting a schema definition at a higher level.

- **Physical Data Independence** (物理数据独立性) – the ability to modify the physical schema without changing the logical schema
- **Logical Data Independence** (逻辑数据独立性) - the ability to modify the logical schema without changing the user view schema

### Data Models

Data models is a collection of tools for describing data structure, data relationships, data semantics, data constraints.

不同层次的数据抽象需要不同的数据模型来描述

- Entity-Relationship model（实体-联系模型）
- Relational model(关系模型)
- Object-based data model（基于对象的数据模型）

    - Object-oriented model（面向对象模型）
    - Object-relational model（对象-关系模型）

- Semi-structured data model（XML）（半结构化数据模型）
- Other older models:

    - Network model (网状模型)
    - Hierarchical model(层次模型)
 
## Database Language 

数据库语言主要分为三类：

- **Data Definition Language (DDL)**：Specification notation for defining the database schema.

    定义数据库的结构、访问方法、一致性约束等，例如创建表、删除表、修改表等

- **Data Manipulation Language (DML)**：Language for accessing and manipulating the data in the database.

    对数据库中的数据进行操作，例如插入数据、删除数据、更新数据等

- **Data Control Language (DCL)**

### Data Definition Language (DDL，数据定义语言)

DDL statements are compiled, resulting in a set of tables stored in a special file called data dictionary (数据字典).

Data dictionary contains **metadata** (元数据，i.e. the data about data) about

- Database schema
- Integrity constraints 

    - Primary Key 
    - Referential integrity 

- Authorization 

### Data Manipulation Language (DML，数据操作语言)

Two classes of languages

- *Procedural* (过程式) – user specifies what data is required and how to get those data（e.g. C）
- *Declarative* (nonprocedural，陈述式，非过程式) – user specifies what data is required without specifying how to get those data（e.g. SQL）

**SQL** is the most widely used query language

### SQL（Structured Query Language）

SQL = DDL + DML + DCL

SQL is the most widely used non-procedural query language. 

<figure>
    <img src="../assets/SQL例子.png" width="80%">
</figure>

## Database Design 

### Entity-Relationship (E-R) Model

<figure>
    <img src="../assets/实体-联系模型.png" width="70%">
</figure>

### Relational Model 

A Sample of Relational Model: University Database

<figure>
    <img src="../assets/关系模型例子.png" width="75%">
</figure>

## Database Users and Administrators 

Users are differentiated by the way they expect to interact with the system.

- **Naive users**（普通用户）– invoke one of the permanent application programs that have been written previously by a high level language
- **Application programmers**（应用程序员）– interact with system via SQL calls
- **Sophisticated users**（高级用户）– form requests in a database query language
- **Specialized users**（专业用户）– write specialized database applications that do not fit into the traditional data processing framework
- **Database administrator** (DBA，数据库管理员): A special user having central control over database and programs accessing those data.


<figure>
    <img src="../assets/数据库用户.png" width="75%">
</figure>

## Transaction Management 

- **Concurrent use/access** is important, but causes problems/conflict. 
- A **transaction** is a collection of operations that performs a single logical function in a database application. 
- **Transaction requirements** include *atomicity, consistence, isolation, durability*. 
- **Transaction-management component** ensures that the database remains in a consistent (or correct) state, although system failures (e.g., power failures and operating system crashes) and transaction failures. 
- **Concurrency-control manager** controls the interaction among the concurrent transactions. 

## Database Architecture 

<figure>
    <img src="../assets/数据库架构.png" width="50%">
</figure>

数据库架构主要有三个部分：

- **Storage manager**
- **Query processor**
- **Database administrator**

### Storage Manager

存储管理器是负责提供底层数据和应用程序之间的接口的模块，包括

- Transaction manager 
- Authorization and integrity manger
- File manager (interaction with the file system to process data files, data dictionary, and index files) 
- Buffer manager 

### Query Processor

Query Processor includes DDL interpreter, DML compiler, and query processing. 

- Parsing and translation 
- Optimization 
- Evaluation 

<figure>
    <img src="../assets/查询处理器.png" width="70%">
</figure>
