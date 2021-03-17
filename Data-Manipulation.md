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
exam %>% summarise(mean_math = mean(math))<br>
<br>
##      mean_math<br>
##1      57.45<br>
<br>

#### group_by(): 집단별로 나누기

#### left_join(): 데이터 합치기(열)

#### bind_rrows(): 데이터 합치기(행)
<br>
