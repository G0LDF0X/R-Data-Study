# Chapter 4

### 데이터 프레임 만들기
데이터 프레임을 만들 때는 **data.frame()** 함수를 사용해서 생성한다.<br>
<br>
df_midterm <- data.frame(english, math)<br>
df_midterm<br>
<br>
.##     english   math<br>
.## 1      90      50<br>
.## 2      80      60<br>
.## 3      60     100<br>
.## 4      70      20<br>
<br>

**달러 기호($)** 는 데이터 프레임 안에 있는 변수를 지정할 때 사용한다.<br>
<br>
mean(df_midterm$english)<br>
<br>
## [1] 75<br>
<br>
데이터 프레임을 생성할 때에는 변수와 값을 나열해서 한 번에 만들 수도 있다.<br>
<br>
df_midterm <- data.frame(english = c(90, 80, 60, 70), math = c(50, 60, 100, 20), class = c(1, 1, 2, 2))<br>
<br>
##    english   math    class<br>
## 1    90        50      1<br>
## 2    80        60      1<br>
## 3    60       100      2<br>
## 4    70        20      2<br>
<br>

### 외부 데이터 이용하기
xlsx 파일을 이용할 때에는 readxl 패키지를 이용한다. install.packages()함수를 통해 readxl를 설치하고, library()함수를 통해 패키지를 로드한다.<br>
readxl 패키지 내부에 있는 **read_excel("파일명.xlsx")** 함수를 통해 파일을 불러올 수 있다. 이때, 프로젝트 폴더 내에 없는 파일이라면 외부 경로를 지정하여 불러올 수 있다. 외부경로를 지정할 때에는 \가 아니라 /를 사용한다.<br>
엑셀 파일 첫번째 항은 반드시 변수명을 입력해야 한다. 그렇지 않으면 데이터 유실이 발생한다.<br>
이때 데이터 유실을 방지하기 위해서는 파일을 불러올 때, **col_names = F** 파라미터를 설정해줘야 한다.<br>
<br>
df_exam_novar <- read_excel("C:/Users/astnv/Downloads/Doit_R-master/Data/excel_exam_novar.xlsx", col_names = F)<br>
<br>
col_names = F의 의미는 '열 이름(Column Name)을 가져올 것인가?'라는 질문에 '그렇지 않다(False)'라고 대답한 것이다.<br>
<br>
가져올 파일이 여러개라면 몇 번째 시트인지 명시하는 **sheet 파라미터** 를 넣어줘야 한다.<br>
<br>
df_exam_sheet <- read_excel("C:/Users/astnv/Downloads/Doit_R-master/Data/excel_exam_sheet.xlsx", sheet=3)<br>
<br>
<br>
CSV 파일을 읽는 **read.csv()** 는 기본적으로 R에 내장되어 있는 함수이며, 엑셀과 마찬가지로 첫 번째 행에 변수명이 없는 CSV 파일을 불러올 때에는 **header = F** 파라미터를 지정하면 된다.<br>
문자가 들어있는 파일을 불러올 때에는 **stringsAsFactors = F** 파라미터를 지정하면 된다.<br>
<br>
CSV 파일을 저장할 때에는 **write.csv()** 함수를 사용하며, 데이터 프레임명을 지정하고 file 파라미터에 파일명을 지정하면 된다.<br>
<br>
write.csv(df_midterm, file = "df_midterm.csv")<br>
<br>
<br>
RDS 파일은 R 전용 데이터 파일로, 다른 파일들에 비해 R에서 읽고 쓰는 속도가 빠르고 용량이 작다.<br>
파일을 RDS로 저장할 때에는 **saveRDS()** 함수를 이용해 데이터 프레임을 .rds 파일로 저장한다.<br>
<br>
saveRDS(df_midterm, file = "df_midterm.rds")<br>
<br>
RDS 파일을 불러올 때에는 **readRDS()** 를 이용한다. **rm()** 함수를 사용하면 또한 데이터를 삭제할 수 있다.<br>
<br>

# Chapter 5

### 데이터를 파악할 때 사용하는 함수들

#### head(): 데이터의 앞부분 출력
앞에서 여섯 번째 행까지 출력해준다.<br>
데이터 프레임 이름 뒤에 쉼표를 쓰고 숫자를 입력하면 입력한 행까지의 데이터를 출력한다.<br>
<br>

#### tail(): 데이터의 뒷부분 출력
뒤에서 여섯 번째 행까지 출력해준다.<br>
데이터 프레임 이름 뒤에 쉼표를 쓰고 숫자를 입력하면 입력한 행까지의 데이터를 출력한다.<br>
<br>

#### View(): 뷰어 창에서 데이터 확인
앞글자는 대문자로 입력할 것. <br>
<br>

#### dim(): 데이터 차원 출력
데이터 프레임이 몇 행, 몇 열로 되어있는지 출력해준다.<br>
<br>

#### str(): 데이터 속성 출력
데이터에 들어있는 변수들의 속성을 보여준다.<br>
<br>

#### summary(): 요약 통계량 출력
변수의 값을 요약한 '요약 통계량'을 산출하는 함수이다.<br>
<br>
Min(Minimum, 최솟값): 가장 작은 값<br>
1st Qu(1st Quantile, 1사분위수): 하위 25%(4분의 1) 지점에 위치하는 값<br>
Median(Median, 중앙값): 중앙에 위치하는 값<br>
3rd Qu(3rd Qunatile, 3사분위수): 상위 75%(4분의 3) 지점에 위치하는 값<br>
Max(Maximum, 최댓값): 가장 큰 값<br>
<br>
**as.data.frame()** 은 데이터 속성을 데이터 프레임 형태로 바꾸는 함수.<br>
ggplot2::mpg는 ggplot2에 들어있는 mgp 데이터를 지칭하는 코드다.<br>
<br>

#### 변수명 바꾸기
변수의 이름을 바꾸기 위해서는 **dplyr** 패키지의 **rename(데이터 프레임명, 새 변수명 = 기존 변수명)** 함수를 사용한다.<br>
보통은 기존 데이터의 복사본을 만들어 진행한다.<br>
<br>
df_new <- rename(df_new, v2 = var2)<br>
<br>

#### 파생변수 만들기
기존의 변수를 변형해 만든 변수를 **파생변수** 라고 한다.<br>
데이터 프레임 안에 파생변수를 만들 때에는, 꼭 달러 기호($)를 이용해 변수명 앞에 데이터 프레임명을 입력해주어야 한다.<br>
<br>
df$var_mean <- (df$var1 + df$var2)/2<br>
<br>
**hist()** 함수를 이용하면 히스토그램을 그릴 수 있다.<br>
<br>
**ifelse(조건, 조건에 맞을 때 부여할 값, 조건에 맞지 않을 때 부여할 값)** 는 조건문 함수다.<br>
ifelse() 안에 또 ifelse()를 넣으면 중첩조건문이 된다.<br>
<br>
mpg$test <- ifelse(mpg$total >= 20, "pass", "fail")<br>
<br>
<br>
**table()** 을 통해 빈도표를 생성할 수 있다.<br>
<br>
ggplot2 패키지에 있는 **qplot()** 을 통해 막대그래프를 그릴 수 있다.<br>
