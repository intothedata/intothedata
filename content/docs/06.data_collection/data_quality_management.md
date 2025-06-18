+++
title = "데이터 품질 관리"
type = "docs"
weight = 1050
+++

## 데이터 품질 관리

### 개요

데이터 품질 관리는 데이터의 정확성, 완전성, 일관성, 적시성을 보장하기 위한 체계적인 프로세스이다. 높은 품질의 데이터는 정확한 분석 결과와 신뢰할 수 있는 의사결정의 기반이 된다.

### 데이터 품질의 차원

#### 정확성 (Accuracy)
```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

class DataAccuracyChecker:
    def __init__(self):
        self.validation_rules = {}
        
    def add_range_validation(self, column, min_val, max_val):
        """범위 검증 규칙 추가"""
        self.validation_rules[column] = {
            'type': 'range',
            'min': min_val,
            'max': max_val
        }
    
    def add_pattern_validation(self, column, pattern):
        """패턴 검증 규칙 추가"""
        self.validation_rules[column] = {
            'type': 'pattern',
            'pattern': pattern
        }
    
    def check_accuracy(self, df):
        """데이터 정확성 검사"""
        accuracy_report = {}
        
        for column, rule in self.validation_rules.items():
            if column not in df.columns:
                continue
                
            if rule['type'] == 'range':
                valid_count = df[
                    (df[column] >= rule['min']) & 
                    (df[column] <= rule['max'])
                ].shape[0]
                
            elif rule['type'] == 'pattern':
                valid_count = df[column].str.match(rule['pattern']).sum()
            
            total_count = df[column].notna().sum()
            accuracy_rate = valid_count / total_count if total_count > 0 else 0
            
            accuracy_report[column] = {
                'valid_records': valid_count,
                'total_records': total_count,
                'accuracy_rate': accuracy_rate
            }
        
        return accuracy_report

# 사용 예시
accuracy_checker = DataAccuracyChecker()
accuracy_checker.add_range_validation('age', 0, 120)
accuracy_checker.add_pattern_validation('email', r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$')

# 샘플 데이터
data = {
    'age': [25, 30, 150, -5, 45],
    'email': ['test@example.com', 'invalid-email', 'user@domain.org', '', 'another@test.co.kr']
}
df = pd.DataFrame(data)

accuracy_report = accuracy_checker.check_accuracy(df)
```

#### 완전성 (Completeness)
```python
class DataCompletenessAnalyzer:
    def __init__(self):
        self.critical_columns = []
        
    def set_critical_columns(self, columns):
        """필수 컬럼 설정"""
        self.critical_columns = columns
    
    def analyze_completeness(self, df):
        """완전성 분석"""
        completeness_report = {}
        
        for column in df.columns:
            total_records = len(df)
            non_null_records = df[column].notna().sum()
            completeness_rate = non_null_records / total_records
            
            completeness_report[column] = {
                'total_records': total_records,
                'non_null_records': non_null_records,
                'null_records': total_records - non_null_records,
                'completeness_rate': completeness_rate,
                'is_critical': column in self.critical_columns,
                'status': 'acceptable' if completeness_rate >= 0.95 else 'needs_attention'
            }
        
        return completeness_report
    
    def identify_patterns_in_missing_data(self, df):
        """결측 데이터 패턴 분석"""
        missing_patterns = {}
        
        # 컬럼별 결측 패턴
        for column in df.columns:
            missing_by_index = df[column].isna()
            
            # 연속된 결측 구간 찾기
            consecutive_missing = []
            start_idx = None
            
            for idx, is_missing in missing_by_index.items():
                if is_missing and start_idx is None:
                    start_idx = idx
                elif not is_missing and start_idx is not None:
                    consecutive_missing.append((start_idx, idx - 1))
                    start_idx = None
            
            missing_patterns[column] = {
                'total_missing': missing_by_index.sum(),
                'consecutive_missing_ranges': consecutive_missing,
                'missing_percentage': missing_by_index.mean() * 100
            }
        
        return missing_patterns

# 사용 예시
completeness_analyzer = DataCompletenessAnalyzer()
completeness_analyzer.set_critical_columns(['user_id', 'timestamp', 'transaction_amount'])

completeness_report = completeness_analyzer.analyze_completeness(df)
missing_patterns = completeness_analyzer.identify_patterns_in_missing_data(df)
```

#### 일관성 (Consistency)
```python
class DataConsistencyChecker:
    def __init__(self):
        self.cross_field_rules = []
        self.reference_data = {}
    
    def add_cross_field_rule(self, rule_name, condition_func, description):
        """교차 필드 검증 규칙 추가"""
        self.cross_field_rules.append({
            'name': rule_name,
            'condition': condition_func,
            'description': description
        })
    
    def add_reference_data(self, column, valid_values):
        """참조 데이터 추가"""
        self.reference_data[column] = set(valid_values)
    
    def check_consistency(self, df):
        """일관성 검사"""
        consistency_report = {}
        
        # 교차 필드 규칙 검사
        for rule in self.cross_field_rules:
            try:
                violations = df[~df.apply(rule['condition'], axis=1)]
                consistency_report[rule['name']] = {
                    'total_records': len(df),
                    'violations': len(violations),
                    'violation_rate': len(violations) / len(df),
                    'description': rule['description']
                }
            except Exception as e:
                consistency_report[rule['name']] = {
                    'error': str(e)
                }
        
        # 참조 데이터 검사
        for column, valid_values in self.reference_data.items():
            if column in df.columns:
                invalid_records = df[~df[column].isin(valid_values)]
                consistency_report[f"{column}_reference"] = {
                    'total_records': len(df),
                    'invalid_records': len(invalid_records),
                    'consistency_rate': 1 - (len(invalid_records) / len(df))
                }
        
        return consistency_report
    
    def check_duplicate_records(self, df, key_columns):
        """중복 레코드 검사"""
        duplicates = df.duplicated(subset=key_columns, keep=False)
        duplicate_groups = df[duplicates].groupby(key_columns).size()
        
        return {
            'total_records': len(df),
            'duplicate_records': duplicates.sum(),
            'duplicate_groups': len(duplicate_groups),
            'duplication_rate': duplicates.sum() / len(df)
        }

# 사용 예시
consistency_checker = DataConsistencyChecker()

# 나이와 생년월일 일치 검사
def age_birth_consistency(row):
    if pd.isna(row['age']) or pd.isna(row['birth_year']):
        return True
    current_year = datetime.now().year
    calculated_age = current_year - row['birth_year']
    return abs(calculated_age - row['age']) <= 1

consistency_checker.add_cross_field_rule(
    'age_birth_consistency',
    age_birth_consistency,
    '나이와 생년월일이 일치하는지 확인'
)

# 유효한 국가 코드 설정
consistency_checker.add_reference_data('country_code', ['KR', 'US', 'JP', 'CN', 'GB'])

consistency_report = consistency_checker.check_consistency(df)
```

### 자동화된 품질 모니터링

#### 실시간 품질 모니터링
```python
import logging
from datetime import datetime
import json

class RealTimeQualityMonitor:
    def __init__(self):
        self.quality_metrics = {}
        self.alert_thresholds = {}
        self.quality_history = []
        
        # 로깅 설정
        logging.basicConfig(level=logging.INFO)
        self.logger = logging.getLogger(__name__)
    
    def set_alert_threshold(self, metric_name, threshold_value, operator='<'):
        """알림 임계값 설정"""
        self.alert_thresholds[metric_name] = {
            'value': threshold_value,
            'operator': operator
        }
    
    def monitor_data_batch(self, df, batch_id):
        """데이터 배치 품질 모니터링"""
        batch_metrics = self.calculate_batch_metrics(df)
        batch_metrics['batch_id'] = batch_id
        batch_metrics['timestamp'] = datetime.now().isoformat()
        
        # 품질 히스토리에 추가
        self.quality_history.append(batch_metrics)
        
        # 알림 체크
        self.check_quality_alerts(batch_metrics)
        
        # 메트릭 업데이트
        self.quality_metrics = batch_metrics
        
        return batch_metrics
    
    def calculate_batch_metrics(self, df):
        """배치 메트릭 계산"""
        metrics = {}
        
        # 기본 메트릭
        metrics['record_count'] = len(df)
        metrics['column_count'] = len(df.columns)
        
        # 완전성 메트릭
        completeness_scores = []
        for column in df.columns:
            completeness = df[column].notna().sum() / len(df)
            completeness_scores.append(completeness)
        
        metrics['avg_completeness'] = np.mean(completeness_scores)
        metrics['min_completeness'] = np.min(completeness_scores)
        
        # 중복률
        metrics['duplicate_rate'] = df.duplicated().sum() / len(df)
        
        # 타입 일관성
        type_consistency_scores = []
        for column in df.columns:
            if df[column].dtype == 'object':
                # 문자열 타입의 일관성 (예: 길이 분포)
                lengths = df[column].astype(str).str.len()
                consistency = 1 - (lengths.std() / lengths.mean()) if lengths.mean() > 0 else 1
                type_consistency_scores.append(max(0, min(1, consistency)))
        
        metrics['avg_type_consistency'] = np.mean(type_consistency_scores) if type_consistency_scores else 1
        
        return metrics
    
    def check_quality_alerts(self, metrics):
        """품질 알림 체크"""
        for metric_name, threshold in self.alert_thresholds.items():
            if metric_name in metrics:
                value = metrics[metric_name]
                threshold_value = threshold['value']
                operator = threshold['operator']
                
                alert_triggered = False
                if operator == '<' and value < threshold_value:
                    alert_triggered = True
                elif operator == '>' and value > threshold_value:
                    alert_triggered = True
                elif operator == '=' and value == threshold_value:
                    alert_triggered = True
                
                if alert_triggered:
                    self.send_quality_alert(metric_name, value, threshold_value, operator)
    
    def send_quality_alert(self, metric_name, current_value, threshold_value, operator):
        """품질 알림 발송"""
        alert_message = f"""
        데이터 품질 알림
        
        메트릭: {metric_name}
        현재 값: {current_value}
        임계값: {operator} {threshold_value}
        시간: {datetime.now()}
        """
        
        self.logger.warning(alert_message)
        
        # 실제 구현에서는 이메일, Slack 등으로 알림 발송
        
    def generate_quality_report(self, hours=24):
        """품질 보고서 생성"""
        cutoff_time = datetime.now() - timedelta(hours=hours)
        
        recent_metrics = [
            m for m in self.quality_history 
            if datetime.fromisoformat(m['timestamp']) > cutoff_time
        ]
        
        if not recent_metrics:
            return None
        
        # 평균 품질 메트릭 계산
        avg_metrics = {}
        for key in recent_metrics[0].keys():
            if key not in ['batch_id', 'timestamp'] and isinstance(recent_metrics[0][key], (int, float)):
                values = [m[key] for m in recent_metrics if key in m]
                avg_metrics[f'avg_{key}'] = np.mean(values)
                avg_metrics[f'min_{key}'] = np.min(values)
                avg_metrics[f'max_{key}'] = np.max(values)
        
        quality_report = {
            'report_period': f'최근 {hours}시간',
            'total_batches': len(recent_metrics),
            'metrics': avg_metrics,
            'trend_analysis': self.analyze_quality_trends(recent_metrics)
        }
        
        return quality_report
    
    def analyze_quality_trends(self, metrics_history):
        """품질 트렌드 분석"""
        if len(metrics_history) < 2:
            return {}
        
        trends = {}
        
        # 완전성 트렌드
        completeness_values = [m['avg_completeness'] for m in metrics_history]
        trends['completeness_trend'] = 'improving' if completeness_values[-1] > completeness_values[0] else 'declining'
        
        # 중복률 트렌드
        duplicate_values = [m['duplicate_rate'] for m in metrics_history]
        trends['duplication_trend'] = 'improving' if duplicate_values[-1] < duplicate_values[0] else 'worsening'
        
        return trends

# 사용 예시
monitor = RealTimeQualityMonitor()

# 알림 임계값 설정
monitor.set_alert_threshold('avg_completeness', 0.9, '<')
monitor.set_alert_threshold('duplicate_rate', 0.05, '>')

# 데이터 배치 모니터링
sample_data = pd.DataFrame({
    'id': range(1000),
    'name': ['User' + str(i) if i % 10 != 0 else None for i in range(1000)],
    'email': ['user' + str(i) + '@example.com' for i in range(1000)]
})

batch_metrics = monitor.monitor_data_batch(sample_data, 'batch_001')
quality_report = monitor.generate_quality_report(hours=1)
```

### 데이터 프로파일링

#### 자동 프로파일링
```python
class DataProfiler:
    def __init__(self):
        self.profile_cache = {}
    
    def profile_dataset(self, df, dataset_name):
        """데이터셋 프로파일링"""
        profile = {
            'dataset_name': dataset_name,
            'profiled_at': datetime.now().isoformat(),
            'basic_info': self.get_basic_info(df),
            'column_profiles': self.profile_columns(df),
            'relationship_analysis': self.analyze_relationships(df)
        }
        
        self.profile_cache[dataset_name] = profile
        return profile
    
    def get_basic_info(self, df):
        """기본 정보 수집"""
        return {
            'total_rows': len(df),
            'total_columns': len(df.columns),
            'memory_usage_mb': df.memory_usage(deep=True).sum() / 1024 / 1024,
            'duplicate_rows': df.duplicated().sum(),
            'duplicate_rate': df.duplicated().sum() / len(df)
        }
    
    def profile_columns(self, df):
        """컬럼별 프로파일링"""
        column_profiles = {}
        
        for column in df.columns:
            profile = {
                'data_type': str(df[column].dtype),
                'non_null_count': df[column].notna().sum(),
                'null_count': df[column].isna().sum(),
                'null_percentage': df[column].isna().sum() / len(df) * 100,
                'unique_count': df[column].nunique(),
                'uniqueness_rate': df[column].nunique() / df[column].notna().sum() if df[column].notna().sum() > 0 else 0
            }
            
            # 숫자형 컬럼 추가 분석
            if pd.api.types.is_numeric_dtype(df[column]):
                profile.update({
                    'min_value': df[column].min(),
                    'max_value': df[column].max(),
                    'mean_value': df[column].mean(),
                    'median_value': df[column].median(),
                    'std_dev': df[column].std(),
                    'outlier_count': self.count_outliers(df[column])
                })
            
            # 문자형 컬럼 추가 분석
            elif df[column].dtype == 'object':
                profile.update({
                    'avg_length': df[column].astype(str).str.len().mean(),
                    'min_length': df[column].astype(str).str.len().min(),
                    'max_length': df[column].astype(str).str.len().max(),
                    'most_common_values': df[column].value_counts().head(5).to_dict()
                })
            
            column_profiles[column] = profile
        
        return column_profiles
    
    def count_outliers(self, series, method='iqr'):
        """이상치 개수 계산"""
        if method == 'iqr':
            Q1 = series.quantile(0.25)
            Q3 = series.quantile(0.75)
            IQR = Q3 - Q1
            lower_bound = Q1 - 1.5 * IQR
            upper_bound = Q3 + 1.5 * IQR
            
            outliers = series[(series < lower_bound) | (series > upper_bound)]
            return len(outliers)
        
        return 0
    
    def analyze_relationships(self, df):
        """변수 간 관계 분석"""
        numeric_columns = df.select_dtypes(include=[np.number]).columns
        
        if len(numeric_columns) < 2:
            return {}
        
        correlation_matrix = df[numeric_columns].corr()
        
        # 강한 상관관계 찾기
        strong_correlations = []
        for i in range(len(correlation_matrix)):
            for j in range(i+1, len(correlation_matrix)):
                corr_value = correlation_matrix.iloc[i, j]
                if abs(corr_value) > 0.7:  # 0.7 이상의 상관관계
                    strong_correlations.append({
                        'column1': correlation_matrix.index[i],
                        'column2': correlation_matrix.columns[j],
                        'correlation': corr_value
                    })
        
        return {
            'correlation_matrix': correlation_matrix.to_dict(),
            'strong_correlations': strong_correlations
        }

# 사용 예시
profiler = DataProfiler()
profile_result = profiler.profile_dataset(sample_data, 'user_dataset')

print("데이터 프로파일링 결과:")
print(f"총 행 수: {profile_result['basic_info']['total_rows']}")
print(f"중복률: {profile_result['basic_info']['duplicate_rate']:.2%}")
```

데이터 품질 관리는 지속적이고 체계적인 접근이 필요한 영역이다. 자동화된 모니터링 시스템과 명확한 품질 기준을 통해 데이터의 신뢰성을 보장하고, 분석 결과의 정확성을 높일 수 있다. 