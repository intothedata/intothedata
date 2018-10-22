http://d2.naver.com/helloworld/711301
cardinality(unique count)를 샘플링해서 알아내는데 사용하는 확률자료구조
세익스피어 전 작품에서의 영어단어 세기
http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html
 Philippe Flajolet 등이 2007년에 발표
HyperLogLog는 상대오차범위 조절 가능
상대오차는

cardinality가 N이라면 레지스터 크기는 log_2log_2N + O(1)
Philippe Flajolet and Éric Fusy and Olivier Gandouet and Frédéric Meunier, "HyperLoglog: the analysis of a near-optimal cardinality estimation algorithm", Discrete Mathematics and Theoretical Computer Science (DMTCS) Proceedings, vol AH, (2007) pp. 127--146. http://www.dmtcs.org/dmtcs-ojs/index.php/proceedings/article/view/dmAH0110
Matt Abrams, "Big Data Counting: How To Count A Billion Distinct Objects Using Only 1.5KB Of Memory", High Scalability, http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html
Ilya Katsov, "Probabilistic Data Structures for Web Analytics and Data Mining", Highly Scalable Blog, http://highlyscalable.wordpress.com/2012/05/01/probabilistic-structures-web-analytics-data-mining/
"stream-lib", https://github.com/addthis/stream-lib/
"Iterated logarithm", Wikipedia, http://en.wikipedia.org/wiki/Iterated_logarithm