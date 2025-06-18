+++
title = "센서 및 IoT 데이터 수집"
type = "docs"
weight = 1030
+++

## 센서 및 IoT 데이터 수집

### 개요

IoT(Internet of Things) 기기와 센서들은 실시간으로 다양한 환경 데이터를 생성한다. 이러한 데이터는 온도, 습도, 압력, 움직임, 위치 등 물리적 세계의 상태를 디지털로 변환하여 새로운 인사이트와 자동화 기회를 제공한다.

### IoT 데이터의 특성

#### 대용량 (Volume)
- 수많은 센서에서 지속적으로 데이터 생성
- 시간당 수백만 개의 데이터 포인트
- 장기간 축적되는 시계열 데이터

#### 고속성 (Velocity)
- 실시간 또는 준실시간 데이터 스트림
- 빠른 의사결정이 필요한 상황
- 지연 시간(Latency) 최소화 필요

#### 다양성 (Variety)
- 다양한 센서 타입과 데이터 형식
- 구조화된 데이터와 비구조화된 데이터 혼재
- 다양한 통신 프로토콜 사용

### 주요 센서 유형과 데이터

#### 환경 센서
```python
# 온도/습도 센서 데이터 예시
import random
import datetime
import json

def generate_environmental_data():
    """
    환경 센서 데이터 생성 시뮬레이션
    """
    data = {
        'sensor_id': 'ENV_001',
        'timestamp': datetime.datetime.now().isoformat(),
        'temperature': round(random.uniform(18.0, 28.0), 2),
        'humidity': round(random.uniform(40.0, 70.0), 2),
        'pressure': round(random.uniform(1000.0, 1020.0), 2),
        'air_quality_index': random.randint(0, 300),
        'location': {
            'building': 'Building A',
            'floor': 3,
            'room': '301'
        }
    }
    return data

# 연속 데이터 생성
import time
def continuous_environmental_monitoring(duration_minutes=60):
    """
    지속적인 환경 모니터링
    """
    end_time = time.time() + (duration_minutes * 60)
    
    while time.time() < end_time:
        sensor_data = generate_environmental_data()
        print(json.dumps(sensor_data, indent=2))
        
        # 데이터 저장 또는 전송 로직
        store_sensor_data(sensor_data)
        
        time.sleep(10)  # 10초마다 데이터 수집
```

#### 움직임/위치 센서
```python
import math

class MovementSensor:
    def __init__(self, sensor_id, location):
        self.sensor_id = sensor_id
        self.location = location
        self.last_position = {'x': 0, 'y': 0, 'z': 0}
    
    def get_accelerometer_data(self):
        """
        가속도계 데이터 생성
        """
        return {
            'sensor_id': self.sensor_id,
            'timestamp': datetime.datetime.now().isoformat(),
            'acceleration': {
                'x': round(random.uniform(-2.0, 2.0), 3),
                'y': round(random.uniform(-2.0, 2.0), 3),
                'z': round(random.uniform(9.5, 10.5), 3)  # 중력 포함
            },
            'gyroscope': {
                'x': round(random.uniform(-180, 180), 2),
                'y': round(random.uniform(-180, 180), 2),
                'z': round(random.uniform(-180, 180), 2)
            }
        }
    
    def get_gps_data(self):
        """
        GPS 위치 데이터 생성
        """
        # 서울 중심 좌표 기준 랜덤 위치
        base_lat = 37.5665
        base_lng = 126.9780
        
        return {
            'sensor_id': self.sensor_id,
            'timestamp': datetime.datetime.now().isoformat(),
            'gps': {
                'latitude': base_lat + random.uniform(-0.01, 0.01),
                'longitude': base_lng + random.uniform(-0.01, 0.01),
                'altitude': round(random.uniform(0, 100), 1),
                'accuracy': round(random.uniform(1, 10), 1)
            },
            'speed': round(random.uniform(0, 50), 1),  # km/h
            'heading': round(random.uniform(0, 360), 1)  # degrees
        }
```

#### 산업용 센서
```python
class IndustrialSensor:
    def __init__(self, machine_id, sensor_type):
        self.machine_id = machine_id
        self.sensor_type = sensor_type
    
    def get_vibration_data(self):
        """
        진동 센서 데이터 (예측 정비용)
        """
        # 정상 범위를 벗어나면 이상 신호
        base_vibration = 2.5
        anomaly_factor = random.choice([1.0, 1.0, 1.0, 1.0, 3.5])  # 20% 확률로 이상
        
        return {
            'machine_id': self.machine_id,
            'sensor_type': 'vibration',
            'timestamp': datetime.datetime.now().isoformat(),
            'vibration_level': round(base_vibration * anomaly_factor, 2),
            'frequency': round(random.uniform(50, 200), 1),
            'amplitude': round(random.uniform(0.1, 5.0), 2),
            'is_anomaly': anomaly_factor > 2.0
        }
    
    def get_temperature_data(self):
        """
        기계 온도 센서 데이터
        """
        normal_temp = 65  # 정상 작동 온도
        current_temp = normal_temp + random.uniform(-5, 15)
        
        return {
            'machine_id': self.machine_id,
            'sensor_type': 'temperature',
            'timestamp': datetime.datetime.now().isoformat(),
            'temperature': round(current_temp, 1),
            'threshold_warning': 80,
            'threshold_critical': 90,
            'status': 'normal' if current_temp < 80 else 'warning' if current_temp < 90 else 'critical'
        }
```

### 데이터 수집 프로토콜

#### MQTT (Message Queuing Telemetry Transport)
```python
import paho.mqtt.client as mqtt
import json
import time

class MQTTDataCollector:
    def __init__(self, broker_host, broker_port=1883):
        self.broker_host = broker_host
        self.broker_port = broker_port
        self.client = mqtt.Client()
        self.collected_data = []
        
        # 콜백 함수 설정
        self.client.on_connect = self.on_connect
        self.client.on_message = self.on_message
    
    def on_connect(self, client, userdata, flags, rc):
        """
        MQTT 브로커 연결 시 호출
        """
        if rc == 0:
            print("MQTT 브로커 연결 성공")
            # 토픽 구독
            client.subscribe("sensors/+/data")  # 모든 센서 데이터
            client.subscribe("alerts/+")        # 알림 메시지
        else:
            print(f"MQTT 브로커 연결 실패: {rc}")
    
    def on_message(self, client, userdata, msg):
        """
        메시지 수신 시 호출
        """
        try:
            topic = msg.topic
            payload = json.loads(msg.payload.decode())
            
            data_record = {
                'topic': topic,
                'timestamp': datetime.datetime.now().isoformat(),
                'data': payload
            }
            
            self.collected_data.append(data_record)
            print(f"데이터 수신: {topic} - {payload}")
            
            # 실시간 처리 로직
            self.process_real_time_data(data_record)
            
        except Exception as e:
            print(f"메시지 처리 오류: {e}")
    
    def process_real_time_data(self, data_record):
        """
        실시간 데이터 처리
        """
        topic = data_record['topic']
        data = data_record['data']
        
        # 임계값 체크
        if 'temperature' in data and data['temperature'] > 80:
            self.send_alert(f"고온 경고: {data['temperature']}°C")
        
        # 데이터베이스 저장
        self.save_to_database(data_record)
    
    def send_alert(self, message):
        """
        경고 메시지 발송
        """
        alert_topic = "alerts/temperature"
        alert_data = {
            'message': message,
            'timestamp': datetime.datetime.now().isoformat(),
            'severity': 'warning'
        }
        
        self.client.publish(alert_topic, json.dumps(alert_data))
    
    def start_collecting(self):
        """
        데이터 수집 시작
        """
        self.client.connect(self.broker_host, self.broker_port, 60)
        self.client.loop_forever()

# 사용 예시
collector = MQTTDataCollector("localhost")
collector.start_collecting()
```

#### HTTP API를 통한 데이터 수집
```python
from flask import Flask, request, jsonify
import sqlite3
from datetime import datetime

app = Flask(__name__)

class IoTDataAPI:
    def __init__(self, db_path='iot_data.db'):
        self.db_path = db_path
        self.init_database()
    
    def init_database(self):
        """
        데이터베이스 초기화
        """
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        
        cursor.execute('''
            CREATE TABLE IF NOT EXISTS sensor_data (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                device_id TEXT NOT NULL,
                sensor_type TEXT NOT NULL,
                timestamp TEXT NOT NULL,
                value REAL NOT NULL,
                unit TEXT,
                location TEXT,
                metadata TEXT
            )
        ''')
        
        conn.commit()
        conn.close()
    
    @app.route('/api/sensors/data', methods=['POST'])
    def receive_sensor_data():
        """
        센서 데이터 수신 엔드포인트
        """
        try:
            data = request.json
            
            # 데이터 검증
            required_fields = ['device_id', 'sensor_type', 'value']
            if not all(field in data for field in required_fields):
                return jsonify({'error': 'Missing required fields'}), 400
            
            # 데이터베이스 저장
            api = IoTDataAPI()
            api.save_sensor_data(data)
            
            return jsonify({'status': 'success', 'message': 'Data received'}), 200
            
        except Exception as e:
            return jsonify({'error': str(e)}), 500
    
    def save_sensor_data(self, data):
        """
        센서 데이터 저장
        """
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        
        cursor.execute('''
            INSERT INTO sensor_data 
            (device_id, sensor_type, timestamp, value, unit, location, metadata)
            VALUES (?, ?, ?, ?, ?, ?, ?)
        ''', (
            data['device_id'],
            data['sensor_type'],
            data.get('timestamp', datetime.now().isoformat()),
            data['value'],
            data.get('unit', ''),
            data.get('location', ''),
            json.dumps(data.get('metadata', {}))
        ))
        
        conn.commit()
        conn.close()

# API 서버 실행
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
```

### 실시간 데이터 처리

#### Apache Kafka를 활용한 스트리밍
```python
from kafka import KafkaProducer, KafkaConsumer
import json
import time

class IoTKafkaProducer:
    def __init__(self, bootstrap_servers=['localhost:9092']):
        self.producer = KafkaProducer(
            bootstrap_servers=bootstrap_servers,
            value_serializer=lambda v: json.dumps(v).encode('utf-8')
        )
    
    def send_sensor_data(self, topic, sensor_data):
        """
        센서 데이터를 Kafka 토픽으로 전송
        """
        try:
            future = self.producer.send(topic, sensor_data)
            record_metadata = future.get(timeout=10)
            print(f"메시지 전송 성공: {record_metadata.topic}:{record_metadata.partition}:{record_metadata.offset}")
        except Exception as e:
            print(f"메시지 전송 실패: {e}")

class IoTKafkaConsumer:
    def __init__(self, topics, bootstrap_servers=['localhost:9092']):
        self.consumer = KafkaConsumer(
            *topics,
            bootstrap_servers=bootstrap_servers,
            value_deserializer=lambda m: json.loads(m.decode('utf-8')),
            group_id='iot-data-processors'
        )
    
    def process_messages(self):
        """
        메시지 처리
        """
        for message in self.consumer:
            topic = message.topic
            data = message.value
            
            print(f"토픽: {topic}, 데이터: {data}")
            
            # 데이터 유형별 처리
            if topic == 'temperature-sensors':
                self.process_temperature_data(data)
            elif topic == 'vibration-sensors':
                self.process_vibration_data(data)
    
    def process_temperature_data(self, data):
        """
        온도 데이터 처리
        """
        if data['temperature'] > 80:
            alert = {
                'alert_type': 'high_temperature',
                'device_id': data['device_id'],
                'temperature': data['temperature'],
                'timestamp': datetime.now().isoformat()
            }
            # 알림 시스템으로 전송
            self.send_alert(alert)
    
    def process_vibration_data(self, data):
        """
        진동 데이터 처리 (예측 정비)
        """
        if data['vibration_level'] > 5.0:
            maintenance_alert = {
                'alert_type': 'maintenance_required',
                'machine_id': data['machine_id'],
                'vibration_level': data['vibration_level'],
                'estimated_failure_time': '72 hours'
            }
            self.schedule_maintenance(maintenance_alert)

# 사용 예시
producer = IoTKafkaProducer()
consumer = IoTKafkaConsumer(['temperature-sensors', 'vibration-sensors'])

# 프로듀서로 데이터 전송
sensor_data = {
    'device_id': 'TEMP_001',
    'temperature': 85.5,
    'timestamp': datetime.now().isoformat()
}
producer.send_sensor_data('temperature-sensors', sensor_data)

# 컨슈머로 데이터 처리
consumer.process_messages()
```

#### Apache Spark Streaming
```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import *
from pyspark.sql.types import *

def create_spark_streaming_session():
    """
    Spark Streaming 세션 생성
    """
    spark = SparkSession.builder \
        .appName("IoTDataStreaming") \
        .config("spark.sql.streaming.checkpointLocation", "/tmp/checkpoint") \
        .getOrCreate()
    
    return spark

def process_iot_stream(spark):
    """
    IoT 데이터 스트림 처리
    """
    # Kafka에서 스트림 읽기
    df = spark \
        .readStream \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "localhost:9092") \
        .option("subscribe", "iot-sensors") \
        .load()
    
    # JSON 데이터 파싱
    schema = StructType([
        StructField("device_id", StringType(), True),
        StructField("sensor_type", StringType(), True),
        StructField("timestamp", TimestampType(), True),
        StructField("value", DoubleType(), True),
        StructField("location", StringType(), True)
    ])
    
    parsed_df = df.select(
        from_json(col("value").cast("string"), schema).alias("data")
    ).select("data.*")
    
    # 윈도우 기반 집계
    windowed_df = parsed_df \
        .withWatermark("timestamp", "10 minutes") \
        .groupBy(
            window(col("timestamp"), "5 minutes"),
            col("device_id"),
            col("sensor_type")
        ) \
        .agg(
            avg("value").alias("avg_value"),
            max("value").alias("max_value"),
            min("value").alias("min_value"),
            count("*").alias("count")
        )
    
    # 결과 출력
    query = windowed_df \
        .writeStream \
        .outputMode("update") \
        .format("console") \
        .trigger(processingTime='30 seconds') \
        .start()
    
    return query

# Spark Streaming 실행
spark = create_spark_streaming_session()
query = process_iot_stream(spark)
query.awaitTermination()
```

### 데이터 저장 및 관리

#### 시계열 데이터베이스 (InfluxDB)
```python
from influxdb_client import InfluxDBClient, Point, WritePrecision
from influxdb_client.client.write_api import SYNCHRONOUS
import pandas as pd

class InfluxDBManager:
    def __init__(self, url, token, org, bucket):
        self.client = InfluxDBClient(url=url, token=token, org=org)
        self.write_api = self.client.write_api(write_options=SYNCHRONOUS)
        self.query_api = self.client.query_api()
        self.bucket = bucket
        self.org = org
    
    def write_sensor_data(self, sensor_data):
        """
        센서 데이터를 InfluxDB에 저장
        """
        point = Point("sensor_measurement") \
            .tag("device_id", sensor_data['device_id']) \
            .tag("sensor_type", sensor_data['sensor_type']) \
            .tag("location", sensor_data.get('location', 'unknown')) \
            .field("value", sensor_data['value']) \
            .time(sensor_data['timestamp'], WritePrecision.S)
        
        self.write_api.write(bucket=self.bucket, org=self.org, record=point)
    
    def query_recent_data(self, device_id, hours=24):
        """
        최근 데이터 조회
        """
        query = f'''
        from(bucket: "{self.bucket}")
        |> range(start: -{hours}h)
        |> filter(fn: (r) => r._measurement == "sensor_measurement")
        |> filter(fn: (r) => r.device_id == "{device_id}")
        |> aggregateWindow(every: 1m, fn: mean, createEmpty: false)
        |> yield(name: "mean")
        '''
        
        result = self.query_api.query(org=self.org, query=query)
        
        # DataFrame으로 변환
        data_points = []
        for table in result:
            for record in table.records:
                data_points.append({
                    'time': record.get_time(),
                    'device_id': record.values.get('device_id'),
                    'sensor_type': record.values.get('sensor_type'),
                    'value': record.get_value()
                })
        
        return pd.DataFrame(data_points)
    
    def calculate_anomalies(self, device_id, threshold_std=2):
        """
        이상치 탐지
        """
        query = f'''
        from(bucket: "{self.bucket}")
        |> range(start: -7d)
        |> filter(fn: (r) => r._measurement == "sensor_measurement")
        |> filter(fn: (r) => r.device_id == "{device_id}")
        |> aggregateWindow(every: 1h, fn: mean)
        |> movingAverage(n: 24)
        '''
        
        # 통계적 이상치 탐지 로직
        df = self.query_to_dataframe(query)
        mean_val = df['value'].mean()
        std_val = df['value'].std()
        
        anomalies = df[
            (df['value'] > mean_val + threshold_std * std_val) |
            (df['value'] < mean_val - threshold_std * std_val)
        ]
        
        return anomalies

# 사용 예시
influx = InfluxDBManager(
    url="http://localhost:8086",
    token="your-token",
    org="your-org",
    bucket="iot-data"
)

# 센서 데이터 저장
sensor_data = {
    'device_id': 'TEMP_001',
    'sensor_type': 'temperature',
    'value': 23.5,
    'location': 'Building A',
    'timestamp': datetime.now()
}

influx.write_sensor_data(sensor_data)
```

#### Apache Cassandra를 활용한 대용량 저장
```python
from cassandra.cluster import Cluster
from cassandra.auth import PlainTextAuthProvider
import uuid
from datetime import datetime

class CassandraIoTStore:
    def __init__(self, hosts, keyspace, username=None, password=None):
        if username and password:
            auth_provider = PlainTextAuthProvider(username=username, password=password)
            self.cluster = Cluster(hosts, auth_provider=auth_provider)
        else:
            self.cluster = Cluster(hosts)
        
        self.session = self.cluster.connect()
        self.keyspace = keyspace
        self.setup_keyspace()
    
    def setup_keyspace(self):
        """
        키스페이스 및 테이블 설정
        """
        # 키스페이스 생성
        self.session.execute(f"""
            CREATE KEYSPACE IF NOT EXISTS {self.keyspace}
            WITH replication = {{
                'class': 'SimpleStrategy',
                'replication_factor': 3
            }}
        """)
        
        self.session.set_keyspace(self.keyspace)
        
        # 테이블 생성 - 시계열 데이터에 최적화
        self.session.execute("""
            CREATE TABLE IF NOT EXISTS sensor_data (
                device_id text,
                year int,
                month int,
                day int,
                timestamp timestamp,
                sensor_type text,
                value double,
                metadata map<text, text>,
                id uuid,
                PRIMARY KEY ((device_id, year, month, day), timestamp, id)
            ) WITH CLUSTERING ORDER BY (timestamp DESC, id ASC)
        """)
    
    def insert_sensor_data(self, sensor_data):
        """
        센서 데이터 삽입
        """
        timestamp = datetime.fromisoformat(sensor_data['timestamp'])
        
        prepared = self.session.prepare("""
            INSERT INTO sensor_data 
            (device_id, year, month, day, timestamp, sensor_type, value, metadata, id)
            VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?)
        """)
        
        self.session.execute(prepared, [
            sensor_data['device_id'],
            timestamp.year,
            timestamp.month,
            timestamp.day,
            timestamp,
            sensor_data['sensor_type'],
            sensor_data['value'],
            sensor_data.get('metadata', {}),
            uuid.uuid4()
        ])
    
    def query_device_data(self, device_id, start_date, end_date):
        """
        특정 디바이스의 날짜 범위 데이터 조회
        """
        results = []
        
        # 날짜별로 쿼리 (파티션 키 때문에)
        current_date = start_date
        while current_date <= end_date:
            rows = self.session.execute("""
                SELECT * FROM sensor_data
                WHERE device_id = ? AND year = ? AND month = ? AND day = ?
                AND timestamp >= ? AND timestamp <= ?
            """, [
                device_id,
                current_date.year,
                current_date.month,
                current_date.day,
                start_date,
                end_date
            ])
            
            results.extend(rows)
            current_date += timedelta(days=1)
        
        return results
```

### 엣지 컴퓨팅과 데이터 전처리

#### 엣지에서의 데이터 필터링
```python
class EdgeDataProcessor:
    def __init__(self, device_id, sampling_rate=1.0):
        self.device_id = device_id
        self.sampling_rate = sampling_rate  # 0.0 ~ 1.0
        self.data_buffer = []
        self.last_values = {}
    
    def should_transmit(self, sensor_data):
        """
        데이터 전송 여부 결정
        """
        sensor_type = sensor_data['sensor_type']
        current_value = sensor_data['value']
        
        # 샘플링 비율 적용
        if random.random() > self.sampling_rate:
            return False
        
        # 변화량 기반 필터링
        if sensor_type in self.last_values:
            last_value = self.last_values[sensor_type]
            change_percentage = abs(current_value - last_value) / last_value
            
            # 5% 이상 변화시에만 전송
            if change_percentage < 0.05:
                return False
        
        self.last_values[sensor_type] = current_value
        return True
    
    def aggregate_data(self, time_window_seconds=60):
        """
        시간 윈도우 기반 데이터 집계
        """
        now = datetime.now()
        window_start = now - timedelta(seconds=time_window_seconds)
        
        # 윈도우 내 데이터 필터링
        window_data = [
            data for data in self.data_buffer
            if datetime.fromisoformat(data['timestamp']) >= window_start
        ]
        
        if not window_data:
            return None
        
        # 센서 타입별 집계
        aggregated = {}
        for data in window_data:
            sensor_type = data['sensor_type']
            if sensor_type not in aggregated:
                aggregated[sensor_type] = []
            aggregated[sensor_type].append(data['value'])
        
        # 통계 계산
        result = {
            'device_id': self.device_id,
            'timestamp': now.isoformat(),
            'window_start': window_start.isoformat(),
            'aggregation': {}
        }
        
        for sensor_type, values in aggregated.items():
            result['aggregation'][sensor_type] = {
                'count': len(values),
                'mean': sum(values) / len(values),
                'min': min(values),
                'max': max(values),
                'std': statistics.stdev(values) if len(values) > 1 else 0
            }
        
        return result
```

### 보안 고려사항

#### 디바이스 인증
```python
import jwt
import hashlib
from cryptography.fernet import Fernet

class IoTSecurityManager:
    def __init__(self, secret_key):
        self.secret_key = secret_key
        self.cipher_suite = Fernet(Fernet.generate_key())
    
    def generate_device_token(self, device_id, valid_hours=24):
        """
        디바이스 인증 토큰 생성
        """
        payload = {
            'device_id': device_id,
            'issued_at': datetime.now().timestamp(),
            'expires_at': (datetime.now() + timedelta(hours=valid_hours)).timestamp()
        }
        
        token = jwt.encode(payload, self.secret_key, algorithm='HS256')
        return token
    
    def verify_device_token(self, token):
        """
        디바이스 토큰 검증
        """
        try:
            payload = jwt.decode(token, self.secret_key, algorithms=['HS256'])
            
            # 만료 시간 확인
            if datetime.now().timestamp() > payload['expires_at']:
                return None
            
            return payload['device_id']
        except jwt.InvalidTokenError:
            return None
    
    def encrypt_sensor_data(self, data):
        """
        센서 데이터 암호화
        """
        json_data = json.dumps(data)
        encrypted_data = self.cipher_suite.encrypt(json_data.encode())
        return encrypted_data
    
    def decrypt_sensor_data(self, encrypted_data):
        """
        센서 데이터 복호화
        """
        decrypted_data = self.cipher_suite.decrypt(encrypted_data)
        return json.loads(decrypted_data.decode())
```

### 모니터링 및 알림 시스템

#### 실시간 대시보드
```python
import dash
from dash import dcc, html, Input, Output
import plotly.graph_objs as go
import pandas as pd

class IoTDashboard:
    def __init__(self, data_source):
        self.app = dash.Dash(__name__)
        self.data_source = data_source
        self.setup_layout()
        self.setup_callbacks()
    
    def setup_layout(self):
        """
        대시보드 레이아웃 설정
        """
        self.app.layout = html.Div([
            html.H1("IoT 센서 모니터링 대시보드"),
            
            dcc.Dropdown(
                id='device-dropdown',
                options=[
                    {'label': 'Temperature Sensor 1', 'value': 'TEMP_001'},
                    {'label': 'Humidity Sensor 1', 'value': 'HUM_001'},
                    {'label': 'Vibration Sensor 1', 'value': 'VIB_001'}
                ],
                value='TEMP_001'
            ),
            
            dcc.Graph(id='real-time-graph'),
            
            dcc.Interval(
                id='interval-component',
                interval=5000,  # 5초마다 업데이트
                n_intervals=0
            ),
            
            html.Div(id='alert-panel')
        ])
    
    def setup_callbacks(self):
        """
        콜백 함수 설정
        """
        @self.app.callback(
            [Output('real-time-graph', 'figure'),
             Output('alert-panel', 'children')],
            [Input('interval-component', 'n_intervals'),
             Input('device-dropdown', 'value')]
        )
        def update_dashboard(n_intervals, selected_device):
            # 최근 데이터 조회
            recent_data = self.data_source.get_recent_data(selected_device, hours=1)
            
            # 그래프 생성
            figure = {
                'data': [
                    go.Scatter(
                        x=recent_data['timestamp'],
                        y=recent_data['value'],
                        mode='lines+markers',
                        name='Sensor Value'
                    )
                ],
                'layout': go.Layout(
                    title=f'{selected_device} Real-time Data',
                    xaxis={'title': 'Time'},
                    yaxis={'title': 'Value'}
                )
            }
            
            # 알림 패널 업데이트
            alerts = self.check_alerts(recent_data)
            alert_components = [
                html.Div(f"⚠️ {alert}", className="alert")
                for alert in alerts
            ]
            
            return figure, alert_components
    
    def check_alerts(self, data):
        """
        알림 조건 확인
        """
        alerts = []
        
        if not data.empty:
            latest_value = data['value'].iloc[-1]
            
            # 임계값 체크
            if latest_value > 80:
                alerts.append("높은 온도 감지!")
            elif latest_value < 0:
                alerts.append("비정상적인 저온 감지!")
        
        return alerts
    
    def run(self, debug=True, port=8050):
        """
        대시보드 실행
        """
        self.app.run_server(debug=debug, port=port)
```

센서 및 IoT 데이터 수집은 실시간성, 확장성, 신뢰성이 핵심 요구사항이다. 적절한 프로토콜 선택, 효율적인 데이터 처리, 보안 강화를 통해 IoT 환경에서 안정적인 데이터 수집 시스템을 구축할 수 있다. 