---
layout : post
comments : true
title : "python 시각화: Seaborn 패키지(1)"
date : 2019-01-13
category : 'python'
---

python을 공부하는 초반이라, 탐색적 분석에 필요한 시각화 방법들을 정리해보려 한다. seaborn은 EDA를 효율적으로 할 수 있다는 점에서 무척 매력적인 패키지다. 그래서 처음으로 공식 문서를 샅샅이 읽어보며 정리해본다.

---

참조 : https://seaborn.pydata.org/introduction.html

#### 왜 만들어졌나?
- 데이터를 탐색하고 이해하는 과정에서 visualization이 핵심 역할을 하게끔 하려고
<br>

#### 특징

- 여러 변수 간의 관계를 탐색하는 데에 좋다.
- 특히 범주형 변수를 이용해 분포를 살펴보거나 집계하는 작업을 잘 지원한다.  
- 데이터의 subset 간에 단일 변수 또는 2개 변수의 분포를 서로 비교하기 쉽다.
- 빌트인된 테마를 이용해 matplotlib의 스타일링 요소들을 간단히 컨트롤할 수 있다.
- 분석하고자 하는 내용에 따라 크게 `relplot()`(relation), `catplot()`(category)를 제공한다.  
<br>

#### 1-1. 연속형 변수 x 연속형 변수

seaborn은 변수 간의 관계를 분석하기 위한 함수들을 `relplot()`으로 묶어서 제공하고 있다.

**1) Scatter plot**

- `relplot()` : 기본적인 scatter plot을 그려줌
```python
# 패키지 Importing은 이하 생략
import seaborn as sns
tips = sns.load_dataset('tips')

sns.relplot(x='total_bill', y='tip', data=tips)
```

- 그룹 간 비교
  - 데이터의 subset별로 선형회귀를 적용한다. (by applying a groupby operation)
  - `hue` argument를 통해 범주형 변수를 지정
  - `pallette` argument를 통해 그룹별 컬러링할 팔레트 설정 (pal)
```python
sns.relplot('total_bill', 'tip', tips, hue='smoker)
```

- subplot들을 Grid로 그려서 비교
  - `row` : n개의 row로 배치
  - `col` : n개의 column으로 배치
  - 둘 다 적용하면 n x n grid가 됨
```python
sns.relplot('total_bill', 'tip', tips,
    col='sex', row='smoker')
```

**2) Linear regression**
  - `lmplot()` : scatter plot + 회귀선
  - 회귀선만 그리고 싶다면 `scatter=None`으로 설정한다.
```python
sns.lmplot(x='total_bill', y='tip', data=tips)
```

<br>


**3) Line plot**
- 시간축에 따른 변화의 추이를 살펴볼 때 사용
- `lineplot()`
- 또는 `relplot()`에서 `kind='line'`로 셋팅

```python
fmri = sns.load_dataset("fmri")

sns.relplot(x='timepoint', y='signal', kind='line', data=fmri)
```

- 신뢰구간(confidence interval)을 자동으로 표시
  - 데이터셋이 커서 계산 시간이 길어지면 `ci=None`으로 비활성화.
- 범주형 변수를 3번째 축으료 이용해 subset별로 살펴보려면 마찬가지로 `hue`와 `style` 사용
```python
sns.relplot(x='timepoint', y='signal', kind='line', data=fmri, hue='event', style='event', ci=None)
```

<br>

#### 2. 범주형 변수 x 연속형 변수

한 변수가 범주형이고 category별로 subset 간의 경향을 비교하고자 할 때 `catplot()`을 사용한다. R에 비해 굉장히 효율적이라고 느껴지는 부분이다. 세부적인 설정도 default로 셋팅해둬 작업이 많이 자동화된다.


**1) catplot()**
- jitter가 자동으로 적용된다.
  - 비활성화 : `jitter=False`
```python
# 요일별 결제액 분포
sns.catplot(x='day', y='total_bill', data=tips)
```


**2) swarmplot()**  
- observation들이 서로 전혀 overlapping되지 않도록 알고리즘을 적용하는 `swarmplot()`도 있다.
  - 모든 값이 겹치지 않고 펼쳐지므로 3번째 변수를 적용해 분석하기 좋다.
```python
sns.catplot(x='day', y='total_bill', hue='smoke', kind='swarm', data=tips)
```

**3) violinplot()**
- swarmplot에 `hue`를 적용한 경우, 두 그룹이 섞여서 표현된다. violinplot은 이 둘을 분리해서 표시한다.
```python
sns.catplot('day','total_bill', hue='time', kind='violin', data=tips)
```

**catplot( ) vs. violinplot( )**
- 아래 코드로 그리면 범례의 스타일 및 위치, x축의 높이 등 전반적인 스타일의 가독성과 시각적 완성도가 떨어진다.
```python
sns.violinplot('day','total_bill', hue='time',data=tips)
```
- 테스트해보니 `swarmplot()` 등도 마찬가지
- 가급적 `catplot()`의 `kind` argument를 사용하는 게 좋을 듯하다.
