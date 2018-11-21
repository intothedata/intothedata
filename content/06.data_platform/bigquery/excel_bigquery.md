+++
title = "엑셀에서 빅쿼리 데이터 가져오기"
+++

Windows용 Excel을 기준으로 했고 Mac에서는 확인하지 않았다.
Windows용 Excel의 파워피봇(Powerpivot)에서 연결할 것이다.
파워피봇은 Excel에 내장된 데이터베이스 및 빅데이터 플랫폼, 클라우드 플랫폼에 연동을 쉽게 할 수 있도록 도와주는 도구인데
Office 365 ProPlus 이상의 버전에서만 활성화되는 기능이다.
파워피봇이 아니어도 데이터를 불어 올 수는 있지만 그냥 편하게 살기 위해서 파워피봇을 쓰기로 한다.
진행
ODBC 드라이버 설치
먼저 ODBC를 다운로드 받는다. Google Bigquery는 자체 제작한 ODBC 드라이버가 없고 Simba(ODBC, JDBC를 전문으로 만드는 회사)에서 제작한 ODBC를 라이센싱해서 제공하고 있다.
구글 클라우드로 가서 Bigquery용 Simba ODBC 드라이버를 다운로드 받아서 설치한다.
주의할 것은 사용하고 있는 Windows OS가 64bit라고 해도 ODBC 64bit, 32bit 2개를 모두 받아서 설치해야 한다는 점이다.
그렇지 않으면 엑셀에서 연결할 때 아키텍쳐 불일치 오류가 발생하는 경우가 많다.
https://cloud.google.com/bigquery/partners/simba-drivers/
위의 URL에 접속해서 ODBC driver 2개를 받아서 모두 설치한다.
Bigquery 접속용 키 가져오기
Bigquery에 접속하기 위해서 Key 다운로드 받아서 저장해 두어야 한다.
Key는 Google cloud console에서 다운로드 받을 수 있다.
Google cloud 대시보드 접속

대시보드에 접속한다. 대시보드에서 상단 왼쪽의 프로젝트를 선택해 준다.
IAM 및 관리자 접속

상단 좌측의 메뉴를 선택해서 "IAM 및 관리자"로 들어간다.
서비스 계정 접속

다시 메뉴에서 “서비스 계정”이란 것을 찾아서 들어가야 한다.
서비스 접속에 보면 사용자를 추가하거나 신규 생성할 수 있는데 만들거나 기존에 추가된 사용자 목록에서 오른쪽에 작은 번트를 눌러 “키 만들기”를 선택한다.
Bigquery 접속키 생성하기

구글 Bigquery는 현재 JSON key를 주로 사용하도록 권장하고 있지만 ODBC는 아직 P12방식을 사용해야 한다.
P12방식을 선택하면 키를 하나 생성해서 파일로 저장할 수 있게 해준다.
그 파일을 잘 보관한다.
파일의 확장자가 p12인 파일을 바로 다운로드 하게 된다.
그리고 키를 생성한 사용자의 이메일 주소를 복사해둔다. 이 이메일 주소는 접속계정이 된다.
ODBC 설정하기
Windows의 시작메뉴에서 “ODBC 데이터 원본 설정(64비트)" 또는 "ODBC 데이터 원본 설정(32비트)"를 찾아서 연다.

위 화면에는 이미 만들어진 것이 2개 보이는데 1개만 만들면 된다.
만들고 난 후에 플랫폼에 32/64비트로 되어 있어야 한다. 만약 ODBC 드라이버를 64비트만 설치하면 플랫폼에 64비트만 선택되어 보여지는데 그렇게 되면 Bigquery에 접속할 수 없다.

ODBC 드라이버는 당연히 Simba ODBC Driver for Google BigQuery를 선택한다.

이제 여기를 잘해야 하는데
ODBC 연결에 적당한 이름을 적어주고 Email에는 앞에서 작업했던 접속 계정의 이메일 주소를 복사 넣고
Key file path에는 아까 다운로드 받은 p12 파일의 경로를 선택해준다.
그리고 나서 하단의 Catalog (Project)에서 오른쪽 아래화살표를 누르게 되면
접속하려고 하는 Bigquery에 있는 Catalog(일종의 데이터베이스)의 목록을 보여주며 선택하도록 하는데
여기에서 목록이 보이지 않고 에러가 발생한다면 위의 계정중에 뭔가를 잘못한 것이므로 처음부터 다시 점검해야 한다.
여기에서 사용하는 예제에서는 목록에 BIGQUERY-BIGDATA-32BIT, BIGQUERY-BIGDATA-64BIT를 따로 만들어 둔 것이 보이는데
사실 2개를 따로 만들 필요는 없다.
이 예제에서는 BIGQUERY-BIGDATA-64BIT만을 사용할 것이다.
Excel에서 데이터 불러오기
파워피봇 들어가기

자신이 사용하는 엑셀에 파워피봇 메뉴가 없다면 앞서 말했듯이 등급이 낮은 Office제품을 사용하고 있기 때문이다.
그문제는 금전적인 문제이므로 알아서 해결한다.
파워피봇에서 데이터 소스 선택하기

위의 화면에서 처럼 “기타 원본” 버튼을 누르고 OLEDB/ODBC를 선택한다.
ODBC연결 설정하기

자 위와 같이 "ODBC 데이터 원본 설정"에서 만들어 둔 BIGQUERY-BIGDATA-64BIT를 선택하고
아래쪽에서 Catalog를 선택할 수 있으면 문제가 없는 것이다.

가져올 데이터가 테이블에 들어 있어서 통째로 가져오기만 해도 되면 위의 테이블을 선택해서 하면되지만
데이터마트에서 가져오는 것이 아니면 그럴일이 없다.
Bigquery를 접속해서 사용한다면 SQL을 작성해야 하는 경우가 많을 것이다.
이 예제에서도 “쿼리 작성”을 선택해서 들어간 후 SQL을 적성할 것이다.
Excel에서 Bigquery 쿼리 작성

엑셀에서 Bigquery를 위한 Query를 작성하는 것은 번거롭다. Google Cloud Bigquery에 웹브라우저로 작성해서 구글에서 제공하는 Query tool로 작성하기 바란다.
Excel 창을 열어둔 상태에서 Bigquery에 접속해서 Query를 작성한다.
Google Bigquery는 사용법은 편하다면 편하고 불편하다면 불편하다. 
넣어둔 데이터가 없기 때문에 샘플 테이블에 있는 세익스피어(shakespeare)데이터로 매우 간단하고 쓸모없는 query를 다음과 같이 작성했다.
단어별로 카운트를 합하는 단순 집계 query이다.
SELECT
  word,
  SUM(word_count)
FROM
  [bigquery-public-data:samples.shakespeare]
GROUP BY
  word
ORDER BY
  2 DESC
LIMIT
  100
위 쿼리를 창에 복사해 넣은 후 “유효성 검사”를 수행하면 실패한다.
Bigquery에서의 문법이 표준SQL과 맞지 않기 때문에 생기는 문제다.
위의 쿼리중 문제가 되는 부분은 대괄호와 데이터세트에 콜론이 포함되어 있기 때문이다.
이걸 손으로 고쳐줘야 한다.
Bigquery 쿼리 수정하기
 

자 아래와 같이 코드를 간단하게 수정한다.
대괄호는 백틱(`)기호로 바꾸고 콜론은 마침표로 바꿔주면 된다.
SELECT
  word,
  SUM(word_count)
FROM
  `bigquery-public-data.samples.shakespeare`
GROUP BY
  word
ORDER BY
  2 DESC
LIMIT
  100
그리고 나서 유효성 검사를 다시 해본다.

문제가 없는 것으로 나온다.

이제 계속해서 다음 버튼을 눌러 진행을 한다.

가져온 데이터가 파워피봇에서 보일텐데 상단의 “피벗 테이블”버튼을 누르면 간단하게 이 데이터를 엑셀의 시트에 피봇테이블로 넣을 수 있다.

피봇테이블로 가져온 데이터는 위의 화면과 같다.
 