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

![image](/images/flatdatabase.png)

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
  
  # Print count
  favorite = input("Favorite: ")
  print(f"{favorite}: {counts[favorite]}")
  ```
---
## Relational Database

Different from csv, it could have manysheets with relations

---
## SQL

Database specific language, standard for **StructuredQuery Language**. It's a declaritive language, which means you would not have 'if else condition' or 'for loop' in this language. You will gonna describe the data you want to get back. 

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
```
CREATE TABLE table (column type,...);
```

- INSERT **data**

```
INSERT INTO table (column...) VALUES(value, ...);
```
---
##  ~~READ~~, SELECT

```
SELECT * FROM (table)
```
---
##  SELECT ... FROM (table);

```
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
```
WHERE
LIKE
LIMIT
ORDER BY
GROUP BY
```

---
##
  ![image](/images/HkqzY8rJR.png)

---
##
  ![image](/images/SJv2KUHJA.png)

---
## UPDATE
```
UPDATE favorites SET 
language = 'SQL', problem = 'Fiftyvil'
WHERE ...
```

---
## DELETE, DROP
  - DELETE **data**
  ```
  DELETE FROM favorites WHERE Timestamp IS NULL;
  ```
  - DROP **table**
  ```
  DROP TABLE table_name;
  ```

---
## 軟刪除

 ![image](/images/softDelete.png)

 ```
 SELECT * FROM (table) WHERE deleteAt IS NOT NULL
 ```

---
##  Why Ralational Database? 
Eliminating redundancy in data storage.

![width:400px](/images/redundancyDatabase.png)

---
##
![image](/images/cs50Week7Slide025.png)

---
## TYPES in SQL

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

> 欄位大小

---
## one-to-one relationship

![image](https://cs50.harvard.edu/x/2024/notes/7/cs50Week7Slide032.png)

---
##
![image](/images/ryWMaB8kA.png)

---
## Primary key 主鍵 & Foreign key 外鍵

  - Primary key 主鍵: unique id (每張表都會有的 key，每一筆資料的 id)
  - Foriegn key 外鍵 : simply the present of primary key in some other table, used to build relationships between tables by pointing to the primary key in another table.

---
## one-to-one relationship

```SQL
SELECT title
FROM shows
WHERE id IN (
    SELECT show_id
    FROM ratings
    WHERE rating >= 6.0
    LIMIT 10
)
```
---
##
![image](/images/rk2hQ_8J0.png)

---
##
``` =SQL
SELECT * FROM shows
JOIN ratings on shows.id = ratings.show_id
WHERE rating >= 6.0
LIMIT 10;
```
---
## one-to-many relationship
![image](/images/one-to-many.png)

--- 
## one-to-many relationship

```SQL
SELECT * FROM shows
JOIN genres
ON shows.id = genres.show_id
WHERE id = 63881;
```
---
## many-to-many relationship
![image](/images/r1Fend8yC.png)

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
##
```SQL
SELECT title from shows
WHERE id IN (
  SELECT show_id FROM stars
  WHERE person_id = (
    SELECT id FROM people
    WHERE name = 'Steve Carell'
  )
);
```
---
##

- JOIN 比較慢

> 補充説明：爲什麽 JOIN 比較慢

> 補充説明：實務上，建立關聯會耗費比較多的效能，在 CRAETE UPDATE 時，要刪除資料的時候，也會有限制。在建立資料的時考慮這一點
---
## **Index**
  A data structure that makes it faster to perform queries.

  > If you are sure about the application is going to search on certain columns frequently, you can prepare the database in advance to build up INDEX in memory. So the answer would get back faster.
---
##
```
-- CREATE INDEX name ON table (columns,...)
CREATE INDEX title_index ON shows (title);
```
---
## B-tree  
![image](/images/H1wcXKI10.png)

---
##
- Primary key is automatically index.Foreign keys are not.
- WHERE columns

---
## Why not index every column in every table? 

Becus indexes take more memories or spaces, **trading off spaces for time.**

> 有 index 的 table 做資料處理時會耗費較多效能，因爲需要重新建立索引表

---
## **Race Condition**
  - 冰箱沒有牛奶

  ![width:480px](/images/race%20condition.jpg)

---
##    
  - `BEGIN TRANSACTION`, `COMMIT`, and `ROLLBACK` can sort of help this problem. Transaction means your code should work all together, or not at all. **atomic**, **not being interupted**.
  - **lock**
  > updating multiple tables
---

## **SQL Injection**


```py
user = "jill --"
password = "hello87"
rows = db.execute(f"SELECT COUNT(*) FROM users WHERE username = {username} AND password = {password}")
```

--- 
## How to Prevent SQL Injection

- 不要用字串插補，要用參數

```py
rows = db.execute("SELECT COUNT(*) FROM users WHERE username = ? AND password = ?", username, password")
```

- **ORM**, e.g. cs50 sql library 會處理掉 escape word 的問題

---
## ORM
Object Relational Mapping
![width:900px](/images/ORM.png)

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
## Mongo DB
