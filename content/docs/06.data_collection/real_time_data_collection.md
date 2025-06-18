+++
title = "실시간 데이터 수집"
type = "docs"
weight = 1045
+++

## 실시간 데이터 수집

### 개요

실시간 데이터 수집은 데이터가 생성되는 즉시 또는 최소한의 지연으로 데이터를 수집하고 처리하는 기술이다. 현대의 비즈니스 환경에서는 빠른 의사결정과 즉각적인 대응이 경쟁 우위의 핵심이 되고 있어, 실시간 데이터 처리 능력이 필수적이다.

### 실시간 수집의 특징

#### 저지연성 (Low Latency)
- 밀리초 단위의 응답 시간 요구
- 네트워크 최적화 및 효율적인 프로토콜 사용
- 메모리 기반 처리로 디스크 I/O 최소화

#### 높은 처리량 (High Throughput)
- 초당 수백만 건의 이벤트 처리
- 수평적 확장 가능한 아키텍처
- 병렬 처리 및 분산 시스템 활용

#### 내결함성 (Fault Tolerance)
- 시스템 장애 시에도 데이터 유실 방지
- 자동 복구 및 페일오버 메커니즘
- 데이터 복제 및 백업 전략

### 주요 기술 스택

#### Apache Kafka
```python
from kafka import KafkaProducer, KafkaConsumer
import json
import time

class RealTimeDataStreamer:
    def __init__(self, bootstrap_servers):
        self.producer = KafkaProducer(
            bootstrap_servers=bootstrap_servers,
            value_serializer=lambda v: json.dumps(v).encode('utf-8')
        )
        
    def send_real_time_event(self, topic, event_data):
        """실시간 이벤트 전송"""
        try:
            future = self.producer.send(topic, event_data)
            record_metadata = future.get(timeout=10)
            return record_metadata
        except Exception as e:
            print(f"이벤트 전송 실패: {e}")
            return None
    
    def stream_sensor_data(self, sensor_id, duration_seconds=60):
        """센서 데이터 스트리밍"""
        end_time = time.time() + duration_seconds
        
        while time.time() < end_time:
            sensor_data = {
                'sensor_id': sensor_id,
                'timestamp': time.time(),
                'temperature': random.uniform(20, 30),
                'humidity': random.uniform(40, 60)
            }
            
            self.send_real_time_event('sensor_data', sensor_data)
            time.sleep(1)  # 1초마다 데이터 전송
```

#### Apache Spark Streaming
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import *
from pyspark.sql.types import *

def create_real_time_processor():
    """실시간 데이터 처리기 생성"""
    spark = SparkSession.builder \
        .appName("RealTimeDataProcessor") \
        .config("spark.sql.streaming.checkpointLocation", "/tmp/checkpoint") \
        .getOrCreate()
    
    # Kafka 스트림 읽기
    df = spark \
        .readStream \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "localhost:9092") \
        .option("subscribe", "real_time_events") \
        .load()
    
    # JSON 데이터 파싱
    schema = StructType([
        StructField("event_id", StringType(), True),
        StructField("user_id", StringType(), True),
        StructField("event_type", StringType(), True),
        StructField("timestamp", TimestampType(), True),
        StructField("value", DoubleType(), True)
    ])
    
    events_df = df.select(
        from_json(col("value").cast("string"), schema).alias("data")
    ).select("data.*")
    
    # 실시간 집계
    aggregated_df = events_df \
        .withWatermark("timestamp", "10 minutes") \
        .groupBy(
            window(col("timestamp"), "1 minute"),
            col("event_type")
        ) \
        .agg(
            count("*").alias("event_count"),
            avg("value").alias("avg_value"),
            max("value").alias("max_value")
        )
    
    return aggregated_df
```

#### Apache Flink
```python
from pyflink.table import EnvironmentSettings, TableEnvironment
from pyflink.table.descriptors import Kafka, Json, Schema

def setup_flink_streaming():
    """Flink 스트리밍 환경 설정"""
    env_settings = EnvironmentSettings.new_instance().in_streaming_mode().build()
    table_env = TableEnvironment.create(env_settings)
    
    # Kafka 소스 테이블 정의
    table_env.connect(Kafka()
        .version("universal")
        .topic("user_events")
        .start_from_earliest()
        .property("bootstrap.servers", "localhost:9092")
    ).with_format(Json()
        .json_schema("{"
            "type: 'object',"
            "properties: {"
                "user_id: {type: 'string'},"
                "event_type: {type: 'string'},"
                "timestamp: {type: 'string', format: 'date-time'},"
                "value: {type: 'number'}"
            "}"
        "}")
    ).with_schema(Schema()
        .field("user_id", "VARCHAR")
        .field("event_type", "VARCHAR")
        .field("timestamp", "TIMESTAMP")
        .field("value", "DOUBLE")
    ).create_temporary_table("user_events")
    
    return table_env
```

### 실시간 모니터링 시스템

#### 대시보드 구현
```python
import dash
from dash import dcc, html, Input, Output
import plotly.graph_objs as go
import redis
import json

class RealTimeDashboard:
    def __init__(self, redis_host='localhost', redis_port=6379):
        self.app = dash.Dash(__name__)
        self.redis_client = redis.Redis(host=redis_host, port=redis_port)
        self.setup_layout()
        self.setup_callbacks()
    
    def setup_layout(self):
        """대시보드 레이아웃"""
        self.app.layout = html.Div([
            html.H1("실시간 데이터 모니터링"),
            
            # 실시간 지표
            html.Div([
                html.Div([
                    html.H3("현재 TPS"),
                    html.H2(id="current-tps", children="0")
                ], className="metric-box"),
                
                html.Div([
                    html.H3("활성 사용자"),
                    html.H2(id="active-users", children="0")
                ], className="metric-box"),
                
                html.Div([
                    html.H3("평균 응답시간"),
                    html.H2(id="avg-response-time", children="0ms")
                ], className="metric-box")
            ], className="metrics-row"),
            
            # 실시간 차트
            dcc.Graph(id="real-time-chart"),
            
            # 자동 새로고침
            dcc.Interval(
                id='interval-component',
                interval=1000,  # 1초마다 업데이트
                n_intervals=0
            )
        ])
    
    def setup_callbacks(self):
        """콜백 함수 설정"""
        @self.app.callback(
            [Output('current-tps', 'children'),
             Output('active-users', 'children'),
             Output('avg-response-time', 'children'),
             Output('real-time-chart', 'figure')],
            [Input('interval-component', 'n_intervals')]
        )
        def update_dashboard(n_intervals):
            # Redis에서 실시간 지표 조회
            tps = self.redis_client.get('current_tps') or 0
            users = self.redis_client.get('active_users') or 0
            response_time = self.redis_client.get('avg_response_time') or 0
            
            # 시계열 데이터 조회
            time_series_data = self.get_time_series_data()
            
            # 차트 생성
            figure = {
                'data': [
                    go.Scatter(
                        x=time_series_data['timestamps'],
                        y=time_series_data['values'],
                        mode='lines',
                        name='TPS'
                    )
                ],
                'layout': go.Layout(
                    title='실시간 TPS 모니터링',
                    xaxis={'title': '시간'},
                    yaxis={'title': 'TPS'}
                )
            }
            
            return str(tps), str(users), f"{response_time}ms", figure
    
    def get_time_series_data(self):
        """시계열 데이터 조회"""
        # Redis에서 최근 데이터 조회
        raw_data = self.redis_client.lrange('tps_timeline', 0, 60)
        
        timestamps = []
        values = []
        
        for item in raw_data:
            data = json.loads(item)
            timestamps.append(data['timestamp'])
            values.append(data['value'])
        
        return {'timestamps': timestamps, 'values': values}
```

### 성능 최적화

#### 메모리 기반 처리
```python
import redis
from collections import deque
import threading
import time

class InMemoryRealTimeProcessor:
    def __init__(self, max_buffer_size=10000):
        self.event_buffer = deque(maxlen=max_buffer_size)
        self.metrics = {
            'total_events': 0,
            'events_per_second': 0,
            'last_update': time.time()
        }
        self.redis_client = redis.Redis()
        self.processing_thread = None
        self.is_running = False
    
    def add_event(self, event):
        """이벤트 추가"""
        event['timestamp'] = time.time()
        self.event_buffer.append(event)
        self.metrics['total_events'] += 1
    
    def start_processing(self):
        """실시간 처리 시작"""
        self.is_running = True
        self.processing_thread = threading.Thread(target=self._process_events)
        self.processing_thread.start()
    
    def stop_processing(self):
        """처리 중단"""
        self.is_running = False
        if self.processing_thread:
            self.processing_thread.join()
    
    def _process_events(self):
        """이벤트 처리 루프"""
        while self.is_running:
            if self.event_buffer:
                # 배치 처리
                batch = []
                for _ in range(min(100, len(self.event_buffer))):
                    if self.event_buffer:
                        batch.append(self.event_buffer.popleft())
                
                if batch:
                    self._process_batch(batch)
                    self._update_metrics(len(batch))
            
            time.sleep(0.01)  # 10ms 대기
    
    def _process_batch(self, events):
        """배치 이벤트 처리"""
        # 집계 계산
        event_counts = {}
        for event in events:
            event_type = event.get('type', 'unknown')
            event_counts[event_type] = event_counts.get(event_type, 0) + 1
        
        # Redis에 결과 저장
        for event_type, count in event_counts.items():
            self.redis_client.incr(f"event_count:{event_type}", count)
    
    def _update_metrics(self, processed_count):
        """메트릭 업데이트"""
        current_time = time.time()
        time_diff = current_time - self.metrics['last_update']
        
        if time_diff >= 1.0:  # 1초마다 업데이트
            self.metrics['events_per_second'] = processed_count / time_diff
            self.metrics['last_update'] = current_time
            
            # Redis에 메트릭 저장
            self.redis_client.set('current_tps', int(self.metrics['events_per_second']))
```

### 장애 대응 및 복구

#### Circuit Breaker 패턴
```python
import time
from enum import Enum

class CircuitState(Enum):
    CLOSED = "closed"
    OPEN = "open"
    HALF_OPEN = "half_open"

class CircuitBreaker:
    def __init__(self, failure_threshold=5, recovery_timeout=60, expected_exception=Exception):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.expected_exception = expected_exception
        
        self.failure_count = 0
        self.last_failure_time = None
        self.state = CircuitState.CLOSED
    
    def call(self, func, *args, **kwargs):
        """함수 호출 with Circuit Breaker"""
        if self.state == CircuitState.OPEN:
            if self._should_attempt_reset():
                self.state = CircuitState.HALF_OPEN
            else:
                raise Exception("Circuit breaker is OPEN")
        
        try:
            result = func(*args, **kwargs)
            self._on_success()
            return result
        except self.expected_exception as e:
            self._on_failure()
            raise e
    
    def _should_attempt_reset(self):
        """재시도 시점 확인"""
        return (time.time() - self.last_failure_time) >= self.recovery_timeout
    
    def _on_success(self):
        """성공 시 처리"""
        self.failure_count = 0
        self.state = CircuitState.CLOSED
    
    def _on_failure(self):
        """실패 시 처리"""
        self.failure_count += 1
        self.last_failure_time = time.time()
        
        if self.failure_count >= self.failure_threshold:
            self.state = CircuitState.OPEN

# 사용 예시
circuit_breaker = CircuitBreaker(failure_threshold=3, recovery_timeout=30)

def unreliable_api_call():
    # 불안정한 API 호출
    if random.random() < 0.3:  # 30% 확률로 실패
        raise Exception("API call failed")
    return "Success"

try:
    result = circuit_breaker.call(unreliable_api_call)
    print(f"결과: {result}")
except Exception as e:
    print(f"에러: {e}")
```

실시간 데이터 수집은 복잡한 기술적 요구사항과 높은 안정성을 요구하는 영역이다. 적절한 아키텍처 설계와 모니터링 시스템을 통해 대규모 실시간 데이터를 안정적으로 처리할 수 있는 시스템을 구축할 수 있다. 