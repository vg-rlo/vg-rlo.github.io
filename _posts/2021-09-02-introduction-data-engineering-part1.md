---
layout: post
title: Introduction to Data Engineering (1)
---

# Introduction to Data Engineering

## What is data engineering?

데이터 사이언티스트가 어떤 비즈니스 모델을 두고, 해당하는 비즈니스 문제를 해결하기 위해 모델링을 진행할 데이터가 필요합니다. 하지만, 뚝딱! 하고 데이터가 있었으면 좋겠지만 분석하고자 하는 문제에 관련된 데이터들은 흩어져 있거나, 분석하기 적합하게 변형되어 있지 않거나 레거시 코드가 데이터 충돌을 일으키는 등의 문제가 있습니다. 데이터 엔지니어는 이러한 문제들을 해결하고 분석하기 용이한 data를 사용할 수 있게 하는 역할을 수행합니다. (Rescue!)

### Problems

- Data is scattered
- Not optimized for analyses
- *Legacy code* is causeing corrupt data

    > Legacy code?
    테스트가 불가능하거나, 기능이 정상적이지 않거나, 해독이 어려운 코드를 의미합니다.
    - 출처: [https://drehzr.tistory.com/56](https://drehzr.tistory.com/56)

### Expected

- Gather data from different sources
    - Extract data from different sources and load it into one single db
- Optimized database **for analyses, not for an application**
    - Optimize *scheme*, Faster, etc.

    > Scheme? DB의 구조나 제약조건

- Removed corrput data

### Definition of Data Engineering

> An engineer that develops, constructs, tests, and maintains architectures such as databases and large-scal processing systems
- 출처: Datacamp 2015

- Processing large amounts of data
- Use of clusters of machines

### Data Engineer vs Data Scientist

- Data Engineer
    - Develop scalable data architecture
    - Streamline data acquisition
    - Set up processes to bring together data
        - Set up scheduled ingestion of data from the application databases to an analytical database.
    - Clean corrupt data
    - Well versed in cloud technology e.g. AWS, Azure, Google Cloud(GCP)
- Data Scientist
    - Mining data for patterns
    - Statistical modeling
        - Apply a statistical model to a large dataset to find outliers.
    - Predictive models using ML
    - Monitor business processes
    - Clean outliers in data

## Tools data engineers use

### Database

- Hold large amounts of data
- *SQL, NoSQL*

> SQL vs NoSQL?

- Support application
- Other DBs are used for analyses

### Processing

하나의 computer로 진행하기 보다, cluster computing framework를 사용하여 진행합니다.

- Clean data
- Aggregate data
- Join data
- Example: using clusters of virtual machines e.g. PySpark framework

    ```python
    df = spark.read.parquet("users.parqut")
    outliers = df.filter(df["age"] > 100)
    print(outliers.count())
    ```

⇒ `abstraction` = 추상화

> 설계를 단순화하는 추상화
하드웨어와 소프트웨어의 생산성을 높이는 핵심 기술 중 하나는 여러 수준에서 설계를 명시하는 추상화(Abstraction)를 사용하는 것이다. 하위 수준의 상세한 사항을 안 보이게 함으로써 상위 수준 모델을 단순화한다.
- 출처: [https://minhyeok-rithm.tistory.com/entry/2021-04-04-ComputerArchitecture](https://minhyeok-rithm.tistory.com/entry/2021-04-04-ComputerArchitecture)

### Scheduling

- Plan jobs w/ specific intervals
- Resolve dependency requirements of jobs

### Existing tools

Processing Tool은 회사마다 다르며, 자체 툴을 개발하여 사용하는 회사들도 있습니다.

- Databases: MySQL, PostgreSQL
- Processing: Spark, Hive
- Scheduling: Airflow, Oozie, cron(Unix tool)

> Cron? 가장 simple한 형태의 scheduler

### Data pipeline

SQL, NoSQL과 같은 Database 외에도 외부 APIs나 다른 format의 파일들도 sources가 될 수 있습니다.

![image](https://user-images.githubusercontent.com/69677950/131829098-82d97996-a02b-4296-8376-8233cfcb2529.png)

## Cloud providers

### Data processing in the cloud

Why we need Clustres of machines required? 

- Cost optimization! ⇒ **필요한 때에 맞게 필요한 만큼의 자원까지만 사용합니다.**
    - Problem: self-host data-center
        - Cover eletrical and maintaenance costs
        - Hard to optimize between Peak/Quiet moments

### Data storage in the cloud

Why we need Clustres of machines required? 

- Reliability!
    - Problem: self-host data-center
        - 천재지변
        - Need different geographical locations

### Cloud service providers: AWS, Azure and Google

- Companies offer: Storage, Computation, Databases
- Market share in 2021

    ![https://p2zk82o7hr3yb6ge7gzxx4ki-wpengine.netdna-ssl.com/wp-content/uploads/q12021-canalys-980x551.jpeg](https://p2zk82o7hr3yb6ge7gzxx4ki-wpengine.netdna-ssl.com/wp-content/uploads/q12021-canalys-980x551.jpeg)

### Storage

온라인 마켓을 예로 들면 Cloud storage service를 통해 판매 제품의 이미지를 저장할 수 있습니다.

- Services
    - AWS S3
    - Azure Blob Storage
    - Google Cloud Storage

### Computation

웹 서버를 호스팅하는 것처럼 Computation service를 제공하고 virtual machine을 필요에 따라 키고 끄고 할 수 있습니다.

- Services
    - AWS EC2
    - Azure Virtual Machines
    - Google Compute Engine

### Databases

- Services
    - AWS RDS
    - Azure SQL Database
    - Google Cloud SQL
