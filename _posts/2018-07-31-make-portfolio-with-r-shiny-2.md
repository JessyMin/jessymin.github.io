---
layout : post
comments : true
title : "R Shiny로 포트폴리오 삽질기 (2)"
date : 2018-07-31
category : 'R'
---

지난 글에 이어서 R Shiny로 웹 애플리케이션을 만들면서 겪고 있는 문제-해결책을, 삽질의 기록을 남겨본다.

<br>

### 1. 데이터 load하기
Shiny 튜토리얼이나 예제들에 내가 원하는 코드가 없어서 오래 걸렸다.
하는 김에 Shiny에 데이터를 load하는 3가지 방법을 정리해보면 다음과 같다.

#### 1) R package에 포함된 데이터 분석

Shiny 예제에 가장 많이 등장하는 방법이다. 주로 아래 데이터셋이 많이 사용된다.

* 기본 탑재된 `datasets` 패키지의 `iris`, `mtcars`, `airqaulity`
* `ggplot2` 패키지의  `diamonds`

Server.R이나 UI.R에서 해당 패키지를 import해서 사용하면 된다.

~~~r
library(shiny)
library(ggplot2)

shinyApp(
  ui = fluidPage(
    sidebarLayout(
      sidebarPanel(
        #Select x-axis
        selectInput(
          "x_var", h4("Select x variable :"),
          choices = colnames(mtcars),
          selected = "cyl"
        ),
        #Select y-axis
        selectInput(
            "y_var", h4("select y variable :"),
            choices = colnames(mtcars),
            selected = "mpg"
        )
      ),
      mainPanel(
          plotOutput("plot")
      )
    )
  ),
  server = function(input, output) {
    output$plot <- renderPlot({
      p <- ggplot(mtcars, aes_string(input$x_var, input$y_var)) +
        geom_point()
      print(p)
    })
  }
)
~~~
주의
* `aes(x, y)`가 아닌 `aes_string(x, y)` 사용
* `print` 해줘야 함

<br>

#### 2) 웹서버에 있는 데이터셋 불러오기
AWS 등에 공개된 데이터셋을 읽어들여 사용하는 방법이 Shiny 튜토리얼에서도 사용된다.

```r

load(url("http://s3.amazonaws.com/assets.datacamp.com/production/course_4850/datasets/movies.Rdata"))

```

<br>

#### 3) 로컬 파일 읽어들이기

프로젝트 폴더 밑에 `/data`라는 하위 폴더에 데이터 파일을 저장해야 한다.

```r
data <- read.csv("./data/sample.csv")

```
* 대소문자 무관하다.
* 폴더명을 임의로 정하면 아래와 같은 에러가 발생한다.

```r
data <- read.csv("./datafolder/sample.csv")

> Error in file(file, "rt") : 커넥션을 열 수 없습니다
```

<br>


__update:__
server.R/ui.R과 같은 디렉토리에 위치하는 것도 무방하다.

```r
data <- read.csv("./sample.csv")
```
* shinyapp.io에 배포할 때는 이렇게 해야만 에러가 발생하지 않았다.
* 그 외의 시도에서는 'no such file or directory' 에러 발생.

<br>

Shiny 튜토리얼에 해당 내용을 다룬 챕터가 있다. 아래 내용은 날잡아서 자세히 살펴보면 좋을 듯 하다.

* 아래 글의 'loading files and file paths'라는 챕터 참고
https://shiny.rstudio.com/tutorial/written-tutorial/lesson5/
    * the directory that you save server.R in will become the working directory of your Shiny app.

<br>

### 2. Reactive 테이블 만들기

Reactive 객체는 따로 다뤄야 할 만큼 중요한 내용이지만, 가장 헤맸던 거 하나만 기록으로 남긴다.

우선 __Reactive는 사용자의 인풋값이 변하면 이를 아웃풋에 반영하는 반영한 아웃풋을 반환하는 것__ 을 말한다. 같은 작업이 반복된다면 Reactive object를 만들어 재사용하게 된다.

내가 하려는 작업은 __'input한 조건에 맞는 rows만 필터링해 테이블로 반환하기'__ 였다. 구글링을 해보면 column 필터링에 대한 내용은 많은 반면, row 필터링한 케이스가 많지 않았다. 그러나 핵심은 아래와 같다.

* 수행하려는 작업을 reactive 함수 안에 넣어서 객체에 할당
  * selectedData <- reactive ({ })
* reactive 객체를 사용할 때는 ( )를 붙일 것
  * selectedData( )

<br>

```r  
library(shiny)
library(DT)
data <- read.csv("./data/sample.csv")

# A basic shiny app with a reactive table
shinyApp(
  ui = fluidPage(
    sidebarLayout(
      sidebarPanel(
        # Select birth year
        selectInput(
          "year", h4("Select birth year :"),
          choices = c(1980:2000),
          selected = 1981
        )
      ),
      mainPanel(
        DT::dataTableOutput("table1")
      )
    )
  ),
  server = function(input, output) {
    # make reactive object(filter with year)
    selectedData <- reactive({
      req(input$year)
      data <- subset(data, year == input$year)
    })
    # use reactive object
    output$table1 <- renderDT({
      DT::datatable(selectedData(), rownames=FALSE)
    })
  }
)

```


요즘 회사에서 점심시간에 틈틈이 DataCamp에서 Shiny 코스를 수강하고 있다. 4챕터짜리 미니 코스인데, 멤버십을 결제해둔 덕에 전체 컨텐츠를 이용할 수가 있다. 이 코스에서는 한 챕터를 통째로 Reactive에 할당하고 있는데, 이걸 진작 들었으면 초반의 삽질을 훨씬 줄일 수 있었을 것 같다.
