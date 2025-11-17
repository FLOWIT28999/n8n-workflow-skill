# RSS to X Auto Posting - 설정 가이드

## 📦 파일 위치

- **워크플로우 JSON**: `rss_to_x_auto_posting.json`
- **이 가이드**: `RSS_TO_X_SETUP_GUIDE.md`

## 🚀 빠른 시작 (3단계)

### 1단계: X (Twitter) API 설정

1. **X Developer Portal 접속**
   - https://developer.x.com/portal 방문
   - X 계정으로 로그인

2. **앱 생성**
   - "Projects & Apps" → "+ Create Project" 클릭
   - 프로젝트 이름 입력
   - "Create App" 클릭
   - App 이름 입력 (예: "RSS Auto Poster")

3. **OAuth 2.0 설정**
   - App Settings → "User authentication settings" → "Set up"
   - App permissions: **Read and Write** 선택
   - Type of App: **Web App, Automated App or Bot** 선택
   - Callback URL: `https://your-n8n-instance.com/rest/oauth2-credential/callback`
     - 예: `http://localhost:5678/rest/oauth2-credential/callback` (로컬)
   - Website URL: 본인 웹사이트 URL (없으면 아무거나)
   - "Save" 클릭

4. **API Keys 복사**
   - **Client ID** 복사
   - **Client Secret** 복사
   - 안전한 곳에 저장 (메모장 등)

### 2단계: n8n에 워크플로우 Import

1. **n8n 접속**
   - n8n 인스턴스에 로그인

2. **Credential 먼저 생성**
   - 좌측 메뉴 "Credentials" 클릭
   - "+ Add Credential" 클릭
   - 검색: "Twitter" 또는 "X"
   - **"X (Formerly Twitter) OAuth2 API"** 선택
   - 입력:
     - Credential Name: `My X Account`
     - Client ID: (1단계에서 복사한 값)
     - Client Secret: (1단계에서 복사한 값)
   - **"Connect my account"** 클릭
   - X 로그인 창이 뜨면 로그인 및 권한 승인
   - **"Save"** 클릭

3. **워크플로우 Import**
   - 좌측 메뉴 "Workflows" 클릭
   - "+ Add Workflow" 클릭
   - 우측 상단 **⋮** (메뉴) → "Import from File" 선택
   - `rss_to_x_auto_posting.json` 파일 선택
   - 워크플로우가 캔버스에 나타남

4. **RSS URL 설정**
   - **"RSS Feed Monitor"** 노드 클릭
   - **Feed URL** 필드를 본인의 RSS URL로 변경
     - 현재: `https://example.com/feed.xml`
     - 변경: `https://your-blog.com/feed` 등
   - 예시:
     - WordPress 블로그: `https://yourblog.com/feed`
     - Medium: `https://medium.com/feed/@username`
     - Ghost: `https://yourblog.ghost.io/rss`

5. **X Credential 연결**
   - **"Post to X"** 노드 클릭
   - **Credential to connect with** 드롭다운
   - "My X Account" 선택
   - 노드 설정 창 닫기

6. **워크플로우 저장**
   - Ctrl+S 또는 우측 상단 "Save" 클릭

### 3단계: 테스트 및 활성화

1. **수동 테스트**
   - 우측 상단 **"Test workflow"** 또는 **"Execute Workflow"** 클릭
   - RSS 피드에서 최신 항목 가져옴
   - 각 노드를 클릭하여 데이터 흐름 확인
   - X(Twitter)에 접속하여 실제 트윗이 게시되었는지 확인

2. **활성화**
   - 테스트 성공 확인 후
   - 우측 상단 **"Inactive"** 토글 클릭
   - **"Active"**로 변경
   - 이제 6시간마다 자동으로 RSS 확인하고 새 항목 포스팅

---

## ⚙️ 커스터마이징

### 실행 주기 변경

RSS Feed Monitor 노드에서 Poll Times 수정 가능:

- **매 1시간**: 노드 설정에서 interval 변경
- **매 3시간**: interval 변경
- **매 12시간**: interval 변경
- **하루 1회**: Schedule Trigger 노드로 변경 권장

### 텍스트 포맷 수정

**"Format for X"** 노드의 JavaScript 코드 수정:

```javascript
// 해시태그 추가 예시
const title = $input.item.json.title || '';
const link = $input.item.json.link || '';
const hashtags = ' #YourHashtag #AutoPost'; // 원하는 해시태그

let tweet = title;

if (link) {
  const maxTitleLength = 280 - 23 - hashtags.length - 3;
  if (tweet.length > maxTitleLength) {
    tweet = tweet.substring(0, maxTitleLength) + '...';
  }
  tweet += ' ' + link + hashtags;
} else {
  const maxLength = 280 - hashtags.length;
  if (tweet.length > maxLength) {
    tweet = tweet.substring(0, maxLength - 3) + '...';
  }
  tweet += hashtags;
}

return { text: tweet };
```

---

## 🔍 문제 해결

### Q: "워크플로우가 실행되지 않아요"

**A:** 다음을 확인하세요:
- [ ] 워크플로우가 "Active" 상태인지
- [ ] RSS URL이 올바른지 (브라우저에서 접속 테스트)
- [ ] n8n이 실행 중인지
- [ ] Executions 탭에서 에러 로그 확인

### Q: "X 인증이 계속 실패해요"

**A:**
1. X Developer Portal에서 API Keys 재생성
2. n8n에서 Credential 삭제 후 재생성
3. Callback URL이 정확한지 확인:
   - 로컬: `http://localhost:5678/rest/oauth2-credential/callback`
   - 서버: `https://your-domain.com/rest/oauth2-credential/callback`
4. X App permissions가 "Read and Write"인지 확인

### Q: "트윗이 280자를 넘어요"

**A:**
- Code 노드의 로직이 자동으로 280자로 제한합니다
- 수동 테스트로 "Format for X" 노드 출력 확인
- 링크가 있으면 23자가 예약됩니다 (X의 t.co shortener)

### Q: "중복 트윗이 올라가요"

**A:**
- RSS Feed Trigger는 기본적으로 중복 방지 기능이 있습니다
- n8n 데이터베이스를 확인하세요 (SQLite/PostgreSQL)
- 워크플로우를 삭제 후 재생성하면 이력이 초기화됩니다

### Q: "RSS 피드가 안 읽혀요"

**A:**
1. RSS URL을 브라우저에서 직접 열어보세요
2. XML 형식이 올바른지 확인
3. CORS 문제일 수 있음 (n8n 서버에서 접근 가능해야 함)
4. RSS Feed Trigger 노드 설정에서 "Ignore SSL Issues" 옵션 체크

---

## 📊 워크플로우 구조

```
┌─────────────────────────┐
│  RSS Feed Monitor       │
│  (RSS Trigger)          │  ← 6시간마다 RSS 피드 체크
│                         │    중복 자동 방지
└────────────┬────────────┘
             │
             │ (새 항목 발견 시만 실행)
             ▼
┌─────────────────────────┐
│  Format for X           │
│  (Code Node)            │  ← 제목 + 링크를 280자로 포맷
│                         │    긴 제목은 자동 truncate
└────────────┬────────────┘
             │
             │ (포맷된 텍스트)
             ▼
┌─────────────────────────┐
│  Post to X              │
│  (Twitter Node)         │  ← X에 트윗 게시
│                         │    실패 시 3회 재시도
└─────────────────────────┘
```

---

## 💡 개선 아이디어

1. **이미지 추가**
   - RSS 피드의 featured image를 X에 첨부
   - HTTP Request로 이미지 다운로드 → Twitter node에 첨부

2. **특정 키워드만 필터링**
   - Code 노드 뒤에 IF 노드 추가
   - 특정 키워드 포함된 항목만 포스팅

3. **에러 알림**
   - Post to X 노드의 error 출력에 Email/Slack 노드 연결
   - 실패 시 알림 받기

4. **AI 요약**
   - OpenAI 노드 추가
   - 긴 글을 AI로 요약하여 트윗

5. **여러 RSS 피드**
   - 워크플로우 복제
   - 각각 다른 RSS URL 설정

---

## 📞 지원

- **n8n 공식 문서**: https://docs.n8n.io
- **n8n 커뮤니티**: https://community.n8n.io
- **X API 문서**: https://developer.x.com/en/docs

---

**생성일**: 2025-01-15
**워크플로우 버전**: 1.0
**n8n 최소 버전**: 1.0+

---

## ✅ 체크리스트

설정 전:
- [ ] X Developer 계정 생성
- [ ] n8n 인스턴스 준비
- [ ] RSS 피드 URL 확인

설정 중:
- [ ] X OAuth 2.0 설정 완료
- [ ] n8n Credential 생성
- [ ] 워크플로우 Import
- [ ] RSS URL 입력
- [ ] Credential 연결

테스트:
- [ ] 수동 실행 테스트
- [ ] X에서 트윗 확인
- [ ] 데이터 흐름 확인

배포:
- [ ] 워크플로우 활성화
- [ ] 첫 자동 실행 대기 (6시간 후)
- [ ] 실행 로그 모니터링

완료! 🎉
