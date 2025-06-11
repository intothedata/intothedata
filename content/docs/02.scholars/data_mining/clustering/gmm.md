---
title: "Gaussian Mixture Model (GMM)"
type: "docs"
tags: [Gaussian Mixture Model, GMM, 클러스터링, 군집분석]
---

## Gaussian Mixture Model (GMM)

Gaussian Mixture Model (GMM)은 각 클러스터를 다변량 가우시안 분포로 모델링하는 확률적 클러스터링 기법이다. 
EM (Expectation-Maximization) 알고리즘을 사용하여 각 클러스터의 평균, 공분산, 혼합 계수를 추정하고, 데이터가 각 클러스터에 속할 확률을 계산한다.

### 특징
- 클러스터가 타원형인 경우에도 유연하게 모델링할 수 있다.
- 데이터에 대한 확률적 해석을 제공한다.
- 초기값 및 클러스터의 수 설정에 민감할 수 있다.

GMM은 데이터의 분포를 확률적으로 모델링하여 복잡한 클러스터 구조를 파악하는 데 유용하다. 