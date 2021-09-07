---
layout: post
title: Streamlined Data Ingestion with pandas (1)
---

# Streamlined Data Ingestion with pandas

## Importing Data from Flat Files

### Flat Files

- 간단, 생산하기 쉬운 구조
- formatting 없는 plain text 형태로 데이터 저장
- 한 라인 당 한 개의 행
- delimiter를 기준으로 각 field의 value가 구분
- 주로 comma separated 사용
- pd.read_csv() in pandas

### Loading CSVs

```python
import pandas as pd
tax_data= pd.read_csv("us_tax_data_2016.csv")
tax_data.head(4) # check the first few rows in tax data
```

### Loading Other Flat Files

- tab separated인 경우

    ```python
    import pandas as pd
    tax_data= pd.read_csv("us_tax_data_2016.csv", sep='\t')
    tax_data.head(4) # check the first few rows in tax data
    ```

### Modifying flat file imports ⇒ "Whittle Down"

- Limiting Columns
- Limiting Rows
- Assigning Column Names

### Limiting Columns

- `usecols`

    ```python
    col_names = ['STATEFIPS', 'STATE', 'zipcode', 'agi_stub', 'N1']
    col_nums = [0, 1, 2, 3, 4]

    # Load by name
    tax_data_byname = pd.read_csv('us_tax_data_2016.csv', usecols=col_names)
    # Load by number
    tax_data_bynum= pd.read_csv('us_tax_data_2016.csv', usecols=col_nums )

    # Check Equality
    print(tax_data_byname.equals(tax_data_bynum)) # return: True/False
    ```

    > DataFrame 일치 여부 확인 함수 DataFrame.equals
    - 공식문서: [https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.equals.html](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.equals.html)

### Limiting Rows

- `nroms`
- `skiprows`

    ```python
    tax_data_first1000 = pd.read_csv('us_tax_data_2016.csv', nrows=500, skiprows=1000, header=None)

    ```

    - column name이 없는 경우, header=None

        ```python
        tax_data_first1000 = pd.read_csv('us_tax_data_2016.csv', nrows=500, skiprows=1000, header=None)

        ```

### Assigning Column Names

```python
col_names = list(tax_data_first1000) # 동일하게 사용할 col_names 가져오기
tax_data = pd.read_csv('us_tax_data_2016.csv', nrows=500, skiprows=1000, header=None, names=col_names)

```

### Handling errors and missing data

- **잘못된 data type인 경우**
    - Specifying Data Types

        `Dataframe.dtypes` ⇒ return: dictionary(column names: data type)

        ```python
        tax_data = pd.read_csv('sample.csv', dtype={"zipcode":str})
        print(tax_data.dypes) # string object로 변환됨. e.g. "category"로도 변환 가능
        ```

        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/53ee6a29-0ff3-4c27-b1d3-ce4c98170592/Untitled.png)

- **결측치가 있는 경우**
    - Customizing Missing Data Values

        pandas는 자동적으로 결측치를 N/A 등과 같이 처리합니다. 예를 들어 column의 특징을 고려했을때 0과 같은 값은 있지만 pandas는 해당 데이터를 Null으로 보지 않습니다.  특정 값을 결측치로 고려해주고 싶을 때 `na_values`를 사용합니다.

        ```python
        tax_data = pd.read_csv('sample.csv', na_values={"zipcode":0})
        print(tax_data[tax_data.zipcode.isna()])
        ```

- **pandas로 안 읽어지는 경우(can't parse)**
    - Line with Errors
        - `error_bad_lines=False` in `pd.read_csv`
        - `warn_bad_lines=True` in `pd.read_csv`
        - Q. corrupt이 발생하는 line이 어딘지 알 수 없다는 문제점이 있지 않은지?

## Importing Data From Excel Files

### Spreadsheets

- tabular form으로 데이터 저장
- formatting과 formula 가능(flat file인 DataFrame과의 차이점)

### Loading Spreadsheets

- `pd.read_excel()` ~= `pd.read_csv()`
    - nrows
    - skiprows
    - usecols by name, positional number, letter e.g. "A:P"

    ```python
    import pandas as pd

    survey_data = pd.read_excel("fcc_survey.xlsx")
    print(survey_data.head())

    # option 사용하여 data load
    survey_data = pd.read_excel("fcc_survey.xlsx", skiprows=2, usecols="W:AB, AR")
    print(survey_data.head())
    ```

### Loading Select Columns and Rows

- Issue: cell값이 non tabular한 데이터인 경우가 있다.  e.g. smaller tables of information

### Selecting Sheetes to Load

- `sheet_name` in `pd.read_excel()` (default: first sheet)

    ```python
    # by position index(number)
    pd.read_excel('sample.xlsx', sheet_name=1)

    # by sheet name
    pd.read_excel('sample.xlsx', sheet_name='2017')
    ```

- 모든 sheet 불러오기
    - `sheet_name=None` to `pd.read_excel()`
    - dict.items()와 반복문 활용

        ```python
        for k, v in survey_responses.items():
        	print(k, type(v)) # result: '2016', '2017'
        ```

        ```python
        all_responses = pd.DataFrame()

        for sheet_name, frame in survey_responses.items():
        	print(sheet_name, type(frame )) # result: '2016', '2017'
        	
        	frame['Year'] = sheet_name
        	all_responses = all_responses.append(frame)

        print(all_responses.Year.unique()) # result: '2016', '2017'
        ```

### Modifying imports: True/False

- pandas에서의 Boolean
    - automatically boolean값을 float으로 읽음
    - True=1, False=0 으로 취급
    - **NA/missing values or, Unrecognized values in boolean columns ⇒ True로 변환**
    - bool type으로 dtype을 명시할 수 있습니다.

        ⇒ `DataFrame.isna().sum()`을 확인해보면 결측치도 True로 취급되어 isna().sum()의 결과는 0으로 나옵니다. 

        - custom: `true_values`, `false_values` to `pd.read_excel()`

        ```python
        bool_data = pd.read_excel("sample.xlsx", dtype={"AttendBootcamp":bool}, true_values=['Yes'], false_values=['No'])
        ```

주의할 점은 boolean으로 취급하게 되면, 결측치를 True로 변환하기 때문에 분석할 때 등 이러한 데이터 처리가 영향을 미치는지 확인할 필요가 있습니다.

### Modifying imports: parsing dates

- `parse_dates` to `pd.read_excel()`

    ```python
    date_cols = ['Part1StartTime', 'Part1StartTime', [['Part2StartDate', 'Part2StartTime']]]

    survey_df = pd.read_excel("sample.xlsx", parse_dates=date_cols)
    ```

    ```python
    date_cols = {'Part1Start':'Part1StartTime', 'Part1End':'Part1StartTime', 'Part2Start':['Part2StartDate', 'Part2StartTime']}

    survey_df = pd.read_excel("sample.xlsx", parse_dates=date_cols)
    ```

    - non-standard datetime format에는 적용X
- `pd.to_datetime()`
    - format
        - %Y
        - %m
        - %d
        - %H
        - %M
        - %S

        ```python
        format_string = "%m%d%Y %H:%M:%S"
        survey_df["Par2EndTime"] = pd.to_datetime(survey_df["Part2EndTime"], format=format_string)
        ```