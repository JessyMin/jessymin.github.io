---
layout : post
comments : true
title : "[공부] Datacamp 머신러닝 - 비지도학습(1)"
date : 2018-12-05
category : 'Machine Learning'
---

DataCamp의 Machine Learning with R - unsupervised learning을 공부한 내용을 정리해본다.



#### Why 왜 하는가?
* 예측 없이 패턴만 찾고 싶을 때
* Clustering / Dimension reduction

<br>

### 1. k-means clustering
* 클러스터의 개수를 미리 정함(k)



### 2. Hierarchical clustering
* 최종 클러스터를 몇 개로 할지 모를 때
* 방법 : Top down vs. bottom up
* Bottom up 방식
  * 각각의 요소를 cluster로 가정하고, 가까운 것끼리 묶어나감 --> iteration
  * 언제 끝나는가? 1개만 남을 때까지.

```R
# Create hierarchical clustering model
hclust.out <- hclust(dist(x))

# Inspect the result
summary(hclust.out)
```
* dist(x) : similarity matrix로 변환

<br>

#### 클러스터 개수 선정하기

* Dendrogram : 계측정 클러스터링 모델을 해석할 때 쓰는 트리 형태의 구조
* cutree( )
 * height(h)나 클러스터 개수(k)를 이용해 나눌 수 있음

```R
cutree(hclust.out, k=3)
cutree(hclust.out, h=7)
```

#### 클러스터 간 거리 측정

* 클러스터 간의 거리를 측정하는 rule은 무엇인가?
* 4개의 similarity 계산 방법

1) Complete linkage : 두 클러스터 내의 모든 관찰값 간의 similarity를 계산한 뒤, **가장 큰 값**을 사용
2) Single linkage : **가장 작은 값** 사용
3) Average linkage : **값들의 평균** 사용
4) Centroid linkage : 각 클러스터의 **centroid 간의 similarity** 사용 (잘 안쓰임)

```R
hclust(x, method = "single")
```

<br>

#### 실질적인 이슈 : 정규화
* 왜 필요한가?
  * feature들의 scale이 다르면 클러스터링이 잘 안된다.
  * distribution이 다르다면 정규화 필요
* 방법
  * 모든 관찰값의 평균을 구함
  * 각 관찰값을 standard deviation으로 나눔
  * 정규화된 값은 평균이 1, sd가 1

```R
#정규화가 필요한지 확인
colMeans(x)
apply(x, 2, sd)

#정규화
scaled_x <- scale(x)
```

<br>

### 3. 두 방법의 비교

#### Balanced tree
* 각 클러스터에 균등하게 배분하고 싶은 경우

#### Unbalanced tree
* 아웃라이어를 검출하고 싶은 경우
* 대부분의 관찰값이 한 클러스터에 할당된 상태에서 나머지 값 절단


#### 비교

```R
# Scale the data
colMeans(pokemon)
apply(pokemon, 2, sd)
pokemon.scaled <- scale(pokemon)

# Create hierarchical clustering model
hclust.pokemon <- hclust(dist(pokemon.scaled), method="complete")

# Apply cutree( )
cut.pokemon <- cutree(hclust.pokemon, k=3)

# Compare methods
table(km.pokemon$cluster, cut.pokemon)

```

* 비지도학습에서 분석가가 할 일
  * 클러스터가 할당되는 양상을 관찰하고
  * 어떤 방법이 더 인사이트를 주는지 결정하는 것
