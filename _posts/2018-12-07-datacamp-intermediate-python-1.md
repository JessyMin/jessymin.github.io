---
layout : post
comments : true
title : "[데캠] python 기초 (1) : Data structure"
date : 2018-12-07
category : 'python'
---

연말을 맞아 그동안 미뤄뒀던 python을 바짝 해보기로 했다. DataCamp의 Intermediate python을 공부한 내용을 정리해본다.

Intermediate R과는 달리 초급 내용이 섞여있어서 전에 Codecademy에서 훑었던 내용과 겹치긴 한다. 하지만 R을 해보니, 기본이 튼튼하지 않으면 나중에 뭘 모를 때 검색하기도 애매했던 것 같다. 어쨌든 기초수업은 마지막이라 생각하고 꼼꼼히 들어보려 한다.

DataCamp의 파이썬 기초강의 목차는 다음과 같다. (순서가 좀 이상하다. 뜬금없이 시각화는 왜 나오다 말았을까...)


**Intro to python for data science**

- Variable & Types
- List
- Functions & Packages
- Numpy

**Intermediate python**
- Matplotlib
- Dictionary
- Pandas Dataframe
- Logic, Control flow & Filtering
- Loops

이 중에서 우선 자료구조에 관련된 내용만 따로 모아본다.   
<br>

----

### 1. List
- 특징
  - Name a collection of values
  - Contain any type
  - Contain different types

- Subset하기
  - zero-based indexing
  - start : inclusive
  - end : exclusive

- 추가 & 삭제
```python
# 추가
list = list + ['n1', 'n2']

# 삭제
del(list[-1])

```

<br>

### 2. Numpy Array

- vector arithmetic을 위해 만들어짐
- 그로 인한 단점은,
    - 서로 다른 데이터 타입을 담을 수 없다. 억지로 만들면 type coercion됨.
    - 산술 연산자(+, -, /, \*)의 용법이 다르다.
```python
    In [1]: np.array([True, 2, 3])
    Out[1]: array([1, 2, 3])
```
- numpy
```python
    import numpy as np
    weight = [50, 60, 70]
    np_weight = np.array(weight)
    type(weight)
    type(np_weight)
```


#### 2D numpy array

- python list of lists —> nD array로 변환 가능.
- single type : coerce됨.

```python
conversion = np.array([[0.0254], [0.453592], [1]])
# 이건 곱해지지 않음.
ValueError: operands could not be broadcast together with shapes (1015,3) (3,1)

conversion = np.array([0.0254, 0.453592, 1])
```

<br>

### 3. Dictionary

- 정의 : Set of key-value pairs
- 필요한 이유
    - 두 리스트 안의 pair 값을 index를 사용하지 않고 연결하기 위해 사용
    - 편리하고 / 직관적으로 access할 수 있음

```python
#index를 사용하는 방법
ind_ger = countries.index('germany')
print(capitals[ind_ger])
```
- 만드는 방법
    - { } 사용, Key : Value 매칭
    - dict_name = { key1 : value1, key2 : value2, key3 : value3, ... }
- key의 조건
    - unique
    - 불변객체(immutable object)여야 함. list 등 사용 불가.

```python
#dictionary 생성
europe = {'spain':'madrid', 'france':'paris', 'germany':'berlin'}

# 탐색
print(europe.keys())
print(europe.values())

# 검색
print(europe['italy'])
print('italy' in europe)

# key가 있는지 여부 확인
In [1]: "germany" in europe
Out[1]: True
```

- Update / Remove
```python
# 추가
dict_name["key_n"] = value_n
europe["sealand"] = 0.0027

# 변경
europe["germany"] = "berlin"

# 삭제
del(europe["sealand"])
```

- 활용
    - unique key를 가진 lookup table이 필요할 때
- 기타 특징
    - 정의할 때 순서대로 출력되지 않음 (inherently unordered)
    - dictionary 내의 value로 dictionary 포함 가능 (list와 동일)

```python
# Dictionary of dictionaries
europe = { 'spain': { 'capital':'madrid', 'population':46.77 },
           'france': { 'capital':'paris', 'population':66.03 },
           'germany': { 'capital':'berlin', 'population':80.62 },
           'norway': { 'capital':'oslo', 'population':5.084 } }

# 셀렉트
europe['spain']['population']
```

<br>

### 4. Pandas Dataframe

- tabular structure
    - row = observation
    - column  = variable

#### python 자료구조 비교
- 2D Numpy array?
  - 한 가지 data type만 사용 가능
- pandas dataframe
  - high level data manipulation tool
  - Built on Numpy
  - high performance / easy to use


#### Dataframe 생성하기
- dictionary에서 변환 : `pd.DataFrame()`
```python
import pandas as pd
cars = pd.DataFrame(my_dict)

# row label 추가
row_labels = ['US', 'AUS', 'JAP', 'IN', 'RU', 'MOR', 'EG']
cars.index = row_labels
```

- csv를 읽어들여 변환
```python
import pandas as pd
cars = pd.read_csv("path/cars.csv")

# 첫 번째 컬럼을 row label로 사용
cars = pd.read_csv('cars.csv', index_col = 0)
```

#### Index data
- Column Access
  - 싱글 [ ] : Series 오브젝트로 반환
  - 더블 [[ ]] : dataframe으로 반환

- Row Access
  - `loc` : label-based
  - `iloc` : integer position-based

```python
In [1]: brics.loc["RU"]
Out[1]: --> Row as Pandas Series

In [2]: brics.loc[["RU", "IN"]]
Out[2]: --> Pandas DataFrame

# Intersection
In [3]: brics.loc[["RU", "IN"],["country","capital"]]

# iloc
In [4]: brics.iloc[[1,2,3]]
In [5]: brics.iloc[[1,2,3],[1,2]]
```
