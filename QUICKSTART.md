# ⚡ 빠른 시작 가이드 (5분 안에 시작하기)

## 📋 체크리스트

시작하기 전에 다음 항목을 준비하세요:

- [ ] n8n 설치 완료 (Self-hosted 또는 Cloud)
- [ ] Google Gemini API 키
- [ ] DART API 키
- [ ] Google Drive OAuth2 설정

---

## 🚀 3단계로 시작하기

### 1단계: API 키 발급 (5분)

#### ✅ Google Gemini API
```
1. https://makersuite.google.com/app/apikey 접속
2. "Create API Key" 클릭
3. 키 복사 → 어딘가에 안전하게 저장
```

#### ✅ DART API
```
1. https://opendart.fss.or.kr/ 접속
2. 회원가입 (간단 - 2분)
3. 로그인 → "오픈API 이용현황" 메뉴
4. "인증키 신청" → 즉시 발급
5. API 키 복사
```

#### ✅ Google Drive OAuth2
```
1. https://console.cloud.google.com/ 접속
2. 프로젝트 생성 ("Stock Analysis" 등)
3. "API 및 서비스" → "라이브러리"
4. "Google Drive API" 검색 및 활성화
5. "사용자 인증 정보" 만들기
   - OAuth 2.0 클라이언트 ID
   - 애플리케이션 유형: 데스크톱 앱
6. Client ID와 Secret 저장
```

---

### 2단계: 워크플로우 Import (1분)

#### n8n에서:

1. **Workflows** 메뉴 열기
2. 우측 상단 **"⋮"** 클릭
3. **"Import from File"** 선택
4. `samsung_portfolio_workflow.json` 업로드
5. **"Import"** 클릭

✅ 완료! 워크플로우가 캔버스에 나타남

---

### 3단계: Credentials 설정 (3분)

#### 🔑 1. Google Gemini 설정

```
노드: "AI 감성 분석" 클릭
→ "Credentials" 탭
→ "+ Create New"
→ API Key 입력
→ "Save"
```

동일한 credential을 "AI 포트폴리오 분석" 노드에도 연결!

#### 🔑 2. DART API 설정

**간단 버전 (추천):**
```
노드: "공시 데이터 수집" 클릭
→ Query Parameters 섹션
→ crtfc_key 값에 API 키 직접 입력
```

"재무제표 수집" 노드도 동일하게!

#### 🔑 3. Google Drive 설정

```
노드: "Google Drive 저장" 클릭
→ "Credentials" 탭
→ "+ Create New"
→ Client ID 입력
→ Client Secret 입력
→ "Connect my account" 클릭
→ Google 로그인 및 권한 승인
```

---

## 🧪 테스트 실행

### 첫 실행 테스트:

1. 워크플로우 캔버스 우측 상단
2. **"Execute Workflow"** 버튼 클릭 (큰 재생 버튼)
3. 실행 진행 상황 확인 (각 노드에 초록색 체크)
4. 완료까지 약 30초~1분 소요

### 결과 확인:

1. **Google Drive** 열기
2. 파일 검색: `samsung_portfolio_report.md`
3. 파일 열기 (마크다운 뷰어 권장)

✅ **성공!** AI 분석 리포트가 생성됨

---

## 🔄 자동화 활성화

### 매일 자동 실행 설정:

1. 워크플로우 우측 상단 토글 스위치
2. **"Inactive"** → **"Active"** 변경
3. 완료!

이제 매일 오전 7시에 자동으로:
- ✅ 주가 데이터 수집
- ✅ 뉴스 분석
- ✅ AI 리포트 생성
- ✅ Google Drive 저장

---

## ❌ 문제 발생 시

### 에러: "Missing credentials"
→ 3단계 Credentials 설정 다시 확인

### 에러: "Request failed"
→ API 키가 올바른지 확인
→ DART API: 키 앞뒤 공백 제거

### 에러: "Node execution failed"
→ README.md의 "문제 해결" 섹션 참고

### 주가 데이터가 이상함
→ 예상: Python yfinance 미설치
→ 해결: README의 "Python 설정" 참고

---

## 📱 다음 단계

### 기본 사용:
✅ 매일 아침 Google Drive에서 리포트 확인
✅ AI 분석 결과로 투자 판단 참고

### 커스터마이징:
- [ ] 실행 시간 변경 (오전 6시? 8시?)
- [ ] 다른 종목 추가 (SK하이닉스, NAVER 등)
- [ ] 알림 추가 (이메일, 슬랙)

자세한 내용은 **README.md** 참고!

---

## 💡 팁

### 비용 절감:
- Gemini API: 월 60회 무료 (하루 2회면 충분)
- DART API: 완전 무료
- Google Drive: 15GB 무료

### 정확도 향상:
- 90일 데이터 → 180일로 확대
- 뉴스 소스 추가 (네이버 API)
- 기술 지표 추가 (MACD, Stochastic 등)

### 시간 절약:
- 매일 아침 Drive 알림 설정
- 중요 신호만 이메일 전송
- 여러 종목 동시 분석

---

## 🎉 완료!

**축하합니다!** 이제 AI 주식 포트폴리오 매니저가 작동 중입니다.

내일 아침 7시에 첫 리포트를 받아보세요!

**문의사항:**
- 상세 설명: README.md
- 고급 설정: README.md의 "고급 커스터마이징"
- 문제 해결: README.md의 "문제 해결"

**Happy Investing! 📈🚀**
