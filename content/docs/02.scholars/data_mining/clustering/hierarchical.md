---
title: "계층적 군집화"
type: "docs"
tags: [계층적 군집화, 클러스터링, 군집분석, 덴드로그램, 병합적, 분할적]
---

## 계층적 군집화 (Hierarchical Clustering)

계층적 군집화는 데이터를 계층적인 트리 구조(dendrogram)로 표현하여 클러스터를 구성하는 방법이다. 
주요 방식은 두 가지가 있다:

1. **병합적 방법 (Agglomerative)**: 각 데이터 포인트를 개별 클러스터로 시작하여, 가장 유사한 클러스터들을 반복적으로 병합한다.
2. **분할적 방법 (Divisive)**: 전체 데이터를 하나의 클러스터로 시작하여, 점진적으로 클러스터를 분할해 나간다.

### 알고리즘 작동 원리

#### 병합적 방법 (Agglomerative) 단계
1. 각 데이터 포인트를 개별 클러스터로 초기화
2. 모든 클러스터 쌍 간의 거리를 계산
3. 가장 가까운 두 클러스터를 병합
4. 새로운 클러스터와 다른 클러스터 간의 거리를 다시 계산
5. 모든 데이터가 하나의 클러스터가 될 때까지 2-4단계를 반복

### 거리 측정 방법

클러스터 간의 거리를 측정하는 다양한 방법이 있다:

#### 1. 유클리드 거리 (Euclidean Distance)
- 가장 일반적으로 사용되는 거리 측정법
- 두 점 사이의 직선 거리를 계산
- 연속형 변수에 적합

#### 2. 맨하탄 거리 (Manhattan Distance)
- 각 차원의 절댓값 차이의 합
- 이상치에 덜 민감
- 고차원 데이터에 유용

#### 3. 코사인 거리 (Cosine Distance)
- 벡터 간의 각도를 기반으로 한 거리
- 텍스트 마이닝이나 추천 시스템에서 주로 사용

### 연결 기준 (Linkage Criteria)

클러스터 간의 거리를 정의하는 방법:

#### 1. 단일 연결 (Single Linkage)
- 두 클러스터에서 가장 가까운 점들 간의 거리
- 체인 효과가 발생할 수 있음

#### 2. 완전 연결 (Complete Linkage)
- 두 클러스터에서 가장 먼 점들 간의 거리
- 구 모양의 클러스터를 형성하는 경향

#### 3. 평균 연결 (Average Linkage)
- 두 클러스터의 모든 점 쌍 간 거리의 평균
- 균형잡힌 클러스터 형성

#### 4. 워드 연결 (Ward Linkage)
- 클러스터 내 분산을 최소화하는 방향으로 병합
- 크기가 비슷한 클러스터를 형성

### 특징 및 장점

- **시각화**: 덴드로그램을 통해 데이터의 계층적 구조를 직관적으로 파악 가능
- **유연성**: 클러스터 수를 사전에 결정할 필요 없음
- **결정론적**: 동일한 데이터와 파라미터로 항상 같은 결과 생성
- **해석성**: 클러스터 형성 과정을 단계별로 추적 가능

### 단점 및 한계

- **계산 복잡도**: O(n³) 시간 복잡도로 대용량 데이터에 부적합
- **메모리 사용량**: 모든 거리 행렬을 저장해야 하므로 메모리 집약적
- **노이즈 민감성**: 이상치에 영향을 받기 쉬움
- **비가역성**: 한 번 병합된 클러스터는 다시 분리할 수 없음

### 실제 적용 사례

1. **생물학**: 계통 분류학에서 종의 진화 관계 분석
2. **마케팅**: 고객 세분화 및 시장 조사
3. **소셜 네트워크**: 커뮤니티 탐지 및 사용자 그룹화
4. **이미지 처리**: 이미지 분할 및 객체 인식
5. **금융**: 주식 포트폴리오 구성 및 리스크 관리

### Python 구현 예제

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.cluster.hierarchy import dendrogram, linkage, fcluster
from sklearn.datasets import make_blobs
from sklearn.preprocessing import StandardScaler

# 샘플 데이터 생성
X, y = make_blobs(n_samples=100, centers=4, n_features=2, 
                  random_state=42, cluster_std=1.5)

# 데이터 표준화
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# 계층적 군집화 수행
linkage_matrix = linkage(X_scaled, method='ward', metric='euclidean')

# 덴드로그램 그리기
plt.figure(figsize=(12, 8))
dendrogram(linkage_matrix, truncate_mode='level', p=5)
plt.title('계층적 군집화 덴드로그램')
plt.xlabel('샘플 인덱스')
plt.ylabel('거리')
plt.show()

# 클러스터 수 지정하여 분류
n_clusters = 4
cluster_labels = fcluster(linkage_matrix, n_clusters, criterion='maxclust')

# 결과 시각화
plt.figure(figsize=(10, 6))
scatter = plt.scatter(X_scaled[:, 0], X_scaled[:, 1], 
                     c=cluster_labels, cmap='viridis', alpha=0.7)
plt.colorbar(scatter)
plt.title('계층적 군집화 결과')
plt.xlabel('특성 1')
plt.ylabel('특성 2')
plt.show()
```

### 성능 평가 지표

#### 1. 실루엣 점수 (Silhouette Score)
- 클러스터 내 응집도와 클러스터 간 분리도를 종합적으로 평가
- -1 ~ 1 범위, 높을수록 좋음

#### 2. 칼린스키-하라바즈 지수 (Calinski-Harabasz Index)
- 클러스터 간 분산 대 클러스터 내 분산의 비율
- 높을수록 좋은 군집화

#### 3. 데이비스-볼딘 지수 (Davies-Bouldin Index)
- 클러스터 내 거리와 클러스터 간 거리의 비율
- 낮을수록 좋은 군집화

### 최적 클러스터 수 결정 방법

1. **덴드로그램 분석**: 큰 거리 차이가 나는 지점에서 절단
2. **엘보우 방법**: 클러스터 수에 따른 성능 지표 변화 관찰
3. **실루엣 분석**: 다양한 클러스터 수에 대한 실루엣 점수 비교

### 고급 기법

#### 1. BIRCH (Balanced Iterative Reducing and Clustering using Hierarchies)
- 대용량 데이터를 위한 계층적 군집화
- 메모리 효율적인 트리 구조 사용

#### 2. DIANA (Divisive Analysis)
- 분할적 계층 군집화의 대표적 알고리즘
- 큰 클러스터부터 시작하여 점진적 분할

### 결론

계층적 군집화는 데이터의 내부 구조를 깊게 이해하고자 할 때 유용한 기법이다. 특히 탐색적 데이터 분석 단계에서 데이터의 자연스러운 그룹화 패턴을 발견하는 데 매우 효과적이다. 다만 대용량 데이터에서는 계산 비용과 메모리 사용량을 고려하여 다른 군집화 기법과의 조합이나 샘플링 전략을 함께 사용하는 것이 바람직하다. 