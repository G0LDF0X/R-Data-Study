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
class(welfare$sex)를 입력해 변수의 타입을 확인하고, table(welfare$sex)를 통해 몇 명이 있는지 확인한다.<br>
이상치가 있는지 확인하고, 만약 있을 경우 ifelse() 함수를 통해 이상치를 처리해준다. 마찬가지로 table(is.na(welfare$sex))를 통해 결측치가 있는지 확인한다.<br>
결측치가 없을 경우에는 ifelse() 함수를 통해 출력값인 1, 2를 각각 male과 female로 변경해준다. table과 qplot 함수를 통해 결과를 확인할 수 있다.<br>
<br>
월급을 나타내는 변수인 income도 위와 같은 처리를 해준다. 단, 월급은 연속값이기 때문에 table()이 아닌 summary(welfare$income)을 통해 요약 통계량을 확인한다.<br>
이후 마찬가지로 ifelse() 함수를 통해 이상치를 전처리해주고, table() 함수를 통해 결측치가 있는지 확인한다.<br>
<br>

### 변수 간 관계 분석하기
이제 성별 월급 평균표를 만들어 비교한다.<br>
<br>
sex_income <- welfare %>% filter(!is.na(income)) %>% group_by(sex) %>% summarise(mean_income = mean(income))<br>
ggplot(data = sex_income, aes(x = sex, y = mean_income)) + geom_col()<br>
<br>
이후 결과를 확인해보면 성별 간 월급의 평균을 알 수 있다. 이를 qqplot() 함수를 이용해 막대그래프로 만든다.<br>
<br>

## 나이와 월급의 관계

### 변수 검토하기
나이를 변수로 지정할 경우에도 전처리 하는 과정은 위에서 income을 처리하는 과정과 유사하다.<br>
단, 여기서 나이는 출생년도로 되어있으므로 이상치를 결측처리 한 뒤 이를 현재 나이로 바꿔주는 파생변수가 만들어지는 과정이 추가된다.<br>
조사가 2015년도에 진행되었으므로 2015년도를 기준으로 나이라는 파생변수를 만드는 과정은 아래와 같다.<br>
<br>
welfare$age <- 2015 - welfare$birth + 1<br>
<br>

### 변수 간 관계 분석하기
이제 나이별 월급 평균표를 만들면 아래와 같다.<br>
<br>
age_income <- welfare %>% filter(!is.na(income)) %>% group_by(age) %>% summarise(mean_income = mean(income))<br>
ggplot(data = age_income, aes(x = age, y = mean_income)) + geom_line()<br>
<br>
여기서는 선 그래프를 이용해 결과를 확인한다.
