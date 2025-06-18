+++
title = "데이터베이스 데이터 수집"
type = "docs"
weight = 1025
+++

## 데이터베이스 데이터 수집

### 개요

데이터베이스는 구조화된 데이터의 저장소로서, 기업의 핵심 데이터 자산이 저장된 곳이다. 데이터베이스에서의 데이터 수집은 SQL을 통한 쿼리 실행, API 연동, ETL 프로세스 등 다양한 방법으로 이루어지며, 데이터의 정확성과 일관성이 보장된다는 장점이 있다.

### 관계형 데이터베이스 (RDBMS)

#### 주요 RDBMS 종류
- **MySQL**: 오픈소스, 웹 애플리케이션에 널리 사용
- **PostgreSQL**: 고급 기능 지원, 복잡한 쿼리 처리에 적합
- **Oracle**: 대기업용, 높은 성능과 안정성
- **SQL Server**: Microsoft 생태계 통합
- **SQLite**: 경량화, 모바일 및 임베디드 시스템

#### Python을 통한 데이터베이스 연결

**MySQL 연결**
```python
import mysql.connector
import pandas as pd

def connect_mysql(host, user, password, database):
    try:
        connection = mysql.connector.connect(
            host=host,
            user=user,
            password=password,
            database=database
        )
        return connection
    except Exception as e:
        print(f"MySQL 연결 오류: {e}")
        return None

def fetch_data_from_mysql(connection, query):
    try:
        df = pd.read_sql(query, connection)
        return df
    except Exception as e:
        print(f"데이터 조회 오류: {e}")
        return None

# 사용 예시
conn = connect_mysql('localhost', 'user', 'password', 'mydb')
query = "SELECT * FROM customers WHERE created_date >= '2023-01-01'"
data = fetch_data_from_mysql(conn, query)
```

**PostgreSQL 연결**
```python
import psycopg2
import pandas as pd
from sqlalchemy import create_engine

def connect_postgresql(host, port, database, user, password):
    try:
        engine = create_engine(
            f'postgresql://{user}:{password}@{host}:{port}/{database}'
        )
        return engine
    except Exception as e:
        print(f"PostgreSQL 연결 오류: {e}")
        return None

def execute_query(engine, query):
    try:
        with engine.connect() as connection:
            df = pd.read_sql(query, connection)
        return df
    except Exception as e:
        print(f"쿼리 실행 오류: {e}")
        return None

# 사용 예시
engine = connect_postgresql('localhost', 5432, 'mydb', 'user', 'password')
query = """
SELECT 
    product_id,
    product_name,
    category,
    price,
    stock_quantity
FROM products 
WHERE category IN ('Electronics', 'Books')
ORDER BY price DESC
"""
products = execute_query(engine, query)
```

#### R을 통한 데이터베이스 연결

**DBI 패키지 활용**
```r
library(DBI)
library(RMySQL)
library(dplyr)

# MySQL 연결
con <- dbConnect(
  RMySQL::MySQL(),
  host = "localhost",
  user = "username",
  password = "password",
  dbname = "database_name"
)

# 쿼리 실행
query <- "
SELECT 
  customer_id,
  order_date,
  total_amount,
  status
FROM orders 
WHERE order_date >= '2023-01-01'
"

orders <- dbGetQuery(con, query)

# 연결 종료
dbDisconnect(con)
```

**RODBC 패키지 활용**
```r
library(RODBC)

# ODBC 연결
channel <- odbcDriverConnect("
  Driver={SQL Server};
  Server=localhost;
  Database=SalesDB;
  Trusted_Connection=yes;
")

# 데이터 조회
sales_data <- sqlQuery(channel, "
  SELECT 
    s.sale_id,
    s.sale_date,
    s.amount,
    c.customer_name,
    p.product_name
  FROM sales s
  JOIN customers c ON s.customer_id = c.customer_id
  JOIN products p ON s.product_id = p.product_id
  WHERE s.sale_date >= DATEADD(month, -6, GETDATE())
")

# 연결 종료
odbcClose(channel)
```

### NoSQL 데이터베이스

#### MongoDB 연결 및 데이터 수집

**Python을 통한 MongoDB 접근**
```python
from pymongo import MongoClient
import pandas as pd
from datetime import datetime

def connect_mongodb(uri, database_name):
    try:
        client = MongoClient(uri)
        db = client[database_name]
        return db
    except Exception as e:
        print(f"MongoDB 연결 오류: {e}")
        return None

def query_mongodb(db, collection_name, query, limit=1000):
    try:
        collection = db[collection_name]
        cursor = collection.find(query).limit(limit)
        data = list(cursor)
        return pd.DataFrame(data)
    except Exception as e:
        print(f"MongoDB 쿼리 오류: {e}")
        return None

# 사용 예시
db = connect_mongodb('mongodb://localhost:27017/', 'ecommerce')

# 최근 30일 주문 데이터 조회
from datetime import datetime, timedelta
thirty_days_ago = datetime.now() - timedelta(days=30)

query = {
    'order_date': {'$gte': thirty_days_ago},
    'status': {'$in': ['completed', 'shipped']}
}

orders = query_mongodb(db, 'orders', query)
```

**Aggregation Pipeline 활용**
```python
def aggregate_mongodb(db, collection_name, pipeline):
    try:
        collection = db[collection_name]
        result = list(collection.aggregate(pipeline))
        return pd.DataFrame(result)
    except Exception as e:
        print(f"Aggregation 오류: {e}")
        return None

# 월별 매출 집계
pipeline = [
    {
        '$match': {
            'order_date': {'$gte': datetime(2023, 1, 1)}
        }
    },
    {
        '$group': {
            '_id': {
                'year': {'$year': '$order_date'},
                'month': {'$month': '$order_date'}
            },
            'total_sales': {'$sum': '$amount'},
            'order_count': {'$sum': 1}
        }
    },
    {
        '$sort': {'_id.year': 1, '_id.month': 1}
    }
]

monthly_sales = aggregate_mongodb(db, 'orders', pipeline)
```

#### Redis 데이터 수집
```python
import redis
import json
import pandas as pd

def connect_redis(host='localhost', port=6379, db=0):
    try:
        r = redis.Redis(host=host, port=port, db=db, decode_responses=True)
        return r
    except Exception as e:
        print(f"Redis 연결 오류: {e}")
        return None

def get_user_sessions(redis_client, pattern='session:*'):
    try:
        keys = redis_client.keys(pattern)
        sessions = []
        
        for key in keys:
            session_data = redis_client.hgetall(key)
            sessions.append(session_data)
        
        return pd.DataFrame(sessions)
    except Exception as e:
        print(f"Redis 데이터 조회 오류: {e}")
        return None

# 사용 예시
r = connect_redis()
user_sessions = get_user_sessions(r)
```

### 빅데이터 플랫폼

#### Apache Spark를 통한 데이터 수집

**PySpark 활용**
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, when, count, avg, sum as spark_sum

def create_spark_session(app_name="DataCollection"):
    try:
        spark = SparkSession.builder \
            .appName(app_name) \
            .config("spark.sql.adaptive.enabled", "true") \
            .config("spark.sql.adaptive.coalescePartitions.enabled", "true") \
            .getOrCreate()
        return spark
    except Exception as e:
        print(f"Spark 세션 생성 오류: {e}")
        return None

def read_from_database(spark, jdbc_url, table, properties):
    try:
        df = spark.read \
            .format("jdbc") \
            .option("url", jdbc_url) \
            .option("dbtable", table) \
            .options(**properties) \
            .load()
        return df
    except Exception as e:
        print(f"데이터베이스 읽기 오류: {e}")
        return None

# 사용 예시
spark = create_spark_session()

jdbc_url = "jdbc:postgresql://localhost:5432/mydb"
properties = {
    "user": "username",
    "password": "password",
    "driver": "org.postgresql.Driver"
}

# 대용량 테이블 읽기
large_table = read_from_database(spark, jdbc_url, "large_transactions", properties)

# 데이터 집계
summary = large_table \
    .groupBy("category") \
    .agg(
        count("*").alias("transaction_count"),
        spark_sum("amount").alias("total_amount"),
        avg("amount").alias("avg_amount")
    ) \
    .orderBy(col("total_amount").desc())

summary.show()
```

**Delta Lake 활용**
```python
from delta.tables import DeltaTable

def read_delta_table(spark, delta_path):
    try:
        df = spark.read.format("delta").load(delta_path)
        return df
    except Exception as e:
        print(f"Delta 테이블 읽기 오류: {e}")
        return None

def incremental_data_collection(spark, delta_path, checkpoint_column, last_checkpoint):
    try:
        df = spark.read.format("delta").load(delta_path)
        incremental_df = df.filter(col(checkpoint_column) > last_checkpoint)
        return incremental_df
    except Exception as e:
        print(f"증분 데이터 수집 오류: {e}")
        return None

# 증분 데이터 수집
last_processed_timestamp = "2023-12-01 00:00:00"
new_data = incremental_data_collection(
    spark, 
    "/path/to/delta/table", 
    "updated_at", 
    last_processed_timestamp
)
```

### 데이터 웨어하우스

#### Amazon Redshift 연결
```python
import redshift_connector
import pandas as pd

def connect_redshift(host, port, database, user, password):
    try:
        conn = redshift_connector.connect(
            host=host,
            port=port,
            database=database,
            user=user,
            password=password
        )
        return conn
    except Exception as e:
        print(f"Redshift 연결 오류: {e}")
        return None

def query_redshift(connection, query):
    try:
        df = pd.read_sql_query(query, connection)
        return df
    except Exception as e:
        print(f"Redshift 쿼리 오류: {e}")
        return None

# 복잡한 분석 쿼리 실행
complex_query = """
WITH monthly_metrics AS (
    SELECT 
        DATE_TRUNC('month', order_date) as month,
        customer_segment,
        COUNT(*) as order_count,
        SUM(total_amount) as revenue,
        AVG(total_amount) as avg_order_value
    FROM orders o
    JOIN customers c ON o.customer_id = c.customer_id
    WHERE order_date >= DATEADD(month, -12, CURRENT_DATE)
    GROUP BY 1, 2
),
growth_metrics AS (
    SELECT 
        *,
        LAG(revenue, 1) OVER (PARTITION BY customer_segment ORDER BY month) as prev_month_revenue,
        revenue / LAG(revenue, 1) OVER (PARTITION BY customer_segment ORDER BY month) - 1 as growth_rate
    FROM monthly_metrics
)
SELECT * FROM growth_metrics
ORDER BY month DESC, customer_segment
"""

conn = connect_redshift('cluster-endpoint', 5439, 'mydb', 'user', 'pass')
results = query_redshift(conn, complex_query)
```

#### Google BigQuery 연결
```python
from google.cloud import bigquery
import pandas as pd

def create_bigquery_client(project_id, credentials_path=None):
    try:
        if credentials_path:
            client = bigquery.Client.from_service_account_json(
                credentials_path, 
                project=project_id
            )
        else:
            client = bigquery.Client(project=project_id)
        return client
    except Exception as e:
        print(f"BigQuery 클라이언트 생성 오류: {e}")
        return None

def query_bigquery(client, query):
    try:
        query_job = client.query(query)
        results = query_job.result()
        df = results.to_dataframe()
        return df
    except Exception as e:
        print(f"BigQuery 쿼리 오류: {e}")
        return None

# BigQuery 표준 SQL 사용
bq_query = """
SELECT 
    FORMAT_DATE('%Y-%m', date) as month,
    country,
    SUM(sessions) as total_sessions,
    SUM(pageviews) as total_pageviews,
    SUM(transactions) as total_transactions,
    SUM(revenue) as total_revenue
FROM `project.dataset.ga_sessions`
WHERE date >= DATE_SUB(CURRENT_DATE(), INTERVAL 6 MONTH)
GROUP BY 1, 2
ORDER BY 1 DESC, total_revenue DESC
"""

client = create_bigquery_client('my-project-id')
ga_data = query_bigquery(client, bq_query)
```

### 데이터 수집 최적화

#### 파티셔닝 활용
```python
def partition_aware_query(engine, table_name, partition_column, start_date, end_date):
    """
    파티션을 고려한 효율적인 쿼리
    """
    query = f"""
    SELECT *
    FROM {table_name}
    WHERE {partition_column} >= '{start_date}'
      AND {partition_column} < '{end_date}'
    """
    
    return pd.read_sql(query, engine)

# 파티션별 병렬 처리
from concurrent.futures import ThreadPoolExecutor
from datetime import datetime, timedelta

def parallel_partition_query(engine, table_name, partition_column, start_date, end_date, chunk_days=7):
    """
    파티션별 병렬 쿼리 실행
    """
    current_date = start_date
    date_ranges = []
    
    while current_date < end_date:
        next_date = min(current_date + timedelta(days=chunk_days), end_date)
        date_ranges.append((current_date, next_date))
        current_date = next_date
    
    def query_partition(date_range):
        return partition_aware_query(engine, table_name, partition_column, 
                                   date_range[0], date_range[1])
    
    with ThreadPoolExecutor(max_workers=4) as executor:
        results = list(executor.map(query_partition, date_ranges))
    
    return pd.concat(results, ignore_index=True)
```

#### 쿼리 최적화
```python
def analyze_query_performance(engine, query):
    """
    쿼리 성능 분석
    """
    import time
    
    # 실행 계획 분석
    explain_query = f"EXPLAIN ANALYZE {query}"
    
    start_time = time.time()
    with engine.connect() as conn:
        # 실행 계획 확인
        explain_result = conn.execute(explain_query)
        print("실행 계획:")
        for row in explain_result:
            print(row)
        
        # 실제 쿼리 실행
        result = pd.read_sql(query, conn)
    
    end_time = time.time()
    execution_time = end_time - start_time
    
    print(f"실행 시간: {execution_time:.2f}초")
    print(f"결과 행 수: {len(result)}")
    print(f"메모리 사용량: {result.memory_usage(deep=True).sum() / 1024 / 1024:.2f} MB")
    
    return result
```

#### 인덱스 활용
```sql
-- 효율적인 인덱스 생성 예시
CREATE INDEX idx_orders_date_customer 
ON orders (order_date, customer_id);

-- 복합 인덱스를 활용한 쿼리
CREATE INDEX idx_products_category_price 
ON products (category, price DESC);

-- 부분 인덱스 (조건부 인덱스)
CREATE INDEX idx_active_users 
ON users (last_login_date) 
WHERE status = 'active';
```

### 실시간 데이터 수집

#### Change Data Capture (CDC)
```python
import time
from datetime import datetime

class DatabaseCDC:
    def __init__(self, engine, table_name, timestamp_column):
        self.engine = engine
        self.table_name = table_name
        self.timestamp_column = timestamp_column
        self.last_sync = datetime.min
        
    def get_changes(self):
        """
        마지막 동기화 이후 변경된 데이터 조회
        """
        query = f"""
        SELECT *
        FROM {self.table_name}
        WHERE {self.timestamp_column} > '{self.last_sync}'
        ORDER BY {self.timestamp_column}
        """
        
        changes = pd.read_sql(query, self.engine)
        
        if not changes.empty:
            self.last_sync = changes[self.timestamp_column].max()
        
        return changes
    
    def start_monitoring(self, callback_function, interval=60):
        """
        주기적으로 변경사항 모니터링
        """
        while True:
            try:
                changes = self.get_changes()
                if not changes.empty:
                    callback_function(changes)
                
                time.sleep(interval)
            except Exception as e:
                print(f"CDC 오류: {e}")
                time.sleep(interval)

def process_changes(changes):
    """
    변경된 데이터 처리
    """
    print(f"새로운 변경사항 {len(changes)}건 감지")
    # 데이터 처리 로직
    pass

# CDC 시작
cdc = DatabaseCDC(engine, 'orders', 'updated_at')
cdc.start_monitoring(process_changes, interval=30)
```

#### 데이터베이스 트리거 활용
```sql
-- 데이터 변경 로그 테이블 생성
CREATE TABLE data_change_log (
    log_id SERIAL PRIMARY KEY,
    table_name VARCHAR(100),
    operation VARCHAR(10),
    record_id INT,
    changed_data JSONB,
    change_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 트리거 함수 생성
CREATE OR REPLACE FUNCTION log_data_changes()
RETURNS TRIGGER AS $$
BEGIN
    IF TG_OP = 'INSERT' THEN
        INSERT INTO data_change_log (table_name, operation, record_id, changed_data)
        VALUES (TG_TABLE_NAME, TG_OP, NEW.id, row_to_json(NEW));
        RETURN NEW;
    ELSIF TG_OP = 'UPDATE' THEN
        INSERT INTO data_change_log (table_name, operation, record_id, changed_data)
        VALUES (TG_TABLE_NAME, TG_OP, NEW.id, row_to_json(NEW));
        RETURN NEW;
    ELSIF TG_OP = 'DELETE' THEN
        INSERT INTO data_change_log (table_name, operation, record_id, changed_data)
        VALUES (TG_TABLE_NAME, TG_OP, OLD.id, row_to_json(OLD));
        RETURN OLD;
    END IF;
    RETURN NULL;
END;
$$ LANGUAGE plpgsql;

-- 트리거 생성
CREATE TRIGGER customers_change_trigger
    AFTER INSERT OR UPDATE OR DELETE
    ON customers
    FOR EACH ROW
    EXECUTE FUNCTION log_data_changes();
```

### 데이터 품질 모니터링

#### 데이터 프로파일링
```python
def profile_database_table(engine, table_name):
    """
    데이터베이스 테이블 프로파일링
    """
    # 기본 정보 조회
    basic_info_query = f"""
    SELECT 
        COUNT(*) as total_rows,
        COUNT(DISTINCT *) as unique_rows
    FROM {table_name}
    """
    
    basic_info = pd.read_sql(basic_info_query, engine)
    
    # 컬럼별 정보 조회
    column_info_query = f"""
    SELECT 
        column_name,
        data_type,
        is_nullable
    FROM information_schema.columns
    WHERE table_name = '{table_name}'
    """
    
    column_info = pd.read_sql(column_info_query, engine)
    
    # 각 컬럼의 통계 정보
    stats = {}
    for column in column_info['column_name']:
        stats_query = f"""
        SELECT 
            COUNT(*) as total_count,
            COUNT({column}) as non_null_count,
            COUNT(DISTINCT {column}) as unique_count
        FROM {table_name}
        """
        
        column_stats = pd.read_sql(stats_query, engine)
        stats[column] = column_stats.iloc[0].to_dict()
    
    return {
        'basic_info': basic_info,
        'column_info': column_info,
        'column_stats': stats
    }
```

#### 데이터 품질 체크
```python
def check_data_quality(engine, table_name, quality_rules):
    """
    데이터 품질 검사
    """
    quality_report = {}
    
    for rule_name, rule_query in quality_rules.items():
        try:
            result = pd.read_sql(rule_query.format(table_name=table_name), engine)
            quality_report[rule_name] = result
        except Exception as e:
            quality_report[rule_name] = f"오류: {e}"
    
    return quality_report

# 품질 규칙 정의
quality_rules = {
    'null_check': """
        SELECT 
            column_name,
            COUNT(*) as null_count,
            COUNT(*) * 100.0 / (SELECT COUNT(*) FROM {table_name}) as null_percentage
        FROM (
            SELECT column_name
            FROM information_schema.columns
            WHERE table_name = '{table_name}'
        ) cols
        CROSS JOIN {table_name}
        WHERE column_name IS NULL
        GROUP BY column_name
    """,
    'duplicate_check': """
        SELECT COUNT(*) as duplicate_count
        FROM (
            SELECT *, COUNT(*) as cnt
            FROM {table_name}
            GROUP BY *
            HAVING COUNT(*) > 1
        ) dups
    """
}

quality_report = check_data_quality(engine, 'customers', quality_rules)
```

데이터베이스에서의 데이터 수집은 구조화된 데이터에 대한 안정적이고 효율적인 접근을 제공한다. 적절한 연결 관리, 쿼리 최적화, 품질 모니터링을 통해 신뢰할 수 있는 데이터 수집 파이프라인을 구축할 수 있다. 