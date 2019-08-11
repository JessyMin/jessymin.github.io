---
layout : post
comments : true
title : "웹크롤링 시 xpath 사용하는 법"
date : 2019-08-11
category : 'python'
---

#### Motivation
기상청 기후 데이터를 분석할 일이 생겼다. 폭염과 일교차가 정말 심해지고 있는지, 최근 몇 년간 얼마나 어떻게 심해지고 있는지를 파악하기 위해서다. 그래서 TimeSeries data를 다루는 방법 및 `DateTimeIndex` 자료형에 대해 공부해보게 되었다.

<br>

#### 데이터셋

- 기상자료개방포털
  - 메인페이지>기후통계분석>기후분석>기온분석 (<a href="https://data.kma.go.kr/stcs/grnd/grndTaList.do?pgmNo=70">링크</a>)
  - 기간과 지역을 설정한 뒤, CSV 파일로 다운받을 수 있다.

<br>

#### 인덱스 설정

**1) 파일을 읽어들일 때 인덱스로 설정하기**
- `read_csv()`할 때 옵션 지정
  - `index_col='date'`
  - `parse_dates=True`

<br>

**2) 데이터프레임의 열을 인덱스로 설정하기**
- 일반적인 방법으로 파일을 읽어들인다.
```python
data = pd.read_csv("seoul.csv", encoding="utf-8", skiprows=8, header=None, names=['date','region','min','mean','max'])
```

- datetime 형으로 변환한 뒤, 인덱스로 설정

``` python
data['date'] = pd.to_datetime(data['date'])
data = data.set_index('date')
```

<br>

#### 추출하기 Subsetting
특정 연/월 등 필요한 데이터만 추출해야 할 때 쉽게 수행할 수 있다. 'January', 'JAN' 등의 변형된 포맷도 사용 가능하다.

```python
data.loc['2017-08'].head()
data.loc['2016-JAN'].head()

data.loc['2017'].count()
```

슬라이싱도 가능하다.
```python
data.loc['2016-07':'2016-08']
```

<br>

#### 리샘플링 Resampling

- `pd.resample()`
  - 리샘플링의 종류에는 upsampling과 downsampling이 있다.
  - 시간 단위로 group by하는 것은 업샘플링에 해당한다.

```python
data.resample('M').max()

output[01]:
                최저기온  평균     최대기온
date			
2010-01-31	5.8	2.4	8.4
2010-02-28	12.2	9.8	17.9
2010-03-31	8.7	5.9	14.2
2010-04-30	16.8	13.3	20.7
```

리샘플링한 뒤의 date 컬럼 값은 구간의 오른쪽 한계값이 표시된다.
- 다운샘플링
  - 더 작은 시간 단위로 쪼개는 것이다.
  - NaN이 발생해 값을 채워야 할 경우가 발생 (보간)
  - backfill, forwardfill, nearest 등

<br>

#### 시각화

습관처럼 seaborn을 사용하면 에러가 발생한다. 인덱스를 x축으로 사용할 수 없어서인 것 같다.

```python
import seaborn as sns
sns.lineplot(x="date", y="max", data=data_2018)

Output[01]: ValueError: Could not interpret input 'date'
```

기본적인 문법을 사용해 오히려 간단하게 plotting할 수 있다.

```python
import matplotlib.pyplot as plt
data_2018 = data.loc['2018']
plt.plot(data_2018)
```
