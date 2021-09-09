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

> **Pandas vs SQL**
- 공식문서: [https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_sql.html](https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_sql.html)

> **Pandas vs Spreadsheets**
- 공식문서: [https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_spreadsheets.html](https://pandas.pydata.org/docs/getting_started/comparison/comparison_with_spreadsheets.html)

### Relational Database

- Entity는 Table로 만들어짐

    > **Entity?**
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

        e.g. "sqlite:///filename.db"

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
        - `=`
        - `>`
        - `≥`
        - `<`
        - `≤`
        - `<>`
    - `AND, OR`

```python
import pandas as pd
from sqlalchemy import create_engine

engine = create_engine("sqllite:///data.db")

query = "SELECT * FROM [table_name] WHERE [column_name] = "[filtering_text]";

weather = pd.read_sql(query, engine)
```

### More complex **SQL queries**

- Get **unique** value - `DISTINCT`
    - `SELECT DISTINCT [column names] FROM [table];`
    - `SELECT DISTINCT * FROM [table];`
- **Aggregate** functions - `SUM`, `AVG`, `MAX`, `MIN`, `COUNT`
    - `SELECT AVG([column name]) FROM [table];`
    - `SELECT COUNT(*) FROM [table_name];`
    - `SELECT COUNT(DISTINCT [column_names]) FROM [table_name];`
- **Summarize** by categories - `GROUP BY`

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

### JSON

- Javascript Object Notation
- Common web data format
- tabular X
    - 데이터 저장에 효과적
    - 속성(atrribute) 생략 가능 = record가 모두 동일한 속성(attribute) 집합을 가질 필요 없음
- 데이터가 객체 모음(collection of objects)으로 구성됨
- 객체는 속성-값(attribute-vale) 쌍을 포함함 ~= Python dictionary {}
- 중첩 가능(nested JSON), 값 자체는 객체 또는 객체 목록일 수 있음

### Reading JSON Data

- `read_json()`
    - `dtype`
    - `orient` : 배열 또는 방향

### Orientation

- Record Orientation
    - 가장 자주 사용되는 JSON arrangement

        ![https://user-images.githubusercontent.com/69677950/132643164-5ff106d6-b87f-452d-b2a3-a2364107c055.png](https://user-images.githubusercontent.com/69677950/132643164-5ff106d6-b87f-452d-b2a3-a2364107c055.png)

- Column Orientation
    - 속성(attribute)을 반복하지 않음으로써 파일 크기 최소화(space-efficient)

        ![https://user-images.githubusercontent.com/69677950/132643234-cdc7197b-2a8f-434c-8e61-f8d4e1eccffd.png](https://user-images.githubusercontent.com/69677950/132643234-cdc7197b-2a8f-434c-8e61-f8d4e1eccffd.png)

- Specifying Orientation
    - 분할 지향적(split oriented) e.g. 열 이름, 색인, 값에 대해 다른 목록을 사용함

        ![https://user-images.githubusercontent.com/69677950/132643266-41753c47-9fe4-4813-8595-138089c05bfe.png](https://user-images.githubusercontent.com/69677950/132643266-41753c47-9fe4-4813-8595-138089c05bfe.png)

```python
import pandas as pd

death_cases = pd.read_json("nyc_death_causes.json", orient="split")
```

> **JSON orientation in Pandas**

- `'split'` : dict like `{index -> [index], columns -> [columns], data -> [values]}`
- `'records'` : list like `[{column -> value}, ... , {column -> value}]`
- `'index'` : dict like `{index -> {column -> value}}`
- `'columns'` : dict like `{column -> {index -> value}}`
- `'values'` : just the values array

- 공식문서: [https://pandas.pydata.org/pandas-docs/version/1.1.3/reference/api/pandas.read_json.html](https://pandas.pydata.org/pandas-docs/version/1.1.3/reference/api/pandas.read_json.html)

### API(Application Programming Interfaces)

- 정의 및 프로토콜 세트로서 애플리케이션 소프트웨어를 구축하고 통합하기 위해 쓰입니다.
- 요청을 보낼 endpoint 제공

### Requests(Python library)

- 웹사이트로부터 데이터를 주고 받을 수 있음
- URL 기반으로 접근하기 때문에 특정 API에만 묶여있지 않음
- `requests.get([urel_string])`
    - `params`: dictionary, parameters and values to cutomize API request
    - `headers`: dictionary, user authentication to API
    - return: `response object` (data & metadata)
        - `response.json()`: 데이터만 가져오기
            - return: dictionary
            - `pd.DataFrame()`을 사용해서 JSON to data frame (not `read_json()`)

```python
import request
import pandas as pd

api_url = "https://api.yelp.com/v3/businesses/search"
params = {"term": "bookstore", "location": "San Francisco"}
headers = {"Authorization": "Bearer {}".format(api_key)}

# Query Yelp API w/ headers and params set
response = requests.get(api_url, params=params, headers=headers)

# Isolate JSON data from response object
date = response.json() # dictionary
bookstores = pd.DataFrame(data["businesses"])
```

### nested JSON

- value 자체가 객체(object)인 경우
- 평탄화 방법
    - `pandas.io.json`
        - 주의사항: own import statement
        - `json_normalize()`
            - ditionary/list of dictionary ~= `pd.DataFrame()`
            - return: flattened data frame
            - column name: `attribute.nestedattribute`
            - sep: 점이나 콤마와 다른 구분 기호를 지정 e.g. "_"

```python
import pandas as pd
import requests
from pandas.io.json import json_normalize

api_url = "https://api.yelp.com/v3/businesses/search"
params = {"term": "bookstore", "location": "San Francisco"}
headers = {"Authorization": "Bearer {}".format(api_key)}

# Query Yelp API w/ headers and params set
response = requests.get(api_url, params=params, headers=headers)

# Isolate JSON data from response object
date = response.json() # dictionary
# bookstores = pd.DataFrame(data["businesses"])

# Flatten nested JSON
bookstores = json_normalize(data["businesses"], sep="_")
```

### Issue - Deeply Nested Data

- 사용자 지정 Flatten 함수 사용
- 분석과 관계 없을 때에는, 그대로 유지
- `json_normalize()`
    - `record_path`: 중첩된 데이터(nested data)에 대한 문자열 속성의 string/list
    - `meta`: higher level attribute
    - `meta_prefix`

```python
df = json_normalize(data["businesses"], sep="_", record_path="categories", 
										meta=["name", "alias", "rating", ["coordinates", "latitude"], ["coordinates", "longitude"]],
										meta_prefix="biz_")
```

### Combining multiple datasets

- Appending
    - `DataFrame.append(DataFrame)`
    - `ignore_index=True/False`
- Merging
    - `DataFrame.merge()`
        - key columns(전제 - same data type)
            - `on`: name 동일할 때
            - `left_on`
            - `right_on`