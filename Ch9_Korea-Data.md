# Chapter 9(1~3)
해당 챕터에서는 '한국복지패널데이터'를 분석한다.<br>
<br>
**foreign** 패키지를 이용하면 SPSS, SAS, STATA등 다양한 통계분석 소프트웨어의 파일을 불러올 수 있다.<br>
<br>

#### 준비단계
install.packages("foreign")<br>
<br>
library(foreign)<br>
library(dplyr)<br>
library(ggplot2)<br>
library(readxl)<br>
<br>
foreign 패키지는 **read.spss()** 함수를 제공해 SPSS 파일을 읽을 수 있게끔 만들어준다.<br>
<br>
raw_welfare <- read.spss(file = "C:/Users/astnv/Downloads/Koweps_hpc10_2015_beta1.sav", to.data.frame = T) # 데이터 불러오기 <br>
*이때 to.data.frame = T는 SPSS파일을 데이터 프레임 형태로 변환하는 기능이다. 이 패러미터가 없으면 데이터를 리스트 형태로 불러온다.* <br>
<br>
welfare <- raw_welfare # 복사본 만들기<br>
<br>
<br>
주어진 데이터는 변수가 많고, 용량이 크기 때문에 분석할 데이터 중 일부의 변수명을 알기 쉬운 형태로 변경해준다. 해당 책의 예제에서는 코드북을 참조하여 성별, 태어난 연도, 혼인 상태, 종교, 월급, 직업 코드, 지역 코드를 변경해준다.<br>
<br>

## 데이터 분석 절차
데이터 분석은 두 단계로 이루어진다.<br>
<br>
### 1단계. 변수 검토 및 전처리
가장 먼저 분석에 사용할 변수들을 전처리한다. 변수의 특성을 파악하고 이상치를 정제한 다음 파생변수를 만든다.<br>
전처리는 분석에 활용할 변수 각각에 대해 실시한다. 예를 들어 '성별에 따른 월급의 차이'를 분석한다면 성별, 월급 두 변수를 각각 전처리한다.<br>
<br>

### 2단계. 변수 간 관계 분석
전처리가 완료되면 본격적으로 변수 간 관계를 파악하는 분석을 한다. 데이터를 요약한 표를 만든 후 분석 결과를 쉽게 이해할 수 있는 그래프를 만든다.<br>
<br>

## 성별에 따른 월급 차이

### 변수 검토하기
### 
