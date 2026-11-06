# MoneyBooks · 돈 교과서 서가

월급 명세서와 예산에서 경제 뉴스, 주식과 부동산, 세금과 보험까지. 숫자를 직접 바꾸며 배우는 한국어 인터랙티브 돈·경제 교과서를 한 저장소에서 관리합니다. 홈은 SemiBooks·MathBooks의 책장·검색·분야 필터·실습·읽는 순서 구성을 따릅니다.

서비스 주소: **https://money.euiyun.com/** · 책 한 권의 홈은 **`/{slug}/`**, 각 장은 **`/{slug}/chapters/{chapter}.html`**입니다.

## 수록 책

| 경로 | 책 | 분야 | 처음 가져온 곳 |
|---|---|---|---|
| `/money/` | MoneyBook · 살아가는 데 필요한 돈 교과서 | 돈과 경제 | `geniuskey/moneybook` |
| `/economics/` | EconomicsBook · 경제 뉴스 읽는 교과서 | 돈과 경제 | `geniuskey/economicsbook` |
| `/stock/` | StockBook · 만져 보며 배우는 주식 교과서 | 투자·집 | `geniuskey/stockbook` |
| `/estate/` | EstateBook · 집값과 전세를 숫자로 푸는 부동산 교과서 | 투자·집 | `geniuskey/estatebook` |
| `/tax/` | TaxBook · 숫자를 직접 바꾸며 배우는 세금 교과서 | 세금·보험 | `geniuskey/taxbook` |
| `/insure/` | InsureBook · 보험이 복잡한 이유를 푸는 교과서 | 세금·보험 | `geniuskey/insurebook` |

준비 중인 책은 `roadmap.json`에 적습니다(지금은 비어 있음).

StockBook의 `chapters/index.html`은 15장 ‘지수와 ETF’입니다. 그래서 빌드·검사·사이트맵은 서가 홈과 책 홈의 `index.html`만 디렉터리 주소(`/stock/`)로 접고, 이 장은 파일 이름 그대로 둡니다.

## 구성

```text
index.html, shelf.css                  서가 홈: 책장·분야 필터·검색, 대표 실험, 읽는 순서
catalog.js                            생성된 책 목록(직접 수정하지 않음)
book.json                             서가 메타데이터, 각 책의 book.json 목록
roadmap.json                          준비 중인 책
migration/books.json                  책마다 slug·서가 정보(표지 motif·색·분야)와 원본 저장소, 주소 바꾸기 규칙
migration/import-state.json           가져온 파일의 원본 커밋과 해시
shared/js/shelf.js                    서가 홈의 책장 렌더링·필터
shared/js/covers.js                   SVG 책 표지(geniuskey/books 포털 코드)
shared/js/book-shell.js               모든 책 상단 바·서랍의 ‘돈 서가’ 연결
shared/img/sims/                      홈 ‘직접 해보기’의 시뮬레이터 화면
{slug}/                               각 책의 본문, 계산 엔진, 용어 데이터, 책별 회귀 검사(scripts/)
tools/import-books.py                 개별 저장소에서 가져오기(원본은 읽기만 함, 이 저장소의 수정은 덮어쓰지 않음)
tools/sync-catalog.py                 catalog.js 생성(migration/books.json + roadmap.json, 장·시뮬레이터 수는 소스에서 셈)
tools/sync-sitemap.py                 루트 사이트맵 생성
tools/build.mjs                       정적 배포물(.book-dist/) 생성, 분석 태그 추가
tools/check.py                        메타데이터·링크 정적 검사
tools/factcheck.cjs                   각 책의 scripts/*.cjs(계산 엔진·인라인 스크립트 회귀 검사) 실행
tools/browser-check.cjs               데스크톱·모바일 브라우저 검사(Playwright)
tools/og.mjs                          서가 공유 이미지 og.png 생성(Playwright)
```

## 실행과 배포

```bash
npm ci
npm test
npm run build
python3 -m http.server 8000 --directory .book-dist
node tools/browser-check.cjs            # Playwright 필요(PW_PATH 또는 ../book 체크아웃)
```

배포물은 `.book-dist/`입니다. GitHub Pages의 Source를 GitHub Actions로 설정하고 커스텀 도메인 `money.euiyun.com`과 DNS를 연결합니다. `main`에 push하거나 Deploy Book 워크플로를 실행하면 검사·빌드 후 배포합니다. 이 저장소의 CNAME 설정만으로 DNS나 GitHub 설정이 자동으로 바뀌지는 않습니다.

여섯 권은 가져올 때의 canonical·Open Graph·구조화 데이터와 KaTeX CDN을 그대로 씁니다(주소만 `money.euiyun.com/{slug}/`로 바꿈). 책 안의 `tools/head.py`·`tools/check.py`는 개별 저장소 시절의 도구로, 서가 빌드에는 쓰지 않습니다.

책·장·시뮬레이터 수가 바뀌면 `npm run catalog`, `npm run sitemap`, `node tools/og.mjs`로 목록과 공유 이미지를 갱신합니다.

## 가져온 책

여섯 권은 2026-11-06에 개별 저장소에서 `tools/import-books.py --write`로 가져왔습니다. 지금부터는 이 저장소에서 직접 고칩니다. 가져올 때 바꾼 것은 다음뿐입니다.

- 각 책의 옛 주소(`https://{책}book.euiyun.com/`)를 `https://money.euiyun.com/{slug}/`로 바꾸고, 본문 링크는 `/{slug}/…` 같은 사이트 안 경로로 바꿨습니다. 여섯 권 사이의 교차 링크도 서가 안 주소를 씁니다.
- 다른 서가로 옮겨 간 책의 주소를 바꿨습니다: SensorBook → `semi.euiyun.com/sensor/`, LithoBook → `semi.euiyun.com/litho/`.
- 쪽마다 `shared/css/book-shell.css`·`shared/js/book-shell.js`를 붙여 상단 바의 ‘전체 책’ 버튼을 ‘돈 서가’로 바꿨습니다. Cloudflare 비컨은 지우고 빌드가 분석 태그를 붙입니다.
- 테마 저장 키를 `money-theme` 하나로 통일했습니다. 각 책의 `book.json`은 서가 주소·저장소·`basePath`로 바꿨습니다.

원본 저장소를 지우기 전까지는 `npm run import:check`로 원본과 달라진 곳을 확인할 수 있습니다. 원본을 지운 뒤에는 MathBooks처럼 `tools/import-books.py`와 `migration/import-state.json`을 지우고, 서가 정보가 든 `migration/books.json`만 남깁니다.

## 라이선스

코드는 MIT, 교육 콘텐츠는 CC BY 4.0입니다. [라이선스 안내](LICENSE.md)와 각 책의 저작권 표시를 참고하세요. 시뮬레이터 수치는 교육용 근사 모델이며 투자·세무·보험 자문을 대신하지 않습니다.
