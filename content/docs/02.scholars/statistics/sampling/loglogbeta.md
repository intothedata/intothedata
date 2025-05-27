+++
title = "로그로그베타"
type = "docs"
alwaysopen = true
tags = []
+++

## 로그로그베타 - Log log beta

### 개요

로그로그베타 분포(Log-logistic beta distribution)는 베타 분포의 변형으로, 생존 분석과 신뢰성 분석에서 널리 사용되는 확률 분포입니다. 이 분포는 특히 생존 시간 모델링과 위험률 분석에서 중요한 역할을 합니다.

### 수학적 정의

로그로그베타 분포의 확률밀도함수(PDF)는 다음과 같이 정의됩니다:

$$f(x; \alpha, \beta) = \frac{\beta}{\alpha} \left(\frac{x}{\alpha}\right)^{\beta-1} \left[1 + \left(\frac{x}{\alpha}\right)^{\beta}\right]^{-2}$$

여기서:
- $x > 0$ (지지집합)
- $\alpha > 0$ (척도 모수, scale parameter)
- $\beta > 0$ (형태 모수, shape parameter)

### 누적분포함수 (CDF)

$$F(x; \alpha, \beta) = \frac{\left(\frac{x}{\alpha}\right)^{\beta}}{1 + \left(\frac{x}{\alpha}\right)^{\beta}}$$

### 생존함수

생존함수 $S(x)$는 다음과 같습니다:

$$S(x; \alpha, \beta) = 1 - F(x; \alpha, \beta) = \frac{1}{1 + \left(\frac{x}{\alpha}\right)^{\beta}}$$

### 위험률 함수 (Hazard Function)

위험률 함수 $h(x)$는:

$$h(x; \alpha, \beta) = \frac{f(x)}{S(x)} = \frac{\beta}{\alpha} \left(\frac{x}{\alpha}\right)^{\beta-1} \frac{1}{1 + \left(\frac{x}{\alpha}\right)^{\beta}}$$

### 주요 특성

1. **단조성**: $\beta > 1$일 때 위험률이 단조증가하고, $\beta < 1$일 때 단조감소합니다.
2. **유연성**: 다양한 형태의 위험률 패턴을 모델링할 수 있습니다.
3. **해석 가능성**: 모수들이 직관적인 의미를 가집니다.

### 모멘트

- **평균**: $E[X] = \alpha \cdot B(1 + \frac{1}{\beta}, 1 - \frac{1}{\beta})$ (단, $\beta > 1$)
- **분산**: 복잡한 형태로 베타 함수를 포함합니다.

여기서 $B(\cdot, \cdot)$는 베타 함수입니다.

### 응용 분야

#### 1. 생존 분석
- 의학 연구에서 환자의 생존 시간 모델링
- 치료 효과 분석
- 재발 시간 예측

#### 2. 신뢰성 공학
- 제품의 수명 분석
- 고장률 모델링
- 유지보수 계획 수립

#### 3. 경제학
- 실업 기간 분석
- 기업 생존 분석
- 시장 진입/퇴출 모델링

#### 4. 환경 과학
- 오염물질 분해 시간 모델링
- 생태계 복원 시간 분석

### R에서의 구현

```r
# 로그로그베타 분포 관련 함수들
library(actuar)

# 확률밀도함수
dloglogistic <- function(x, shape, scale) {
  (shape/scale) * (x/scale)^(shape-1) / (1 + (x/scale)^shape)^2
}

# 누적분포함수
ploglogistic <- function(x, shape, scale) {
  (x/scale)^shape / (1 + (x/scale)^shape)
}

# 분위수 함수
qloglogistic <- function(p, shape, scale) {
  scale * (p/(1-p))^(1/shape)
}

# 난수 생성
rloglogistic <- function(n, shape, scale) {
  u <- runif(n)
  qloglogistic(u, shape, scale)
}
```

### Python에서의 구현

```python
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt

class LogLogisticBeta:
    def __init__(self, alpha, beta):
        self.alpha = alpha  # 척도 모수
        self.beta = beta    # 형태 모수
    
    def pdf(self, x):
        """확률밀도함수"""
        return (self.beta/self.alpha) * (x/self.alpha)**(self.beta-1) / \
               (1 + (x/self.alpha)**self.beta)**2
    
    def cdf(self, x):
        """누적분포함수"""
        return (x/self.alpha)**self.beta / (1 + (x/self.alpha)**self.beta)
    
    def survival(self, x):
        """생존함수"""
        return 1 / (1 + (x/self.alpha)**self.beta)
    
    def hazard(self, x):
        """위험률 함수"""
        return self.pdf(x) / self.survival(x)
```

### 모수 추정

#### 최대우도추정법 (MLE)
로그우도함수를 최대화하여 모수를 추정합니다:

$$\ell(\alpha, \beta) = n\log\beta - n\beta\log\alpha + (\beta-1)\sum_{i=1}^n \log x_i - 2\sum_{i=1}^n \log\left(1 + \left(\frac{x_i}{\alpha}\right)^{\beta}\right)$$

#### 모멘트 방법
표본 모멘트와 이론적 모멘트를 일치시켜 모수를 추정합니다.

### 다른 분포와의 관계

1. **로지스틱 분포**: 특별한 경우로 연결됩니다.
2. **와이불 분포**: 경쟁 위험 모델에서 함께 사용됩니다.
3. **지수 분포**: $\beta = 1$일 때 특별한 형태를 가집니다.

### 장단점

#### 장점
- 유연한 위험률 패턴 모델링 가능
- 해석하기 쉬운 모수
- 닫힌 형태의 생존함수

#### 단점
- 복잡한 모멘트 계산
- 일부 경우에서 수치적 불안정성
- 제한된 형태의 위험률 패턴

### 참고문헌

1. Lawless, J.F. (2003). Statistical Models and Methods for Lifetime Data. Wiley.
2. Klein, J.P. & Moeschberger, M.L. (2003). Survival Analysis: Techniques for Censored and Truncated Data. Springer.
3. Collett, D. (2015). Modelling Survival Data in Medical Research. CRC Press.
