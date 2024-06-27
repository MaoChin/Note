# MySQL 语句

表的操作CRUD：增加(Create)、查询(Retrieve)、更新(Update)、删除(Delete)  

## 1. 增加

```sql
INSERT INTO student VALUES (100, 10000, '唐三藏', NULL);
```

## 2. 查询

### 0. 别名

别名可以在 order by 中使用，但是==不能在 where 中使用。==

```sql
SELECT name, chinese + math + english as total FROM student ORDER BY total DESC;
```

### 1. 去重

```sql
ELECT DISTINCT math FROM exam_result;
```

### 2. 排序

```sql
SELECT name, qq_mail FROM student ORDER BY qq_mail DESC;
```

### 3. 分页

```sql
# 从 0 开始偏移三个
SELECT id, name, math, english, chinese FROM exam_result ORDER BY id LIMIT 3 OFFSET 0;
```

### 4. 条件

```sql
SELECT name, english FROM exam_result WHERE english < 60;
```

| 运算符            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| >, >=, <, <=      | 大于，大于等于，小于，小于等于                               |
| =                 | 等于，NULL 不安全，例如 NULL = NULL 的结果是 NULL            |
| <=>               | 等于，NULL 安全，例如 NULL <=> NULL 的结果是 TRUE(1)         |
| !=, <>            | 不等于                                                       |
| BETWEEN a0 AND a1 | 范围匹配，[a0, a1]，如果 a0 <= value <= a1，返回 TRUE(1)     |
| IN (option, ...)  | 如果是 option 中的任意一个，返回 TRUE(1)                     |
| IS NULL           | 是 NULL                                                      |
| IS NOT NULL       | 不是 NULL                                                    |
| LIKE              | 模糊匹配。% 表示任意多个（包括 0 个）任意字符；_ 表示任意一个字 符 |
| AND               | 多个条件必须都为 TRUE(1)，结果才是 TRUE(1)                   |
| OR                | 任意一个条件为 TRUE(1), 结果为 TRUE(1)                       |
| NOT               | 条件为 TRUE(1)，结果为 FALSE(0)                              |

### 5. 聚合查询（行和行之间的运算）

#### 1. 聚合函数

| 函数                   | 说明                                        |
| ---------------------- | ------------------------------------------- |
| COUNT([DISTINCT] expr) | 返回查询到的数据的 数量                     |
| SUM([DISTINCT] expr)   | 返回查询到的数据的 总和，不是数字没有意义   |
| AVG([DISTINCT] expr)   | 返回查询到的数据的 平均值，不是数字没有意义 |
| MAX([DISTINCT] expr)   | 返回查询到的数据的 最大值，不是数字没有意义 |
| MIN([DISTINCT] expr)   | 返回查询到的数据的 最小值，不是数字没有意义 |

#### 2. group by

==针对指定的列进行分组查询==，而后可以进行聚合查询。一般分组聚合是搭配使用的。

```sql
select role,max(salary),min(salary),avg(salary) from emp group by role;
```

#### 3. having

GROUP BY 子句进行分组以后，需要==对分组结果再进行条件过滤==时，不能使用 WHERE 语句，而需要用HAVING。

```sql
select role,max(salary),min(salary),avg(salary) from emp group by role
having avg(salary)<1500;
```

### 6. 联合查询

#### 1. 内连接

...（inner）join .... on ....

#### 2. 外连接

外连接分为左外连接（left join）和右外连接（right join）。如果联合查询，左侧的表完全显示我们就说是左外连接；右侧的表完全显示我们就说是右外连接。  

一般两个表的连接条件不能完全对应时考虑使用外连接。

```sql
select * from student left join score on student.id=score.student_id;
```

#### 3. 自连接

自连接是指同一张表连接自身进行查询。 可以把行之间的比较关系转换成列之间的比较关系。

#### 4. 子查询 

子查询是指嵌入在其他sql语句中的select语句，也叫嵌套查询。（不建议用

#### 5. 合并查询

使用 union 和 union all 时，前后查询的结果集中，字段需要一致。

`union`：取得两个结果集的并集。当使用该操作符时，会自动去掉结果集中的重复行。

`union all`：取得两个结果集的并集。当使用该操作符时，不会去掉结果集中的重复行。

## 3. 更新

```sql
UPDATE exam_result SET math = 80 WHERE name = '孙悟空';
```

## 4. 删除

```sql
DELETE FROM exam_result WHERE name = '孙悟空';
```

## 5. 约束

| 约束类型           | 说明                         | 示例                                      |
| ------------------ | ---------------------------- | ----------------------------------------- |
| NULL约束           | 使用NOT NULL指定列不为 空    | name varchar(20) not null,                |
| UNIQUE唯一约束     | 指定列为唯一的、不重复的     | name varchar(20) unique,                  |
| DEFAULT默认值约 束 | 指定列为空时的默认值         | age int default 20,                       |
| 主键约束           | NOT NULL 和 UNIQUE 的 结合   | id int primary key,                       |
| 外键约束           | 关联其他表的==主键或唯一键== | foreign key (字段名) references 主 表(列) |
| CHECK约束（了 解） | 保证列中的值符合指定的条 件  | check (sex ='男' or sex='女')             |

