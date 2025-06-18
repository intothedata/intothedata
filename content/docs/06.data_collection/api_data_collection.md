+++
title = "API 데이터 수집"
type = "docs"
weight = 1015
+++

## API를 통한 데이터 수집

### 개요

API (Application Programming Interface)는 애플리케이션 간의 데이터 교환을 위한 규칙과 프로토콜의 집합이다. API를 통한 데이터 수집은 웹 스크래핑보다 안정적이고 구조화된 데이터를 제공받을 수 있어, 현대 데이터 수집의 핵심 방법이 되었다.

### API의 장점

#### 구조화된 데이터
- JSON, XML 등 표준 형식으로 데이터 제공
- 일관된 데이터 스키마 보장
- 파싱 오류 위험 최소화

#### 안정성과 신뢰성
- 공식적으로 지원되는 데이터 접근 방법
- 버전 관리를 통한 하위 호환성 보장
- 오류 처리 및 상태 코드 제공

#### 실시간성
- 최신 데이터에 즉시 접근 가능
- 실시간 알림 및 웹훅 지원
- 스트리밍 API를 통한 실시간 데이터 처리

#### 법적 안전성
- 공식적으로 허용된 데이터 접근 방법
- 명확한 이용약관 및 제한사항
- 개발자 친화적인 문서화

### REST API 기초

#### HTTP 메서드
- **GET**: 데이터 조회
- **POST**: 새로운 데이터 생성
- **PUT**: 기존 데이터 전체 수정
- **PATCH**: 기존 데이터 부분 수정
- **DELETE**: 데이터 삭제

#### 상태 코드
- **200 OK**: 요청 성공
- **201 Created**: 리소스 생성 성공
- **400 Bad Request**: 잘못된 요청
- **401 Unauthorized**: 인증 실패
- **403 Forbidden**: 권한 없음
- **404 Not Found**: 리소스 없음
- **429 Too Many Requests**: 요청 한도 초과
- **500 Internal Server Error**: 서버 오류

### Python을 활용한 API 데이터 수집

#### 기본 요청 처리
```python
import requests
import json

# 기본 GET 요청
response = requests.get('https://api.example.com/users')

# 응답 상태 확인
if response.status_code == 200:
    data = response.json()
    print(json.dumps(data, indent=2))
else:
    print(f'Error: {response.status_code}')
```

#### 헤더와 인증 처리
```python
# API 키 인증
headers = {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json'
}

response = requests.get(
    'https://api.example.com/data',
    headers=headers
)

# OAuth 2.0 인증
import requests_oauthlib

# OAuth 토큰 획득
oauth = requests_oauthlib.OAuth2Session(
    client_id='your_client_id',
    redirect_uri='your_redirect_uri'
)

authorization_url, state = oauth.authorization_url(
    'https://api.example.com/oauth/authorize'
)
```

#### 페이지네이션 처리
```python
def fetch_all_pages(base_url, headers, params=None):
    all_data = []
    page = 1
    
    while True:
        current_params = params.copy() if params else {}
        current_params['page'] = page
        current_params['per_page'] = 100
        
        response = requests.get(base_url, headers=headers, params=current_params)
        
        if response.status_code != 200:
            break
            
        data = response.json()
        items = data.get('items', [])
        
        if not items:
            break
            
        all_data.extend(items)
        page += 1
        
        # 마지막 페이지 확인
        if page > data.get('total_pages', page):
            break
    
    return all_data
```

### R을 활용한 API 데이터 수집

#### httr 패키지 활용
```r
library(httr)
library(jsonlite)

# GET 요청
response <- GET("https://api.example.com/data",
                add_headers("Authorization" = "Bearer YOUR_API_KEY"))

# 응답 처리
if (status_code(response) == 200) {
  content <- content(response, "text", encoding = "UTF-8")
  data <- fromJSON(content)
  print(data)
} else {
  cat("Error:", status_code(response), "\n")
}
```

#### 반복 요청 처리
```r
fetch_paginated_data <- function(base_url, headers, max_pages = 100) {
  all_data <- list()
  page <- 1
  
  while (page <= max_pages) {
    url <- paste0(base_url, "?page=", page, "&per_page=100")
    response <- GET(url, add_headers(.headers = headers))
    
    if (status_code(response) != 200) {
      break
    }
    
    data <- fromJSON(content(response, "text"))
    
    if (length(data$items) == 0) {
      break
    }
    
    all_data[[page]] <- data$items
    page <- page + 1
  }
  
  return(do.call(rbind, all_data))
}
```

### 주요 공개 API 활용

#### 정부 데이터 포털 (data.go.kr)
```python
import requests
import xml.etree.ElementTree as ET

# 공공데이터 API 호출
api_key = "YOUR_API_KEY"
base_url = "http://apis.data.go.kr/1360000/VilageFcstInfoService/getVilageFcst"

params = {
    'serviceKey': api_key,
    'pageNo': 1,
    'numOfRows': 100,
    'dataType': 'JSON',
    'base_date': '20231201',
    'base_time': '0500',
    'nx': 55,
    'ny': 127
}

response = requests.get(base_url, params=params)
weather_data = response.json()
```

#### 소셜미디어 API
```python
# Twitter API v2 활용
import tweepy

# 인증 설정
bearer_token = "YOUR_BEARER_TOKEN"
client = tweepy.Client(bearer_token=bearer_token)

# 트윗 검색
tweets = client.search_recent_tweets(
    query="데이터사이언스 -is:retweet lang:ko",
    max_results=100,
    tweet_fields=['created_at', 'author_id', 'public_metrics']
)

for tweet in tweets.data:
    print(f"{tweet.created_at}: {tweet.text}")
```

#### 금융 데이터 API
```python
import yfinance as yf
import pandas as pd

# Yahoo Finance API
# 주식 데이터 수집
ticker = "AAPL"
stock = yf.Ticker(ticker)

# 1년간 일별 데이터
hist = stock.history(period="1y")
print(hist.head())

# 기업 정보
info = stock.info
print(f"Company: {info['longName']}")
print(f"Sector: {info['sector']}")
```

### API 호출 최적화

#### 비동기 처리
```python
import asyncio
import aiohttp
import time

async def fetch_data(session, url):
    try:
        async with session.get(url) as response:
            return await response.json()
    except Exception as e:
        print(f"Error fetching {url}: {e}")
        return None

async def fetch_multiple_endpoints(urls):
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_data(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
    return results

# 사용 예시
urls = [
    "https://api.example.com/endpoint1",
    "https://api.example.com/endpoint2",
    "https://api.example.com/endpoint3"
]

# 비동기 실행
results = asyncio.run(fetch_multiple_endpoints(urls))
```

#### 캐싱 전략
```python
import requests
from functools import lru_cache
import time

class APIClient:
    def __init__(self, base_url, api_key, cache_ttl=300):
        self.base_url = base_url
        self.api_key = api_key
        self.cache_ttl = cache_ttl
        self._cache = {}
    
    def _is_cache_valid(self, cache_key):
        if cache_key not in self._cache:
            return False
        
        cached_time = self._cache[cache_key]['timestamp']
        return time.time() - cached_time < self.cache_ttl
    
    def get_data(self, endpoint, params=None):
        # 캐시 키 생성
        cache_key = f"{endpoint}_{str(params)}"
        
        # 캐시 확인
        if self._is_cache_valid(cache_key):
            return self._cache[cache_key]['data']
        
        # API 호출
        url = f"{self.base_url}/{endpoint}"
        headers = {'Authorization': f'Bearer {self.api_key}'}
        
        response = requests.get(url, headers=headers, params=params)
        data = response.json()
        
        # 캐시 저장
        self._cache[cache_key] = {
            'data': data,
            'timestamp': time.time()
        }
        
        return data
```

### Rate Limiting 대응

#### 지수 백오프 (Exponential Backoff)
```python
import time
import random
from requests.exceptions import RequestException

def api_request_with_backoff(url, headers, max_retries=5):
    for attempt in range(max_retries):
        try:
            response = requests.get(url, headers=headers)
            
            if response.status_code == 429:  # Too Many Requests
                # 재시도 간격 계산 (지수 백오프 + 지터)
                wait_time = (2 ** attempt) + random.uniform(0, 1)
                print(f"Rate limited. Waiting {wait_time:.2f} seconds...")
                time.sleep(wait_time)
                continue
            
            response.raise_for_status()
            return response.json()
            
        except RequestException as e:
            if attempt == max_retries - 1:
                raise e
            time.sleep(2 ** attempt)
    
    raise Exception("Max retries exceeded")
```

#### Token Bucket 알고리즘
```python
import time
import threading

class TokenBucket:
    def __init__(self, capacity, refill_rate):
        self.capacity = capacity
        self.tokens = capacity
        self.refill_rate = refill_rate
        self.last_refill = time.time()
        self.lock = threading.Lock()
    
    def consume(self, tokens=1):
        with self.lock:
            now = time.time()
            # 토큰 보충
            self.tokens = min(
                self.capacity,
                self.tokens + (now - self.last_refill) * self.refill_rate
            )
            self.last_refill = now
            
            if self.tokens >= tokens:
                self.tokens -= tokens
                return True
            return False
    
    def wait_for_token(self, tokens=1):
        while not self.consume(tokens):
            time.sleep(0.1)

# 사용 예시: 분당 60회 요청 제한
bucket = TokenBucket(capacity=60, refill_rate=1.0)

def rate_limited_request(url, headers):
    bucket.wait_for_token()
    return requests.get(url, headers=headers)
```

### 오류 처리 및 모니터링

#### 포괄적 오류 처리
```python
import logging
from requests.exceptions import Timeout, ConnectionError, HTTPError

class APIError(Exception):
    def __init__(self, message, status_code=None, response=None):
        self.message = message
        self.status_code = status_code
        self.response = response
        super().__init__(self.message)

def robust_api_call(url, headers, params=None, timeout=30):
    try:
        response = requests.get(
            url, 
            headers=headers, 
            params=params, 
            timeout=timeout
        )
        
        if response.status_code == 401:
            raise APIError("Authentication failed", response.status_code, response)
        elif response.status_code == 403:
            raise APIError("Access forbidden", response.status_code, response)
        elif response.status_code == 429:
            raise APIError("Rate limit exceeded", response.status_code, response)
        elif response.status_code >= 500:
            raise APIError("Server error", response.status_code, response)
        
        response.raise_for_status()
        return response.json()
        
    except Timeout:
        logging.error(f"Timeout error for URL: {url}")
        raise APIError("Request timeout")
    except ConnectionError:
        logging.error(f"Connection error for URL: {url}")
        raise APIError("Connection error")
    except HTTPError as e:
        logging.error(f"HTTP error for URL: {url}, Status: {e.response.status_code}")
        raise APIError(f"HTTP error: {e.response.status_code}")
    except Exception as e:
        logging.error(f"Unexpected error for URL: {url}, Error: {str(e)}")
        raise APIError(f"Unexpected error: {str(e)}")
```

#### 성능 모니터링
```python
import time
import psutil
from dataclasses import dataclass
from typing import Dict, List

@dataclass
class APIMetrics:
    endpoint: str
    response_time: float
    status_code: int
    data_size: int
    cpu_usage: float
    memory_usage: float
    timestamp: float

class APIMonitor:
    def __init__(self):
        self.metrics: List[APIMetrics] = []
    
    def measure_api_call(self, endpoint, func, *args, **kwargs):
        start_time = time.time()
        start_cpu = psutil.cpu_percent()
        start_memory = psutil.virtual_memory().percent
        
        try:
            result = func(*args, **kwargs)
            status_code = 200
            data_size = len(str(result)) if result else 0
        except APIError as e:
            status_code = e.status_code or 0
            data_size = 0
            result = None
        
        end_time = time.time()
        end_cpu = psutil.cpu_percent()
        end_memory = psutil.virtual_memory().percent
        
        metrics = APIMetrics(
            endpoint=endpoint,
            response_time=end_time - start_time,
            status_code=status_code,
            data_size=data_size,
            cpu_usage=end_cpu - start_cpu,
            memory_usage=end_memory - start_memory,
            timestamp=end_time
        )
        
        self.metrics.append(metrics)
        return result
    
    def get_performance_summary(self) -> Dict:
        if not self.metrics:
            return {}
        
        response_times = [m.response_time for m in self.metrics]
        success_count = len([m for m in self.metrics if m.status_code == 200])
        
        return {
            'total_requests': len(self.metrics),
            'success_rate': success_count / len(self.metrics),
            'avg_response_time': sum(response_times) / len(response_times),
            'max_response_time': max(response_times),
            'min_response_time': min(response_times)
        }
```

### 데이터 저장 및 관리

#### 데이터베이스 저장
```python
import sqlite3
import pandas as pd
from datetime import datetime

class APIDataManager:
    def __init__(self, db_path):
        self.db_path = db_path
        self.init_database()
    
    def init_database(self):
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        
        cursor.execute('''
            CREATE TABLE IF NOT EXISTS api_data (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                endpoint VARCHAR(255),
                data_json TEXT,
                collected_at TIMESTAMP,
                data_hash VARCHAR(64)
            )
        ''')
        
        conn.commit()
        conn.close()
    
    def save_data(self, endpoint, data):
        import hashlib
        import json
        
        # 데이터 해시 생성 (중복 확인용)
        data_str = json.dumps(data, sort_keys=True)
        data_hash = hashlib.sha256(data_str.encode()).hexdigest()
        
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        
        # 중복 확인
        cursor.execute(
            'SELECT id FROM api_data WHERE data_hash = ?',
            (data_hash,)
        )
        
        if cursor.fetchone() is None:
            cursor.execute('''
                INSERT INTO api_data (endpoint, data_json, collected_at, data_hash)
                VALUES (?, ?, ?, ?)
            ''', (endpoint, data_str, datetime.now(), data_hash))
            
            conn.commit()
            print(f"New data saved for endpoint: {endpoint}")
        else:
            print(f"Duplicate data ignored for endpoint: {endpoint}")
        
        conn.close()
    
    def load_data(self, endpoint=None, start_date=None, end_date=None):
        conn = sqlite3.connect(self.db_path)
        
        query = "SELECT * FROM api_data WHERE 1=1"
        params = []
        
        if endpoint:
            query += " AND endpoint = ?"
            params.append(endpoint)
        
        if start_date:
            query += " AND collected_at >= ?"
            params.append(start_date)
        
        if end_date:
            query += " AND collected_at <= ?"
            params.append(end_date)
        
        df = pd.read_sql_query(query, conn, params=params)
        conn.close()
        
        return df
```

### 실무 활용 팁

#### API 문서 분석 체크리스트
- [ ] 인증 방법 확인 (API Key, OAuth, JWT 등)
- [ ] Rate Limiting 정책 파악
- [ ] 응답 데이터 구조 이해
- [ ] 에러 코드 및 처리 방법 숙지
- [ ] 페이지네이션 방식 확인
- [ ] 필터링 및 정렬 옵션 파악
- [ ] 웹훅 또는 실시간 업데이트 지원 여부

#### 지속적인 모니터링
```python
# 정기적인 API 상태 체크
import schedule
import time

def health_check():
    try:
        response = requests.get('https://api.example.com/health')
        if response.status_code == 200:
            print(f"[{datetime.now()}] API is healthy")
        else:
            print(f"[{datetime.now()}] API issue: {response.status_code}")
    except Exception as e:
        print(f"[{datetime.now()}] API error: {e}")

# 5분마다 상태 체크
schedule.every(5).minutes.do(health_check)

while True:
    schedule.run_pending()
    time.sleep(1)
```

API를 통한 데이터 수집은 현대 데이터사이언스의 핵심 기술이다. 적절한 도구와 모범 사례를 활용하여 안정적이고 효율적인 데이터 수집 파이프라인을 구축할 수 있으며, 이는 고품질의 데이터 분석과 인사이트 도출의 기반이 된다. 