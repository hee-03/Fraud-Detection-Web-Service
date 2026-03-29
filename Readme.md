# 💧 XGBoost AI + Flask Web Service

> Python Flask + XGBoost로 구현한 금융 거래 이상 감지 AI 웹 서비스

![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=flat-square&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-2.x-000000?style=flat-square&logo=flask&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-1.7+-189AB4?style=flat-square)
![Oracle](https://img.shields.io/badge/Oracle_DB-F80000?style=flat-square&logo=oracle&logoColor=white)
![WebSocket](https://img.shields.io/badge/WebSocket-실시간채팅-38BDF8?style=flat-square)
![Version](https://img.shields.io/badge/Version-v1.0-brightgreen?style=flat-square)

---

## 📋 프로젝트 개요

Flask Blueprint 기반 모듈화 백엔드와 XGBoost AI 모델을 활용하여 금융 거래의 이상 여부를 탐지하고, 관리자·은행 담당자 간 통합 관리 플랫폼을 제공하는 웹 서비스입니다.

### 핵심 특징 3가지

**1. 역할 기반 접근 제어 시스템**  
관리자·은행 권한을 완전 분리하여 URL 직접 접근 및 타행 데이터 열람까지 서버 단에서 차단하는 보안 아키텍처를 설계 및 구현했습니다.

**2. 실시간 양방향 통신**  
WebSocket 기반 채팅 모달을 구현하여 관리자↔은행, 동일 은행 내 사용자 간 페이지 이동 없는 실시간 커뮤니케이션을 제공합니다.

**3. 통합 관리 플랫폼 Full-Stack 구현**  
대시보드·이상거래 분석의뢰 게시판·채팅·FullCalendar 스케줄러를 하나의 서비스로 통합한 백엔드 API 및 DB를 설계·총괄했습니다.

### 프로젝트 정보

| 항목 | 내용 |
|------|------|
| 기간 | 2026.01.30 ~ 2026.03.25 (약 2개월) |
| 팀 구성 | 3인 팀 프로젝트 |
| 본인 역할 | 시스템 아키텍처·DB·백엔드 설계 총괄, WebSocket 채팅 구현, Git 버전 관리 체계 구축, 이상거래 분석의뢰 시스템·관리자 대시보드 백엔드 개발 |
| 프로젝트 유형 | Web Full-Stack + AI/ML |

---

## 🎯 AI/ML 상세 — XGBoost 이상거래 분석

### 모델 선정

| 모델 | 검토 결과 |
|------|----------|
| Logistic Regression | 비선형 패턴 처리 불가 — 탈락 |
| Decision Tree | 과적합 경향 강함 — 탈락 |
| Random Forest | SMOTE 별도 처리 필요, 속도 낮음 — 탈락 |
| **XGBoost ✅** | **최종 선택** — Boosting 기반 오류 보정, 불균형 데이터 강인성, Regularization 지원, 속도 우수 |

### 5가지 입력 Feature

| Feature | 설명 |
|---------|------|
| 거래금액 이상점수 | 평균 대비 이상 금액 Rule 채점 |
| 거래장소 | Haversine 거리 기반 위치 이상 탐지 |
| 거래시간 | Velocity Rule — 단시간 다건 거래 탐지 |
| 피싱 패턴 유사성 | 0~100점 채점 |
| 블랙리스트 여부 | 0/1 이진 플래그 |

### 평가 지표

| 지표 | 내용 |
|------|------|
| is_fraud | 0/1 이진 분류 |
| fraud_probability | 0~100% 실수 확률값 |
| SHAP Feature importance | 예측 근거 시각화 |

### 분석 파이프라인

```
거래 데이터 입력
  → Rule Engine (5가지 룰 채점)
  → XGBoost 모델 예측
  → SHAP 시각화
  → 결과 JSON 반환 (is_fraud + fraud_probability + SHAP Base64 이미지)
  → 프론트 그래프 렌더링
```

---

## 🛠 기술 스택

| 구분 | 기술 |
|------|------|
| Frontend | HTML5, CSS3, JavaScript (ES6+), Bootstrap, jQuery, Jinja2, Chart.js, FullCalendar |
| Backend | Python, Flask, Flask Blueprint, RESTful API, Session-Based Authentication, Flask-Mail, oracledb |
| Database | Oracle Database, SQL Developer, DBeaver |
| AI/ML | XGBoost, SHAP, pandas, scikit-learn, joblib, matplotlib |
| 실시간 통신 | WebSocket |
| 협업 도구 | Git, GitHub, PyCharm, IntelliJ, VS Code |

---

## 🏗 시스템 아키텍처

```
[클라이언트] Chrome
  → HTML/JS/Jinja2 (Chart.js, FullCalendar)
  → HTTP/JSON REST API
  → Flask Backend (Blueprint 모듈화)
      ├─ Login       — 인증·세션 관리
      ├─ Board       — 게시판 CRUD
      ├─ Dashboard   — 관리자·은행 대시보드
      ├─ Chat        — WebSocket 실시간 채팅
      ├─ Scheduler   — FullCalendar 일정·출퇴근
      └─ Transaction — 이상거래 분석의뢰
  → Session 기반 인증 및 권한 제어 (@login_required)
  → Oracle Database
      ├─ FFG_MEMBER   — 회원 정보
      ├─ FFG_TX_DATA  — 거래 데이터
      ├─ FFG_BOARD    — 게시판
      ├─ FFG_CHAT     — 채팅 메시지
      └─ FFG_SCHEDULE — 일정·출퇴근

[AI 분석 모듈]
  거래 데이터 입력
  → Rule Engine (5가지 룰 채점)
  → XGBoost 모델 예측
  → SHAP 시각화
  → 결과 JSON 반환

[실시간 통신]
  WebSocket /chat — 관리자↔은행, 동일 은행 내 사용자 간
```

---

## 📡 API 명세

| 메서드 | 엔드포인트 | 기능 | 권한 |
|--------|------------|------|------|
| POST | `/login` | 은행코드/아이디/비밀번호 인증 | 공개 |
| GET | `/main_admin` | 관리자 대시보드 | 관리자 전용 |
| GET | `/main_bank` | 은행 대시보드 (bank_code 기반 필터) | 은행 전용 |
| POST | `/admin_data_input` | 거래 데이터 입력 및 AI 분석 요청 | 인증 필요 |
| GET | `/error_detail` | 이상거래 고객 상세 데이터 | 인증 필요 |
| GET/POST | `/boardList/<board_type>` | 게시판 목록/등록/수정/삭제 | 인증 필요 |
| WS | `/chat` | WebSocket 실시간 채팅 | 인증 필요 |
| GET/POST | `/scheduler` | 일정 CRUD + 출퇴근 기록 | 인증 필요 |

**인증 방식:** Session-Based Authentication (Flask session)  
**응답 포맷:** JSON (AI 분석 결과, SHAP 이미지 Base64 포함)  
**에러 처리:** 권한 없음 → 로그인 페이지 리다이렉트 | 모델 미로드 → `{"error": "모델이 로드되지 않았습니다."}`

---

## 🐛 주요 트러블슈팅

<details>
<summary><strong>📁 파일 업/다운로드 경로 불일치</strong></summary>

**원인**: 파일 저장 경로를 절대 경로로 하드코딩하여 팀원마다 로컬 경로가 달라 파일 기능이 정상 작동하지 않음

**해결**: 프로젝트 루트 기준 상대 경로로 전면 변경
```python
UPLOAD_FOLDER = os.path.join(os.path.dirname(__file__), 'static/uploads')
```

**성과**: 모든 개발 환경에서 파일 업로드·다운로드 정상 동작
</details>

<details>
<summary><strong>🔑 은행코드 PK 중복 제약으로 동일 은행 다중 계정 생성 불가</strong></summary>

**원인**: bank_code 컬럼에 PK 제약 설정으로 같은 은행 소속의 여러 직원 계정을 만들 수 없음

**해결**: bank_code 컬럼의 PK 제약 해제, 복합 키 또는 별도 member_id PK로 DB 재설계

**성과**: 동일 은행 소속 복수 계정 정상 등록 가능
</details>

<details>
<summary><strong>🔓 로그아웃 후 관리자 대시보드 재진입 가능 (세션 미삭제)</strong></summary>

**원인**: 로그아웃 시 세션이 완전히 삭제되지 않아 URL 직접 접근으로 관리자 페이지에 재진입 가능

**해결**: 로그아웃 라우트에서 `session.clear()` 명시적 호출 및 모든 보호 라우트에 `@login_required` 데코레이터 적용

```python
# 로그아웃 라우트
session.clear()

# 보호 라우트 예시
@app.route('/main_admin')
@login_required
def main_admin():
    ...
```

**성과**: 로그아웃 후 보호 페이지 접근 시 로그인 페이지로 강제 리다이렉트
</details>

<details>
<summary><strong>🤖 AI 모델 파일 미존재로 신규 환경에서 서버 기동 실패</strong></summary>

**원인**: Git에 학습된 모델 파일(.pkl)이 포함되지 않아 새 환경에서 바로 실행하면 모델 로드 오류 발생

**해결**: README에 최초 실행 전 모델 학습·저장 스크립트 실행 필수 명시, 예외 처리 추가
```python
if model is None:
    return jsonify({"error": "모델이 로드되지 않았습니다."})
```

**성과**: 신규 환경 온보딩 절차 명확화 및 서비스 안정성 향상
</details>

---

## 🚀 실행 가이드

### 1. 의존성 설치

```bash
pip install flask oracledb flask-mail xgboost shap pandas scikit-learn joblib matplotlib
```

### 2. DB 설정

Oracle DB 연결 정보를 `config.py`에 입력 후 DDL 스크립트를 실행하여 테이블을 생성합니다.

```
FFG_MEMBER, FFG_TX_DATA, FFG_BOARD, FFG_CHAT, FFG_SCHEDULE
```

### 3. AI 모델 학습 (최초 1회 필수)

> ⚠️ Git에는 `.pkl` 파일이 포함되지 않습니다. 서버 실행 전 반드시 먼저 실행해야 합니다.

```bash
python train_model.py
# → model.pkl 생성 완료
```

### 4. 서버 실행

```bash
python app.py
# 또는
flask run --host=0.0.0.0 --port=8000
```

### 5. 접속 및 로그인

```
http://192.168.0.46:8000/login
# 관리자 계정: 은행코드 01
```

---

## 👥 팀 협업

| 팀원 | 역할 | 담당 기능 |
|------|------|----------|
| 손세진 | PM · Frontend | UI/UX 설계, 메인 대시보드·FAQ·스케줄러 구현 |
| 김담현 | Backend · AI | 공지사항·커뮤니티 게시판 CRUD, 핵심기술 문서화, 단위 테스트 |
| **박희정** | **Architecture · QA · Backend** | **시스템 아키텍처·DB 설계, 이상거래 분석의뢰 게시판, WebSocket 채팅** |

**브랜치 전략:** Git 기능별 브랜치 + GitHub 원격 저장소 통합  
**코드 리뷰:** 주간 크로스 테스트 (개발자↔테스터 역할 교차) → 피드백 반영 후 머지

---

## 🔭 향후 고도화 방향

- [ ] **Vue.js + Spring Boot 전환** — Jinja2 SSR → Vue.js SPA + Spring Boot REST API 분리 (2차 전환 작업 진행 중)
- [ ] **실시간 스트리밍 이상탐지** — Apache Kafka 또는 WebSocket 기반 스트리밍 처리로 탐지 지연 시간 단축
- [ ] **AI 모델 고도화** — 앙상블 또는 딥러닝(LSTM) 추가 실험, SMOTE 불균형 처리 전략 적용
- [ ] **실시간 알림 시스템** — 이상거래 탐지 즉시 이메일·SMS 알림 자동 발송 파이프라인 구축
- [ ] **멀티테넌시 강화** — 은행별 완전 격리된 데이터 환경 및 API 키 기반 외부 연동 지원
- [ ] **Docker 배포 패키징** — 컨테이너 기반 배포 자동화 및 .env 환경설정 가이드 제공

---

## 📄 라이선스

본 프로젝트는 학습 및 포트폴리오 목적으로 제작되었습니다.