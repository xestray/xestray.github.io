---
    comments: true
    statistics: true
---

# Relational Model

## Structure of Relational Databases

### Basic Structure

Formally, given set $D_1, D_2, \cdots, D_n$, a relation $r$ is a subset of $D_1 \times D_2 \times \cdots \times D_n$. —— a Cartesian product (笛卡儿积) of a list of domain $D_i$
Thus a relation is a set of n-tuple $(a_{1j}, a_{2j}, \cdots, a_{nj})$ where each $a_{ij} \in D_i(i \in [1, n])$

### Attribute Types

- Each attribute of a relation has a name.
- The set of allowed values for each attribute is called the domain (域)  of the attribute.
- The special value null is a member of every domain. 

### Relation Schema

Assume $A_1, A_2, \cdots, A_n$ are **attributes**, then $R = (A_1, A_2, \cdots, A_n)$ is a relation schema.
e.g. instructor = (ID, name, dept_name, salary).

A relation instance $r$ defined over schema $R$ is denoted by $r(R)$. (or $r(R)$ us a relation on relation schema $R$)
e.g. instructor(instructor-schema) = instructor(ID, name, dept_name, salary) 

### Relation Instance

The current values (i.e. relation instance) of a relation are specified by a table.

An element $t$ of $r$ is a **tuple**, represented by a **row** in a table.

- 若 $t$ 是一个元组，那么 $t[name]$ 表示元组 $t$ 中名称为 $name$ 的属性的值。
 















