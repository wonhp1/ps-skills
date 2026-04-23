# 인스타그램 분석기 프롬프트

1. API 사용 크롤링

   https://apify.com/apify/instagram-scraper 를 사용해서 인스타그램 게시물을 크롤링하는 서버를 만들고싶어 이 api로 어떤 정보들을 가져올 수 있는지 알려줘

2. 키워드 검색을 통해서 나오는 게시물들을 크롤링하고싶어. 키워드는 입력할 수있는 모든 방식을 구현할거야

크롤링된 게시물에는 표시할 수 있는 모든 정보들을 표시해주고, 크롤링된 게시물들을 정리해서 서버에 카드형식으로 표시하고싶어.
사진의 경우에는 썸네일 뿐만 아니라, 모든 사진들이 전부 나와야해.

크롤링된 내용 저장은 로컬 프로젝트 내에 저장시킬거야. 모두 이미지를 다운받도록 해주고, 영상도 다운받도록 해줘

크롤링 갯수는 사용자가 설정한 갯수만큼 크롤링할거야.

nect.js로 구현해주고, 내 의도를 이해했다면 나에게 설명해봐
apify api 토큰은 발급받을 수 있도록 링크 안내해줘.

⚠️ Apify 호출할 때 꼭 지켜야 하는 것 (이거 안 지키면 검색 결과 0건 나옴):

- `apify/instagram-scraper` 액터의 입력 필드는 **`directUrls`, `search`, `searchType`, `resultsType`, `resultsLimit`** 만 존재해. `hashtags`, `username` 같은 필드는 **없어** — 보내봤자 무시됨.
- 그래서 키워드/해시태그/사용자 검색은 모두 **`directUrls`에 인스타 URL을 직접 조립해서** 넣어야 해.
  - 해시태그·키워드 → `https://www.instagram.com/explore/tags/{태그}/` (한글은 `encodeURIComponent` 처리)
  - 사용자 아이디 → `https://www.instagram.com/{아이디}/`
  - URL이 그대로 들어오면 → 그대로 사용
- `search` + `searchType: "hashtag"` 방식은 **Apify가 내부적으로 구글에 검색 쿼리를 던져서** 인스타 URL을 찾는데, 한글 키워드는 구글이 0건을 반환하는 경우가 많아. 절대 이 방식 쓰지 마.
- Next.js + Turbopack 환경에서 `apify-client`는 동적 require를 써서 번들링 에러("Cannot find module as expression is too dynamic")가 나. `next.config.ts`에 `serverExternalPackages: ["apify-client"]` 꼭 추가해줘.
- API 라우트는 Apify 호출이 30초~2분 걸리니까 `export const maxDuration = 300; export const runtime = "nodejs";` 명시해.
- 인스타 이미지 CDN 호스트(`*.cdninstagram.com`, `*.fbcdn.net`)는 `next.config.ts`의 `images.remotePatterns`에 등록해야 `next/image`가 차단 안 함.
- `.env.local` 파일은 너(Claude)가 직접 만들지 못하게 막혀있을 수 있어. 그땐 사용자한테 직접 만들어달라고 안내하거나 `bash`로 우회해.

3. 이제 크롤링된 게시물을 분석하는 기능을 만들거야
분석은 claude cli를 통해서 구독 요금제를 사용하도록 할거야
분석의 기준은 md파일을 통해서 md파일을 확인한 뒤에 ai가 분석해서 결과를 보여줄 수 있도록 하고싶어 

크롤링 결과에서 분석할 게시물을 선택한 뒤에, 분석 버튼을 눌러서 분석결과를 표시하도록 하고싶어

처음 기준은 너가 알아서 작성해줘 추후에 내가 수정하도록 할게

내 의도를 이해했다면 나에게 설명해봐