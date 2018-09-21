---
layout : post
comments : true
title : "[번역/요약] 검색 로그 분석 : 웹UX 리서치에서 가장 과소평가된 기회영역"
date : 2018-08-26
category : 'Research'
---


* 대전제 : making more accurate predictions

### 3. Logistic Regression

#### Automatic Feature selection

* Stepwise Regression
  * 로지스틱 회귀는 사전에 예측변수를 정해야 함
  * 종류
    * Forward Stepwise : 변수를 지워나감
    * Backward Stepwise : 변수를 추가해나감
  * 몇몇 단점으로 인해 자주 쓰이진 않음
  * 단점
    * 최적의 모델을 찾아낸다고 장담할 수 없음
    * 이 절차는 통계적인 가정을 위배함
    * 현실 세계에 대해 잘 설명하지 못하는 모델을 만들어낼 수 있음
  * 결론 : 문제점은 있지만 어디서 시작해야 할 지 모를때 유용함


```r
null_model <- glm(donated ~ 1, data = donors, family = "binomial")

full_model <- glm(donated ~ veteran + bad_address + has_children + wealth_rating + interest_veterans + interest_religion + pet_owner + catalog_shopper + recency + frequency + money + missing_age + imputed_age, data = donors, family = "binomial")

step_model <- step(null_model, scope = list(lower = null_model, upper = full_model), direction = "forward")

# Estimate the probability
step_prob <- predict(step_model, type='response')

# Plot the ROC
library(pROC)
ROC <- roc(donors$donated, step_prob)
plot(ROC, col = "red")
auc(ROC)

```

<br>

### 4. Classification Trees

* 통계가 필요 없기 때문에 비즈니스 전략에 유용하게 쓰임
  * 특히 투명성이 중요한 분야. ex) 대출 조건 심사/승인
* root node -> decision nodes
* leaf node = final decision
* R 패키지
  * `rpart( )`


``` r
library(rpart)

loan_model <- rpart(outcome ~ loan_amount + credit_score, data = loans, method = "class", control = rpart.control(cp = 0))

predict(loan_model, good_credit, type = "class")
predict(loan_model, bad_credit, type = "class")

#visualizing classification trees
library(rpart.plot)
rpart.plot(loan_model)

```

#### 4-1) Growing larger classification Trees

* Choosing where to split
  * Devide-and-conquer
    * The group it can split to create the greatest improvement in subgroup homogeneity.
    * Divide-and-conquer always looks to create the split resulting in the greatest improvement to purity.
  * 늘 Axis parallel split을 만든다.
  * 쓸데없이 복잡해질 수 있음
  * noise를 모델링하느라 overfit 될 위험성
* Tree performance 측정하기
  * training set / test set으로 분리
  * 랜덤 샘플 형성 : `sample( )`

```r
nrow(loans)
nrow(loans)*0.75

sample_rows <- sample(11312, 8484)

loans_train <- loans[sample_rows,]
loans_test <- loans[-sample_rows,]

loan_model <- rpart(outcome ~ ., data = loans_train, method = "class", control = rpart.control(cp = 0))

loans_test$pred <- predict(loan_model, loans_test, type="class")

# Examine the confusion matrix
table(loans_test$pred, loans_test$outcome)

# Compute the accuracy
mean(loans_test$outcome == loans_test$pred)
```
