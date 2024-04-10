---
marp: true
theme: default
paginate: true
class: invert
---

# Week7 SQL
---
## Flat-File Database
code for a text file containing all of your data
  - .csv : comma-seperated values 
  - rows and cloumns

![image](https://chilinglee.github.io/cs50-week7-sql/images/flatdatabase.png)

---
## 
```py
# Gets a specific count
import csv
from collections import Counter

# Open CSV file
with open("favorites.csv", "r") as file:
# Create DictReader
reader = csv.DictReader(file)

# Counts
counts = Counter()
# Iterate over CSV file, counting favorites
for row in reader:
    favorite = row["problem"]
    counts[favorite] += 1
  
# Print counts
for favorite in sorted(counts):
    print(f"{favorite}: {counts[favorite]}")
```
---
## Relational Database

Different from csv, it could have manysheets with relations

---
## SQL

Database specific language, standard for **Structured Query Language**. It's a **declaritive** language, which means you would not have 'if else condition' or 'for loop' in this language. You will gonna describe the data you want to get back. 

---
## CRUD

In relational database, you can really only do these four things.

- CREAT
- READ
- UPDATE
- DELETE

--- 
## CREATE, INSERT

- CREATE **table**
```SQL
CREATE TABLE table (column type,...);
```

- INSERT **data**

```SQL
INSERT INTO table (column...) VALUES(value, ...);
```
---
##  ~~READ~~, SELECT

```SQL
SELECT * FROM (table)
```
---
##  SELECT ... FROM (table);

```SQL
AVG
COUNT
DISTINCT
MAX
MIN
LOWER
UPPER
```

---
## SELECT * FROM (table) ...;
```SQL
WHERE
LIKE
LIMIT
ORDER BY
GROUP BY
```

---
##
  ![image](https://chilinglee.github.io/cs50-week7-sql/images/HkqzY8rJR.png)

---
##
  ![image](https://chilinglee.github.io/cs50-week7-sql/images/SJv2KUHJA.png)

---
## UPDATE
```SQL
UPDATE favorites SET 
language = 'SQL', problem = 'Fiftyvil'
WHERE ...
```

---
## DELETE, DROP
- DELETE **data**
```SQL
DELETE FROM favorites WHERE Timestamp IS NULL;
```
- DROP **table**
```SQL
DROP TABLE table_name;
```

---
## 軟刪除

![image](https://chilinglee.github.io/cs50-week7-sql/images/softDelete.png)

```SQL
SELECT * FROM (table) WHERE deleteAt IS NOTNULL
```

---
##  Why Ralational Database? 
Eliminating redundancy in data storage.

![width:400px](https://chilinglee.github.io/cs50-week7-sql/images/redundancyDatabase.png)

--- 
### data
![image](https://chilinglee.github.io/cs50-week7-sql/images/datasample.png)
<br />

### table schema
![image](https://chilinglee.github.io/cs50-week7-sql/images/columnsInMSSQL.png)

---
## TYPES in SQLite

```SQL
BLOB       -- binary large objects that are groups of ones and zeros
INTEGER    -- an integer
NUMERIC    -- for numbers that are formatted specially like dates
REAL       -- like a float
TEXT       -- for strings and the like
```

---
## Contrains

```SQL
NOT NULL
UNIQUE
```
## 欄位大小限制

![image](https://chilinglee.github.io/cs50-week7-sql/images/columnsInMSSQL.png)

---
## [IMDb](https://www.imdb.com/)
![width:1000px](https://chilinglee.github.io/cs50-week7-sql/images/cs50Week7Slide025.png)

---
## Relations
- one-to-one
- one-to-many
- many-to-many

---
## one-to-one relationship

![image](https://cs50.harvard.edu/x/2024/notes/7/cs50Week7Slide032.png)

---
## Schema
![width:750px](https://chilinglee.github.io/cs50-week7-sql/images/ryWMaB8kA.png)

---
## Primary key 主鍵 & Foreign key 外鍵

  - Primary key 主鍵: unique id (基本上每張表都會有的 key，每一筆資料的 id)
  - Foriegn key 外鍵 : simply the present of primary key in some other table, used to build relationships between tables by pointing to the primary key in another table.

---
## one-to-one relationship
### Subquery
```SQL
SELECT *
FROM shows
WHERE id IN (
    SELECT show_id
    FROM ratings
    WHERE rating >= 6.0
    LIMIT 10
)
```
---
### Subquery

![width:1200px](https://chilinglee.github.io/cs50-week7-sql/images/rk2hQ_8J0.png)

---
### JOIN
``` SQL
SELECT title, rating
FROM shows
JOIN ratings on shows.id = ratings.show_id
WHERE rating >= 6.0
LIMIT 10;
```

![image](https://chilinglee.github.io/cs50-week7-sql/images/titlerating.png)

---
## one-to-many relationship
![width:750px](https://chilinglee.github.io/cs50-week7-sql/images/one-to-many.png)

--- 
## one-to-many relationship

```SQL
SELECT * FROM shows
JOIN genres ON shows.id = genres.show_id
WHERE id = 63881;
```
---
## many-to-many relationship
![image](https://chilinglee.github.io/cs50-week7-sql/images/r1Fend8yC.png)

---
##
```SQL
SELECT person_id FROM stars
WHERE show_id = (
  SELECT id FROM shows
  WHERE title = 'The Office' AND year = 2005
);
```
---
##
```SQL
SELECT name FROM people
WHERE id IN (
    SELECT person_id FROM stars
    WHERE show_id = (
      SELECT id FROM shows
      WHERE title = 'The Office' AND year = 2005
    )
);
```
---
## Subquery
A subquery is a query that is nested inside another query.
## JOIN 
A join is a query that combines data from two or more tables based on a common column or condition.

> Performance： depends on db 
> Readability
> Flexibility

---
## When building relations...

- CREATE/UPDATE/DELETE 維持資料完整性
- 複雜的關聯會影響效能，需優化查詢
- QUERY 適當建立 INDEX 提高查詢效能


---
## **Index**
  A data structure that makes it faster to perform queries.

  > If you are sure about the application is going to search on certain columns frequently, you can prepare the database in advance to build up INDEX in memory. So the answer would get back faster.
---
## B-tree  
![image](https://chilinglee.github.io/cs50-week7-sql/images/H1wcXKI10.png)

---
##
```
-- CREATE INDEX name ON table (columns,...)
CREATE INDEX title_index ON shows (title);
```
---
##
- Primary key is automatically index.
- Foreign keys are not.

---
## Why not index every column in every table? 

- Becus indexes take more memories or spaces, **trading off spaces for time.**

- 有 index 的 table 做資料操作時會耗費較多效能，因爲需要重新建立索引表

### Wher to index
將 index 建立在時常作爲 WHERE 條件的欄位
<br/>

>叢集索引/非叢集索引

---
## **Race Condition**
  冰箱沒有牛奶

---
## **Race Condition**

  ![width:520px](https://chilinglee.github.io/cs50-week7-sql/images/race%20condition.jpg)

---
## Transaction
  - `BEGIN TRANSACTION`, `COMMIT`, and `ROLLBACK` can sort of help this problem. Transaction means your code should work all together, or not at all. **atomic**, **not being interupted**.
> 在資料庫的每一筆交易中只有兩種可能發生，第一種是全部完全(commit)，第二種是全部不完成(rollback)
  - **lock**
---

## **SQL Injection**


```py
user = "jill --"
password = "hello87"
rows = db.execute(f"SELECT COUNT(*) FROM users WHERE username = {username} AND password = {password}")
```

--- 
## How to Prevent SQL Injection

### 不要用字串插補，使用參數

```py
rows = db.execute("SELECT COUNT(*) FROM users WHERE username = ? AND password = ?", username, password")
```

### 使用 ORM
e.g. cs50 sql library 會處理掉 escape word 的問題

---
## ORM
Object Relational Mapping
![width:900px](https://chilinglee.github.io/cs50-week7-sql/images/ORM.png)

---
## ORM
```js
import { DataTypes, Model } from 'sequelize';

export class UserModel extends Model {}

UserModel.init(
  {
    user_id: {
      type: DataTypes.UUID,
      defaultValue: DataTypes.UUIDV4,
      primaryKey: true,
    },
    name: {
      type: DataTypes.STRING(200),
    },
    email: {
      type: DataTypes.STRING(200),
      allowNull: false,
    },
    password: {
      type: DataTypes.STRING(200),
      allowNull: false,
    },
    point: {
      type: DataTypes.INTEGER,
    },
  },
  {
    sequelize,
    modelName: 'User',
    tableName: 'users',
    timestamps: true,
    createdAt: 'created_on',
    updatedAt: 'update_on',
  }
);
```
---
## ORM

Before
```SQL
SELECT * 
FROM users 
WHERE user_id = 20
```
After
```js
UserModel.findByPk(id);
```
---
## NoSQL
- **collections** and **documents**, e.g MongoDB
![height:500px](https://chilinglee.github.io/cs50-week7-sql/images/doc.png)

--- 
## NoSQL
- **key-value**, e.g. redis
![image](https://chilinglee.github.io/cs50-week7-sql/images/key-value.png)

--- 
## NoSQL

- without schema, adjustable
- big data storage
- horizontally scaling

---

## RDBMS v.s NoSQL

![image](https://chilinglee.github.io/cs50-week7-sql/images/sqlNosql.png)

---
## References
- [[Rails 效能優化] 資料庫索引 Database Indexing](https://medium.com/@jinghua.shih/rails-%E7%B6%B2%E7%AB%99%E6%95%88%E8%83%BD%E5%84%AA%E5%8C%96-%E4%BA%8C-%E8%B3%87%E6%96%99%E5%BA%AB%E7%B4%A2%E5%BC%95-database-index-bd89fa3757a)
- [RDBMS vs. NOSQL | 關聯式資料庫 vs. 非關聯式資料庫](https://medium.com/@eric248655665/rdbms-vs-nosql-%E9%97%9C%E8%81%AF%E5%BC%8F%E8%B3%87%E6%96%99%E5%BA%AB-vs-%E9%9D%9E%E9%97%9C%E8%81%AF%E5%BC%8F%E8%B3%87%E6%96%99%E5%BA%AB-1423c9fbb91a)
- [初步認識分散式資料庫與 NoSQL CAP 理論](https://oldmo860617.medium.com/%E5%88%9D%E6%AD%A5%E8%AA%8D%E8%AD%98%E5%88%86%E6%95%A3%E5%BC%8F%E8%B3%87%E6%96%99%E5%BA%AB%E8%88%87-nosql-cap-%E7%90%86%E8%AB%96-a02d377938d1)
- [高併發情況下正確處理共享資料筆記](https://blog.twjoin.com/%E9%AB%98%E4%BD%B5%E7%99%BC%E6%83%85%E6%B3%81%E4%B8%8B%E6%AD%A3%E7%A2%BA%E8%99%95%E7%90%86%E5%85%B1%E4%BA%AB%E8%B3%87%E6%96%99%E7%AD%86%E8%A8%98-5e80ab488686)