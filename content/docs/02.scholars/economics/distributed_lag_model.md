+++
title = "분산지연모델"
type = "docs"
alwaysopen = true
tags = ["분산지연모델", "Distributed Lag Model", "시계열분석", "계량경제학", "지연효과", "동적모델"]
+++

## 분산지연모델 - Distributed Lag Model

분산지연모델(Distributed Lag Model)은 종속변수가 독립변수의 현재값뿐만 아니라 과거값들에도 영향을 받는다고 가정하는 시계열 회귀모델이다. 경제학에서 정책이나 충격의 효과가 시간에 걸쳐 점진적으로 나타나는 현상을 모델링하는 데 널리 사용된다.

### 분산지연모델의 기본 개념

경제 현상에서는 한 변수의 변화가 다른 변수에 즉시 영향을 미치기보다는 시간을 두고 점진적으로 영향을 미치는 경우가 많다. 예를 들어:

- **통화정책의 효과**: 금리 변화가 경제성장에 미치는 영향은 수개월에 걸쳐 나타남
- **광고의 효과**: 광고 지출이 매출에 미치는 영향은 여러 기간에 걸쳐 지속됨
- **투자의 효과**: 설비투자가 생산성에 미치는 영향은 장기간에 걸쳐 실현됨

### 분산지연모델의 수학적 표현

일반적인 분산지연모델은 다음과 같이 표현된다:

$$Y_t = \alpha + \beta_0 X_t + \beta_1 X_{t-1} + \beta_2 X_{t-2} + \cdots + \beta_p X_{t-p} + \varepsilon_t$$

여기서:
- \( Y_t \): 시점 t에서의 종속변수
- \( X_t, X_{t-1}, \ldots, X_{t-p} \): 독립변수의 현재값과 과거값들
- \( \beta_0, \beta_1, \ldots, \beta_p \): 각 시점별 회귀계수(지연가중치)
- \( p \): 최대 지연기간
- \( \varepsilon_t \): 오차항

### 지연가중치의 해석

각 지연가중치 $\beta_i$는 다음과 같이 해석된다:

- **$\beta_0$**: 즉시효과(Impact Effect) - $X$의 변화가 같은 기간 $Y$에 미치는 즉각적 영향
- **$\beta_i$ (i>0)**: 지연효과(Lag Effect) - $X$의 변화가 i기간 후 $Y$에 미치는 영향
- **$\sum_{i=0}^{p} \beta_i$**: 장기승수(Long-run Multiplier) - $X$의 영구적 변화가 $Y$에 미치는 총 효과

### 분산지연모델의 유형

#### 1. 유한분산지연모델 (Finite Distributed Lag Model)
지연기간이 유한한 모델로, 실제 추정에서 가장 많이 사용된다:

$$Y_t = \alpha + \sum_{i=0}^{p} \beta_i X_{t-i} + \varepsilon_t$$

**장점**: 해석이 명확하고 추정이 상대적으로 간단
**단점**: 적절한 지연기간 p를 결정하기 어려움

#### 2. 무한분산지연모델 (Infinite Distributed Lag Model)
지연기간이 무한한 모델:

$$Y_t = \alpha + \sum_{i=0}^{\infty} \beta_i X_{t-i} + \varepsilon_t$$

**특징**: 이론적으로는 완전하지만 실제 추정에는 제약이 있음

#### 3. 기하분산지연모델 (Geometric Distributed Lag Model)
지연가중치가 기하급수적으로 감소하는 모델:

$$\beta_i = \beta_0 \lambda^i, \quad 0 < \lambda < 1$$

이 경우 모델은 다음과 같이 변환될 수 있다:

$$Y_t = \alpha(1-\lambda) + \beta_0 X_t + \lambda Y_{t-1} + \varepsilon_t - \lambda \varepsilon_{t-1}$$

### 추정상의 문제점과 해결방법

#### 1. 다중공선성 문제
지연변수들 간의 높은 상관관계로 인한 문제:

**해결방법**:
- **알몬 다항식 지연모델**: 지연가중치를 다항식으로 제약
- **주성분 회귀**: 지연변수들의 주성분을 사용
- **능형회귀**: 정규화를 통한 추정

#### 2. 자유도 손실
많은 지연변수로 인한 자유도 감소:

**해결방법**:
- **정보기준**: AIC, BIC를 이용한 최적 지연기간 선택
- **단계적 회귀**: 유의하지 않은 지연변수 제거

#### 3. 내생성 문제
$Y$의 과거값이 $X$의 현재값에 영향을 미치는 경우:

**해결방법**:
- **도구변수법**: 외생적 도구변수 사용
- **VAR 모델**: 모든 변수를 내생변수로 처리

### 알몬 다항식 지연모델 (Almon Polynomial Distributed Lag)

지연가중치를 k차 다항식으로 제약하는 방법:

$$\beta_i = \sum_{j=0}^{k} \gamma_j i^j$$

이를 통해 추정해야 할 모수의 수를 크게 줄일 수 있다.

**장점**: 모수 절약, 다중공선성 완화
**단점**: 다항식 차수 선택의 자의성

### 실증분석 예시

#### 통화정책의 효과 분석
중앙은행의 기준금리 변화가 GDP 성장률에 미치는 영향:

$$GDP\_growth_t = \alpha + \sum_{i=0}^{8} \beta_i Interest\_rate_{t-i} + \varepsilon_t$$

**예상 결과**:
- $\beta_0, \beta_1$: 작거나 양수 (즉시효과는 제한적)
- $\beta_2, \beta_3, \beta_4$: 음수 (2-4분기 후 본격적인 효과)
- $\beta_5$ 이후: 점진적으로 0에 수렴

#### 광고효과 분석
광고비 지출이 매출에 미치는 영향:

$$Sales_t = \alpha + \sum_{i=0}^{12} \beta_i Advertising_{t-i} + \varepsilon_t$$

### 동적승수 (Dynamic Multipliers)

분산지연모델에서 중요한 개념들:

#### 1. 즉시승수 (Impact Multiplier)
$$\frac{\partial Y_t}{\partial X_t} = \beta_0$$

#### 2. 중간승수 (Interim Multiplier)
$$\frac{\partial Y_{t+s}}{\partial X_t} = \beta_s$$

#### 3. 누적승수 (Cumulative Multiplier)
$$\sum_{i=0}^{s} \beta_i$$

#### 4. 장기승수 (Long-run Multiplier)
$$\sum_{i=0}^{\infty} \beta_i$$

### 모델 진단과 검정

#### 1. 지연기간 선택
- **정보기준**: AIC, BIC, HQ 기준
- **F-검정**: 추가 지연변수의 유의성 검정
- **잔차분석**: 자기상관 검정

#### 2. 모델 적합도
- **결정계수**: $R^2$, 수정된 $R^2$
- **잔차진단**: 정규성, 등분산성, 독립성 검정
- **구조변화 검정**: Chow 검정, CUSUM 검정

### 확장모델

#### 1. 자기회귀분산지연모델 (ARDL)
종속변수의 지연값도 포함하는 모델:

$$Y_t = \alpha + \sum_{i=1}^{p} \phi_i Y_{t-i} + \sum_{j=0}^{q} \beta_j X_{t-j} + \varepsilon_t$$

#### 2. 벡터자기회귀모델 (VAR)
여러 변수를 모두 내생변수로 처리:

$$\mathbf{Y}_t = \mathbf{A}_0 + \sum_{i=1}^{p} \mathbf{A}_i \mathbf{Y}_{t-i} + \mathbf{u}_t$$

### 실무 적용 시 고려사항

#### 1. 데이터 특성
- **표본 크기**: 충분한 관측치 확보
- **데이터 빈도**: 월별, 분기별, 연별 데이터의 특성 고려
- **계절성**: 계절조정 여부 결정

#### 2. 경제적 해석
- **이론적 근거**: 경제학적 논리와 일치하는지 확인
- **정책적 함의**: 정책 수립에 활용 가능한 시사점 도출
- **예측 정확도**: 모델의 예측 성능 평가

### 소프트웨어 구현

주요 통계 소프트웨어에서의 분산지연모델 추정:

#### R

분산지연모형은 R의 `dynlm` 패키지 또는 `lm` 함수와 `lag()` 함수를 활용하여 쉽게 추정할 수 있습니다. 예를 들어, $Y_t$에 대한 $X_t$, $X_{t-1}$, $X_{t-2}$의 분산지연모형을 추정하는 기본 코드는 다음과 같습니다.

```r
# X_t, X_{t-1}, X_{t-2}의 분산지연모형 추정 예시
library(dynlm)

# 예시 데이터 생성
set.seed(123)
n <- 100
X <- rnorm(n)
Y <- 1 + 0.5*X + 0.3*dplyr::lag(X, 1) + 0.2*dplyr::lag(X, 2) + rnorm(n)

# 시계열 객체로 변환
X_ts <- ts(X)
Y_ts <- ts(Y)

# 분산지연모형 적합
model <- dynlm(Y_ts ~ L(X_ts, 0:2))

# 결과 출력
summary(model)
# 계수, 유의성, 결정계수 등 확인 가능

```

#### Python

분산지연모형은 Python의 `statsmodels` 패키지의 `OLS`(또는 `AutoReg`, `ARDL` 등)로 구현할 수 있습니다. 예를 들어, $Y_t$에 대한 $X_t$, $X_{t-1}$, $X_{t-2}$의 분산지연모형을 추정하려면 다음과 같이 코드를 작성할 수 있습니다.

```python
import numpy as np
import pandas as pd
import statsmodels.api as sm

# 예시 데이터 생성
np.random.seed(123)
n = 100
X = np.random.randn(n)
# X의 시차 1, 2 생성 (맨 앞은 NaN)
X_lag1 = pd.Series(X).shift(1)
X_lag2 = pd.Series(X).shift(2)
# Y 생성: Y_t = 1 + 0.5*X_t + 0.3*X_{t-1} + 0.2*X_{t-2} + 오차
Y = 1 + 0.5*X + 0.3*X_lag1 + 0.2*X_lag2 + np.random.randn(n)

# 데이터프레임으로 정리
df = pd.DataFrame({
    'Y': Y,
    'X': X,
    'X_lag1': X_lag1,
    'X_lag2': X_lag2
})

# 결측치(시차로 인해 앞부분에 생김) 제거
df = df.dropna()

# 독립변수와 종속변수 지정
X_reg = df[['X', 'X_lag1', 'X_lag2']]
X_reg = sm.add_constant(X_reg)  # 상수항 추가
y_reg = df['Y']

# OLS(최소제곱법)로 분산지연모형 적합
model = sm.OLS(y_reg, X_reg).fit()

# 결과 출력
print(model.summary())
# 계수, 유의성, 결정계수 등 확인 가능

```
