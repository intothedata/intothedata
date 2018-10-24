---
title: "분산 - Variance"
categories: [statistics,통계]
tags: [분산,variance]
---

분산은 데이터가 평균으로 부터 얼마나 많이 퍼져있는지를 나타내는 통계값이다.
샘플 분산(모분산이 아닌 샘플링한 데이터의 분산)의 공식은 다음과 같다.

$${{\sum {{{(x - \bar x)}^2}} } \over {n - 1}}$$

분산은 평균을 구한 후에 계산이 가능한데 그렇다고 해서 분산을 구하기 위해서 데이터의 모든 요소를 2번 반복해서 접근 할 필요없이 한 번의 반복(iteration)으로 계산할 수 있다.
즉, Map/Reduce로 천만개의 수치값에 대한 분산을 구한다고 할 때 (사실 실제로 그럴일이 별로 없다)한 번의 Map/Reduce로도 가능하다.

$${s^2} = {{\sum\limits_{i = 1}^N {(x_i^2 - 2\bar x{x_i} + {{\bar x}^2})} } \over {N - 1}} = {{\sum\limits_{}^{} {x_i^2 - 2N{{\bar x}^2} + N{{\bar x}^2}} } \over {N - 1}} = {{\sum {x_i^2 - N{{\bar x}^2}} } \over {N - 1}}$$

하지만 위의 식은 데이터가 크거나 데이터 요소의 수치들이 큰 경우 저장 변수 메모리를 초과해서 에러를 발생시키기 쉽다.

Welford's method 라는 방식을 사용하면 이 문제를 해결할 수 있다.

\begin{equation}
   (N - 1)s_N^2 - (N - 2)s_{N - 1}^2 \\
    = \sum\limits_{i = 1}^N {{{({x_i} - {{\bar x}_N})}^2}}  - \sum\limits_{i = 1}^{N - 1} {{{({x_i} - {{\bar x}_{N - 1}})}^2}}   
    = {({x_N} - {{\bar x}_N})^2} + \sum\limits_{i = 1}^{N - 1} {\left( {{{({x_i} - {{\bar x}_N})}^2} - {{({x_i} - {{\bar x}_{N - 1}})}^2}} \right)}   
    = {({x_N} - {{\bar x}_N})^2} + \sum\limits_{i = 1}^{N - 1} {({x_i} - {{\bar x}_N} + {x_i} - {{\bar x}_{N - 1}})} ({{\bar x}_{N - 1}} - {{\bar x}_N})  
    = {({x_N} - {{\bar x}_N})^2} + ({{\bar x}_N} - {x_N})({{\bar x}_{N - 1}}?{{\bar x}_N})  
    = ({x_N} - {{\bar x}_N})({x_N} - {{\bar x}_N} - {{\bar x}_{N - 1}} + {{\bar x}_N})  
    = ({x_N} - {{\bar x}_N})({x_N} - {{\bar x}_{N - 1}}) 
\end{equation}

복잡해 보이지만 결국 사용해야 할 것은 마지막의 수식이다.

$$({x_N} - {{\bar x}_N})({x_N} - {{\bar x}_{N - 1}})$$
 
