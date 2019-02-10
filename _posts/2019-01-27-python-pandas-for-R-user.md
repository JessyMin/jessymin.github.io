---
layout : post
comments : true
title : "[요약] R 유저를 위한 Python/Pandas 패키지"
date : 2019-01-27
category : 'python'
---


 R을 쓰는 중이면서 python을 배우려 할 때 딱 맞는 강의를 찾아냈다. DataCamp의 '<a href='https://campus.datacamp.com/courses/python-for-r-users'>Python for R users</a>'라는 코스다. 입문자를 위한 코스들과 달리 좀 더 압축적으로 빠르게 진도를 뺀다. 중간중간 R과 비교해주는 포인트도 좋다.
<br>
강사 특성인지는 모르겠지만 설명도 더 쏙들어오게 해주는 것 같다. 하는 김에 R문법과 비교하며 Pandas의 기초적인 부분을 짚어보았다.  

---


### 1. Subsetting
- python에는 R과 달리 dataframe같은 빌트인된 자료구조가 없음
- pandas를 이용

#### Column 선택하기
  - `.` 또는 `[]` 사용
  - `.` notation이 편리하지만 에러 발생하는 경우 있음
    -  컬럼명에 공백이 포함됐을 때
    - 컬럼명이 데이터프레임의 attribute와 충돌을 일으키는 경우

``` python
# Seaborn 패키지의 tips 데이터셋 로딩 (이하 생략)
import seaborn as sns
tips = sns.load_dataset('tips')

# 컬럼 선택 - python
print(tips.day)
print(tips['time'])
```

특정 범위만 인덱스로 슬라이싱하고 싶다면, 다음과 같이 가능하다.

```python
# Python
# 인덱스 슬라이싱
tips['time'].iloc[0:5]

# 여러 컬럼 선택
tips[['day','time']]
```

```R
# R
# 인덱스 슬라이싱
> tips$time[1:5]

# 여러 컬럼 선택 - basic R
> tips[, c('day','time')]
```

<br>

#### Row 선택하기
- `.loc` : row name 사용
- `.iloc` : row index 사용
- 위의 두 accessor는 column에 액세스할 때도 사용 가능
- python의 인덱싱은 R과 달리 0에서 시작함

```python
# 첫번째 행 출력하기
# python
tips.iloc[0,:]

# R  
> mtcars[1,]
```

- 조건부로 subset하려면 `.loc`에 불리언 표현 사용
  - `.iloc`이나 R과 달리 row 조건만 명기
  - `|`,`&` 사용 시 각 조건을 괄호로 감싸줘야 함

```python  
# python  
tips.loc[tips['smoker']=='No']

tips.loc[(tips.smoker=='No') & (tips.time=='Dinner')]

# R
> tips[tips$smoker=='No',]

> tips[(tips$smoker=='No') & (tips$time=='Dinner'),]
```

<br>

#### Row x Column 선택하기
- row와 column을 모두 subset하는 문법은 R과 유사함
  - df.loc[row_labels, column_labels]
  - df.iloc[row_indices, column_indices]

```python
# python
tips.loc[tips.smoker=='No', ['total_bill','tip']]

tips.iloc[0:3, 0:3]

# R
> tips[tips$smoker=='No', c('total_bill','tip')]

> tips[1:3, 1:3]

```

<br>
### 2. Data type

#### 데이터 타입 파악하기

- `.info()` 메쏘드 또는 `.dtypes` 속성 사용
  - `.dtypes`는 데이터프레임과 특정 컬럼에 둘 다 사용 가능
  - `info()`는 데이터프레임만 가능

```python  
# python
# 데이터프레임 전체
tips.info()

tips.dtypes

# 특정 컬럼
tips['total_bill'].dtypes
```

```R
# R
# 데이터프레임 전체
> str(tips)

# 특정 컬럼
> class(tips$total_bill)
```

#### 데이터 타입 변경하기
- `.astype()` 사용

```python
# python
In [0]: tips['size'].dtypes

Out[0]: dtype('int64')


In [1]: tips['size'] = tips['size'].astype('category')
        tips['size'].dtypes  

Out[1]: CategoricalDtype(categories=[1, 2, 3, 4, 5, 6], ordered=False)
```

```R
# R
> class(mtcars$cyl)
[1] "numeric"

> mtcars$cyl <- as.factor(mtcars$cyl)
> class(mtcars$cyl)
[1] "factor"
```
<br>
- 카테고리 리스트 확인
```python
print(tips['time'].cat.categories)
```

- 카테고리의 순서(R에서의 level) 변경은 아래와 같이 한다.
  - df['column_name'].cat.reorder_categories(['low', 'high'], ordered=True)

- category 데이터 타입은 퍼포먼스 측면에서 장점이 있음  

#### String
- string을 포함한 컬럼은 python에서 `object` 타입으로 표현됨
- 이런 데이터가 많으므로 다루는 법을 잘 익혀둬야 함
- `.str` accessor를 통해 내장 메소드를 이용할 수 있음
  - `.str.upper()`
  - `.str.lower()`

```python
# 대문자로 변환
# python
tips.smoker = tips.smoker.str.upper()

# R
> tips$smoker <- toupper(tips$smoker)
```

<br>

### 3. Missing Value

- pandas에서 결측값은 NaN으로 표현
- 확인에 `isnull()` 함수 사용
  - pd.isnull(df['column'])
  - True/False 반환
- R과의 차이점
  - `mean()` 등의 연산 시 자동으로 결측값 무시  
- 다른 값으로 치환 : `.fillna()` 메쏘드
- NA값 제거 : `.dropna()`

```python
# 결측값이 있는 row 출력하기
tips.loc[pd.isnull(tips['total_bill'])]

# total_bill 컬럼의 평균값 계산
tbill_mean = tips['total_bill'].mean()

# 평균값으로 결측값 치환
print(tips['total_bill'].fillna(tbill_mean))
```

```R
# R
> tips[is.na(tips$total_bill),]

> tbill_mean = mean(tips$total_bill,
                na.rm=T)

> tips[is.na(tips$total_bill),'total_bill'] <- tbill_mean

```

<br>

### 중간 정리/느낀 점
- 나중에 python을 배울 생각이 있다면, R을 처음 배울 때 boolean indexing 부분을 탄탄히 하면 좋을 것 같다.
- 그런 점에서 Coursera-Johns Hopkins Uiv.의 데이터 사이언스 수업으로 R을 시작했던 점이 도움 되었다. 하지만 어느 순간부터 쉽다는 이유로 manipulation에 `dplyr` 패키지를 주로 사용해온 점이 아쉽다.
- 데이터셋이 커지면 퍼포먼스 측면에서 `data.table` 패키지가 유리한 점이 있는 듯 하니, 좀 더 python 문법에 가까운 `data.table`도 가끔 써봐야겠다.
