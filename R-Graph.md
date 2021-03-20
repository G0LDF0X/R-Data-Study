# Chapter 8
R에서 그래프를 만들 때 가장 많이 사용하는 패키지는 ggplot2이다. 해당 패키지를 이용해 산점도, 막대 그래프, 선 그래프, 상자 그림을 만들 수 있다.<br>
<br>

### 산점도 : 변수 간 관계 표현하기
데이터를 X축과 Y축에 점으로 표현한 그래프를 **산점도(Scater Plot)** 라고 한다.<br>
ggplot2의 문법은 **레이어 구조** 로 되어있다. 배경을 만들고, 그 위에 그래프의 형태를, 그리고 마지막으로 축, 범위, 색, 표식 등 설정을 추가하는 순서로 그래프를 만든다.<br>
<br>

#### 배경 그리기
ggplot() 함수를 이용해 먼저 데이터의 배경이 될 부분을 그려준다. data에는 그래프를 그리는 데 사용할 데이터를, aes에는 x축과 y축에 사용할 변수를 지정하면 배경이 만들어진다.<br>
<br>
ggplot(data = mpg, aes(x = displ, y = hwy))<br>
<br>

#### 그래프 추가하기
그 다음, 산점도를 그리는 **geom_point()** 를 추가해 산점도를 그린다. 이때, 앞서 적었던 코드 앞에 +를 붙여 연결한다. dplyr 패키지 함수들은 %>% 기호로 연결하는 반면, ggplot2 패키지 함수들은 + 기호로 연결한다.<br>
<br>
ggplot(data = mpg, aes(x = displ, y = hwy)) + geom_point()<br>
<br>

#### 축 범위 주정하는 설정 추가하기
+ 기호를 이용해 그래프 설정을 변경하는 코드를 추가할 수 있다.<br>
축은 기본적으로 최솟값에서 최댓값까지 모든 범위의 데이터가 표현되도록 설정되어 있기 때문에, 일부만 표현하고 싶을 때에는 축 범위를 설정하면 된다.<br>
축 범위를 설정할 때에는 **xlim()** 과 **ylim()** 함수를 사용한다.<br>
<br>
ggplot(data = mpg, aes(x = displ, y = hwy)) + geom_point() + xlim(3, 6) + ylim(10, 30)<br>
<br>
이때, 데이터가 너무 커 지수 표기법으로 표현될 경우, **options(scipen = 99)** 를 설정해주면 정수 표현으로 바뀐다. 다시 지수표현으로 바꾸고 싶을 때에는 **options(scipen = 0)** 으로 실행해주면 된다. 해당 설정은 R 스튜디오를 재실행하면 원상복구된다.<br>
<br>

### 막대 그래프 : 집단 간 차이 표현하기

#### 집단별 평균표 만들기
평균 막대 그래프를 만들려면 집단별 평균으로 구성된 **데이터 프레임** 이 필요하다. 따라서 dplyr 패키지를 통해 먼저 데이터 프레임을 만들어준다.<br>
<br>
df_mpg <- mpg %>% group_by(drv) %>% summarise(mean_hwy = mean(hwy))<br>
<br>

#### 그래프 생성하기
막대 그래프를 만드는 함수는 **geom_col()** 이다. 해당 함수를 이용해서 그래프를 만든다.<br>
<br>
ggplot(data = df_mpg, aes(x = drv, y = mean_hwy)) + geom_col()<br>
<br>

#### 크기 순으로 정렬하기
막대는 기본적으로 범주의 알파벳 순서로 정렬된다. **reorder()** 함수를 사용하면 막대를 값의 크기 순으로 정렬할 수 있다. reorder()에 x축 변수와 정렬 기준으로 삼을 변수를 지정하면 된다. 이때 정렬 기준 변수 앞에 - 기호를 붙이면 내림차순으로 정렬한다.<br>
<br>
ggplot(data = df_mpg, aes(x = reorder(drv, mean_hwy), y = mean_hwy)) + geom_col() # 오름차순 정렬<br> 
ggplot(data = df_mpg, aes(x = reorder(drv, -mean_hwy), y = mean_hwy)) + geom_col() # 내림차순 정렬<br>
<br>

#### 빈도 막대 그래프 만들기
빈도 막대 그래프를 만들 때에는 **geom_bar()** 함수를 사용한다. 이때는 y축 없이 x축만 지정한다. 또한 *데이터 프레임을 만드는 과정 없이* 진행된다.<br>
이때 x축에 연속 변수를 지정하면 값의 분포를 파악할 수 있다.<br>
<br>
ggplot(data = mpg, aes(x=drv)) + geom_bar() # x가 이산값<br>
ggplot(data = mpg, aes(x=hwy)) + geom_bar() # x가 연속값<br>
<br>

