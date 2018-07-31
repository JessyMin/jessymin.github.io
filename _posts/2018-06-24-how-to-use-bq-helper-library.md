---
layout : post
comments : true
title : "Kaggle Kernel로 BigQuery 사용하는 법"
date : 2018-06-24
category : 'SQL'
---

대용량 데이터를 다뤄보고 싶을 때, Kaggle Kernel을 이용하면 BigQuery를 사용해볼 수 있다. 물론 Google BigQuery 사이트에서 곧바로 이용해도 된다. 하지만 Kernel을 이용하면 데이터를 추출한 뒤 곧바로 시각화해볼 수 있다는 장점이 있다.  

Kaggle은 이와 같은 Filtering - Manipulation - Visualization 작업을 매끄럽게 할 수 있도록 bq_helper라는 python 라이브러리를 제공한다.  

<br>

### bq_helper 라이브러리

#### 1. 개요
* BigQuery 데이터를 쉽게 조회, pandas 데이터프레임으로 변환해서 다룰 수 있도록 Kaggle에서 만든 python 라이브러리

<br>

* Kaggle Kernel 내에서만 사용 가능. back단에서 돌아가는 작업을 Kaggle 시스템이 담당하고 있기 때문.

<br>

#### 2. 사용법
먼저 pandas 라이브러리가 필요하다.
```Python
import pandas
import bq_helper
```

아래와 같이 import해도 된다.
```Python
import pandas
from bq_helper import BigQueryHelper
```

<br>

다음으로, 연결하고자 하는 DB에 접속해서 object를 생성한다.

```python
us_name = bq_helper.BigQueryHelper(active_project="bigquery-public-data",
                                   dataset_name="usa_names")
us_name = BigQueryHelper("bigquery-public-data", "usa_names")
```
이렇게 `us_name`이라는 object를 생성했다. 이제부터는 `bq_helper`의 함수를 이용해 작업할 수 있다.
<br>


### 3. 주요 함수
#### 3-1. 데이터셋 구조 파악하기

#### 테이블 목록
먼저 해당 DB 내에 어떤 테이블들이 있는지 살펴본다.

```Python
us_name.list_tables()
```
이는 MySQL의 아래 쿼리와 동일한 기능을 수행한다.

```sql
SHOW tables;
```

#### 데이터 스키마

테이블 목록을 파악한 뒤에는, 특정 테이블의 컬럼을 살펴볼 수 있다.

```python
us_name.table_schema("usa_1910_current")
```
```sql
-- MySQL
DESC usa_1910_current;
```

<br>


#### 3-2. 데이터 일부를 미리 살펴보기
#### head()

특정 테이블의 처음 n개 행을 살펴볼 수 있다.
이것을 preview라고 표현하는데, 테이블을 scan하지 않기 때문이다.

```Python
us_name.head("usa_1910_current")
```
```sql
SELECT * FROM usa_1910_current
LIMIT 10;
```

BigQueryHelper.head() 메소드는 조회하고자 하는 열의 개수나 특정 컬럼을 지정할 수 있다.

```python
us_name.head("usa_1910_current",
             selected_columns="name",
             num_rows=10)
```

아쉽게도 R과 같이 `head()`와 짝을 이루는 `tail()` 같은 역할의 함수는 없다.


#### 3-3. 쿼리 사이즈 미리 측정하기
Kaggle의 Kernal을 통해 BigQuery를 이용할 때는 정해진 쿼터가 있다.


따라서 bq_helper는 쿼리를 실행하기 전, 해당 쿼리가 데이터셋 스캔에 사용하게 되는 용량을 미리 측정할 수 있다.

```sql
query = """
   SELECT
     year
     , name
     , SUM(number) AS count
   FROM
     `bigquery-public-data.usa_names.usa_1910_current`
   WHERE year > 2015 AND gender = 'F'
   GROUP BY year, name  
   ORDER BY year, SUM(number)  
"""

us_name.estimate_query_size(query)
```

이 때 결과는 0.15303234476596117 이런 식으로 나오는데, 단위는 __GB__ 이다.

<br>

#### 3-4. 쿼리 실행하기
쿼리를 실행하는 방법은 2가지다.
위의 방법으로 쿼리 사이즈를 파악했다면 그냥 실행해도 무방하다.

```python
us_name.query_to_pandas(query)
```
이렇게만 하면 데이터프레임으로 변환된 결과가 출력된다.
물론 object로 저장해 놓고 쓰는 게 좋다.

```python
df = us_name.query_to_pandas(query)
```

안전모드로 실행할 수도 있다.
쿼리 사이즈가 기준치를 넘으면 실행하지 않고 에러 메시지를 띄워준다.

```python
df = us_name.query_to_pandas_safe(query)
```

* 디폴트 값은 __1GB__ 가 기준이다.
* 아래와 같이 별도로 지정해줄 수 있다.

```python
# 100MB로 기준값 변경하기
df = us_name.query_to_pandas_safe(query,
     max_gb_scanned = 0.1)
```

<br>

#### 소소한 L&L
* `Legacy SQL`은 사용 불가한 듯 하다. 확실하게 확인 필요.


<br>

#### 참고자료


* <a href="https://www.kaggle.com/dansbecker/getting-started-with-sql-and-bigquery"> Kaggle Learning 튜토리얼
</a>

* <a href="
https://github.com/SohierDane/BigQuery_Helper/blob/master/bq_helper.py"> bq_helper 소스코드(Github)
</a>

* <a href="https://medium.com/google-cloud/learning-to-analyze-huge-bigquery-datasets-using-python-on-kaggle-2c6c6153f542"> Learning to analyze huge BigQuery datasets using Python on Kaggle
</a> : Kaggle 데이터 사이언티스트인 Megan Risdal이 만든 튜토리얼. 시각화 과정까지 함께 보여준다.
