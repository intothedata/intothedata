+++
title = "엑셀에서 빅쿼리 데이터 가져오기"
type = "docs"
+++

## 엑셀에서 빅쿼리 데이터 가져오기

### 개요

Microsoft Excel에서 Google BigQuery의 데이터를 직접 가져와서 분석할 수 있는 방법을 설명한다. BigQuery는 대용량 데이터 분석에 특화된 클라우드 데이터 웨어하우스이지만, 많은 비즈니스 사용자들이 익숙한 Excel 환경에서도 이 데이터를 활용할 수 있다.

이 가이드에서는 Windows용 Excel의 파워피봇(PowerPivot) 기능과 ODBC 드라이버를 사용하여 BigQuery와 연결하는 방법을 단계별로 설명한다. 이를 통해 SQL 쿼리 없이도 Excel의 친숙한 인터페이스에서 BigQuery의 강력한 데이터 처리 능력을 활용할 수 있다.

**주요 장점:**
- Excel의 익숙한 환경에서 BigQuery 데이터 활용 가능
- 실시간 데이터 연결을 통한 최신 정보 반영
- Excel의 피벗테이블, 차트 등 분석 도구와 연계 활용
- 비개발자도 쉽게 빅데이터 분석 수행 가능

**필요 조건:**
- Windows용 Excel (Office 365 ProPlus 이상 권장)
- Google Cloud Platform 계정 및 BigQuery 프로젝트
- 관리자 권한으로 ODBC 드라이버 설치 가능한 환경


### 작업

이 가이드는 Windows용 Excel을 기준으로 작성되었으며, Mac에서는 테스트하지 않았습니다.

### 1. ODBC 드라이버 설치

**드라이버 다운로드**
- Google BigQuery는 Simba에서 제작한 ODBC 드라이버를 라이센싱하여 제공합니다
- [Google Cloud BigQuery ODBC 드라이버 페이지](https://cloud.google.com/bigquery/partners/simba-drivers/)에서 다운로드

**중요 사항**
- Windows 64bit 환경이라도 **32bit와 64bit 드라이버를 모두 설치**해야 합니다
- 하나만 설치할 경우 Excel 연결 시 아키텍처 불일치 오류가 발생할 수 있습니다

### 2. BigQuery 접속 키 생성

**Google Cloud Console 접속**
1. Google Cloud Console 대시보드에 접속
2. 상단 좌측에서 사용할 프로젝트 선택

**서비스 계정 키 생성**
1. 좌측 메뉴에서 "IAM 및 관리자" → "서비스 계정" 선택
2. 기존 서비스 계정을 선택하거나 새로 생성
3. 서비스 계정 우측의 점 3개 메뉴에서 "키 만들기" 선택
4. **P12 형식**을 선택 (ODBC는 아직 JSON을 지원하지 않음)
5. 생성된 .p12 파일을 안전한 위치에 저장
6. 서비스 계정의 이메일 주소를 복사해 둡니다

### 3. ODBC 데이터 원본 설정

**ODBC 관리자 실행**
1. Windows 시작 메뉴에서 "ODBC 데이터 원본 설정(64비트)" 검색 후 실행
2. "시스템 DSN" 탭에서 "추가" 버튼 클릭
3. "Simba ODBC Driver for Google BigQuery" 선택

**연결 설정**
- **데이터 원본 이름**: 적절한 이름 입력 (예: BIGQUERY-CONNECTION)
- **Email**: 앞서 복사한 서비스 계정 이메일 주소
- **Key file path**: 다운로드한 .p12 파일 경로 선택
- **Catalog (Project)**: 드롭다운에서 BigQuery 프로젝트 선택

**연결 테스트**
- Catalog 드롭다운에서 프로젝트 목록이 정상적으로 표시되면 설정 완료
- 목록이 보이지 않거나 오류 발생 시 앞의 단계들을 다시 확인

### 4. Excel에서 데이터 가져오기

**파워피봇 접속**
1. Excel에서 "파워피봇" 탭 선택 (Office 365 ProPlus 이상에서만 사용 가능)
2. "관리" 버튼을 클릭하여 파워피봇 창 열기

**데이터 원본 연결**
1. 파워피봇에서 "홈" 탭의 "외부 데이터 가져오기" 그룹에서 "기타 원본" 클릭
2. "OLEDB/ODBC" 선택
3. 앞서 생성한 ODBC 데이터 원본 선택
4. 연결 테스트 후 "다음" 클릭

**쿼리 작성**
1. "쿼리 작성" 옵션 선택 (테이블 전체 가져오기보다 권장)
2. SQL 쿼리 작성 창에서 BigQuery 쿼리 입력

### 5. BigQuery 쿼리 작성 및 수정

**쿼리 작성 팁**
- Google Cloud Console의 BigQuery 웹 인터페이스에서 먼저 쿼리를 작성하고 테스트하는 것을 권장
- Excel의 ODBC 연결에서는 표준 SQL 문법을 사용해야 함

**문법 수정 사항**
BigQuery 웹 콘솔의 쿼리를 Excel에서 사용하려면 다음과 같이 수정:

**수정 전 (BigQuery 레거시 SQL):**