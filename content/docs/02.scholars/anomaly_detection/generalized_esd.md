---
title: "Generalized ESD"
type: "docs"
---

**Generalized ESD Test for Outliers**

이 기법은 이상감기 기법이다. 원래 명칭을 조금 길에 풀어쓰면 “Generalized Extreme Studentized Deviate Test”이다.
하나 이상의 이상치(outlier)를 찾는 방법이며 원래 명칭은 “Grubbs' test for outliers” 이다.

“Grubbs' test for outlies”는 다른 이름으로 “Maximum normed residual test” 또는 “Extreme studentized deviate test”로 표기 되기도 한다.

이 방법은 다음과 같은 조건이 만족되어야 한다.

- 정규분포
- 단변량

## 이상값 outlier

통계적 접근 방식에서는 일반적인 대부분의 데이터가 어떤 통계 모델을 따르고, 그 모델을 따르지 않는 데이터는 이상값이라고 가정한다.

## GEST의 장점

[Grubbs 테스트](https://www.itl.nist.gov/div898/handbook/eda/section3/eda35h1.htm) 와 [Tietjen-Moore 테스트](https://www.itl.nist.gov/div898/handbook/eda/section3/eda35h2.htm) 가 의심되는 이상치 수 _k를_ 정확하게 지정해야 한다는 불편한 점이 있다. *k*가 올바르게 지정되지 않으면 이러한 테스트의 결론이 왜곡될 수 있다는 것이고 *k*를 잘 지정하는 것은 매우 어렵다.
GEST는 k를 지정하는 것을 해결한 것이다.
GESD 테스트에서는 의심되는 이상값 수의 상한값만 지정하면 된다.


## 참고자료

https://towardsdatascience.com/anomaly-detection-with-generalized-extreme-studentized-deviate-in-python-f350075900e2
