---
layout : post
title : "R에서 파일 읽는 방법 비교"
date : 2018-11-18
category : 'R'
---

R에서 CSV 파일을 읽는 방법은 크게 3가지가 있다. 어떤 상황에 어떤 함수를 써야 효율적인지 궁금해서 간단히 알아보았다. 
<br>
- `read.csv( )` : base R 함수
- `read_csv( )` : readr 패키지, tidyverse 패키지의 일부
- `fread( )` : data.table 패키지

<br>

#### 테스트 데이터
instacart 데이터셋의 orders.csv 파일을 이용했다.
 - 크기 : 109MB
 - 3421083 rows x 7 colums
 - 이 중 1개 컬럼만 text, 나머지는 numeric



#### 1. base R : read.csv( )

``` R
# read.csv()
> system.time(d <- read.csv("orders.csv"))
   user  system elapsed
 21.808   0.958  23.238

> class(d)
[1] "data.frame"
```
- 전체를 `character`로 간주해 메모리에 읽어들인 뒤, `integer`나 `numeric`으로 coerce한다.
- 따라서 `colClasses`, `nrows` 등을 셋팅하지 않으면 느리다.


<br>

#### 2. readr 패키지 : read_csv( )
```R
# read_csv()
> library(readr)
> system.time(d <- read_csv("orders.csv"))
Parsed with column specification:
cols(
  order_id = col_integer(),
  user_id = col_integer(),
  eval_set = col_character(),
  order_number = col_integer(),
  order_dow = col_integer(),
  order_hour_of_day = col_character(),
  days_since_prior_order = col_double()
)
|===============================|100% 103 MB
   user  system elapsed
  3.934   0.693   5.214

> class(d)
[1] "tbl_df"     "tbl"        "data.frame"
```
- `tidyverse` 패키지의 일부
  - `tbl_df`, `tbl`로 읽어들임
- base R보다 빠르다. (up to x10)
- fread( )보다는 느리다. (약 ~1.2-2x)
- 최초 n행을 읽어들여 column type을 결정한다.
  - 따라서, 컬럼 타입을 지정하는 게 나을 때도 있다.
  - [경험] 특정 컬럼값의 앞쪽 절반이 'F'(여성)인 경우, `logical`로 판별해 뒷쪽 'M'에서 모조리 에러가 나는 경우가 있었다.
- 참고 : https://readr.tidyverse.org/


```R  
df <- read_csv("orders.csv"), col_types =
  cols(
    user_id = col_integer(),
    eval_set = 'c',
    order_number = 'i',
    order_dow = 'i',
    order_hour_of_day = 'i',
    days_since_prior_order = 'n'
  )
)
```
이렇게 디폴트 값을 지정해주면 override한다.

<br>

#### 3. data.table 패키지 : fread( )
```R  
> library(data.table)
> system.time(d <- fread("orders.csv"))
   user  system elapsed
  1.007   0.366   0.738
> class(d)
[1] "data.table" "data.frame"
```

- 속도가 가장 빠르다.
  - data.table은 속도를 최우선으로 설계됨
  - 파일을 메모리에 맵핑한 뒤, 포인터를 이용해 iterate함
  - `verbose=TRUE` : 상세 프로세스/소요시간 보여줌
- `data.table`로 읽어들인다.
  - 따라서 특정 column을 key값으로 설정하거나 index를 걸 수 있다.(`setkey()`/`setindex()`)
  - 즉, 큰 데이터를 탐색/연산/병합할 때 유리

<br>


#### quick하게 공부해 본 결론
- 데이터가 크고 join, subset 등의 연산을 해야 한다면
   --> `data.table`패키지의 `fread()`

- 데이터를 tidy하게 만들어야 한다면
  --> `readr` 패키지의 `read_csv()`

- 그 외 vector parsing, column specification 등에서의 차이점을 상세 비교 필요.
