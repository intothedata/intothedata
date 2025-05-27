+++
title = "네이만 배분법"
type = "docs"
+++

## 네이만 배분법 - neyman allocation

네이만 배분법은 비용이 일정할 때 층화추출에서 각 층별 표본크기를 결정하는 방법이다. 모집단을 여러 층으로 나누었을 때, 각 층의 표준편차와 크기를 고려하여 최적의 표본 크기를 배분한다.

### 네이만 배분법의 원리

네이만 배분법에서는 다음 공식에 따라 각 층의 표본 크기를 결정한다.

$$n_h = n \cdot \frac{N_h \cdot \sigma_h}{\sum_{i=1}^{L} N_i \cdot \sigma_i}$$

여기서:
- $n_h$: h번째 층의 표본 크기
- $n$: 전체 표본 크기
- $N_h$: h번째 층의 모집단 크기
- $\sigma_h$: h번째 층의 표준편차
- $L$: 층의 총 개수

### 네이만 배분법의 특징

1. 각 층의 크기와 변동성을 모두 고려한다.
2. 변동성($\sigma_h$)이 큰 층에서 더 많은 표본을 추출한다.
3. 크기($N_h$)가 큰 층에서 더 많은 표본을 추출한다.
4. 모집단의 모수에 대한 분산을 최소화하는 최적의 배분 방법이다.

### 네이만 배분법의 활용

네이만 배분법은 다음과 같은 상황에서 유용하게 활용된다:

1. 각 층마다 변동성이 크게 다를 때
2. 비용이 모든 층에서 동일할 때
3. 전체 추정량의 분산을 최소화하고자 할 때

### 네이만 배분법과 비례배분법의 비교

비례배분법은 각 층의 크기에 비례하여 표본을 배분하는 방법인 반면, 네이만 배분법은 층의 크기와 표준편차를 모두 고려한다. 층별 표준편차가 모두 같다면 네이만 배분법은 비례배분법과 동일한 결과를 보인다.

### 실제 적용 예시

농업 조사에서 농장 규모별로 층화했을 때, 대규모 농장은 수는 적지만 생산량의 변동이 크다. 이런 경우 네이만 배분법을 적용하면 대규모 농장에서 상대적으로 더 많은 표본을 추출하게 된다.

### 수정절사법

수정절사법은 비용과 시간을 고려하여 표본 크기를 결정하는 방법이다. 네이만 배분법을 적용했을 때 너무 작은 표본이 할당되는 층이 있다면, 이를 최소 크기로 조정하고 나머지 층들의 표본 크기를 재조정한다.

### 참고자료

[https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=9&cad=rja&uact=8&ved=0ahUKEwiQ9u2SqtfVAhXEabwKHX4SB70QFghSMAg&url=http%3A%2F%2Fwww.5365.co.kr%2Fbbs_download.php%3Ftmp_name%3D20101116142614.2960.3.0%26name%3D%25BB%25F9%25C7%25C3%25C7%25A5%25BA%25BB%25C3%25DF%25C3%25E2%25B9%25E6%25B9%25FD%25B9%25E6%25B9%25FD4.hwp&usg=AFQjCNEqSdirh1eEFIgLNgD-NHBDEjSVOA](https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=9&cad=rja&uact=8&ved=0ahUKEwiQ9u2SqtfVAhXEabwKHX4SB70QFghSMAg&url=http%3A%2F%2Fwww.5365.co.kr%2Fbbs_download.php%3Ftmp_name%3D20101116142614.2960.3.0%26name%3D%25BB%25F9%25C7%25C3%25C7%25A5%25BA%25BB%25C3%25DF%25C3%25E2%25B9%25E6%25B9%25FD%25B9%25E6%25B9%25FD4.hwp&usg=AFQjCNEqSdirh1eEFIgLNgD-NHBDEjSVOA)

[https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=8&cad=rja&uact=8&ved=0ahUKEwiQ9u2SqtfVAhXEabwKHX4SB70QFghMMAc&url=http%3A%2F%2Flunchbreak.tistory.com%2F26&usg=AFQjCNHQg_P6jC6ybqOi_bnHmbuE2VXCNg](https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=8&cad=rja&uact=8&ved=0ahUKEwiQ9u2SqtfVAhXEabwKHX4SB70QFghMMAc&url=http%3A%2F%2Flunchbreak.tistory.com%2F26&usg=AFQjCNHQg_P6jC6ybqOi_bnHmbuE2VXCNg)
