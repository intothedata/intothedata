+++
title = "하이퍼로그로그"
type = "docs"
+++

## 하이퍼로그로그 (HyperLogLog)

줄여서 **HLL**이라고 많이 부른다.

### 개요

하이퍼로그로그는 **카디널리티(cardinality)**, 즉 고유한 원소의 개수를 추정하는 확률적 자료구조이자 알고리즘이다. 2007년 Philippe Flajolet 등에 의해 발표되었으며, 매우 적은 메모리로 대용량 데이터셋의 고유 원소 개수를 근사적으로 계산할 수 있다.

### 핵심 특징

- **메모리 효율성**: 수십억 개의 고유 원소를 1.5KB 정도의 메모리로 추정 가능
- **상대 오차 조절**: 상대 오차 범위를 조절할 수 있음
- **스트리밍 처리**: 데이터를 한 번만 스캔하여 처리 가능
- **병합 가능**: 여러 HLL 구조를 병합하여 전체 카디널리티 추정 가능

### 작동 원리

1. **해시 함수 적용**: 입력 데이터에 해시 함수를 적용하여 균등하게 분포된 값 생성
2. **선행 0의 개수 계산**: 해시값의 이진 표현에서 연속된 선행 0의 개수를 계산
3. **버킷 분할**: 해시값의 일부를 사용하여 여러 버킷으로 분할
4. **최대값 저장**: 각 버킷에서 관찰된 선행 0의 최대 개수를 저장
5. **카디널리티 추정**: 저장된 값들을 이용하여 전체 카디널리티를 추정

### 수학적 배경

카디널리티가 N이라면 레지스터 크기는 `log₂log₂N + O(1)`이다.

상대 오차는 대략 `1.04/√m`이며, 여기서 m은 사용하는 버킷의 개수이다.

### 실제 사용 예시

**세익스피어 전 작품에서의 영어 단어 세기**
- 전통적 방법: 모든 단어를 메모리에 저장 (수 MB 필요)
- HyperLogLog: 1.5KB로 99% 정확도 달성

### 장단점

**장점:**
- 극도로 적은 메모리 사용량
- 빠른 처리 속도
- 스트리밍 데이터 처리 가능
- 분산 환경에서 병합 가능

**단점:**
- 근사치만 제공 (정확한 값 아님)
- 작은 카디널리티에서는 오차가 클 수 있음
- 개별 원소의 존재 여부 확인 불가

### 활용 분야

- **웹 분석**: 고유 방문자 수 계산
- **데이터베이스**: DISTINCT COUNT 연산 최적화
- **네트워크 모니터링**: 고유 IP 주소 수 추정
- **빅데이터 처리**: 대용량 데이터셋의 카디널리티 추정

### 구현체

- **Redis**: HyperLogLog 자료구조 내장 지원
- **PostgreSQL**: HLL 확장 모듈
- **Apache Spark**: 내장 HyperLogLog 함수
- **stream-lib**: Java 구현체

### 관련 알고리즘

- **LogLog**: HyperLogLog의 전신
- **MinHash**: 집합 유사도 추정
- **Count-Min Sketch**: 빈도 추정
- **Bloom Filter**: 멤버십 테스트

### 참고 자료

- [네이버 D2 - HyperLogLog 소개](http://d2.naver.com/helloworld/711301)
- [빅데이터 카운팅: 1.5KB 메모리로 10억 개 고유 객체 세기](http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html)
- Philippe Flajolet and Éric Fusy and Olivier Gandouet and Frédéric Meunier, "HyperLoglog: the analysis of a near-optimal cardinality estimation algorithm", Discrete Mathematics and Theoretical Computer Science (DMTCS) Proceedings, vol AH, (2007) pp. 127--146. [논문 링크](http://www.dmtcs.org/dmtcs-ojs/index.php/proceedings/article/view/dmAH0110)
- Matt Abrams, "Big Data Counting: How To Count A Billion Distinct Objects Using Only 1.5KB Of Memory", High Scalability
- Ilya Katsov, "Probabilistic Data Structures for Web Analytics and Data Mining", Highly Scalable Blog
- [stream-lib GitHub 저장소](https://github.com/addthis/stream-lib/)
- [Iterated logarithm - Wikipedia](http://en.wikipedia.org/wiki/Iterated_logarithm)
