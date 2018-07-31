---
layout : post
comments : true
title : "SQL 독학할 때 실습하는 방법"
date : 2018-05-27
category : 'SQL'
---

실무에서 SQL을 쓰게 되면 개구리 올챙이 시절 생각 못하듯 싹 잊어버릴 고민이 있다. 어떻게 SQL을 실습할 것인가, 직접 쿼리를 돌려볼 것인가 하는 문제다.

작년 이맘때 내가 SQL 독학을 시작할 때만 하더라도 이 부분이 많이 답답했다. 하지만 공부를 하면서 여러 가지 방법이 있음을 알게 되었다.

개인적으로 1 -> 3 -> 2 -> 4의 순서로 경험해가는 중이지만, SQL에 처음 입문하는 경우를 고려해 난이도(?) 순으로 바꿔 보았다.


<br>

### 1. 인터랙티브 강의

입문용으로 익히 알려진 Codecademy, DataCamp 등은 인터랙티브 강의 플랫폼을 제공한다. 각 코스에는 실습용 데이터셋이 업로드되어 있으므로, 이런저런 쿼리를 실습해볼 수 있다.


#### Codecademy

- <a href="https://www.codecademy.com/learn/learn-sql"> Learn SQL </a>
- <a href="https://www.codecademy.com/learn/sql-table-transformation"> Table Transformation </a>
- <a href="https://www.codecademy.com/learn/sql-analyzing-business-metrics"> Analyzing Business Metrics </a>


#### DataCamp
- <a href="https://www.datacamp.com/courses/intro-to-sql-for-data-science"> Intro to SQL for Data Science </a> (무료)
- <a href="https://www.datacamp.com/courses/joining-data-in-postgresql"> Joining Data in PostgreSQL </a> (유료)
- <a href="https://www.datacamp.com/courses/introduction-to-relational-databases-in-python">Introduction to Databases in Python </a> (유료)

2017년 이후로 SQL 코스는 계속 추가되는 추세다. 위의 목록은 계속 업데이트될 듯하다.


#### W3School
여기도 간단한 실습용 데이터셋이 탑재된 에디터가 있다.
<a href="https://www.w3schools.com/sql/trysql.asp?filename=trysql_select_all">W3School.com : Try-SQL Editor</a>


<br>

### 2. HackerRank

Practice에서 문제풀이를 하면서 초급을 벗어날 수 있다.

<a href="https://www.hackerrank.com/domains/sql"> HackerRank : SQL Practice </a>

제공하는 데이터베이스는 MySQL, Oracle, MsSQL Server 등이다.
MySQL은 아마도 5.x 버전인 듯하며, `WITH`문과 윈도함수를 사용할 수 없다.

<br>

### 3. 분석툴 서비스 이용 : Mode Analytics

구글링하다가 우연히 발견한 데이터 분석 툴이다. 이 회사가 제공하는 서비스인 Mode Studio에 자기 데이터베이스를 연결해서 각종 분석/시각화를 손쉽게 할 수 있게 해준다.

마케팅의 일환으로 홈페이지에 SQL 튜토리얼을 제공하고 있으며, 여기 사용되는 실습용 데이터셋을 Mode Studio에서 이용할 수 있다. 데이터나 트레이닝 과제가 다른 교육 코스에 비해 좀 더 실제 상황에 가까운 것이 특징이다.

<a href="https://community.modeanalytics.com/sql/"> Mode Analytics : SQL Tutorial </a>

<br>

### 4. 로컬에 MySQL + Workbench 설치

내 경우 처음에는 생활코딩 수업을 들으면서 Anaconda를 이용해 Apach/MySQL/PHP를 한꺼번에 설치했기 때문에, MySQL이 5.x으로 셋팅되었다. 하지만 `WITH`문과 `Window Function`을 써보기 위해 8.x 버전을 추가로 설치했다.

이렇게 내 PC에 서버를 설치해 실습하는 경우, 분석할 데이터가 필요하다. 이를 위해 한동안 구글링과 Kaggle 사이트를 샅샅이 뒤졌으나, 입맛에 맞는 데이터셋을 찾기 어려웠다.

지금까지 본 데이터셋 중 가장 RDB의 특징을 잘 갖춘 것은 식료품 이커머스 서비스인 Instacart의 데이터셋이다. 5개의 테이블로 구성되어 있어, `JOIN`을 실컷 해보기 좋다.

<a href="https://www.kaggle.com/c/instacart-market-basket-analysis/data"> Kaggle : Instacart Market Basket Analysis </a>


<br>


### 5. Rstudio에서 sqldf 패키지 이용

R을 어느 정도 다룰 줄 아는데 SQL은 입문인 상태라면, Rstudio에서 간단히 실습할 수 있다. Workspace에 올려져 있는 데이터프레임들을 테이블로 다룰 수 있다.

```r
> library(sqldf)
> sqldf(
"
  SELECT *
  FROM book b
  JOIN author a
    ON b.author_id = a.author_id
  WHERE year >= 2000
")
```


물론 이건 좀 편법이다. 제대로 하려면 RMySQL 패키지를 이용해 진짜 SQL서버에 연결해야 한다. 하지만 해보니 잘 해결되지 않는 오류때문에 쉽지 않았다.

서버를 연결하지 않고 그냥 sqldf 패키지만 이용하면, 디폴트는 SQLite 문법을 사용하게 되어 있다. 

<br>


### 6. Kaggle의 Kernel 이용 : Bigquery

SQL 독학이 슬슬 초급을 벗어나면서 드는 고민은 '대용량 데이터를 다뤄보고 싶다'는 것이었다. 하지만 적당한 데이터셋을 구하기도 쉽지 않거니와, 위에 언급한 Instacart 데이터를 로컬 서버에 올릴 때도 시간이 꽤 걸렸었다.

최근에 해결 방법을 찾았다. Kaggle의 Kernel을 이용하는 것이다. Learn이라는 메뉴에 있는 SQL 코스는 Kaggle의 데이터셋을 Kernel에서 분석하는 방법을 안내하고 있다.

<a href="https://www.kaggle.com/dansbecker/getting-started-with-sql-and-bigquery"> Getting Started with SQL and BigQuery </a>

요약하면 Kaggle Kernel의 python 노트북 환경에서, Kaggle측이 만든 bq_helper라는 라이브러리를 이용해 BigQuery로 핸들링하는 것이다. 이렇게 하면 내 노트북만으로도 수백 GB 규모의 데이터셋을 만져볼 수 있다. (아직 해보진 않았지만 이론상 가능하다)

다만 Kaggle Kernel은 데이터를 스캔할 수 있는 한도가 정해져 있다. 매월 5TB까지 스캔할 수 있으며, 용량이 다 차면 다음 달에 리셋되기를 기다려야 한다.


<br>

4번은 아직 해보지 않았기 때문에, 조만간 경험해보고 다시 글을 올리게 될 듯하다.
