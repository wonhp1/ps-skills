# 인스타그램 분석기 프롬프트

## 프로젝트 개요

Apify Instagram Scraper API를 활용하여 키워드 기반으로 인스타그램 게시물을 크롤링하고,
수집된 데이터를 Next.js 서버에서 카드 형식으로 시각화하는 웹 애플리케이션.

---

## 1. Apify Instagram Scraper API 수집 가능 정보

**API**: https://apify.com/apify/instagram-scraper

### 게시물 기본 정보

- 게시물 ID, URL, 타입 (이미지 / 동영상 / 캐러셀)
- 캡션 (본문 텍스트 전체)
- 좋아요 수, 댓글 수
- 게시 날짜/시간 (timestamp)
- 해시태그 목록
- 멘션 목록
- 위치 정보 (장소명, 위도/경도)

### 미디어

- 단일 이미지 URL (고해상도)
- 캐러셀(다중 이미지) — 포함된 **모든 이미지 URL** 개별 수집
- 동영상 URL + 썸네일 URL
- 동영상 재생 시간

### 작성자 정보

- 사용자명 (username), 전체 이름 (full name)
- 팔로워 수, 팔로잉 수
- 게시물 수
- 프로필 사진 URL
- 계정 소개 (bio)
- 공개/비공개 여부, 인증 여부 (파란 뱃지)

### 댓글

- 댓글 목록 (작성자, 내용, 날짜, 좋아요 수)
- 대댓글 포함 가능

---

## 2. 구현 기능 명세

### 키워드 입력 방식 (모든 방식 구현)

- 텍스트 입력창 직접 입력
- 엔터 / 버튼 클릭으로 추가
- 복수 키워드 동시 입력 (쉼표, 스페이스, 엔터 구분)
- 태그 형식으로 키워드 추가/삭제 (chip/badge UI)
- 크롤링 수량 직접 설정 (숫자 입력 또는 슬라이더)

### 크롤링 결과 표시 (카드 형식)

각 게시물 카드에 표시할 정보:

- 작성자 프로필 사진 + 사용자명
- 게시 날짜/시간
- 캡션 (전체 또는 접기/펼치기)
- **모든 이미지 슬라이드** (캐러셀 포함, 썸네일만 아닌 전체)
- **동영상 인라인 재생** + 썸네일
- 좋아요 수, 댓글 수
- 해시태그 (클릭 시 해당 태그 재검색 가능)
- 위치 정보
- 원본 게시물 링크

### 데이터 저장 (로컬)

- 크롤링 결과 JSON 파일로 프로젝트 내 저장
- 이미지 전부 다운로드 → `/public/downloads/{키워드}/{게시물ID}/` 폴더에 저장
- 동영상 다운로드 → 동일 경로에 저장
- 저장 진행률 표시 (다운로드 중 프로그레스바)

---

## 3. 기술 스택

- **프레임워크**: Next.js (App Router)
- **API 통신**: Apify Client (`apify-client` npm 패키지)
- **파일 저장**: Node.js `fs`, `stream` 모듈 (서버 액션 활용)
- **UI**: Tailwind CSS + 카드 컴포넌트
- **이미지/동영상**: Next.js `<Image>`, HTML5 `<video>`

---

## 4. Apify API 토큰 발급 방법

1. https://apify.com 에서 회원가입 / 로그인
2. 우측 상단 프로필 → **Settings** → **Integrations** 클릭
3. **API tokens** 섹션에서 **+ Create new token** 클릭
4. 토큰 이름 입력 후 생성 → 복사
5. 프로젝트 `.env.local` 에 저장:

```
APIFY_API_TOKEN=your_token_here
```

**토큰 발급 직접 링크**: https://console.apify.com/account/integrations

> ⚠️ 무료 플랜은 월 $5 크레딧 제공. Instagram Scraper는 1,000건당 약 $0.50 소모.

---

## 5. 프로젝트 구조 (예상)

```
instagram-analyzer/
├── app/
│   ├── page.tsx              # 메인 검색 페이지
│   ├── api/
│   │   ├── crawl/route.ts    # Apify 크롤링 API 라우트
│   │   └── download/route.ts # 이미지/동영상 다운로드 라우트
│   └── results/page.tsx      # 결과 카드 페이지
├── components/
│   ├── KeywordInput.tsx       # 키워드 입력 컴포넌트
│   ├── PostCard.tsx           # 게시물 카드 컴포넌트
│   └── MediaSlider.tsx        # 다중 이미지 슬라이더
├── public/downloads/          # 다운로드된 이미지/동영상 저장
├── data/                      # 크롤링 결과 JSON 저장
└── .env.local                 # APIFY_API_TOKEN
```
