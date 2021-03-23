# Chapter 6

### 데이터 전처리(Data Preprocessing)
dplyr은 데이터 전처리 작업에 가장 많이 사용되는 패키지이다.<br>
<br>

#### filter(): 행 추출
filter 안에는 조건문이 들어가며, dplyr 패키지는 **%>%** 기호(파이프 연산자)를 이용해 함수들을 나열하는 방식으로 코드를 작성한다. 단축키는 Ctrl+Shift+M이다.<br>
<br>
또한 **&** 연산자를 통해 여러 조건을 걸 수 있다.<br>
**|** 연산자를 사용할 경우에는 조건 중 하나라도 만족하는 데이터를 추출할 수 있다.<br>
또한 **%in%** 기호(매치 연산자)를 사용하면 코드를 좀 더 간편하게 적을 수 있다. 해당 기호는 변수의 값이 지정한 조건 목록에 해당하는지 확인한다.<br>
<br>
exam %>% filter(class == 1)<br>
exam %>% filter(class %in% c(1, 3, 5))<br>
<br>

#### select(): 열(변수) 추출
괄호 안에 추출할 변수(attribute)를 입력한다. 변수가 여러 개일 경우에는 쉼표로 구분한다.<br>
변수를 제외할 때에는 제외할 변수명 앞에 빼기기호(-)를 입력한다.<br>
<br>
파이프 기호를 여러 개 나열하며 함수를 여러 번 사용할 수도 있다.<br>
<br>
exam %>% select(id, math) %>% head(10)<br>
<br>

#### arrange(): 정렬
괄호 안에는 정렬의 기준을 삼을 변수를 입력하면 된다.<br>
기본적으로 정렬은 오름차순(낮음->높음)이다.<br>
내림차순으로 정렬하고 싶은 경우에는 변수에 desc()를 적용하면 된다.<br>
<br>
exam %>% arrange(desc(math)) # math 내림차순 정렬<br>
<br>
정렬 기준으로 삼을 변수를 여러 개 지정하려면 쉼표를 이용해 변수를 나열한다. 이때, 먼저 첫번째 변수를 기준으로 오름차순 정렬한 뒤, 두번째 변수를 기준으로 다시 한 번 정렬한다.<br>
<br>

#### mutate(): 변수 추가
괄호 안에는 **파생변수명 = 공식** 을 입력하면 된다.<br>
파생변수를 여러 개 생성할 경우에는 쉼표로 구분한다.<br>
<br>
exam %>% mutate(total = math + english + science) %>% head<br>
<br>

#### summarise(): 통계치 산출
summarise()는 전체를 요약한 값을 구하기보다는 group_by()와 조합해 집단별 요약표를 만들 때 사용한다.<br>
사용처를 보면, group_by()로 뽑아낸 변수 옆에 summarise()로 통계를 낸 변수가 들어간다.<br>
<br>
exam %>% summarise(mean_math = mean(math))<br>
<br>
.##      mean_math<br>
.## 1      57.45<br>
<br>
mutate()와 마찬가지로 summarise() 또한 여러 요약 통계량을 한 번에 산출할 수 있다.<br>
<br>
exam %>% group_by(class) %>% summarise(mean_math = mean(math), sum_math = sum(math), median_math = median(math), n = n())<br>
<br>
.## # A tibble: 5 x 5<br>
.##      class mean_math sum_math median_math  n<br>
.##    <int>     <dbl>    <int>       <dbl> <int><br>
.## 1     1      46.2      185        47.5     4<br>
.## 2     2      61.2      245        65       4<br>
.## 3     3      45        180        47.5     4<br>
.## 4     4      56.8      227        53       4<br>
.## 5     5      78        312        79       4<br>
<br>
summarise로 만든 변수 mean_math, sum_math, median_math, n이 group_by로 뽑아낸 class 옆에 있는 것을 확인할 수 있다.<br>
이때 n()은 빈도 수를 나타내는 함수다.<br>

#### group_by(): 집단별로 나누기
괄호 안에는 분리할 attribute를 넣는다. 해당 변수만을 출력한다.<br>
group_by()는 출력 결과를 데이터 프레임의 업그레이드 버전인 **tibble** 형태로 만들어준다. tibble은  데이터 프레임에 몇 가지 기능이 추가된 것으로, 데이터 프레임을 다룰 때와 동일한 방식으로 활용할 수 있다.<br>
<br>

#### left_join(): 데이터 합치기(열)
괄호 안에 합칠 프레임들을 나열하고, 기준으로 삼을 변수명을 **by** 에 지정하면 된다.<br>
<u>이때 by에 기준 변수를 지정할 때, 변수명 앞뒤에 따옴표를 입력해야 한다.</u><br>
<br>
exam_new <- left_join(exam, name, by = "class")<br>
<br>

#### bind_rrows(): 데이터 합치기(행)
괄호 안에 합칠 프레임을 나열하면 된다. 데이터를 합칠 때에는 두 데이터의 변수명이 같아야 한다. 변수명이 다를 경우에는 rename()을 통해 동일하게 맞춘 뒤 합치면 된다.<br>
<br>
group_all <- bind_rows(group_a, group_b)<br>
