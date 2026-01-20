+++
title = "선형대수학"
type = "docs"
weight = 1081
tags = ["수학", "선형대수학", "행렬", "벡터", "linear algebra", "데이터과학"]
+++

## 선형대수학 - Linear Algebra

### 정의

선형대수학(Linear Algebra)은 벡터 공간과 그 위에서 정의되는 선형 변환을 연구하는 수학의 한 분야이다. 행렬과 벡터를 다루는 수학으로, 연립일차방정식의 해법에서 출발하여 현대에는 데이터과학, 기계학습, 컴퓨터 그래픽스 등 다양한 분야의 핵심 도구로 활용된다.

### 기본 개념

#### 벡터 (Vector)

벡터는 크기와 방향을 가진 양으로, n개의 숫자를 순서대로 나열한 것이다.

$$\mathbf{v} = \begin{bmatrix} v_1 \\ v_2 \\ \vdots \\ v_n \end{bmatrix}$$

- **영벡터**: 모든 성분이 0인 벡터
- **단위벡터**: 크기가 1인 벡터
- **벡터의 내적**: $\mathbf{u} \cdot \mathbf{v} = \sum_{i=1}^{n} u_i v_i$
- **벡터의 노름(크기)**: $\|\mathbf{v}\| = \sqrt{\mathbf{v} \cdot \mathbf{v}}$

#### 행렬 (Matrix)

행렬은 숫자를 직사각형 형태로 배열한 것으로, m개의 행과 n개의 열로 구성된다.

$$A = \begin{bmatrix} a_{11} & a_{12} & \cdots & a_{1n} \\ a_{21} & a_{22} & \cdots & a_{2n} \\ \vdots & \vdots & \ddots & \vdots \\ a_{m1} & a_{m2} & \cdots & a_{mn} \end{bmatrix}$$

#### 벡터 공간 (Vector Space)

벡터 공간은 벡터의 덧셈과 스칼라 곱이 정의되어 있고, 특정 공리들을 만족하는 집합이다. 주요 성질은 다음과 같다:

- 교환법칙: $\mathbf{u} + \mathbf{v} = \mathbf{v} + \mathbf{u}$
- 결합법칙: $(\mathbf{u} + \mathbf{v}) + \mathbf{w} = \mathbf{u} + (\mathbf{v} + \mathbf{w})$
- 분배법칙: $c(\mathbf{u} + \mathbf{v}) = c\mathbf{u} + c\mathbf{v}$

### 주요 행렬 연산

#### 행렬 덧셈

같은 크기의 행렬끼리 대응하는 성분을 더한다.

$$(A + B)_{ij} = a_{ij} + b_{ij}$$

#### 행렬 곱셈

m×n 행렬 A와 n×p 행렬 B의 곱 AB는 m×p 행렬이다.

$$(AB)_{ij} = \sum_{k=1}^{n} a_{ik} b_{kj}$$

행렬 곱셈은 교환법칙이 성립하지 않는다: $AB \neq BA$ (일반적으로)

#### 전치 행렬 (Transpose)

행과 열을 바꾼 행렬이다.

$$(A^T)_{ij} = a_{ji}$$

#### 역행렬 (Inverse Matrix)

정방행렬 A에 대해 $AA^{-1} = A^{-1}A = I$를 만족하는 행렬 $A^{-1}$이다. 역행렬이 존재하는 행렬을 가역행렬(invertible matrix) 또는 정칙행렬(non-singular matrix)이라 한다.

### 핵심 개념

#### 행렬식 (Determinant)

정방행렬에 대해 정의되는 스칼라 값으로, 행렬의 가역성을 판별하는 데 사용된다.

- $\det(A) \neq 0$이면 A는 가역행렬이다
- $\det(AB) = \det(A) \cdot \det(B)$
- $\det(A^T) = \det(A)$

2×2 행렬의 행렬식:
$$\det\begin{bmatrix} a & b \\ c & d \end{bmatrix} = ad - bc$$

#### 고유값과 고유벡터 (Eigenvalue & Eigenvector)

정방행렬 A에 대해 다음을 만족하는 스칼라 λ와 영이 아닌 벡터 $\mathbf{v}$를 각각 고유값과 고유벡터라 한다.

$$A\mathbf{v} = \lambda\mathbf{v}$$

고유값은 특성방정식 $\det(A - \lambda I) = 0$의 해이다.

#### 랭크 (Rank)

행렬의 선형독립인 행(또는 열)의 최대 개수이다. 행렬의 본질적인 차원을 나타낸다.

- $\text{rank}(A) = \text{rank}(A^T)$
- $\text{rank}(AB) \leq \min(\text{rank}(A), \text{rank}(B))$

#### 선형독립과 선형종속

벡터 집합 $\{\mathbf{v}_1, \mathbf{v}_2, \ldots, \mathbf{v}_n\}$이 선형독립이라 함은 다음을 만족하는 경우뿐이다:

$$c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \cdots + c_n\mathbf{v}_n = \mathbf{0}$$

를 만족하는 스칼라가 $c_1 = c_2 = \cdots = c_n = 0$인 경우뿐일 때이다.

### 행렬 분해 (Matrix Decomposition)

#### LU 분해

정방행렬을 하삼각행렬(L)과 상삼각행렬(U)의 곱으로 분해한다.

$$A = LU$$

연립방정식을 효율적으로 푸는 데 활용된다.

#### QR 분해

행렬을 직교행렬(Q)과 상삼각행렬(R)의 곱으로 분해한다.

$$A = QR$$

최소제곱법 문제를 푸는 데 유용하다.

#### 특이값 분해 (SVD, Singular Value Decomposition)

임의의 m×n 행렬을 다음과 같이 분해한다.

$$A = U\Sigma V^T$$

- U: m×m 직교행렬 (왼쪽 특이벡터)
- Σ: m×n 대각행렬 (특이값)
- V: n×n 직교행렬 (오른쪽 특이벡터)

SVD는 데이터 압축, 노이즈 제거, 추천 시스템 등에 널리 활용된다.

#### 고유값 분해 (Eigendecomposition)

대칭행렬 A를 고유벡터와 고유값으로 분해한다.

$$A = Q\Lambda Q^T$$

- Q: 고유벡터로 구성된 직교행렬
- Λ: 고유값을 대각 성분으로 가지는 대각행렬

### 데이터과학에서의 응용

#### 1. 차원 축소

**주성분 분석(PCA)**: 고차원 데이터를 저차원으로 투영하여 데이터의 주요 특성을 보존하면서 차원을 줄인다. 공분산 행렬의 고유값 분해 또는 데이터 행렬의 SVD를 활용한다.

#### 2. 추천 시스템

**행렬 인수분해(Matrix Factorization)**: 사용자-아이템 평점 행렬을 저랭크 행렬들의 곱으로 분해하여 빈 항목을 예측한다. Netflix Prize에서 유명해진 기법이다.

#### 3. 기계학습

- **선형 회귀**: 최소제곱법을 통한 회귀 계수 추정
- **신경망**: 가중치 행렬을 통한 층간 변환
- **서포트 벡터 머신**: 커널 행렬 활용

#### 4. 자연어 처리

- **단어 임베딩**: 단어-문서 행렬의 SVD를 통한 잠재 의미 분석(LSA)
- **토픽 모델링**: 비음수 행렬 분해(NMF)를 활용한 토픽 추출

#### 5. 이미지 처리

- **이미지 압축**: SVD를 이용한 손실 압축
- **얼굴 인식**: 고유얼굴(Eigenfaces) 방법

#### 6. 그래프 분석

- **PageRank**: 웹 페이지 중요도를 행렬의 고유벡터로 계산
- **스펙트럴 클러스터링**: 그래프 라플라시안의 고유벡터 활용

### 수치 선형대수학

실제 계산에서는 다음과 같은 문제들을 고려해야 한다:

- **수치적 안정성**: 반올림 오차의 누적 문제
- **조건수(Condition Number)**: 입력의 작은 변화가 출력에 미치는 영향
- **희소 행렬(Sparse Matrix)**: 대부분의 원소가 0인 행렬의 효율적 처리
- **대규모 행렬 연산**: 메모리 효율적인 알고리즘 필요

### 주요 정리

#### 차원 정리 (Rank-Nullity Theorem)

$$\dim(\text{Col}(A)) + \dim(\text{Null}(A)) = n$$

행렬 A의 열공간 차원(랭크)과 영공간 차원의 합은 열의 개수와 같다.

#### 스펙트럴 정리 (Spectral Theorem)

대칭 행렬은 직교 대각화가 가능하며, 모든 고유값이 실수이다.

#### 케일리-해밀턴 정리 (Cayley-Hamilton Theorem)

모든 정방행렬은 자신의 특성다항식을 만족한다.

### 참고 자료

- Gilbert Strang, "Introduction to Linear Algebra"
- David C. Lay, "Linear Algebra and Its Applications"
- 3Blue1Brown, "Essence of Linear Algebra" (시각적 이해에 도움이 되는 영상 시리즈)

선형대수학은 데이터과학의 언어라고 할 수 있다. 데이터를 행렬과 벡터로 표현하고, 선형 변환을 통해 분석하는 것이 현대 데이터과학의 핵심이다.
