+++
title = "파일 형식별 데이터 처리"
type = "docs"
weight = 1055
+++

## 파일 형식별 데이터 처리

### 개요

데이터는 다양한 형식으로 저장되고 전송되며, 각 형식마다 고유한 특성과 처리 방법이 있다. 효율적인 데이터 수집을 위해서는 각 파일 형식의 특성을 이해하고 적절한 도구와 기법을 사용해야 한다.

### 구조화된 데이터 형식

#### CSV (Comma-Separated Values)
```python
import pandas as pd
import csv
from io import StringIO

class CSVProcessor:
    def __init__(self):
        self.encoding_options = ['utf-8', 'cp949', 'euc-kr', 'latin-1']
    
    def read_csv_safe(self, file_path, **kwargs):
        """안전한 CSV 읽기 (인코딩 자동 감지)"""
        for encoding in self.encoding_options:
            try:
                df = pd.read_csv(file_path, encoding=encoding, **kwargs)
                print(f"성공적으로 읽음: {encoding} 인코딩")
                return df
            except UnicodeDecodeError:
                continue
            except Exception as e:
                print(f"오류 발생 ({encoding}): {e}")
                continue
        
        raise Exception("모든 인코딩 옵션으로 읽기 실패")
    
    def detect_delimiter(self, file_path, sample_size=1024):
        """구분자 자동 감지"""
        with open(file_path, 'r', encoding='utf-8') as file:
            sample = file.read(sample_size)
            
        sniffer = csv.Sniffer()
        delimiter = sniffer.sniff(sample).delimiter
        return delimiter
    
    def process_large_csv(self, file_path, chunk_size=10000, process_func=None):
        """대용량 CSV 청크 단위 처리"""
        results = []
        
        for chunk in pd.read_csv(file_path, chunksize=chunk_size):
            if process_func:
                processed_chunk = process_func(chunk)
                results.append(processed_chunk)
            else:
                results.append(chunk)
        
        return pd.concat(results, ignore_index=True)
    
    def validate_csv_structure(self, df, expected_columns=None, required_columns=None):
        """CSV 구조 검증"""
        validation_result = {
            'is_valid': True,
            'issues': []
        }
        
        if expected_columns:
            missing_cols = set(expected_columns) - set(df.columns)
            if missing_cols:
                validation_result['is_valid'] = False
                validation_result['issues'].append(f"누락된 컬럼: {missing_cols}")
        
        if required_columns:
            for col in required_columns:
                if col in df.columns:
                    null_count = df[col].isnull().sum()
                    if null_count > 0:
                        validation_result['issues'].append(f"{col}: {null_count}개 null 값")
        
        return validation_result

# 사용 예시
csv_processor = CSVProcessor()
df = csv_processor.read_csv_safe('data.csv')
delimiter = csv_processor.detect_delimiter('data.csv')
validation = csv_processor.validate_csv_structure(df, expected_columns=['name', 'age', 'email'])
```

#### JSON (JavaScript Object Notation)
```python
import json
import pandas as pd
from pandas import json_normalize

class JSONProcessor:
    def __init__(self):
        pass
    
    def read_json_file(self, file_path, lines=False):
        """JSON 파일 읽기"""
        try:
            if lines:
                # JSON Lines 형식
                data = []
                with open(file_path, 'r', encoding='utf-8') as file:
                    for line in file:
                        data.append(json.loads(line.strip()))
                return data
            else:
                # 일반 JSON 형식
                with open(file_path, 'r', encoding='utf-8') as file:
                    return json.load(file)
        except Exception as e:
            print(f"JSON 읽기 오류: {e}")
            return None
    
    def flatten_json(self, data, separator='_'):
        """중첩된 JSON 구조 평면화"""
        if isinstance(data, list):
            return [self.flatten_json(item, separator) for item in data]
        elif isinstance(data, dict):
            flattened = {}
            for key, value in data.items():
                if isinstance(value, dict):
                    nested = self.flatten_json(value, separator)
                    for nested_key, nested_value in nested.items():
                        flattened[f"{key}{separator}{nested_key}"] = nested_value
                elif isinstance(value, list) and value and isinstance(value[0], dict):
                    # 리스트 내 딕셔너리 처리
                    for i, item in enumerate(value):
                        nested = self.flatten_json(item, separator)
                        for nested_key, nested_value in nested.items():
                            flattened[f"{key}{separator}{i}{separator}{nested_key}"] = nested_value
                else:
                    flattened[key] = value
            return flattened
        else:
            return data
    
    def json_to_dataframe(self, json_data, normalize_level=0):
        """JSON 데이터를 DataFrame으로 변환"""
        if isinstance(json_data, list):
            if normalize_level > 0:
                return json_normalize(json_data, sep='_', max_level=normalize_level)
            else:
                return pd.DataFrame(json_data)
        elif isinstance(json_data, dict):
            return pd.DataFrame([json_data])
        else:
            raise ValueError("지원되지 않는 JSON 구조")
    
    def extract_nested_fields(self, data, field_path):
        """중첩된 필드 추출 (예: 'user.profile.name')"""
        try:
            fields = field_path.split('.')
            result = data
            
            for field in fields:
                if isinstance(result, list):
                    result = [item.get(field) if isinstance(item, dict) else None for item in result]
                elif isinstance(result, dict):
                    result = result.get(field)
                else:
                    return None
            
            return result
        except Exception:
            return None

# 사용 예시
json_processor = JSONProcessor()

# 샘플 JSON 데이터
sample_json = {
    "users": [
        {
            "id": 1,
            "name": "김철수",
            "profile": {
                "age": 30,
                "address": {
                    "city": "서울",
                    "district": "강남구"
                }
            },
            "orders": [
                {"id": 101, "amount": 50000},
                {"id": 102, "amount": 30000}
            ]
        }
    ]
}

flattened = json_processor.flatten_json(sample_json)
df = json_processor.json_to_dataframe(sample_json['users'])
```

#### XML (eXtensible Markup Language)
```python
import xml.etree.ElementTree as ET
import pandas as pd
from bs4 import BeautifulSoup
import xmltodict

class XMLProcessor:
    def __init__(self):
        pass
    
    def parse_xml_to_dict(self, file_path):
        """XML을 딕셔너리로 변환"""
        try:
            with open(file_path, 'r', encoding='utf-8') as file:
                xml_content = file.read()
            
            return xmltodict.parse(xml_content)
        except Exception as e:
            print(f"XML 파싱 오류: {e}")
            return None
    
    def xml_to_dataframe(self, file_path, record_path=None):
        """XML을 DataFrame으로 변환"""
        try:
            tree = ET.parse(file_path)
            root = tree.getroot()
            
            records = []
            
            if record_path:
                # 특정 경로의 요소들을 레코드로 처리
                for element in root.findall(record_path):
                    record = {}
                    for child in element:
                        record[child.tag] = child.text
                    records.append(record)
            else:
                # 루트의 직접 자식들을 레코드로 처리
                for child in root:
                    record = {}
                    for subchild in child:
                        record[subchild.tag] = subchild.text
                    records.append(record)
            
            return pd.DataFrame(records)
        
        except Exception as e:
            print(f"XML to DataFrame 변환 오류: {e}")
            return None
    
    def extract_xml_attributes(self, file_path, element_path):
        """XML 요소의 속성 추출"""
        try:
            tree = ET.parse(file_path)
            root = tree.getroot()
            
            attributes_list = []
            
            for element in root.findall(element_path):
                attributes = element.attrib.copy()
                attributes['text'] = element.text
                attributes_list.append(attributes)
            
            return attributes_list
        
        except Exception as e:
            print(f"XML 속성 추출 오류: {e}")
            return []

# 사용 예시
xml_processor = XMLProcessor()

# 샘플 XML 파일 생성
sample_xml = """<?xml version="1.0" encoding="UTF-8"?>
<customers>
    <customer id="1">
        <name>김철수</name>
        <email>kim@example.com</email>
        <age>30</age>
    </customer>
    <customer id="2">
        <name>이영희</name>
        <email>lee@example.com</email>
        <age>25</age>
    </customer>
</customers>"""

# 파일로 저장
with open('sample.xml', 'w', encoding='utf-8') as f:
    f.write(sample_xml)

df = xml_processor.xml_to_dataframe('sample.xml', './/customer')
```

### 반구조화된 데이터 형식

#### Excel 파일
```python
import pandas as pd
import openpyxl
from openpyxl.styles import PatternFill

class ExcelProcessor:
    def __init__(self):
        pass
    
    def read_excel_advanced(self, file_path, **kwargs):
        """고급 Excel 읽기"""
        try:
            # 모든 시트 이름 가져오기
            excel_file = pd.ExcelFile(file_path)
            sheet_names = excel_file.sheet_names
            
            sheets_data = {}
            
            for sheet_name in sheet_names:
                try:
                    df = pd.read_excel(file_path, sheet_name=sheet_name, **kwargs)
                    sheets_data[sheet_name] = df
                except Exception as e:
                    print(f"시트 '{sheet_name}' 읽기 실패: {e}")
            
            return sheets_data
        
        except Exception as e:
            print(f"Excel 파일 읽기 오류: {e}")
            return None
    
    def detect_data_range(self, file_path, sheet_name=0):
        """데이터 범위 자동 감지"""
        workbook = openpyxl.load_workbook(file_path)
        
        if isinstance(sheet_name, int):
            worksheet = workbook.worksheets[sheet_name]
        else:
            worksheet = workbook[sheet_name]
        
        # 사용된 범위 감지
        min_row = worksheet.min_row
        max_row = worksheet.max_row
        min_col = worksheet.min_column
        max_col = worksheet.max_column
        
        return {
            'min_row': min_row,
            'max_row': max_row,
            'min_col': min_col,
            'max_col': max_col,
            'range': f"{worksheet.cell(min_row, min_col).coordinate}:{worksheet.cell(max_row, max_col).coordinate}"
        }
    
    def handle_merged_cells(self, file_path, sheet_name=0):
        """병합된 셀 처리"""
        workbook = openpyxl.load_workbook(file_path)
        
        if isinstance(sheet_name, int):
            worksheet = workbook.worksheets[sheet_name]
        else:
            worksheet = workbook[sheet_name]
        
        # 병합된 셀 정보 수집
        merged_ranges = list(worksheet.merged_cells.ranges)
        
        # 병합 해제 및 값 복제
        for merged_range in merged_ranges:
            # 병합된 영역의 첫 번째 셀 값 가져오기
            min_col, min_row, max_col, max_row = merged_range.bounds
            first_cell_value = worksheet.cell(min_row, min_col).value
            
            # 병합 해제
            worksheet.unmerge_cells(str(merged_range))
            
            # 모든 셀에 값 복제
            for row in range(min_row, max_row + 1):
                for col in range(min_col, max_col + 1):
                    worksheet.cell(row, col).value = first_cell_value
        
        return workbook
    
    def export_to_excel(self, dataframes_dict, output_path):
        """여러 DataFrame을 Excel 파일로 내보내기"""
        with pd.ExcelWriter(output_path, engine='openpyxl') as writer:
            for sheet_name, df in dataframes_dict.items():
                df.to_excel(writer, sheet_name=sheet_name, index=False)
                
                # 스타일링 적용
                workbook = writer.book
                worksheet = writer.sheets[sheet_name]
                
                # 헤더 스타일링
                header_fill = PatternFill(start_color="366092", end_color="366092", fill_type="solid")
                for cell in worksheet[1]:
                    cell.fill = header_fill

# 사용 예시
excel_processor = ExcelProcessor()
sheets_data = excel_processor.read_excel_advanced('data.xlsx')
data_range = excel_processor.detect_data_range('data.xlsx', 'Sheet1')
```

### 비구조화된 데이터 형식

#### 텍스트 파일
```python
import re
import chardet
from collections import Counter

class TextProcessor:
    def __init__(self):
        self.encoding_cache = {}
    
    def detect_encoding(self, file_path):
        """파일 인코딩 감지"""
        if file_path in self.encoding_cache:
            return self.encoding_cache[file_path]
        
        with open(file_path, 'rb') as file:
            raw_data = file.read()
            result = chardet.detect(raw_data)
            
        encoding = result['encoding']
        confidence = result['confidence']
        
        self.encoding_cache[file_path] = {'encoding': encoding, 'confidence': confidence}
        
        return {'encoding': encoding, 'confidence': confidence}
    
    def read_text_file(self, file_path, encoding=None):
        """텍스트 파일 안전 읽기"""
        if not encoding:
            detection_result = self.detect_encoding(file_path)
            encoding = detection_result['encoding']
        
        try:
            with open(file_path, 'r', encoding=encoding) as file:
                return file.read()
        except Exception as e:
            print(f"파일 읽기 오류: {e}")
            return None
    
    def extract_structured_data(self, text, patterns):
        """정규식을 사용한 구조화된 데이터 추출"""
        extracted_data = {}
        
        for field_name, pattern in patterns.items():
            matches = re.findall(pattern, text, re.MULTILINE | re.IGNORECASE)
            extracted_data[field_name] = matches
        
        return extracted_data
    
    def parse_log_file(self, file_path, log_pattern=None):
        """로그 파일 파싱"""
        if log_pattern is None:
            # 기본 로그 패턴 (Apache Combined Log Format)
            log_pattern = r'(\S+) \S+ \S+ \[([\w:/]+\s[+\-]\d{4})\] "(\S+) (\S+) (\S+)" (\d{3}) (\d+) "([^"]*)" "([^"]*)"'
        
        text_content = self.read_text_file(file_path)
        if not text_content:
            return None
        
        log_entries = []
        
        for line in text_content.split('\n'):
            if line.strip():
                match = re.match(log_pattern, line)
                if match:
                    log_entries.append(match.groups())
        
        return log_entries
    
    def analyze_text_statistics(self, text):
        """텍스트 통계 분석"""
        words = re.findall(r'\b\w+\b', text.lower())
        
        statistics = {
            'total_characters': len(text),
            'total_words': len(words),
            'unique_words': len(set(words)),
            'average_word_length': sum(len(word) for word in words) / len(words) if words else 0,
            'most_common_words': Counter(words).most_common(10),
            'lines': len(text.split('\n')),
            'sentences': len(re.split(r'[.!?]+', text))
        }
        
        return statistics

# 사용 예시
text_processor = TextProcessor()

# 이메일 주소와 전화번호 추출 패턴
patterns = {
    'emails': r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b',
    'phones': r'\b\d{2,3}-\d{3,4}-\d{4}\b',
    'urls': r'http[s]?://(?:[a-zA-Z]|[0-9]|[$-_@.&+]|[!*\\(\\),]|(?:%[0-9a-fA-F][0-9a-fA-F]))+'
}

text_content = text_processor.read_text_file('sample.txt')
extracted = text_processor.extract_structured_data(text_content, patterns)
stats = text_processor.analyze_text_statistics(text_content)
```

#### PDF 파일
```python
import PyPDF2
import pdfplumber
import pandas as pd
from io import StringIO

class PDFProcessor:
    def __init__(self):
        pass
    
    def extract_text_pypdf2(self, file_path):
        """PyPDF2를 사용한 텍스트 추출"""
        try:
            with open(file_path, 'rb') as file:
                pdf_reader = PyPDF2.PdfReader(file)
                text = ""
                
                for page in pdf_reader.pages:
                    text += page.extract_text() + "\n"
                
                return text
        except Exception as e:
            print(f"PDF 텍스트 추출 오류: {e}")
            return None
    
    def extract_text_pdfplumber(self, file_path):
        """pdfplumber를 사용한 고급 텍스트 추출"""
        try:
            text = ""
            
            with pdfplumber.open(file_path) as pdf:
                for page in pdf.pages:
                    page_text = page.extract_text()
                    if page_text:
                        text += page_text + "\n"
            
            return text
        except Exception as e:
            print(f"PDF 텍스트 추출 오류: {e}")
            return None
    
    def extract_tables_from_pdf(self, file_path, page_numbers=None):
        """PDF에서 테이블 추출"""
        try:
            tables = []
            
            with pdfplumber.open(file_path) as pdf:
                pages_to_process = page_numbers or range(len(pdf.pages))
                
                for page_num in pages_to_process:
                    if page_num < len(pdf.pages):
                        page = pdf.pages[page_num]
                        page_tables = page.extract_tables()
                        
                        for table in page_tables:
                            if table:  # 빈 테이블 제외
                                df = pd.DataFrame(table[1:], columns=table[0])
                                tables.append({
                                    'page': page_num + 1,
                                    'dataframe': df
                                })
            
            return tables
        except Exception as e:
            print(f"PDF 테이블 추출 오류: {e}")
            return []
    
    def get_pdf_metadata(self, file_path):
        """PDF 메타데이터 추출"""
        try:
            with open(file_path, 'rb') as file:
                pdf_reader = PyPDF2.PdfReader(file)
                metadata = pdf_reader.metadata
                
                info = {
                    'title': metadata.get('/Title', ''),
                    'author': metadata.get('/Author', ''),
                    'subject': metadata.get('/Subject', ''),
                    'creator': metadata.get('/Creator', ''),
                    'producer': metadata.get('/Producer', ''),
                    'creation_date': metadata.get('/CreationDate', ''),
                    'modification_date': metadata.get('/ModDate', ''),
                    'pages': len(pdf_reader.pages)
                }
                
                return info
        except Exception as e:
            print(f"PDF 메타데이터 추출 오류: {e}")
            return {}

# 사용 예시
pdf_processor = PDFProcessor()
text = pdf_processor.extract_text_pdfplumber('document.pdf')
tables = pdf_processor.extract_tables_from_pdf('document.pdf')
metadata = pdf_processor.get_pdf_metadata('document.pdf')
```

### 대용량 파일 처리

#### 스트리밍 처리
```python
import os
import mmap
from itertools import islice

class LargeFileProcessor:
    def __init__(self):
        pass
    
    def process_file_in_chunks(self, file_path, chunk_size=1024*1024, process_func=None):
        """파일을 청크 단위로 처리"""
        results = []
        
        with open(file_path, 'r', encoding='utf-8') as file:
            while True:
                chunk = file.read(chunk_size)
                if not chunk:
                    break
                
                if process_func:
                    result = process_func(chunk)
                    results.append(result)
                else:
                    results.append(chunk)
        
        return results
    
    def read_lines_batch(self, file_path, batch_size=1000):
        """라인을 배치 단위로 읽기"""
        with open(file_path, 'r', encoding='utf-8') as file:
            while True:
                batch = list(islice(file, batch_size))
                if not batch:
                    break
                yield batch
    
    def memory_mapped_search(self, file_path, search_term):
        """메모리 맵을 사용한 대용량 파일 검색"""
        matches = []
        
        with open(file_path, 'r', encoding='utf-8') as file:
            with mmap.mmap(file.fileno(), 0, access=mmap.ACCESS_READ) as mmapped_file:
                content = mmapped_file.read().decode('utf-8')
                
                lines = content.split('\n')
                for line_num, line in enumerate(lines, 1):
                    if search_term in line:
                        matches.append({
                            'line_number': line_num,
                            'content': line.strip()
                        })
        
        return matches
    
    def get_file_info(self, file_path):
        """파일 정보 조회"""
        stat = os.stat(file_path)
        
        return {
            'size_bytes': stat.st_size,
            'size_mb': stat.st_size / (1024 * 1024),
            'size_gb': stat.st_size / (1024 * 1024 * 1024),
            'created': stat.st_ctime,
            'modified': stat.st_mtime,
            'accessed': stat.st_atime
        }

# 사용 예시
large_file_processor = LargeFileProcessor()

# 대용량 파일 처리 함수
def count_words_in_chunk(chunk):
    return len(chunk.split())

word_counts = large_file_processor.process_file_in_chunks('large_file.txt', process_func=count_words_in_chunk)
total_words = sum(word_counts)

# 배치 단위 라인 처리
for batch in large_file_processor.read_lines_batch('large_file.txt', batch_size=5000):
    # 배치 처리 로직
    processed_batch = [line.strip().upper() for line in batch]
```

파일 형식별 데이터 처리는 각 형식의 특성을 이해하고 적절한 도구를 선택하는 것이 핵심이다. 대용량 데이터의 경우 메모리 효율성과 처리 속도를 고려한 스트리밍 방식의 접근이 필요하다. 