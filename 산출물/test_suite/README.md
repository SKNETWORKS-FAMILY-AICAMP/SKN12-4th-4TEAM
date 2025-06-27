# RAG 기반 챗봇 시스템 테스트 스위트

이 테스트 스위트는 Ballzzi AI 챗봇 시스템의 모든 주요 컴포넌트를 종합적으로 테스트합니다.

## 📁 테스트 파일 구조

```
test_suite/
├── run_all_tests.py           # 통합 테스트 러너
├── test_question_routing.py   # 질문 라우팅 시스템 테스트
├── test_fm_module.py         # FM 모듈 (축구 선수 정보) 테스트  
├── test_hr_rag.py           # HR 모듈 RAG 시스템 테스트
├── test_django_api.py       # Django API 엔드포인트 테스트
└── README.md               # 이 파일
```

## 🚀 실행 방법

### 전체 테스트 실행 (권장)
```bash
cd test_suite
python run_all_tests.py
```

### 개별 테스트 실행
```bash
# 질문 라우팅 테스트만 실행
python test_question_routing.py

# FM 모듈 테스트만 실행  
python test_fm_module.py

# HR RAG 테스트만 실행
python test_hr_rag.py

# Django API 테스트만 실행
python test_django_api.py
```

## 📋 테스트 항목별 설명

### 1. 질문 라우팅 테스트 (`test_question_routing.py`)
- **목적**: Sentence-Transformer 기반 질문 분류 정확도 검증
- **테스트 케이스**: 25개 (FM: 10개, HR: 10개, 경계사례: 5개)
- **검증 항목**:
  - FAISS 인덱스 검색 성능
  - FM/HR 모듈 분기 로직 정확도
  - 응답 시간 측정

### 2. FM 모듈 테스트 (`test_fm_module.py`)
- **목적**: 축구 선수 정보 처리 시스템 검증
- **테스트 케이스**: 17개 (선수정보, 예산검색, 포지션검색, 비교분석 등)
- **검증 항목**:
  - LangChain SQL 쿼리 생성 정확도
  - SQLite DB 연동 안정성
  - JSON 응답 파싱 성공률
  - 데이터베이스 연결 상태

### 3. HR RAG 테스트 (`test_hr_rag.py`)
- **목적**: RAG 시스템 (벡터검색 + 리랭커) 성능 검증
- **테스트 케이스**: 18개 (HR정책, 회사정보, 외부지식 등)
- **검증 항목**:
  - FAISS 벡터 검색 정확도
  - BGE 리랭커 재정렬 품질
  - 하이브리드 검색 (내부/외부) 성능
  - GPT-4o-mini 응답 품질 평가

### 4. Django API 테스트 (`test_django_api.py`)
- **목적**: Django 백엔드 API 엔드포인트 검증
- **테스트 케이스**: 10개 (GET/POST 요청, CSRF 보호, 에러처리)
- **검증 항목**:
  - 챗봇 페이지 렌더링
  - POST API 응답 형식
  - CSRF 토큰 보안
  - 인증 시스템 작동

## 📊 결과 해석

### 성공률 기준
- **95% 이상**: 🎉 우수함 - 프로덕션 배포 준비 완료
- **90% 이상**: 👍 양호함 - 일부 개선 후 배포 가능  
- **80% 이상**: ⚠️ 주의 - 추가 튜닝 필요
- **80% 미만**: 🚨 심각 - 시스템 점검 필요

### 응답 시간 기준
- **2초 미만**: ⚡ 우수
- **5초 미만**: 👍 양호
- **5초 이상**: ⚠️ 개선 필요

## 📁 출력 파일

테스트 실행 후 다음 파일들이 생성됩니다:

- `question_routing_test_results.json` - 질문 라우팅 상세 결과
- `fm_module_test_results.json` - FM 모듈 상세 결과
- `hr_rag_test_results.json` - HR RAG 상세 결과
- `django_api_test_results.json` - Django API 상세 결과
- `integrated_test_results_YYYYMMDD_HHMMSS.json` - 통합 결과
- `test_summary_YYYYMMDD_HHMMSS.txt` - 요약 보고서

## ⚙️ 환경 요구사항

### 필수 조건
```bash
# Django 서버가 실행되어야 함 (테스트 전 확인)
python manage.py runserver

# 필수 환경변수 설정
export OPENAI_API_KEY="your-api-key"
export HUGGINGFACE_TOKEN="your-hf-token"  # 선택사항
```

### 데이터베이스 확인
```bash
# SQLite DB 파일 존재 확인
ls myapp/source/FM/data/players_position.db

# FAISS 벡터 DB 확인
ls myapp/source/HR/data/faiss_win/
ls myapp/source/HR/data/faiss_org_hr/
```

## 🔧 문제 해결

### 자주 발생하는 오류

1. **ModuleNotFoundError**: Django 경로 설정 문제
   ```bash
   # 프로젝트 루트에서 실행
   cd SKN12-4th-4TEAM
   python test_suite/run_all_tests.py
   ```

2. **FAISS 로드 실패**: 벡터 DB 파일 누락
   ```bash
   # 벡터 DB 파일 확인
   find . -name "*.faiss" -o -name "*.pkl"
   ```

3. **API 연결 오류**: Django 서버 미실행
   ```bash
   # 별도 터미널에서 서버 실행
   python manage.py runserver
   ```

4. **인증 오류**: 테스트 사용자 생성 실패
   ```bash
   # Django 쉘에서 수동 생성
   python manage.py shell
   >>> from django.contrib.auth.models import User
   >>> User.objects.create_user('testuser', 'test@example.com', 'testpass123')
   ```

## 📈 성능 최적화 팁

1. **벡터 검색 속도 향상**
   - FAISS 인덱스 압축
   - GPU 가속 활용

2. **응답 시간 단축**
   - LLM 모델 캐싱
   - 데이터베이스 인덱싱

3. **메모리 사용량 최적화**
   - 배치 크기 조정
   - 모델 양자화 적용

## 🛠️ 테스트 확장

새로운 테스트 추가 시:

1. `test_새로운기능.py` 파일 생성
2. `run_all_tests.py`의 `test_modules` 리스트에 추가
3. 동일한 구조로 테스트 클래스 구현

```python
# 예시 구조
class NewFeatureTester:
    def __init__(self):
        self.test_cases = []
    
    def run_single_test(self, test_case):
        # 테스트 로직
        pass
    
    def run_all_tests(self):
        # 전체 실행 로직
        pass

def main():
    tester = NewFeatureTester()
    return tester.run_all_tests()
```

## 📞 지원

테스트 관련 문의사항이 있으면 개발팀으로 연락하세요.

---

**작성일**: 2024년 12월 19일  
**버전**: 1.0  
**작성자**: QA 팀 