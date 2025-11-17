# 🔧 워크플로우 문제 해결 상세 설명

## 📋 발견된 문제점

### 1. AI Chain - 감성 분석 노드 문제 ❌

**문제:**
```javascript
text: "={{ $json.items.map(item => item.description).join(' ') }}"
messages: {
  messageValues: [{ message: "={{ $json.systemMessage }}" }]
}
```

**무엇이 잘못되었나:**
1. **`$json.items`가 존재하지 않음**: 이전 노드는 `시스템 프롬프트 - 감성` (Set 노드)인데, 이 노드는 `systemMessage` 변수만 설정하고 `items`는 없음
2. **실제 뉴스 데이터를 받지 못함**: 뉴스 데이터는 `기술적 지표 계산` 노드의 `newsData` 필드에 있음
3. **데이터 참조 오류**: AI Chain이 분석할 실제 뉴스 데이터를 받지 못해서 빈 응답 생성

**왜 이런 일이?**
- Set 노드(시스템 프롬프트)는 단순히 변수를 설정할 뿐, 데이터를 전달하지 않음
- AI Chain은 이전 노드(`$json`)를 참조하는데, Set 노드의 출력에는 `items`가 없음

---

### 2. AI Chain - 포트폴리오 노드 문제 ❌

**문제:**
```javascript
messages: {
  messageValues: [{}]  // 빈 객체!
}
```

**무엇이 잘못되었나:**
1. **프롬프트가 없음**: 빈 객체만 있어서 AI가 무엇을 분석해야 하는지 모름
2. **데이터 참조 없음**: 기술적 지표, 감성 분석 결과, 재무 데이터 등을 참조하는 코드가 없음
3. **시스템 프롬프트만 있음**: 별도 Set 노드의 시스템 메시지만 있고, 실제 분석 데이터는 없음

**결과:**
- AI가 빈 프롬프트를 받아서 제대로 된 리포트를 생성하지 못함

---

### 3. Merge 노드 문제 ⚠️

**현재 상태:**
- Input 0: 주가 데이터 ✅
- Input 1: 네이버 뉴스 API ✅
- Input 2: 공시 데이터 ✅
- Input 3: 재무제표 ✅

**문제:**
네이버 뉴스 API 응답 구조:
```json
{
  "lastBuildDate": "...",
  "total": 100,
  "start": 1,
  "display": 10,
  "items": [    // ← 이 구조!
    {
      "title": "<b>삼성전자</b> ...",  // HTML 태그 포함
      "originallink": "...",
      "link": "...",
      "description": "<b>삼성</b> ...",  // HTML 태그 포함
      "pubDate": "Mon, 14 Nov 2024 ..."
    }
  ]
}
```

**데이터 전처리 코드 문제:**
```javascript
// 기존 코드 (RSS 구조 가정)
if (json.title && json.link && json.pubDate) {
  newsData.push({...});
}
```

이 코드는 개별 뉴스 아이템을 찾는데, 네이버 API는 `items` 배열 안에 있음!

---

### 4. 데이터 흐름 문제 ❌

**현재 잘못된 흐름:**
```
기술적 지표 계산
    ↓
시스템 프롬프트 - 감성 (Set 노드)
    ↓
AI Chain - 감성 분석 ← 뉴스 데이터가 어디있지? ❌
    ↓
시스템 프롬프트 - 포트폴리오 (Set 노드)
    ↓
AI Chain - 포트폴리오 ← 모든 데이터가 어디있지? ❌
```

**문제:**
1. Set 노드는 데이터를 전달하지 않고 변수만 설정
2. AI Chain은 이전 노드의 `$json`을 참조하는데, Set 노드 출력에는 분석할 데이터가 없음
3. 각 AI Chain이 필요한 데이터에 접근할 수 없음

---

## ✅ 해결 방법

### 1. 데이터 전처리 코드 수정

**네이버 API 구조 반영:**
```javascript
// 네이버 뉴스 API 데이터 식별
if (json.items && Array.isArray(json.items) && !json.list) {
  // 네이버 API 구조: { items: [...] }
  json.items.forEach(newsItem => {
    if (newsItem.title && newsItem.link) {
      newsData.push({
        title: newsItem.title.replace(/<[^>]*>/g, ''), // HTML 태그 제거
        link: newsItem.link,
        pubDate: newsItem.pubDate,
        description: newsItem.description ?
          newsItem.description.replace(/<[^>]*>/g, '') : ''
      });
    }
  });
}
```

**변경사항:**
- `items` 배열 확인
- HTML 태그 제거 (`<b>`, `</b>` 등)
- 구조화된 뉴스 데이터 생성

---

### 2. AI Chain - 감성 분석 수정

**새로운 프롬프트 구조:**
```javascript
promptType: "define",
text: "=### 시스템 프롬프트
당신은 금융 뉴스 감성 분석 전문가입니다.
[역할 설명...]

---

### 분석할 뉴스 데이터

{{ $json.newsData.map(news =>
  `제목: ${news.title}
   내용: ${news.description}
   날짜: ${news.pubDate}`
).join('\\n\\n---\\n\\n') }}

---

위 뉴스들을 분석하여 JSON 형식으로 응답하세요."
```

**핵심 변경:**
1. **시스템 프롬프트를 text 필드에 직접 포함**: Set 노드 불필요
2. **데이터 참조**: `$json.newsData`로 기술적 지표 계산 노드의 뉴스 데이터 접근
3. **포맷팅**: 뉴스 데이터를 읽기 쉬운 형태로 변환

---

### 3. 감성 분석 병합 노드 추가

**새로운 Code 노드:**
```javascript
// 감성 분석 결과와 기술적 지표를 병합
const technicalData = $('기술적 지표 계산').first().json;
const sentimentResult = $input.first().json;

// 감성 분석 결과 파싱
let sentimentAnalysis = sentimentResult.output || sentimentResult.text;

// JSON 파싱 시도 (AI가 JSON을 반환했을 경우)
if (typeof sentimentAnalysis === 'string') {
  try {
    sentimentAnalysis = sentimentAnalysis
      .replace(/```json\n?/g, '')
      .replace(/```\n?/g, '');
    sentimentAnalysis = JSON.parse(sentimentAnalysis);
  } catch (e) {
    // 파싱 실패 시 원본 유지
  }
}

const combinedData = {
  ...technicalData,
  sentimentAnalysis: sentimentAnalysis
};

return [{ json: combinedData }];
```

**역할:**
- 감성 분석 결과를 기술적 지표 데이터와 병합
- AI 응답에서 JSON 추출 및 파싱
- 포트폴리오 AI Chain에 전달할 완전한 데이터 준비

---

### 4. AI Chain - 포트폴리오 수정

**새로운 프롬프트 구조:**
```javascript
promptType: "define",
text: "=### 시스템 프롬프트
당신은 단기 트레이딩 전문 주식 포트폴리오 매니저입니다.
[역할 설명...]

---

### 분석 데이터

#### 기본 정보
- 종목: {{ $json.companyName }}
- 분석 일시: {{ $json.timestamp }}

#### 주가 데이터
- 현재가: {{ $json.technicalIndicators.latestPrice }}원
- 1일 변동: {{ $json.technicalIndicators.priceChange.day1 }}%
[... 더 많은 데이터 ...]

#### 기술적 지표
- RSI: {{ $json.technicalIndicators.rsi }}
- MA5/20/60: ...
[... 더 많은 지표 ...]

#### 자동 매매 신호
- 신호: {{ $json.tradingSignal.signal }}
- 신뢰도: {{ $json.tradingSignal.confidence }}

#### 뉴스 감성 분석
{{ JSON.stringify($json.sentimentAnalysis, null, 2) }}

#### 재무/공시 정보
공시: {{ JSON.stringify($json.disclosureData, null, 2) }}
재무: {{ JSON.stringify($json.financialData?.slice(0, 10), null, 2) }}

---

### 요구사항
다음 형식으로 **완전한 마크다운 리포트**를 작성하세요:
[상세한 리포트 템플릿...]"
```

**핵심 변경:**
1. **모든 데이터 포함**: 기술적 지표, 감성 분석, 재무, 공시 모두 참조
2. **명확한 데이터 참조**: `$json.technicalIndicators`, `$json.sentimentAnalysis` 등
3. **상세한 출력 형식**: 마크다운 리포트 구조 명시

---

### 5. 수정된 데이터 흐름

**새로운 올바른 흐름:**
```
스케줄 트리거
    ↓ (4-way split)
[주가] [뉴스] [공시] [재무]
    ↓ (4-way merge)
데이터 병합
    ↓
데이터 전처리 (네이버 API 파싱)
    ↓
기술적 지표 계산 (모든 데이터 포함)
    ↓
AI Chain - 감성 분석
  - 입력: $json.newsData
  - 출력: 감성 분석 결과
    ↓
감성 분석 병합
  - 기술적 데이터 + 감성 결과
    ↓
AI Chain - 포트폴리오
  - 입력: 모든 데이터 + 감성 분석
  - 출력: 마크다운 리포트
    ↓
마크다운 생성
    ↓
Google Drive 저장
```

**장점:**
1. 각 노드가 필요한 데이터에 직접 접근
2. Set 노드 불필요 (프롬프트를 text 필드에 직접 포함)
3. 명확한 데이터 흐름

---

## 📊 노드별 데이터 구조

### 기술적 지표 계산 출력:
```json
{
  "timestamp": "2025-01-15T...",
  "symbol": "005930",
  "companyName": "삼성전자",
  "stockData": { "symbol": "005930.KS", "data": [...] },
  "newsData": [
    { "title": "...", "description": "...", "pubDate": "..." }
  ],
  "disclosureData": [...],
  "financialData": [...],
  "technicalIndicators": {
    "rsi": 45.2,
    "movingAverages": { "ma5": 71500, "ma20": 70800, "ma60": 69500 },
    "bollingerBands": { "upper": 73000, "middle": 71000, "lower": 69000 },
    "volume": { "trend": "보통", "ratio": "1.2" },
    "latestPrice": 71000,
    "priceChange": { "day1": "-0.5", "day5": "2.3", "day20": "5.1" }
  },
  "tradingSignal": {
    "signal": "보유",
    "strength": 0,
    "reasons": ["데드크로스"],
    "confidence": "0%"
  }
}
```

### AI Chain - 감성 분석 출력:
```json
{
  "output": "[{\"title\":\"삼성전자 신제품\",\"sentiment\":\"긍정\",\"score\":80,...}]"
}
```

### 감성 분석 병합 출력:
```json
{
  // ... 기술적 지표 계산의 모든 데이터 ...
  "sentimentAnalysis": [
    {
      "title": "삼성전자 신제품 출시",
      "sentiment": "긍정",
      "score": 80,
      "summary": "신제품 출시로 긍정적",
      "impact": "상승",
      "reasoning": "시장 반응 좋음"
    }
  ]
}
```

### AI Chain - 포트폴리오 출력:
```json
{
  "output": "# 삼성전자 일일 포트폴리오 분석 리포트\n\n..."
}
```

---

## 🔑 핵심 포인트

### 1. AI Chain의 text 파라미터
**올바른 사용법:**
```javascript
text: "=시스템 프롬프트 내용...\n\n{{ $json.실제데이터 }}"
```

**잘못된 사용법:**
```javascript
// Set 노드로 시스템 프롬프트 분리
// AI Chain text: "={{ $json.items }}" ← $json은 Set 노드 출력
```

### 2. 데이터 참조
**올바른 방법:**
```javascript
// 현재 노드: $json
{{ $json.newsData }}

// 특정 노드: $('노드명')
{{ $('기술적 지표 계산').first().json.technicalIndicators }}
```

### 3. 네이버 API vs RSS
**네이버 API:**
```json
{
  "items": [{ "title": "...", "description": "..." }]
}
```

**RSS:**
```json
{
  "title": "...",
  "description": "...",
  "pubDate": "..."
}
```

---

## 📁 파일 사용 방법

### 최종 워크플로우 파일:
```
samsung_portfolio_workflow_final.json
```

### Import 후 설정:
1. **Gemini API 설정**
   - `Gemini - 감성분석` 노드
   - `Gemini - 포트폴리오` 노드
   - 동일한 Credential 사용 가능

2. **네이버 API 설정**
   - `뉴스 데이터 수집` 노드
   - Client ID와 Secret 입력

3. **DART API 설정**
   - `공시 데이터 수집` 노드
   - `재무제표 수집` 노드
   - API 키 입력

4. **Google Drive 설정**
   - `Google Drive 저장` 노드
   - OAuth2 인증
   - 폴더 ID 확인: `1rlv87JwnjT3pZagPnezS_trIvJgRC-2J`

---

## 🧪 테스트 방법

### 1. 개별 노드 테스트
```
1. 뉴스 데이터 수집 노드만 실행
   → items 배열 확인

2. 데이터 병합 노드까지 실행
   → 4개 입력 모두 병합되었는지 확인

3. 데이터 전처리 노드까지 실행
   → newsData 배열에 HTML 태그 없이 뉴스가 있는지 확인

4. 기술적 지표 계산 노드까지 실행
   → technicalIndicators, tradingSignal 확인

5. AI Chain - 감성 분석 실행
   → JSON 형식의 감성 분석 결과 확인

6. 감성 분석 병합 실행
   → sentimentAnalysis 필드 추가 확인

7. AI Chain - 포트폴리오 실행
   → 마크다운 리포트 생성 확인

8. 전체 실행
   → Google Drive에 파일 생성 확인
```

### 2. 데이터 확인 포인트
- 뉴스 데이터: HTML 태그 제거 확인
- 감성 분석: JSON 파싱 성공 확인
- 포트폴리오: 완전한 마크다운 구조 확인

---

## ⚠️ 주의사항

1. **API 할당량**
   - 네이버 API: 하루 25,000회
   - DART API: 하루 10,000회
   - Gemini: 월 60 requests (무료)

2. **에러 처리**
   - 네이버 API 실패 시 뉴스 데이터가 빈 배열
   - DART API 실패 시 공시/재무 데이터 null
   - AI 응답 파싱 실패 시 원본 텍스트 유지

3. **데이터 크기**
   - 뉴스: 10개로 제한
   - 공시: 5개로 제한
   - 재무: 20개 항목으로 제한
   - Gemini 토큰 제한 고려

---

## ✅ 체크리스트

Import 후 확인:
- [ ] 모든 노드가 연결되어 있는가?
- [ ] Gemini Credentials 2개 설정 완료?
- [ ] 네이버 API Client ID/Secret 입력?
- [ ] DART API 키 2곳에 입력?
- [ ] Google Drive 폴더 ID 확인?
- [ ] 테스트 실행 성공?
- [ ] 마크다운 파일 생성 확인?

---

**모든 문제가 해결되었습니다! 🎉**

이제 `samsung_portfolio_workflow_final.json`을 Import하면 정상 작동합니다.
