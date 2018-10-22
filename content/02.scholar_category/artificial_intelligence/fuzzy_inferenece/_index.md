+++
title = "퍼지 추론 - Fuzzy Inferenece"
alwaysopen = true
+++

## 개요

Fuzzy는 애매한 것을 말한다. Fuzzy logic은 애매한 것을 말하는 모형이나 시스템을 말하며 보통 가전제품, 자동차와 같은 기계장치에 내장되어 있는 경우가 많다.
기계는 애매한 것을 결정하지 못하기 때문에 애매한 것을 기계가 알아들을 수 있도록 애매한 표현을 정량화해서 알려주는 방법을 고민해야 하는 그런 것에 이용되는 것이 퍼지 추론(fuzzy inference)이다.
 
### R코드로 된 예제

#### 패키지 설치

이 예제는 온라인블로그에서 가져온 것으로 매우 간단한 보험 등급 평가 시스템을 구현하는 것이다.
보험평가원의 입장에 보험가입자의 등급을 알아낼때 입력값으로 언어적 변수를 어떻게 사용하고 설정하는 가에 대해 배울 것이다.
이 예제에서는 sets 패키지를 사용할 것이다. 우선 sets 패키지를 설치하고 로딩한다.

```r
install.packages("sets")
library(sets)
```

#### 영역 설정

먼저 퍼지 영역(universe)에 대한 범위(range)와 세분성(granularity)을 정의해야 한다.
이 예제서의 영역은 0 ~ 40의 범위와 0.1의 세분성을 줄 것이다.
모든 변수들의 입력(input)은 이 범위안에 적합(fit)될 것이다.
추가적으로 세분성은 퍼지 추론의 정확도 지정하는 의미도 있다.
좀더 외형적인 수준으로 확인해보기위해서 범위(range)는 플롯에서는 x축으로 정의해야 한다.
아래와 같은 것을 범위와 세분성으로 정의했고

- range: 0 ~ 40
- granularity: 0.1

코드로는 영역을 다음과 같이 범위와 세분성을 주고 정의한다.

```r
sets_options("universe", seq(from = 0, to = 40, by = 0.1))
```

#### 언어학적 변수(Linguistic Variables) 설정

언어학적 변수는 설명하는 단어들을 쓸 수 있는데 체중미달(underweight), 비만(obese)등과 보편적으로 수치값을 표현하는 단어들이다.
보험업자들은 여러가지 다른 편수들을 사용해서 피보험 가능성(potential insured)에 등급(rating)을 사용한다.
이 예제에서는 간단하게 hemoglobin A1c (HbA1c) blood test 라는 테스트 수치, 고혈압 등급(hypertension class)을 사용할 것이고, BMI(body mass index) 수치도 사용할 것이다.
그리고 이 변수들을 variables라는 이름의 집합으로 정의할 것이다.
집의 정의는 집합 이름에 set함수로 할당하는 것을 시작한다.
여기에서는 집합 이름은 variables가 된다.
아래와 같은 표현식인데 set 안쪽에 변수들을 정의해서 넣어야 한다.

```r
variables <-  set()
```

먼저 BMI에 대한 집합 정의를 할 것이다. BMI는 body mass index로 사람의 키를 체중으로 나눈 값이다. 

BMI도 몇가지 연어적 변수를 사용해서 지정할 수 있을 것인데 낮음 "under", 적당함 "fit",초과 "over", 비만 "obese"를 사용한다.

여기에서 각각의 변수들에 대한 표준편차를 3.0으로 두기로 한다. 변수를 정의하기 위한 퍼지 멤버쉽 함수(fuzzy membership function)는 여러가지가 있다. BMI는 정규분포를 사용한다.
표준편차가 3.0인 것에 대한 이유는 없다. 적당히 알아서 지정해 줘야 한다는 뜻으로 풀이된다.
BMI 에대한 변수 정의는 다음과 같이 해서 set 함수 안에 넣는다.

```r
bmi =
 fuzzy_partition(varnames =
 c(under = 9.25, fit = 21.75,
  over = 27.5, obese = 35),
  sd = 3.0),
```


여기의 값들은 작성자가 적당한 상식으로 넣은 값이다. 따라서 그에 대한 지식(domain knowledge)가 필요하다.
이제 a1c 변수를 정의해야 하는데 반경을 5로 가지는 conic function(원뿔형 함수)을 사용할 것이다.
a1c에 대한 각각의 언어적 변수 이름은 다음과 같이 축약어로 쓸 것이다.

- l: low (낮음)
- n: normal (보통)
- h: high (높음)
 
그래서 코드는 다음과 같이 된다.

```r
a1c = fuzzy_partition(varnames = c(l = 4, n = 5.25, h = 7),
                          FUN = fuzzy_cone,
                          radius = 5),
```

등급(rating)변수에 대해서도 똑같이 원뿔형 함수를 사용할 것이다. 
이 변수는 보험업자가 부여할 등급에 대한 것이다.
거절을 1로 두고 승인을 10으로 해서 적당한 등급을 가지도록 할 것이다.
그래서 다음과 같이 정의한다.

- DC: decline (거절), 1점
- ST: normal (보통), 5점
- PF: preferred (승인), 10점

코드는 다음과 같다.

```r
rating =
fuzzy_partition(varnames =
 c(DC = 10, ST = 5, PF = 1),
 FUN = fuzzy_cone, radius = 5),
```

이제 blood pressure(혈압)에 대한 "bp"변수를 정의할 것이다.

여기서는 심장의 수축과 확장에 대한 것을 하나의 수치로 표현할 것인데 0값을 정상으로 30을 심각한 고혈합(severe hypertension)으로 할 것이다
.
이 예제에서는 어떻게 수치를 정규화 할 것인가는 중요하지 않아 대충했지만 필요하다면 잘 알려진 표를 참조하는 것도 좋다.

http://www.mayoclinic.org/diseases-conditions/high-blood-pressure/in-depth/blood-pressure/art-20050982

bp 변수에 대해서는 표준편차를 2.5로 가지는 정규분포 퍼지 멤버쉽 함수를 사용할 것이다.
다음과 같이 정의할 것이다.
 
- norm: normal
- pre: prehypertension
- hyp: hypertension
- shyp: severe hypertension

```r
fuzzy_partition(varnames =
   c(norm = 0, pre = 10, hyp = 20,
    shyp = 30), sd = 2.5)
)
```

그래서 변수 설정 부분의 완성된 코드는 다음과 같다.

```r
variables <-
  set(
    bmi = fuzzy_partition(varnames = c(under = 9.25,
                                       fit = 21.75,
                                       over = 27.5,
                                       obese = 35),
                          sd = 3.0),
    a1c = fuzzy_partition(varnames = c(l = 4,
                                       n = 5.25,
                                       h = 7),
                          FUN = fuzzy_cone,
                          radius = 5),
    rating = fuzzy_partition(varnames = c(DC = 10,
                                          ST = 5,
                                          PF = 1),
                             FUN = fuzzy_cone,
                             radius = 5),
    bp = fuzzy_partition(varnames = c(norm = 0,
                                      pre = 10,
                                      hyp = 20,
                                      shyp = 30),
                         sd = 2.5)
)
```

이제 퍼지 규칙(fuzzy rule)이라는 것을 만들어서 앞서 정의한 변수들에 연결해 주어야 한다.
이 예제에서는 3개의 규칙을 만들어서 연결 시킬 것이고 코드는 다음과 같다.

```r
rules <- set(
  fuzzy_rule(bmi %is% under || bmi %is% obese || a1c %is% l, rating %is% DC),
  fuzzy_rule(bmi %is% over || a1c %is% n || bp %is% pre, rating %is% ST),
  fuzzy_rule(bmi %is% fit && a1c %is% n && bp %is% norm, rating %is% PF)
)
```

첫번재 규칙은 DC 즉, 거절(deccline)에 대한 규칙인데 bmi가 under이거나 obese이거나 a1c가 low이면 등급이 거절이 되는 것이다.

"%is%" 연산자는 fuzzy에서의 is의 의미이다. 즉 “이면” 또는 “같으면” 이라는 의미로 생각하면 된다. "||"기호는 “또는 (OR)”의 의미이다.

규칙은 영어 문장으로 가독성을 어느 정도 보장하도록 되어 있다.

두번째 규칙은 ST 표준에 대한 정의인데 bmi가 over이거나 a1c가 노멀이거나 bp가 다소 높은 경우에 ST로 등급을 부여하게 정의되어 있다.

마지막으로 세번째 규칙은 PF 등급에 대한 것으로 보험가입이 승인되는 등급이다.

이 규칙은 BMI가 “fit”이면서 a1c가 “norm”이고 그리고 bp 가 “norm”인 경우이다.

위에서 "&&"는 “그리고 (AND)”의 의미를 가진다.

물론 위의 규칙은 예제를 위한 것으로 실제로 보험심사에서 저런 규칙을 사용하는 것은 아니다.
이제 정의한 변수와 규칙으로 퍼지 시스템을 생성한다. 그리고 생성된 시스템의 내용을 살펴보고 플롯도 그려본다.

코드는 다음과 같다.

```r
system <- fuzzy_system(variables, rules)
print(system)
plot(system)
```

위의 전체 코드는 다음과 같다.

```r
sets_options("universe", seq(from = 0, to = 40, by = 0.1))
variables <-
  set(
    bmi = fuzzy_partition(varnames = c(under = 9.25,
                                       fit = 21.75,
                                       over = 27.5,
                                       obese = 35),
                          sd = 3.0),
    a1c = fuzzy_partition(varnames = c(l = 4,
                                       n = 5.25,
                                       h = 7),
                          FUN = fuzzy_cone,
                          radius = 5),
    rating = fuzzy_partition(varnames = c(DC = 10,
                                          ST = 5,
                                          PF = 1),
                             FUN = fuzzy_cone,
                             radius = 5),
    bp = fuzzy_partition(varnames = c(norm = 0,
                                      pre = 10,
                                      hyp = 20,
                                      shyp = 30),
                         sd = 2.5)
)
  
rules <- set(
  fuzzy_rule(bmi %is% under || bmi %is% obese || a1c %is% l, rating %is% DC),
  fuzzy_rule(bmi %is% over || a1c %is% n || bp %is% pre, rating %is% ST),
  fuzzy_rule(bmi %is% fit && a1c %is% n && bp %is% norm, rating %is% PF)
)
system <- fuzzy_system(variables, rules)
print(system)
plot(system)
```

위의 결과에서 플롯은 다음과 같이 그려진다.

※ 그림

위에서 만든 시스템으로 이제 보험심사원을 위한 퍼지 시스템으로 등급을 추론할 수 있다.
bmi가 29이고 a1c가 5이고 bp가 20인 사람의 추론은 다음과 같다.

```r
fi <- fuzzy_inference(system, list(bmi = 29, a1c=5, bp=20))
plot(fi)
```

※ 그림

위의 시스템은 단일값을 주지 않고 모든 등급에 대해서 확률값을 준다.
출력된 플롯을 보면 두개의 높은 봉우리가 보일 텐데 하나는 X축에서 7에 가깝고 다른 하나는 X축에서 10의 근처에 있다.

따라서 유력한 등급은 7이거나 10이다.

위의 결과로 결정을 할 수는 있지만 결정이 망설여질 수 있다.
역퍼지화(defuzzification)는 위의 결과값을 수치값으로 변환하는 것이다. 역퍼지화에는 몇가지 알고리즘이 있는데 가장 간단한 것은 centroid 방식이다.

아래와 같이 간단한 방법으로 위의 결과값을 역퍼지화해서 수치로 바꿀 수 있다.

```r
gset_defuzzify(fi, "centroid")
```

결과값은 7.445238이 나올 것이다.  
입력값에 대한 등급은 7.44가 된다.  
작업이 다 끝났다면 사용한 퍼지 영역을 초기화할 수 있다.  
다음과 같이 한다.  

```r
sets_options("universe", NULL)
```

## 참고자료

- [http://www.soa.org/news-and-publications/newsletters/forecasting-futurism/default.aspx](http://www.soa.org/news-and-publications/newsletters/forecasting-futurism/default.aspx)
- [http://cran.r-project.org/web/packages/sets/sets.pdf](http://cran.r-project.org/web/packages/sets/sets.pdf)

