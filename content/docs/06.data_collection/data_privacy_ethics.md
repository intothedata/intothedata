+++
title = "데이터 수집 시 개인정보보호와 윤리"
type = "docs"
weight = 1040
+++

## 데이터 수집 시 개인정보보호와 윤리

### 개요

데이터 수집 과정에서 개인정보보호와 윤리적 고려사항은 단순한 법적 의무를 넘어 조직의 신뢰성과 지속가능성을 결정하는 핵심 요소이다. 적절한 데이터 거버넌스와 윤리적 프레임워크를 구축하여 데이터 주체의 권리를 보호하면서도 데이터의 가치를 극대화해야 한다.

### 주요 법적 프레임워크

#### 개인정보보호법 (한국)
```
핵심 원칙:
- 개인정보 처리의 적법성, 정당성, 투명성
- 목적 제한 원칙 (수집 목적 외 이용 금지)
- 최소 수집 원칙 (필요 최소한의 정보만 수집)
- 정확성 원칙 (정확하고 최신의 정보 유지)
- 보관 기간 제한 (목적 달성 후 즉시 파기)
- 적절한 보안 조치
```

#### GDPR (EU 일반 데이터 보호 규정)
```python
# GDPR 준수를 위한 동의 관리 시스템 예시
import datetime
import json
import uuid

class GDPRConsentManager:
    def __init__(self):
        self.consent_records = {}
    
    def record_consent(self, user_id, purpose, legal_basis="consent"):
        """
        사용자 동의 기록
        """
        consent_id = str(uuid.uuid4())
        consent_record = {
            'consent_id': consent_id,
            'user_id': user_id,
            'purpose': purpose,
            'legal_basis': legal_basis,
            'timestamp': datetime.datetime.now().isoformat(),
            'is_active': True,
            'withdrawal_date': None
        }
        
        self.consent_records[consent_id] = consent_record
        return consent_id
    
    def withdraw_consent(self, consent_id):
        """
        동의 철회
        """
        if consent_id in self.consent_records:
            self.consent_records[consent_id]['is_active'] = False
            self.consent_records[consent_id]['withdrawal_date'] = datetime.datetime.now().isoformat()
            return True
        return False
    
    def check_consent_validity(self, user_id, purpose):
        """
        특정 목적에 대한 유효한 동의 확인
        """
        for record in self.consent_records.values():
            if (record['user_id'] == user_id and 
                record['purpose'] == purpose and 
                record['is_active']):
                return True
        return False
    
    def get_user_consents(self, user_id):
        """
        사용자의 모든 동의 내역 조회
        """
        user_consents = []
        for record in self.consent_records.values():
            if record['user_id'] == user_id:
                user_consents.append(record)
        return user_consents

# 사용 예시
consent_manager = GDPRConsentManager()

# 동의 기록
consent_id = consent_manager.record_consent(
    user_id="user123",
    purpose="marketing_emails",
    legal_basis="consent"
)

# 동의 유효성 확인
is_valid = consent_manager.check_consent_validity("user123", "marketing_emails")
```

#### CCPA (캘리포니아 소비자 프라이버시 법)
```python
class CCPAComplianceManager:
    def __init__(self):
        self.data_categories = [
            "identifiers",
            "personal_info",
            "protected_characteristics",
            "commercial_info",
            "biometric_info",
            "internet_activity",
            "geolocation_data",
            "sensory_data",
            "professional_info",
            "education_info",
            "inferences"
        ]
        
        self.consumer_requests = {}
    
    def generate_privacy_notice(self, business_name, data_categories, purposes):
        """
        개인정보 처리방침 생성
        """
        notice = f"""
        {business_name} 개인정보 처리방침
        
        수집하는 개인정보 카테고리:
        {', '.join(data_categories)}
        
        개인정보 사용 목적:
        {', '.join(purposes)}
        
        귀하의 권리:
        - 개인정보 수집 및 판매 현황을 알 권리
        - 개인정보 삭제를 요구할 권리
        - 개인정보 판매를 거부할 권리
        - 차별받지 않을 권리
        
        문의: privacy@{business_name.lower().replace(' ', '')}.com
        """
        return notice
    
    def handle_consumer_request(self, request_type, consumer_id, details):
        """
        소비자 요청 처리
        """
        request_id = str(uuid.uuid4())
        
        request_record = {
            'request_id': request_id,
            'consumer_id': consumer_id,
            'request_type': request_type,  # 'know', 'delete', 'opt-out'
            'details': details,
            'timestamp': datetime.datetime.now().isoformat(),
            'status': 'received',
            'response_due_date': (datetime.datetime.now() + datetime.timedelta(days=45)).isoformat()
        }
        
        self.consumer_requests[request_id] = request_record
        return request_id
    
    def process_deletion_request(self, consumer_id):
        """
        삭제 요청 처리
        """
        # 실제 구현에서는 모든 시스템에서 해당 사용자 데이터 삭제
        deletion_log = {
            'consumer_id': consumer_id,
            'deletion_date': datetime.datetime.now().isoformat(),
            'systems_affected': ['database', 'backup', 'analytics', 'logs'],
            'verification_method': 'manual_review'
        }
        
        return deletion_log
```

### 데이터 수집 시 윤리적 고려사항

#### 투명성 원칙
```python
class DataCollectionTransparencyManager:
    def __init__(self):
        self.collection_activities = []
    
    def create_data_collection_notice(self, collection_purpose, data_types, retention_period):
        """
        데이터 수집 고지문 생성
        """
        notice = {
            'title': '데이터 수집 고지',
            'purpose': collection_purpose,
            'data_types': data_types,
            'retention_period': retention_period,
            'legal_basis': '정당한 이익' if collection_purpose == 'service_improvement' else '동의',
            'opt_out_method': '설정 > 개인정보 > 데이터 수집 거부',
            'contact_info': 'privacy@company.com',
            'effective_date': datetime.datetime.now().isoformat()
        }
        
        return notice
    
    def log_collection_activity(self, user_id, data_type, source, purpose):
        """
        데이터 수집 활동 로깅
        """
        activity = {
            'timestamp': datetime.datetime.now().isoformat(),
            'user_id': user_id,
            'data_type': data_type,
            'source': source,
            'purpose': purpose,
            'consent_status': self.check_consent_status(user_id, purpose)
        }
        
        self.collection_activities.append(activity)
        return activity
    
    def check_consent_status(self, user_id, purpose):
        """
        동의 상태 확인 (실제 구현에서는 동의 관리 시스템과 연동)
        """
        # 실제 동의 상태 확인 로직
        return "consented"  # 또는 "not_consented", "not_required"
    
    def generate_transparency_report(self):
        """
        투명성 보고서 생성
        """
        total_collections = len(self.collection_activities)
        consent_based = len([a for a in self.collection_activities if a['consent_status'] == 'consented'])
        
        report = {
            'total_data_collections': total_collections,
            'consent_based_collections': consent_based,
            'consent_rate': consent_based / total_collections if total_collections > 0 else 0,
            'data_types_collected': list(set([a['data_type'] for a in self.collection_activities])),
            'collection_sources': list(set([a['source'] for a in self.collection_activities])),
            'report_period': f"{min([a['timestamp'] for a in self.collection_activities])} ~ {max([a['timestamp'] for a in self.collection_activities])}"
        }
        
        return report
```

#### 최소 수집 원칙
```python
class MinimalDataCollectionManager:
    def __init__(self):
        self.collection_rules = {}
        self.purpose_definitions = {}
    
    def define_collection_purpose(self, purpose_id, description, required_data, optional_data):
        """
        수집 목적별 필요 데이터 정의
        """
        self.purpose_definitions[purpose_id] = {
            'description': description,
            'required_data': required_data,
            'optional_data': optional_data,
            'retention_period': None,
            'deletion_criteria': None
        }
    
    def validate_collection_request(self, purpose_id, requested_data):
        """
        수집 요청 데이터의 적절성 검증
        """
        if purpose_id not in self.purpose_definitions:
            return {'valid': False, 'reason': 'Unknown purpose'}
        
        purpose = self.purpose_definitions[purpose_id]
        required_data = set(purpose['required_data'])
        optional_data = set(purpose['optional_data'])
        allowed_data = required_data.union(optional_data)
        requested_set = set(requested_data)
        
        # 필수 데이터 누락 확인
        missing_required = required_data - requested_set
        if missing_required:
            return {
                'valid': False, 
                'reason': f'Missing required data: {missing_required}'
            }
        
        # 불필요한 데이터 요청 확인
        unnecessary_data = requested_set - allowed_data
        if unnecessary_data:
            return {
                'valid': False,
                'reason': f'Unnecessary data requested: {unnecessary_data}'
            }
        
        return {'valid': True, 'approved_data': list(requested_set)}
    
    def create_privacy_by_design_checklist(self):
        """
        Privacy by Design 체크리스트 생성
        """
        checklist = [
            "수집 목적이 명확하게 정의되었는가?",
            "수집하는 데이터가 목적 달성을 위한 최소한인가?",
            "사용자에게 수집 목적과 범위를 명확히 고지했는가?",
            "적절한 동의를 받았는가?",
            "데이터 보관 기간이 명시되었는가?",
            "데이터 삭제 절차가 수립되었는가?",
            "접근 권한이 적절히 제한되었는가?",
            "데이터 보안 조치가 적절한가?",
            "제3자 공유 시 별도 동의를 받는가?",
            "데이터 주체의 권리 행사 방법이 제공되는가?"
        ]
        
        return checklist

# 사용 예시
minimal_collector = MinimalDataCollectionManager()

# 회원가입 목적 정의
minimal_collector.define_collection_purpose(
    purpose_id="user_registration",
    description="서비스 회원가입",
    required_data=["email", "password"],
    optional_data=["name", "phone", "birth_year"]
)

# 수집 요청 검증
validation_result = minimal_collector.validate_collection_request(
    purpose_id="user_registration",
    requested_data=["email", "password", "name", "address"]  # address는 불필요
)
```

### 데이터 익명화 및 가명처리

#### 식별자 제거 및 마스킹
```python
import hashlib
import random
import string
from faker import Faker

class DataAnonymizer:
    def __init__(self):
        self.fake = Faker('ko_KR')
        self.salt = self.generate_salt()
    
    def generate_salt(self):
        """
        해시화를 위한 솔트 생성
        """
        return ''.join(random.choices(string.ascii_letters + string.digits, k=32))
    
    def hash_identifier(self, identifier):
        """
        식별자 해시화 (되돌릴 수 없는 익명화)
        """
        salted_id = f"{identifier}{self.salt}"
        return hashlib.sha256(salted_id.encode()).hexdigest()[:16]
    
    def mask_email(self, email):
        """
        이메일 마스킹
        """
        if '@' not in email:
            return email
        
        local, domain = email.split('@')
        if len(local) <= 2:
            masked_local = local[0] + '*' * (len(local) - 1)
        else:
            masked_local = local[0] + '*' * (len(local) - 2) + local[-1]
        
        return f"{masked_local}@{domain}"
    
    def mask_phone(self, phone):
        """
        전화번호 마스킹
        """
        # 숫자만 추출
        digits = ''.join(filter(str.isdigit, phone))
        if len(digits) >= 8:
            return digits[:3] + '-****-' + digits[-4:]
        return '*' * len(phone)
    
    def generalize_age(self, age, range_size=10):
        """
        나이 일반화 (연령대로 변환)
        """
        if age < 20:
            return "20세 미만"
        elif age >= 80:
            return "80세 이상"
        else:
            lower_bound = (age // range_size) * range_size
            upper_bound = lower_bound + range_size - 1
            return f"{lower_bound}-{upper_bound}세"
    
    def randomize_location(self, address, level='district'):
        """
        위치 정보 일반화
        """
        if level == 'city':
            # 시/도 단위로만 표시
            return address.split()[0] if address else ''
        elif level == 'district':
            # 구/군 단위까지 표시
            parts = address.split()
            return ' '.join(parts[:2]) if len(parts) >= 2 else address
        else:
            return address
    
    def k_anonymize_dataset(self, df, quasi_identifiers, k=5):
        """
        K-익명성 보장을 위한 데이터셋 일반화
        """
        # 각 quasi-identifier 조합의 빈도 계산
        group_counts = df.groupby(quasi_identifiers).size()
        
        # k 미만인 그룹 식별
        small_groups = group_counts[group_counts < k]
        
        if len(small_groups) == 0:
            return df  # 이미 k-익명성 만족
        
        # 일반화 로직 (간단한 예시)
        anonymized_df = df.copy()
        
        for group_values, count in small_groups.items():
            # 해당 그룹의 레코드들을 더 일반화
            mask = True
            for i, col in enumerate(quasi_identifiers):
                mask &= (anonymized_df[col] == group_values[i])
            
            # 일반화 적용 (예: 나이를 더 큰 범위로)
            if 'age' in quasi_identifiers:
                age_col_idx = quasi_identifiers.index('age')
                anonymized_df.loc[mask, 'age'] = self.generalize_age(
                    group_values[age_col_idx], range_size=20
                )
        
        return anonymized_df
    
    def differential_privacy_noise(self, value, epsilon=1.0, sensitivity=1.0):
        """
        차등프라이버시를 위한 라플라스 노이즈 추가
        """
        scale = sensitivity / epsilon
        noise = random.laplace(0, scale)
        return value + noise

# 사용 예시
anonymizer = DataAnonymizer()

# 샘플 데이터
sample_data = {
    'user_id': 'user123',
    'email': 'john.doe@example.com',
    'phone': '010-1234-5678',
    'age': 28,
    'address': '서울특별시 강남구 테헤란로 123'
}

# 익명화 적용
anonymized_data = {
    'user_id_hash': anonymizer.hash_identifier(sample_data['user_id']),
    'email_masked': anonymizer.mask_email(sample_data['email']),
    'phone_masked': anonymizer.mask_phone(sample_data['phone']),
    'age_group': anonymizer.generalize_age(sample_data['age']),
    'location': anonymizer.randomize_location(sample_data['address'], level='district')
}

print("익명화 결과:", anonymized_data)
```

### 동의 관리 시스템

#### 세분화된 동의 관리
```python
class ConsentManagementSystem:
    def __init__(self):
        self.consent_purposes = {
            'service_essential': {
                'name': '서비스 필수 기능',
                'description': '서비스 제공을 위한 필수적인 개인정보 처리',
                'required': True,
                'retention_period': 'service_termination'
            },
            'service_improvement': {
                'name': '서비스 개선',
                'description': '서비스 품질 향상을 위한 데이터 분석',
                'required': False,
                'retention_period': '2_years'
            },
            'marketing': {
                'name': '마케팅 활용',
                'description': '맞춤형 광고 및 마케팅 정보 제공',
                'required': False,
                'retention_period': '3_years'
            },
            'third_party_sharing': {
                'name': '제3자 정보 제공',
                'description': '제휴사와의 정보 공유',
                'required': False,
                'retention_period': '1_year'
            }
        }
        
        self.user_consents = {}
    
    def create_consent_form(self, user_id):
        """
        동적 동의서 생성
        """
        consent_form = {
            'user_id': user_id,
            'timestamp': datetime.datetime.now().isoformat(),
            'purposes': []
        }
        
        for purpose_id, purpose_info in self.consent_purposes.items():
            purpose_form = {
                'purpose_id': purpose_id,
                'name': purpose_info['name'],
                'description': purpose_info['description'],
                'required': purpose_info['required'],
                'retention_period': purpose_info['retention_period'],
                'consent_given': None,  # 사용자가 선택
                'legal_basis': 'consent' if not purpose_info['required'] else 'contract'
            }
            consent_form['purposes'].append(purpose_form)
        
        return consent_form
    
    def process_consent_response(self, consent_form):
        """
        동의서 응답 처리
        """
        user_id = consent_form['user_id']
        
        # 필수 항목 확인
        for purpose in consent_form['purposes']:
            if purpose['required'] and not purpose['consent_given']:
                return {
                    'success': False,
                    'error': f"필수 항목에 동의가 필요합니다: {purpose['name']}"
                }
        
        # 동의 정보 저장
        self.user_consents[user_id] = {
            'timestamp': datetime.datetime.now().isoformat(),
            'consents': {
                purpose['purpose_id']: {
                    'granted': purpose['consent_given'],
                    'timestamp': datetime.datetime.now().isoformat(),
                    'version': '1.0'
                }
                for purpose in consent_form['purposes']
            }
        }
        
        return {'success': True, 'user_id': user_id}
    
    def update_consent(self, user_id, purpose_id, new_consent_value):
        """
        동의 정보 업데이트
        """
        if user_id not in self.user_consents:
            return {'success': False, 'error': 'User not found'}
        
        if purpose_id not in self.consent_purposes:
            return {'success': False, 'error': 'Invalid purpose'}
        
        # 필수 항목은 철회 불가
        if self.consent_purposes[purpose_id]['required'] and not new_consent_value:
            return {
                'success': False, 
                'error': 'Cannot withdraw consent for required purposes'
            }
        
        self.user_consents[user_id]['consents'][purpose_id] = {
            'granted': new_consent_value,
            'timestamp': datetime.datetime.now().isoformat(),
            'version': '1.0'
        }
        
        return {'success': True}
    
    def check_data_processing_permission(self, user_id, purpose_id):
        """
        특정 목적의 데이터 처리 권한 확인
        """
        if user_id not in self.user_consents:
            return False
        
        consent_info = self.user_consents[user_id]['consents'].get(purpose_id)
        if not consent_info:
            return False
        
        return consent_info['granted']
    
    def generate_consent_dashboard(self, user_id):
        """
        사용자용 동의 현황 대시보드 생성
        """
        if user_id not in self.user_consents:
            return None
        
        user_consent_data = self.user_consents[user_id]
        dashboard = {
            'user_id': user_id,
            'last_updated': user_consent_data['timestamp'],
            'consent_status': []
        }
        
        for purpose_id, purpose_info in self.consent_purposes.items():
            consent_status = user_consent_data['consents'].get(purpose_id, {})
            
            status_item = {
                'purpose_id': purpose_id,
                'name': purpose_info['name'],
                'description': purpose_info['description'],
                'required': purpose_info['required'],
                'current_status': consent_status.get('granted', False),
                'last_changed': consent_status.get('timestamp'),
                'can_withdraw': not purpose_info['required']
            }
            
            dashboard['consent_status'].append(status_item)
        
        return dashboard

# 사용 예시
cms = ConsentManagementSystem()

# 동의서 생성
consent_form = cms.create_consent_form('user456')

# 사용자 응답 시뮬레이션
consent_form['purposes'][0]['consent_given'] = True  # 필수
consent_form['purposes'][1]['consent_given'] = True  # 서비스 개선
consent_form['purposes'][2]['consent_given'] = False  # 마케팅
consent_form['purposes'][3]['consent_given'] = False  # 제3자 제공

# 동의 처리
result = cms.process_consent_response(consent_form)
print("동의 처리 결과:", result)

# 동의 현황 대시보드
dashboard = cms.generate_consent_dashboard('user456')
```

### 데이터 보안 및 접근 제어

#### 역할 기반 접근 제어 (RBAC)
```python
import json
from enum import Enum
from datetime import datetime, timedelta

class DataSensitivityLevel(Enum):
    PUBLIC = "public"
    INTERNAL = "internal"
    CONFIDENTIAL = "confidential"
    RESTRICTED = "restricted"

class DataAccessController:
    def __init__(self):
        self.users = {}
        self.roles = {}
        self.data_classifications = {}
        self.access_logs = []
    
    def define_role(self, role_name, permissions, data_access_levels):
        """
        역할 정의
        """
        self.roles[role_name] = {
            'permissions': permissions,
            'data_access_levels': data_access_levels,
            'created_at': datetime.now().isoformat()
        }
    
    def create_user(self, user_id, name, email, roles):
        """
        사용자 생성
        """
        self.users[user_id] = {
            'name': name,
            'email': email,
            'roles': roles,
            'created_at': datetime.now().isoformat(),
            'last_access': None,
            'is_active': True
        }
    
    def classify_data(self, dataset_id, sensitivity_level, data_types, purposes):
        """
        데이터 분류
        """
        self.data_classifications[dataset_id] = {
            'sensitivity_level': sensitivity_level,
            'data_types': data_types,
            'purposes': purposes,
            'classified_at': datetime.now().isoformat(),
            'retention_period': self.calculate_retention_period(data_types, purposes)
        }
    
    def calculate_retention_period(self, data_types, purposes):
        """
        데이터 유형과 목적에 따른 보관 기간 계산
        """
        # 간단한 예시 로직
        if 'financial' in data_types:
            return '7_years'
        elif 'marketing' in purposes:
            return '3_years'
        else:
            return '2_years'
    
    def check_access_permission(self, user_id, dataset_id, operation):
        """
        데이터 접근 권한 확인
        """
        # 사용자 존재 및 활성 상태 확인
        if user_id not in self.users or not self.users[user_id]['is_active']:
            return {'allowed': False, 'reason': 'User not found or inactive'}
        
        # 데이터셋 분류 정보 확인
        if dataset_id not in self.data_classifications:
            return {'allowed': False, 'reason': 'Dataset not classified'}
        
        user = self.users[user_id]
        dataset = self.data_classifications[dataset_id]
        
        # 사용자 역할별 권한 확인
        for role_name in user['roles']:
            if role_name not in self.roles:
                continue
            
            role = self.roles[role_name]
            
            # 작업 권한 확인
            if operation not in role['permissions']:
                continue
            
            # 데이터 민감도 수준 확인
            if dataset['sensitivity_level'] in role['data_access_levels']:
                # 접근 로그 기록
                self.log_access_attempt(user_id, dataset_id, operation, True)
                return {'allowed': True, 'role': role_name}
        
        # 접근 거부 로그 기록
        self.log_access_attempt(user_id, dataset_id, operation, False)
        return {'allowed': False, 'reason': 'Insufficient permissions'}
    
    def log_access_attempt(self, user_id, dataset_id, operation, allowed):
        """
        접근 시도 로그 기록
        """
        log_entry = {
            'timestamp': datetime.now().isoformat(),
            'user_id': user_id,
            'dataset_id': dataset_id,
            'operation': operation,
            'allowed': allowed,
            'ip_address': '192.168.1.100',  # 실제로는 요청에서 추출
            'user_agent': 'DataAnalysisTool/1.0'  # 실제로는 요청에서 추출
        }
        
        self.access_logs.append(log_entry)
    
    def audit_access_logs(self, start_date=None, end_date=None):
        """
        접근 로그 감사
        """
        filtered_logs = self.access_logs
        
        if start_date or end_date:
            filtered_logs = []
            for log in self.access_logs:
                log_time = datetime.fromisoformat(log['timestamp'])
                
                if start_date and log_time < start_date:
                    continue
                if end_date and log_time > end_date:
                    continue
                
                filtered_logs.append(log)
        
        # 통계 생성
        total_attempts = len(filtered_logs)
        denied_attempts = len([log for log in filtered_logs if not log['allowed']])
        
        audit_report = {
            'period': f"{start_date} ~ {end_date}" if start_date or end_date else "전체",
            'total_access_attempts': total_attempts,
            'denied_attempts': denied_attempts,
            'denial_rate': denied_attempts / total_attempts if total_attempts > 0 else 0,
            'most_accessed_datasets': self.get_most_accessed_datasets(filtered_logs),
            'suspicious_activities': self.detect_suspicious_activities(filtered_logs)
        }
        
        return audit_report
    
    def get_most_accessed_datasets(self, logs):
        """
        가장 많이 접근된 데이터셋 분석
        """
        dataset_counts = {}
        for log in logs:
            if log['allowed']:
                dataset_counts[log['dataset_id']] = dataset_counts.get(log['dataset_id'], 0) + 1
        
        return sorted(dataset_counts.items(), key=lambda x: x[1], reverse=True)[:10]
    
    def detect_suspicious_activities(self, logs):
        """
        의심스러운 활동 탐지
        """
        suspicious = []
        
        # 짧은 시간 내 다수 접근 시도
        user_attempts = {}
        for log in logs:
            user_id = log['user_id']
            if user_id not in user_attempts:
                user_attempts[user_id] = []
            user_attempts[user_id].append(log)
        
        for user_id, attempts in user_attempts.items():
            # 1시간 내 50회 이상 접근 시도
            recent_attempts = [
                a for a in attempts 
                if datetime.fromisoformat(a['timestamp']) > datetime.now() - timedelta(hours=1)
            ]
            
            if len(recent_attempts) > 50:
                suspicious.append({
                    'type': 'high_frequency_access',
                    'user_id': user_id,
                    'attempt_count': len(recent_attempts),
                    'time_window': '1_hour'
                })
        
        return suspicious

# 사용 예시
access_controller = DataAccessController()

# 역할 정의
access_controller.define_role(
    role_name='data_analyst',
    permissions=['read', 'query'],
    data_access_levels=[DataSensitivityLevel.PUBLIC.value, DataSensitivityLevel.INTERNAL.value]
)

access_controller.define_role(
    role_name='data_scientist',
    permissions=['read', 'query', 'analyze'],
    data_access_levels=[DataSensitivityLevel.PUBLIC.value, DataSensitivityLevel.INTERNAL.value, DataSensitivityLevel.CONFIDENTIAL.value]
)

# 사용자 생성
access_controller.create_user(
    user_id='analyst001',
    name='김분석',
    email='analyst@company.com',
    roles=['data_analyst']
)

# 데이터 분류
access_controller.classify_data(
    dataset_id='customer_data',
    sensitivity_level=DataSensitivityLevel.CONFIDENTIAL.value,
    data_types=['personal_info', 'behavioral'],
    purposes=['service_improvement']
)

# 접근 권한 확인
permission_result = access_controller.check_access_permission(
    user_id='analyst001',
    dataset_id='customer_data',
    operation='read'
)

print("접근 권한 결과:", permission_result)
```

### 데이터 라이프사이클 관리

#### 자동 데이터 삭제 시스템
```python
import schedule
import time
from datetime import datetime, timedelta

class DataLifecycleManager:
    def __init__(self):
        self.retention_policies = {}
        self.deletion_logs = []
        self.data_inventory = {}
    
    def define_retention_policy(self, policy_name, data_types, retention_period, deletion_method):
        """
        데이터 보관 정책 정의
        """
        self.retention_policies[policy_name] = {
            'data_types': data_types,
            'retention_period': retention_period,  # days
            'deletion_method': deletion_method,  # 'hard_delete', 'anonymize', 'archive'
            'created_at': datetime.now(),
            'is_active': True
        }
    
    def register_data(self, data_id, data_type, created_at, metadata=None):
        """
        데이터 등록
        """
        self.data_inventory[data_id] = {
            'data_type': data_type,
            'created_at': created_at,
            'metadata': metadata or {},
            'status': 'active',
            'scheduled_deletion': None
        }
    
    def calculate_deletion_date(self, data_id):
        """
        삭제 예정일 계산
        """
        if data_id not in self.data_inventory:
            return None
        
        data_info = self.data_inventory[data_id]
        data_type = data_info['data_type']
        
        # 해당 데이터 타입에 적용되는 정책 찾기
        applicable_policy = None
        for policy_name, policy in self.retention_policies.items():
            if data_type in policy['data_types'] and policy['is_active']:
                applicable_policy = policy
                break
        
        if not applicable_policy:
            return None
        
        deletion_date = data_info['created_at'] + timedelta(days=applicable_policy['retention_period'])
        return deletion_date
    
    def schedule_data_deletion(self):
        """
        삭제 예정 데이터 스케줄링
        """
        scheduled_count = 0
        
        for data_id, data_info in self.data_inventory.items():
            if data_info['status'] != 'active':
                continue
            
            deletion_date = self.calculate_deletion_date(data_id)
            if deletion_date and datetime.now() >= deletion_date:
                data_info['scheduled_deletion'] = datetime.now()
                scheduled_count += 1
        
        return scheduled_count
    
    def execute_scheduled_deletions(self):
        """
        예정된 삭제 작업 실행
        """
        deleted_count = 0
        
        for data_id, data_info in self.data_inventory.items():
            if data_info['scheduled_deletion'] and data_info['status'] == 'active':
                # 해당 데이터 타입의 정책 찾기
                data_type = data_info['data_type']
                policy = None
                
                for policy_name, p in self.retention_policies.items():
                    if data_type in p['data_types']:
                        policy = p
                        break
                
                if policy:
                    success = self.execute_deletion(data_id, policy['deletion_method'])
                    if success:
                        deleted_count += 1
        
        return deleted_count
    
    def execute_deletion(self, data_id, deletion_method):
        """
        실제 삭제 작업 수행
        """
        try:
            if deletion_method == 'hard_delete':
                # 실제 데이터 완전 삭제
                result = self.hard_delete_data(data_id)
            elif deletion_method == 'anonymize':
                # 개인정보 익명화
                result = self.anonymize_data(data_id)
            elif deletion_method == 'archive':
                # 아카이브로 이동
                result = self.archive_data(data_id)
            else:
                result = False
            
            if result:
                # 삭제 로그 기록
                self.deletion_logs.append({
                    'data_id': data_id,
                    'deletion_method': deletion_method,
                    'executed_at': datetime.now(),
                    'status': 'success'
                })
                
                # 인벤토리에서 상태 업데이트
                self.data_inventory[data_id]['status'] = 'deleted'
                
            return result
            
        except Exception as e:
            # 삭제 실패 로그
            self.deletion_logs.append({
                'data_id': data_id,
                'deletion_method': deletion_method,
                'executed_at': datetime.now(),
                'status': 'failed',
                'error': str(e)
            })
            return False
    
    def hard_delete_data(self, data_id):
        """
        데이터 완전 삭제 (실제 구현에서는 데이터베이스/파일 시스템에서 삭제)
        """
        print(f"완전 삭제 실행: {data_id}")
        return True
    
    def anonymize_data(self, data_id):
        """
        데이터 익명화 (실제 구현에서는 개인정보 필드를 익명화)
        """
        print(f"익명화 실행: {data_id}")
        return True
    
    def archive_data(self, data_id):
        """
        데이터 아카이브 (실제 구현에서는 별도 저장소로 이동)
        """
        print(f"아카이브 실행: {data_id}")
        return True
    
    def generate_compliance_report(self):
        """
        규정 준수 보고서 생성
        """
        total_data = len(self.data_inventory)
        active_data = len([d for d in self.data_inventory.values() if d['status'] == 'active'])
        deleted_data = len([d for d in self.data_inventory.values() if d['status'] == 'deleted'])
        
        # 보관 기간 초과 데이터 확인
        overdue_data = []
        for data_id, data_info in self.data_inventory.items():
            if data_info['status'] == 'active':
                deletion_date = self.calculate_deletion_date(data_id)
                if deletion_date and datetime.now() > deletion_date:
                    overdue_data.append(data_id)
        
        report = {
            'report_date': datetime.now().isoformat(),
            'total_data_records': total_data,
            'active_records': active_data,
            'deleted_records': deleted_data,
            'overdue_deletions': len(overdue_data),
            'deletion_success_rate': self.calculate_deletion_success_rate(),
            'retention_policies': len(self.retention_policies),
            'compliance_status': 'compliant' if len(overdue_data) == 0 else 'non_compliant'
        }
        
        return report
    
    def calculate_deletion_success_rate(self):
        """
        삭제 성공률 계산
        """
        if not self.deletion_logs:
            return 1.0
        
        successful_deletions = len([log for log in self.deletion_logs if log['status'] == 'success'])
        return successful_deletions / len(self.deletion_logs)
    
    def start_automated_lifecycle_management(self):
        """
        자동화된 데이터 라이프사이클 관리 시작
        """
        # 매일 자정에 삭제 스케줄링
        schedule.every().day.at("00:00").do(self.schedule_data_deletion)
        
        # 매일 오전 2시에 삭제 실행
        schedule.every().day.at("02:00").do(self.execute_scheduled_deletions)
        
        print("자동화된 데이터 라이프사이클 관리 시작")
        
        while True:
            schedule.run_pending()
            time.sleep(3600)  # 1시간마다 스케줄 확인

# 사용 예시
lifecycle_manager = DataLifecycleManager()

# 보관 정책 정의
lifecycle_manager.define_retention_policy(
    policy_name='user_activity_logs',
    data_types=['activity_log', 'session_log'],
    retention_period=90,  # 90일
    deletion_method='anonymize'
)

lifecycle_manager.define_retention_policy(
    policy_name='marketing_data',
    data_types=['email_campaign', 'user_preferences'],
    retention_period=1095,  # 3년
    deletion_method='hard_delete'
)

# 데이터 등록
lifecycle_manager.register_data(
    data_id='user_activity_001',
    data_type='activity_log',
    created_at=datetime.now() - timedelta(days=100)  # 100일 전 생성
)

# 규정 준수 보고서
compliance_report = lifecycle_manager.generate_compliance_report()
print("규정 준수 보고서:", json.dumps(compliance_report, indent=2, ensure_ascii=False))
```

데이터 수집 시 개인정보보호와 윤리적 고려사항은 단순한 규정 준수를 넘어 조직의 신뢰성과 지속가능성을 보장하는 핵심 요소이다. 체계적인 거버넌스 프레임워크와 기술적 보호조치를 통해 데이터 주체의 권리를 보호하면서도 데이터의 가치를 극대화할 수 있다. 