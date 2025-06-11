---
title: "DBSCAN"
type: "docs"
tags: [DBSCAN, 클러스터링, 군집분석]
---

## DBSCAN (Density-Based Spatial Clustering of Applications with Noise)

DBSCAN은 밀도 기반 클러스터링 알고리즘이다. 데이터의 밀도에 따라 클러스터를 형성한다. 이 알고리즘은 군집의 형태가 불규칙하거나 크기가 다양할 때 효과적으로 동작하며, 노이즈 데이터(이상치)를 효과적으로 분리할 수 있다.

### 주요 개념

- **반경 (ε, Epsilon)**: 각 데이터 포인트 주변에서 이웃을 찾기 위한 최대 거리이다.
- **최소 샘플 수 (minPts)**: 클러스터를 형성하기 위해 필요한 최소 데이터 포인트 수이다.
- **핵심 포인트 (Core Point)**: 주변 반경 내에 최소 샘플 수 이상의 데이터 포인트가 존재하는 데이터 포인트이다.
- **경계 포인트 (Border Point)**: 핵심 포인트의 이웃이지만, 자체적으로는 최소 샘플 수를 만족하지 못하는 데이터 포인트이다.
- **노이즈 (Noise/Outlier)**: 어느 클러스터에도 속하지 않는 데이터 포인트이다.

### 알고리즘 동작 원리

#### 상세 단계

1. **이웃 탐색**: 모든 데이터 포인트에 대해, 반경 ε 내에 있는 이웃 데이터 포인트의 수를 계산한다.
2. **핵심 포인트 식별**: 최소 샘플 수(minPts)보다 많은 이웃을 가진 포인트를 **핵심 포인트**로 식별한다.
3. **클러스터 형성**: 핵심 포인트를 중심으로 인접한 핵심 포인트와 경계 포인트를 확장하며 클러스터를 형성한다.
4. **노이즈 분류**: 어떤 클러스터로도 확장되지 않은 포인트는 노이즈로 분류한다.

#### 의사코드

```
DBSCAN(D, ε, minPts):
    C = 0  // 클러스터 번호
    for each point p in D:
        if p is visited:
            continue
        mark p as visited
        NeighborPts = regionQuery(p, ε)
        if |NeighborPts| < minPts:
            mark p as NOISE
        else:
            C = C + 1
            expandCluster(p, NeighborPts, C, ε, minPts)

expandCluster(p, NeighborPts, C, ε, minPts):
    add p to cluster C
    for each point p' in NeighborPts:
        if p' is not visited:
            mark p' as visited
            NeighborPts' = regionQuery(p', ε)
            if |NeighborPts'| >= minPts:
                NeighborPts = NeighborPts joined with NeighborPts'
        if p' is not yet member of any cluster:
            add p' to cluster C
```

### 매개변수 선택 방법

#### ε (Epsilon) 값 선택

1. **k-distance 그래프 방법**:
   - 각 포인트에서 k번째 최근접 이웃까지의 거리를 계산
   - k-distance를 오름차순으로 정렬하여 그래프를 그림
   - 그래프에서 "무릎점(knee point)"을 찾아 ε 값으로 설정

```python
# k-distance 그래프 예제
from sklearn.neighbors import NearestNeighbors
import matplotlib.pyplot as plt

def find_epsilon(X, k=4):
    neigh = NearestNeighbors(n_neighbors=k)
    nbrs = neigh.fit(X)
    distances, indices = nbrs.kneighbors(X)
    
    # k번째 이웃까지의 거리만 선택
    k_distances = distances[:, k-1]
    k_distances = np.sort(k_distances)
    
    plt.plot(k_distances)
    plt.ylabel('k-NN distance')
    plt.xlabel('Data Points sorted by distance')
    plt.title('k-distance Graph')
    plt.show()
    
    return k_distances
```

#### minPts 값 선택

- **일반적인 규칙**: `minPts = 2 × 차원수`
- **최소값**: 차원수 + 1
- **2D 데이터**: 보통 4개 이상 권장
- **고차원 데이터**: 더 큰 값 필요

### Python 구현 예제

#### scikit-learn 사용

```python
from sklearn.cluster import DBSCAN
from sklearn.datasets import make_blobs
import matplotlib.pyplot as plt
import numpy as np

# 샘플 데이터 생성
X, _ = make_blobs(n_samples=300, centers=4, n_features=2, 
                  random_state=42, cluster_std=0.60)

# DBSCAN 적용
dbscan = DBSCAN(eps=0.3, min_samples=10)
cluster_labels = dbscan.fit_predict(X)

# 결과 시각화
plt.figure(figsize=(10, 8))
unique_labels = set(cluster_labels)
colors = plt.cm.Spectral(np.linspace(0, 1, len(unique_labels)))

for k, col in zip(unique_labels, colors):
    if k == -1:
        # 노이즈 포인트는 검은색으로 표시
        col = 'black'
        marker = 'x'
    else:
        marker = 'o'
    
    class_member_mask = (cluster_labels == k)
    xy = X[class_member_mask]
    plt.plot(xy[:, 0], xy[:, 1], marker, markerfacecolor=col,
             markeredgecolor='k', markersize=6)

plt.title('DBSCAN Clustering')
plt.xlabel('Feature 1')
plt.ylabel('Feature 2')
plt.show()

print(f"발견된 클러스터 수: {len(set(cluster_labels)) - (1 if -1 in cluster_labels else 0)}")
print(f"노이즈 포인트 수: {list(cluster_labels).count(-1)}")
```

#### 직접 구현

```python
import numpy as np
from scipy.spatial.distance import cdist

class DBSCANCustom:
    def __init__(self, eps=0.5, min_samples=5):
        self.eps = eps
        self.min_samples = min_samples
    
    def fit_predict(self, X):
        n_points = len(X)
        labels = np.full(n_points, -1)  # -1은 노이즈를 의미
        cluster_id = 0
        
        # 모든 포인트 간의 거리 계산
        distances = cdist(X, X)
        
        for i in range(n_points):
            if labels[i] != -1:  # 이미 처리된 포인트
                continue
            
            # 이웃 찾기
            neighbors = self._find_neighbors(distances, i)
            
            if len(neighbors) < self.min_samples:
                labels[i] = -1  # 노이즈로 분류
            else:
                # 새 클러스터 시작
                labels = self._expand_cluster(X, distances, labels, i, 
                                            neighbors, cluster_id)
                cluster_id += 1
        
        return labels
    
    def _find_neighbors(self, distances, point_idx):
        return np.where(distances[point_idx] <= self.eps)[0]
    
    def _expand_cluster(self, X, distances, labels, point_idx, 
                       neighbors, cluster_id):
        labels[point_idx] = cluster_id
        i = 0
        
        while i < len(neighbors):
            neighbor_idx = neighbors[i]
            
            if labels[neighbor_idx] == -1:  # 노이즈였던 경우
                labels[neighbor_idx] = cluster_id
            elif labels[neighbor_idx] == -1:  # 미분류 상태
                labels[neighbor_idx] = cluster_id
                
                # 새로운 이웃들 찾기
                new_neighbors = self._find_neighbors(distances, neighbor_idx)
                if len(new_neighbors) >= self.min_samples:
                    neighbors = np.concatenate([neighbors, new_neighbors])
            
            i += 1
        
        return labels
```

### 다른 클러스터링 알고리즘과의 비교

| 특성 | DBSCAN | K-Means | Hierarchical |
|------|--------|---------|--------------|
| 클러스터 수 사전 지정 | 불필요 | 필요 | 불필요 |
| 클러스터 모양 | 임의 형태 | 구형 | 임의 형태 |
| 노이즈 처리 | 우수 | 미흡 | 미흡 |
| 시간 복잡도 | O(n log n) | O(nkt) | O(n³) |
| 밀도 변화 | 어려움 | 불가능 | 가능 |

### 실제 응용 분야

#### 1. 이미지 분할
```python
from sklearn.cluster import DBSCAN
from sklearn.preprocessing import StandardScaler
import cv2

def image_segmentation_dbscan(image_path):
    # 이미지 로드 및 전처리
    img = cv2.imread(image_path)
    img = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
    
    # 픽셀 데이터 reshape
    pixel_data = img.reshape(-1, 3)
    
    # 정규화
    scaler = StandardScaler()
    pixel_data_scaled = scaler.fit_transform(pixel_data)
    
    # DBSCAN 적용
    dbscan = DBSCAN(eps=0.3, min_samples=50)
    labels = dbscan.fit_predict(pixel_data_scaled)
    
    # 결과 이미지 생성
    segmented_img = labels.reshape(img.shape[:2])
    
    return segmented_img, labels
```

#### 2. 고객 세그멘테이션
```python
def customer_segmentation(customer_data):
    # 특성 선택 및 정규화
    features = ['annual_spending', 'frequency', 'recency']
    X = customer_data[features]
    
    scaler = StandardScaler()
    X_scaled = scaler.fit_transform(X)
    
    # DBSCAN 적용
    dbscan = DBSCAN(eps=0.5, min_samples=5)
    clusters = dbscan.fit_predict(X_scaled)
    
    # 결과 분석
    customer_data['cluster'] = clusters
    
    return customer_data
```

#### 3. 이상 탐지
```python
def anomaly_detection(sensor_data):
    # DBSCAN을 이용한 이상 탐지
    dbscan = DBSCAN(eps=0.3, min_samples=10)
    labels = dbscan.fit_predict(sensor_data)
    
    # 노이즈로 분류된 포인트들을 이상값으로 간주
    anomalies = sensor_data[labels == -1]
    
    return anomalies, labels
```

### 성능 평가 지표

#### 1. 실루엣 계수 (Silhouette Score)
```python
from sklearn.metrics import silhouette_score

def evaluate_dbscan(X, labels):
    # 노이즈 포인트 제외하고 계산
    mask = labels != -1
    if len(set(labels[mask])) > 1:
        score = silhouette_score(X[mask], labels[mask])
        return score
    else:
        return -1  # 클러스터가 1개 이하인 경우
```

#### 2. 조정 랜드 지수 (Adjusted Rand Index)
```python
from sklearn.metrics import adjusted_rand_score

def compare_clustering(true_labels, pred_labels):
    return adjusted_rand_score(true_labels, pred_labels)
```

### 장점과 단점

#### 장점
- **임의 형태의 클러스터**: 클러스터의 모양이 불규칙해도 효과적으로 동작한다.
- **노이즈 처리**: 노이즈나 이상치를 효과적으로 분리할 수 있다.
- **클러스터 수 자동 결정**: 사전에 클러스터 수를 지정할 필요가 없다.
- **강건성**: 이상값에 대해 강건하다.

#### 단점
- **매개변수 민감성**: 데이터의 밀도가 크게 차이나는 경우, 적절한 ε 값을 찾기 어렵다.
- **고차원 문제**: 고차원 데이터에서는 성능이 저하될 수 있다 (차원의 저주).
- **밀도 변화**: 밀도가 크게 다른 클러스터들을 동시에 찾기 어렵다.
- **메모리 사용량**: 모든 포인트 간의 거리를 계산해야 하므로 메모리 사용량이 클 수 있다.

### 변형 알고리즘

#### HDBSCAN (Hierarchical DBSCAN)
- 계층적 밀도 기반 클러스터링
- 다양한 밀도의 클러스터를 효과적으로 처리
- 더 안정적인 클러스터 추출

```python
import hdbscan

def hdbscan_clustering(X):
    clusterer = hdbscan.HDBSCAN(min_cluster_size=10)
    cluster_labels = clusterer.fit_predict(X)
    return cluster_labels
```

#### OPTICS (Ordering Points To Identify Clustering Structure)
- DBSCAN의 확장 버전
- 클러스터 추출을 위한 순서 정보 제공
- 다양한 밀도 레벨에서 클러스터 분석 가능

### 실습 과제

1. **매개변수 최적화**: k-distance 그래프를 이용하여 최적의 ε 값을 찾아보세요.
2. **실제 데이터 적용**: 실제 데이터셋(예: Iris, Wine)에 DBSCAN을 적용하고 결과를 분석해보세요.
3. **성능 비교**: DBSCAN과 K-Means의 성능을 다양한 데이터셋에서 비교해보세요.
4. **이상 탐지**: DBSCAN을 이용한 이상 탐지 시스템을 구현해보세요.

DBSCAN은 데이터의 밀도 기반 특성을 살리고 노이즈를 효과적으로 다루고자 할 때 매우 유용한 알고리즘이다. 특히 클러스터의 형태가 불규칙하거나 이상값이 많은 실제 데이터에서 뛰어난 성능을 보인다.
