---
layout: post
title: Streamlined Data Ingestion with pandas (2)
---

# Streamlined Data Ingestion with pandas

## Importing Data from Databases

- Introduction to database
- Refining imports with SQL queries
- More complex SQL queries
- Loading multiple table with joins

### Pandas

> Pandas vs SQL
Pandas vs Spreadsheets
- 공식문서: [https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_sql.html](https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_sql.html)
- 공식문서: [https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_spreadsheets.html](https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_spreadsheets.html)

### Relational Database

- Entity는 Table로 만들어짐

    > Entity?
    실체, 객체란 의미로 저장되기 위한 타겟, 업무상 관리가 필요한 대상 등을 명사로 표현한 것을 의미합니다.
    - 출처: [https://brunch.co.kr/@ambler/55](https://brunch.co.kr/@ambler/55)

- 각 row와 record는 Entity의 Instance
- 각 columndms 속성(attribute)에 대한 정보를 가지고 잇음
- **Table들은 각 Table간의 유일키(unique key)를 통해 연결(link, relate)될 수 있음**

    > Table과 json, dataframe, flat file, excel sheet 등과의 차이점입니다.

- spreadsheet나 flat file에 비해 더 많은 데이터와 여러 명의 동시 사용자들(multiple simultaneous users), data quality control 지원 가능
- 각 column마다 data type이 명시됨 e.g. enforcing column data type
- SQL(Structured Query Language) 사용한 interaction

### Common Relational Database

- Microsoft SQL Server
- ORACLE
- PostgreSQL
- SQLite: computer files

### Connecting to Databases

- **Create way to connect to database**
    - `create_engine(string URL)` in `sqlalchemy`

        e.g. "sqlite:///filename.db

- **Query database with SQL and pandas**
    - `pd.read_sql(query, engine)`

        > 공식문서: [https://pandas.pydata.org/docs/reference/api/pandas.read_sql.html](https://pandas.pydata.org/docs/reference/api/pandas.read_sql.html)

- SQL 문법
    - `SELECT [column_name] FROM [table_name];`
    - `SELECT * FROM [table_name];`

```python
import pandas as pd
from sqlalchemy import create_engine

engine = create_engine("sqllite:///data.db")

# 1. use table name
weather = pd.read_sql("weather", engine)

# 2. use sql statement
weather = pd.read_sql("SELECT * FROM weather", engine)
```

### Refining imports with SQL queries

- SELECT
    - `SELECT [column_name] FROM [table_name];`
- WHERE
    - `SELECT [column_name] FROM [table_name] WHERE [condition];`
    - Filtering
        - `=, >, ≥. <. ≤, <>`
    - `AND, OR`

```python
import pandas as pd
from sqlalchemy import create_engine

engine = create_engine("sqllite:///data.db")

query = "SELECT * FROM [table_name] WHERE [column_name] = "[filtering_text]";

weather = pd.read_sql(query, engine)
```

### More complex **SQL queries**

- Get unique value - `DISTINCT`
    - `SELECT DISTINCT [column names] FROM [table];`
    - `SELECT DISTINCT * FROM [table];`
- Aggregate functions - `SUM`, `AVG`, `MAX`, `MIN`, `COUNT`
    - `SELECT AVG([column name]) FROM [table];`
    - `SELECT COUNT(*) FROM [table_name];`
    - `SELECT COUNT(DISTINCT [column_names]) FROM [table_name];`
- Summarize by categories - `GROUP BY`

    ```python
    # GROUP BY & COUNT 예제
    query = """
    SELECT borough, COUNT(*)
    FROM hpd311calls
    WHERE complaint_type = 'PLUMBING'
    GROUP BY borough;
    """

    data = pd.read_sql(query, engine)
    ```

### Loading multiple tables with joins ⇒ "순서"

- `SELECT` ⇒ `FROM` ⇒ `JOIN` ⇒ `WHERE`

```python
# JOIN 예제
SELECT * FROM hpd311calls
JOIN weather
ON hqd311calss.created_date = weather.date;

# JOIN & Filtering 예제
SELECT * FROM hpd311calls
JOIN weather
ON hqd311calss.created_date = weather.date
WHERE hqd311calls.complaint_type = 'HEAT/HOT WATER';

# JOIN & Aggregating 예제
SELECT hqd311calls.created_date, COUNT(*)
FROM hpd311calls
JOIN weather
ON hqd311calls.created_date = weather.date
GROUP BY hqd311calls.created_date;
```

## Importing JSON Data and Working with APIs