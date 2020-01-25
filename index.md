R을 활용하여 웹데이터 수집하기
================

웹에서 데이터 가져오기
----------------------

텍스트 데이터 분석에 앞서 데이터 수집의 단계를 거치게 됩니다.

오늘은 다음 영화 리뷰 사이트에서 최근 개봉한 [남산의 부장들](https://movie.daum.net/moviedb/main?movieId=122091)의 영화 **리뷰**를 수집하여 분석하도록 하겠습니다.

우선, 사용할 패키지를 설치하고 호출합니다. 'httr','rvest', 'KoNLP','stringr','tm','qgraph','xml2' 패키지가 필요합니다

``` r
#install.packages(c('httr','rvest','KoNLP','stringr','tm','sna','xml2'))

library(rvest);library(httr);library(KoNLP);library(stringr); library(tm);library(sna);library(xml2)
```

    ## Loading required package: xml2

    ## Fail to install scala-library-2.11.8.jar. Recommand to install library manually in C:/Users/Park Jung/Documents/R/win-library/3.5/KoNLP/java

    ## Checking user defined dictionary!

    ## Loading required package: NLP

    ## 
    ## Attaching package: 'NLP'

    ## The following object is masked from 'package:httr':
    ## 
    ##     content

    ## Loading required package: statnet.common

    ## 
    ## Attaching package: 'statnet.common'

    ## The following object is masked from 'package:base':
    ## 
    ##     order

    ## Loading required package: network

    ## network: Classes for Relational Data
    ## Version 1.16.0 created on 2019-11-30.
    ## copyright (c) 2005, Carter T. Butts, University of California-Irvine
    ##                     Mark S. Handcock, University of California -- Los Angeles
    ##                     David R. Hunter, Penn State University
    ##                     Martina Morris, University of Washington
    ##                     Skye Bender-deMoll, University of Washington
    ##  For citation information, type citation("network").
    ##  Type help("network-package") to get started.

    ## sna: Tools for Social Network Analysis
    ## Version 2.5 created on 2019-12-09.
    ## copyright (c) 2005, Carter T. Butts, University of California-Irvine
    ##  For citation information, type citation("sna").
    ##  Type help(package="sna") to get started.

### 스크래핑할 URL 알려주기

``` r
url_base <- 'https://movie.daum.net/moviedb/grade?movieId=122091&type=netizen&page='
```

### 빈공간 만들기

``` r
all.reviews <- c() 
```

### for 문을 이용하여 리뷰 수집하기

총 리뷰 수 확인하고 반복문 횟수 정하기 url\_base의 뒤에 페이지를 1~30 리뷰 페이지 까지 증가

``` r
for(page in 1:30){                                         #예시로 20까지만 코딩함. 리뷰 갯수에 따라 달라짐.
  url <- paste(url_base, page, sep='', encoding="euc-kr")  
  htxt <- read_html(url)                                   #html 코드 불러오기
  table <- html_nodes(htxt, '.review_info')                #리뷰가 있는 위치 찾아 들어가기 
  content <- html_nodes(table, '.desc_review')             
  reviews <- html_text(content)                            #실제 리뷰의 text 파일만 추출
  if( length(reviews) == 0 ){ break }                      
  reviews <- str_trim(reviews)                             #앞뒤 공백문자 제거  
  all.reviews <- c(all.reviews, reviews)                   #결과값 저장
  print(page)
  }
```

    ## [1] 1
    ## [1] 2
    ## [1] 3
    ## [1] 4
    ## [1] 5
    ## [1] 6
    ## [1] 7
    ## [1] 8
    ## [1] 9
    ## [1] 10
    ## [1] 11
    ## [1] 12
    ## [1] 13
    ## [1] 14
    ## [1] 15
    ## [1] 16
    ## [1] 17
    ## [1] 18
    ## [1] 19
    ## [1] 20
    ## [1] 21
    ## [1] 22
    ## [1] 23
    ## [1] 24
    ## [1] 25
    ## [1] 26
    ## [1] 27
    ## [1] 28
    ## [1] 29
    ## [1] 30

### 스크래핑 결과 확인

``` r
head(all.reviews)
```

    ## [1] ""                                                                                                                                                                                                                        
    ## [2] "오늘 롯타에서 보고왔어요~\r영화 그때 그사람들이 훨씬 재밌습니다.\r역사적 사실이니 비슷한 부분이 많을수밖에 없구요\r설연휴라 어른들 위주로 맞춰서 이 영화 고른건데\r진짜 돈아까워 죽는줄 ㅡ.ㅡ 그때 그 사람들 보세요 그냥"
    ## [3] "몰입감 죽여줌 \r시간 간줄 모르고 보았음\r아카데미 후보감  역시 역사를 주제인지라\r대한국민 필수적으로 봐도 강추"                                                                                                         
    ## [4] ""                                                                                                                                                                                                                        
    ## [5] "완벽한 연기 \r완벽한 영화"                                                                                                                                                                                               
    ## [6] "여기도 태극기부대 쉐끼들이 짝 깔려있네\r소재 자체가  실제 상황이라 엄청 몰입감 2시간10분"

### 결과를 텍스트 파일로 저장

``` r
write.table(all.reviews,'namsan.txt')
```
