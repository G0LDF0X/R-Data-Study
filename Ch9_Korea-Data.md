# Chapter 9
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

## 연령대에 따른 월급 차이
이번에는 나이가 아니라 연령대(30세, 59세 등의 기준을 나누어 분류)에 따른 월급의 차이를 계산하고자 한다.<br>
<br>

### 파생변수 만들기
연령대(ageg)라는 파생변수를 만들어 범주에 해당하는 데이터를 각각 넣는 작업을 한다. 이 과정에는 **mutate()** 함수를 이용한다.<br>
<br>
welfare <- welfare %>% mutate(ageg = ifelse(age < 30, "young", ifelse(age <= 59, "middle", "old")))<br>
<br>

### 변수 간 관계 분석하기
먼저 연령대별로 평균 월급이 다른지 알아보기 위해 연령대별 월급 평균표를 만든다.<br>
이후 ggplot의 geom_col() 함수를 이용해 막대 그래프를 그린다. 이때 초년, 중년, 노년의 나이 순서로 정렬하기 위해 **scale_x_discrete(limtis = c())** 함수를 이용한다. c() 안에 범주 순서를 지정해주면 된다.<br>
<br>
ageg_income <- welfare %>% filter(!is.na(income)) %>% group_by(ageg) %>% summarise(mean_income = mean(income))<br>
ggplot(data = ageg_income, aes(x = ageg, y = mean_income)) + geom_col() + scale_x_discrete(limits = c("young", "middle", "old"))<br>
<br>

## 연령대 및 성별 월급 차이
성별의 월급 차이가 연령대에 따라 다른 양상을 보이는지 확인하기 위해 분석을 진행한다.<br>
<br>
먼저 연령대 및 성별 월급 평균표를 만든다.<br>
이후 만들어진 데이터로 그래프를 그리는데, 막대그래프를 그리기 위해 geom_col() 함수를 이용한다. 이때 성별에 따라 다른 색으로 표현되도록 aes() 안에 **fill()** 함수를 이용해 **sex** 를 지정해준다. 축의 순서는 위에서와 마찬가지로 **scale_x_discrete(limits = c())** 함수를 이용해 초년, 중년, 노년으로 정렬한다.<br>
<br>
sex_income <- welfare %>% filter(!is.na(income)) %>% group_by(ageg, sex) %>% summarise(mean_income = mean(income))<br>
ggplot(data = sex_income, aes(x = ageg, y = mean_income, fill = sex)) + geom_col(position = "dodge") + scale_x_discrete(limits = c("young", "middle", "old"))<br>
<br>
이때, 만약 성별 막대를 분리하고 싶다면 geom_col() 함수 안에 **position = "dodge"** 파라미터를 추가하면 된다. 파라미터가 없을 경우에는 하나의 막대 안에 두 개의 색이 나타나고, 파라미터를 추가해줄 경우에는 총 6개의 막대그래프가 생긴다.<br>
<br>
만약 연령대로 구분짓지 않고 연속값을 가지는 나이로 계산할 경우에는 막대 그래프가 아닌 선 그래프로 나타내면 된다. 이때는 geom_line() 함수를 이용하며, 마찬가지로 월급 평균 선이 성별에 따라 다른 색으로 표현되도록 하고싶다면 aes()안에 **col = sex** 파라미터를 추가해주면 된다.<br>
<br>
sex_age <- welfare %>% filter(!is.na(income)) %>% group_by(age, sex) %>% summarise(mean_income = mean(income))<br>
ggplot(data = sex_age, aes(x = age, y = mean_income, col = sex)) + geom_line()<br>
<br>

## 직업 별 월급 차이
이번에는 직업 별 월급의 차이를 알아보고자 한다.<br>
<br>
먼저 직업을 나타내는 **code_job** 변수를 살펴보면, 숫자 코드로 적혀있기 때문에 코드북을 참조하여 이를 매칭시키는 작업이 필요하다. 여기서 **left_join()** 을 사용한다.<br>
데이터를 불러와 필요한 데이터를 **list_job** 객체에 저장한 뒤, welfare와 list_job에 들어있는 code_job을 기준으로 둘을 결합시키는 작업을 진행한다.<br>
<br>
list_job <- read_excel("C:/Users/astnv/Downloads/Doit_R-master/Data/Koweps_Codebook.xlsx", col_names = T, sheet = 2)<br>
welfare <- left_join(welfare, list_job, id = "code_job")<br>
<br>
이후 직업별 월급 평균표를 만들면 된다. 과정은 지난 작업과 동일하다. 이때 직업이 없거나(is.na(job)) 혹은 월급이 없는 사람(is.na(income))은 분석 대상이 아니므로 filter 함수를 이용해 분석에서 제외해준다.<br>
<br>
job_income <- welfare %>% filter(!is.na(income) & !is.na(job)) %>% group_by(job) %>% summarise(mean_income = mean(income))<br>
<br>
이때 상위 10개의 직업을 보고 싶다면 월급을 내림차순으로 정렬하고 상위 10개를 추출하면 된다.<br>
직업 이름이 길기 때문에 기존처럼 세로 막대 그래프를 그릴 경우, 이름이 겹칠 수 있다. 따라서 이러한 경우를 방지하기 위해 가로 막대 그래프를 그린다. 이때 사용하는 함수는 **coord_flip()** 이다.<br>
<br>
top10 <- job_income %>% arrange(desc(mean_income)) %>% head(10)<br>
ggplot(data = top10, aes(x = reorder(job, mean_income), y = mean_income)) + geom_col() + coord_flip()<br>
<br>
반대로 하위 10개의 직업을 보고 싶다면, 월급을 오름차순으로 정렬하고 상위 10개를 추출하면 된다.<br>
이때, 상위 10위 그래프와 비교할 수 있도록 y축을 0부터 850까지 표현될 수 있도록 제한한다. (제한하지 않으면 상위에 비해 값이 작기 때문에 y축의 최대값이 확연히 줄어든다.)<br>
물론 오름차순 정렬이므로 reorder()에서 -mean_income이 되는 부분에 유의한다.<br>
<br>
bottom10 <- job_income %>% arrange(mean_income) %>% head(10)<br>
ggplot(data = bottom10, aes(x = reorder(job, -mean_income), y = mean_income)) + geom_col() + coord_flip() + ylim(0, 850)<br>

## 성별 직업 빈도
해당 챕터에서는 성별에 따라 어떤 직업이 많은지에 대한 분석을 진행한다. 성별과 직업 변수에 대한 전처리 작업은 앞에서 진행되었으니 바로 변수간 관계 분석으로 들어간다.<br>
<br>
먼저 각 성별마다 직업별 빈도를 구해 상위 10개를 추출한다.<br>
<br>
job_male <- welfare %>% filter(!is.na(job) & sex == "male") %>% group_by(job) %>% summarise(n = n()) %>% arrange(desc(n)) %>% head(10)<br>
job_female <- welfare %>% filter(!is.na(job) & sex == "female") %>% group_by(job) %>% summarise(n=n()) %>% arrange(desc(n)) %>% head(10)<br>
<br>
이렇게 제작된 빈도표를 가지고 그래프를 만들면 아래와 같다.<br>
<br>
ggplot(data = job_male, aes(x = reorder(job, n), y = n)) + geom_col() + coord_flip()<br>
ggplot(data = job_female, aes(x = reorder(job, n), y = n)) + geom_col() + coord_flip()<br>
<br>
이때 빈도표를 만들때에는 앞서 geom_bar()을 사용한다고 이야기 했었는데, 해당 함수는 **원자료를 이용해 막대 그래프를 만들 때** 사용한다. 현재 과정에서는 직업별 빈도표를 이용해 막대그래프를 만들었기 때문에 geom_col()을 사용했다. (ggplot이 직접 세는게 아니라 표를 가지고 만들었기 때문에)<br>
<br>

## 종교 유무에 따른 이혼율
해당 챕터에서는 다양한 변수와 종교 유무와 이혼율에 대해서 분석하고자 한다. 먼저 종교의 유무와 이혼율에 대한 변수의 전처리를 진행한다.<br>
코드북을 참조하여 종교 변수의 1, 2를 각각 yes, no로 변환한다.<br>
또한 혼인 상태 변수 또한 코드북을 참조하여 유배우/이혼의 경우일 때만 데이터 분석에 사용한다. welfare에 group_marriage라는 변수를 만들어 결혼을 했을 경우에는 marriage, 이혼했을 경우에는 divorce로 표기한다. 물론 이 과정에서도 누락되는 NA가 존재하므로, group_marriage를 분석에 사용할 경우에는 NA를 제외해주는 과정이 항상 필요로 들어가게 된다.<br>
<br>
이후 종교 유무에 따른 이혼율 표를 만든다. 먼저 종교 유무 및 결혼 상태별로 나눠 빈도표를 구한 뒤, 구해진 빈도를 전체 빈도로 나눠 비율을 구한다. 이때 round()함수를 이용하여 소수 첫째자리까지 표현되도록 한다.<br>
<br>
religion_marriage <- welfare %>% filter(!is.na(group_marriage)) %>% group_by(religion, group_marriage) %>% summarise(n = n()) %>% mutate(tot_group = sum(n)) %>% mutate(pct = round(n/tot_group * 100, 1))<br>
<br>
이때, **group_by(religion, group_marriage) %>% summarise(n = n())** 의 과정은 **count()** 라는 함수 하나로 요약할 수 있다. count() 함수를 사용하여 나타낼 경우는 아래와 같다.<br>
<br>
religion_marriage <- welfare %>% filter(!is.na(group_marriage)) %>% count(religion, group_marriage) %>% group_by(religion) %>% mutate(pct = round(n/sum(n) * 100, 1))<br>
<br>
위 과정을 거치면 이혼율 표가 만들어지며, 만들어진 표에서 이혼한 경우만을 모으면 아래와 같다.<br>
<br>
divorce <- religion_marriage %>% filter(group_marriage == "divorce") %>% select(religion, pct)<br>
ggplot(data = divorce, aes(x = religion, y = pct)) + geom_col()<br>
<br>
이후 종교 뿐만이 아니라 연령대별에 대한 이혼율 표를 만들어보자. 연령대 및 결혼상태별 비율표를 만든 뒤, 앞에서 진행했던 것처럼 이혼한 경우를 추출해 이혼율 표를 만든다.<br>
이때, 과정에서 초년인 "young"은 제외한다. 과정은 아래와 같다.<br>
<br>
ageg_marriage <- welfare %>% filter(!is.na(group_marriage)) %>% count(ageg, group_marriage) %>% group_by(ageg) %>% mutate(pct = round(n/sum(n) * 100, 1))<br>
ageg_divorce <- ageg_marriage %>% filter(ageg != "young" & group_marriage == "divorce") %>% select(ageg, pct)<br>
ggplot(data = ageg_divorce, aes(x = ageg, y = pct)) + geom_col()<br>
<br>
이 다음은 연령대 및 종교 유무에 따른 이혼율 표를 만든다. 과정은 지금까지 해왔던 것과 동일하다.<br>
<br>
ageg_religion_marriage <- welfare %>% filter(!is.na(group_marriage) & ageg != "young") %>% count(ageg, religion, group_marriage) %>% group_by(ageg, religion) %>% mutate(pct = round(n/sum(n) * 100, 1))<br>
df_divorce <- ageg_religion_marriage %>% filter(group_marriage == "divorce") %>% select(ageg, religion, pct)<br>
ggplot(data = df_divorce, aes(x = ageg, y = pct, fill = religion)) + geom_col(position = "dodge")<br>
<br>

## 지역별 연령대 비율
해당 챕터에서는 지역별 연령대 비율을 분석해, 노년층이 많은 지역이 어디인지 알아본다. 연령대 변수는 앞에서 처리했으니 지역 변수를 검토하고 처리한다.<br>
코드북을 참조하면 코드에 해당하는 지역이 어디인지 알 수 있으므로 데이터 프레임을 생성한 뒤, left_join()을 이용해 welfare에 삽입시킨다. 과정은 아래와 같다.<br>
<br>
list_region <- data.frame(code_region = c(1:7), region = c("서울", "수도권(인천/경기)", "부산/경남/울산", "대구/경북", "대전/충남", "강원/충북", "광주/전남/전북/제주도"))<br>
welfare <- left_join(welfare, list_region, id = code_region)<br>
<br>
이후 welfare에서 select()를 이용해 code_region과 region을 확인해보면 코드북과 동일하게 기입되었음을 확인할 수 있다.<br>
<br>
이후에는 변수 간 관계를 분석하는 작업으로 들어간다. 먼저 지역별 연령대 비율표를 만든다. 과정은 앞서 했던 것처럼 지역에 따른 연령의 비율 분포를 계산한다.<br>
<br>
region_ageg <- welfare %>% count(region, ageg) %>% group_by(region) %>% mutate(pct = round(n/sum(n) * 100, 2))<br>
<br>
이후 그래프를 만들면 아래와 같다.<br>
<br>
ggplot(data = region_ageg, aes(x = region, y = pct, fill = ageg)) + geom_col() + coord_flip()<br>
<br>
이때, 노년층 비율이 높은 순서로 막대를 정렬하려면 먼저 노년층 비율을 내림차순으로 정렬한 뒤, 해당 순서대로 지역명 변수를 만들고 scale_x_discrete(limits = )에 해당 변수를 집어넣으면 된다. 과정은 아래와 같다.<br>
<br>
list_order_old <- region_ageg %>% filter(ageg == "old") %>% arrange(pct)<br>
order <- list_order_old$region<br>
ggplot(data = region_ageg, aes(x = region, y = pct, fill = ageg)) + geom_col() + coord_flip() + scale_x_discrete(limits = order)<br>
<br>
이때, 그래프를 보면 그래프의 색상 순서가 young, old, middle인 것을 확인할 수 있다. 해당 순서를 young, middle, old로 바꿔주기 위해 level을 생성하기로 한다.<br>
막대 색깔을 순서대로 나열하려면 fill 파라미터에 지정할 변수의 범주(levels) 순서를 지정하면 된다.<br>
현재 ageg 변수는 character 타입이기 때문에 levels가 존재하지 않는다.
이때 **factor()** 을 이용해 ageg 변수를 **factor 타입** 으로 변환하고, **level 파라미터** 를 이용해 순서를 지정한다.<br>
<br>
region_ageg$ageg <- factor(region_ageg$ageg, level = c("old", "middle", "young"))<br>
ggplot(data = region_ageg, aes(x = region, y = pct, fill = ageg)) + geom_col() + coord_flip()  + scale_x_discrete(limits = order)<br>
