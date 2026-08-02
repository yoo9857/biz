# CrawlCred 업그레이드 지시안 — 2026-07-26 (일)

| 항목 | 내용 |
|---|---|
| 문서 번호 | WO-2026-0726-03A (WO-03 수익 스프린트 내 일일 지시) |
| 발행일 | 2026-07-26 (일) |
| 대상 기간 | **오늘 (7/26)** — 미완 항목은 7/27~7/28 이관 |
| 발행 근거 | 앱 셋업 실사 (레포·라이브 사이트·서버 직접 확인, 2026-07-26) |
| 상위 문서 | `CrawlCred-업무지시안-WO03-수익스프린트.md` (8/24까지 첫 결제 1건) / 기준 문서 `CrawlCred-사업기획안.md` |
| 한 줄 결론 | **오늘은 새 기능이 아니라 "배포 경로 복구"부터다. 지금 코드는 어디에도 커밋되지 않은 채 라이브에만 존재한다.** |

---

# Part 0. 셋업 실사 결과 (2026-07-26 확인)

## 0.1 확인 방법

로컬 레포(`C:\biz\crawlcheck`) git 상태·원격, 서버 베어 레포 훅, 라이브 사이트 HTTP 응답·메타태그·sitemap·`/api/stats`·`/api/contractors`, 루트 테스트 스위트를 직접 실행/조회.

## 0.2 문서와 실제가 다른 항목 (중요)

| # | 문서상 기재 | **실제 확인 결과** | 판정 |
|---|---|---|---|
| ① | 사업기획안 §4.3 "웹 — push→자동 배포. 서버 빌드 금지" / repo CLAUDE.md "Push to `main` → GitHub Actions 테스트·빌드·배포" | **그 파이프라인은 작동할 수 없는 상태다.** `git remote -v` 결과 원격은 `server` (ssh://root@172.236.235.9/srv/crawlcheck/repo.git) **하나뿐 — GitHub 원격이 없다.** 따라서 `.github/workflows/deploy.yml`(npm test 게이트 → web 빌드 → rsync)은 트리거될 수 없다. 서버 베어 레포 `hooks/`에도 `.sample` 파일만 있고 **활성 post-receive 훅이 없다** → 푸시해도 배포되지 않는다 | 🚨 **P0 구조 결함** |
| ② | WO-03 F8/F9 "og 수정·favicon 수정 완료(7/24), 배포는 A2에 포함" | **라이브에는 이미 반영되어 있으나, 코드는 커밋되지 않았다.** 라이브 `/terms/` og:title = "Terms of Service \| CrawlCred"(페이지별 채움 = 수정판), `/favicon.ico` 200, `/apple-icon.png` 200. 반면 로컬은 `web/app/layout.tsx`·`web/app/page.tsx` 미커밋 수정 + `favicon.ico`·`apple-icon.png` **미추적**. `server/main`과 로컬 `main`은 0/0 (동일) → **라이브가 모든 커밋보다 앞서 있다** = 수동 빌드+rsync 배포 흔적 | 🚨 **소실·회귀 위험** |
| ③ | 사업기획안 "마켓플레이스·프로필 라이브" / 콜드메일 1통 "Your verified profile is live: [링크]" | **업체 개별 프로필은 색인 불가 페이지다.** `web/app/pro/page.tsx`에 `robots: { index: false }`, URL은 `/pro/?c=<slug>` 쿼리 파라미터 방식, sitemap 39 URL에 **미포함**. 코드 주석에도 "indexable per-slug pages come with real data"로 TODO 명시 | ⚠️ **최대 SEO 기회 미회수** |

## 0.3 정상 확인된 항목

| 항목 | 상태 |
|---|---|
| 라이브 사이트 | 정상 — `/` 200, `/north-carolina/charlotte/` 200 (도시 페이지 title·canonical·og 페이지별 정상), sitemap 39 URL (코어 15 + 가이드 10 + 주 5 + 도시 10) |
| `/api/stats` | `{"listed":67,"verified":7,"checks":77,"cities":41}` — 홈 숫자 라인 근거 정상 |
| `/api/contractors` | 정상 (slug·verifications[kind/status/detail] 반환 — 빌드 타임 소스로 사용 가능) |
| 루트 테스트 | **그린 (31 pass / 0 fail, 0.6s)** — 오늘 작업의 베이스라인 확보 |
| 결제 설정 | `web/lib/featured.ts`에 UUID 3종 반영 확인 ($99 / $149 / $199), `FOUNDING` 90%·10코드·2026-08-24 마감 + `foundingActive()` 존재 |
| GA4/GTM | 커밋 5085d02·38793ec 반영 (F10 이중 집계 확인은 오너 미결) |

## 0.4 미착수로 확인된 WO-03 항목

- **A2** (결제 배관 e2e) — 기한 7/26 = 오늘. 미완.
- **B3** (타깃 리스트 49곳) — `C:\biz`에 산출물 파일 없음. 미착수.
- **F2** (사전 생성 체크아웃 URL 컬럼) — B3와 함께 미착수.
- **F3** (`/api/my/leads-count`) — 코드 검색 결과 **미구현**.
- **F5** (이메일 없는 업체 집계) — 미착수.
- 미추적 파일: `web/public/illustrations/logo-m1~m6·m5-source` 7개(F11 오너 결정 대기), `crawlcheck/maketer/` 이미지 폴더(레포에 들어갈 물건 아님 → `.gitignore` 대상).

---

# Part 1. 오늘의 판단

## 1.1 왜 배포 경로가 오늘 1순위인가

수익 스프린트의 크리티컬 패스는 7/29 콜드메일 1차 발송이고, 그 앞에 A2(결제 배관 검증)와 B3(타깃 리스트)가 있다. 그런데 **A2를 통과시켜도 배포할 방법이 문서화된 대로 존재하지 않는다.** 지금 라이브는 누군가 로컬에서 빌드해 밀어 넣은 산출물이고, 그 소스는 워킹트리에만 있다. 이 상태의 실질 리스크 두 가지:

1. **소실**: 로컬 디스크 사고 = og/favicon 수정 소실 (재작성 30분이라 치명적이진 않으나, 앞으로 쌓일 모든 작업이 같은 위험에 놓인다).
2. **회귀**: 누군가(=미래의 나) "정상 경로"로 배포하면 커밋 기준으로 빌드되어 **og:url 홈 고정 버그(F8)가 되살아난다.** F8은 오너가 직접 발견한, 전 페이지 검색·공유 트래픽을 홈으로 귀속시키던 버그다. 되돌리면 안 된다.

원칙: 병목 아닌 곳의 개선은 낭비지만, **배관이 새는 상태로 물을 더 붓는 것도 낭비다.** 45분이면 막힌다.

## 1.2 오늘의 유일한 신규 개발: 업체별 색인 페이지 (G2)

색인 가능한 업체 프로필 67개가 지금 시점에 **가장 값이 큰 한 수**다. 이유는 세 가지가 겹치기 때문:

- **검색 의도가 최상단.** "회사명 + license / reviews / legit"은 계약 직전에 치는 검색어다. 비용 가이드보다 전환까지 짧다.
- **경쟁자가 복제 못 한다.** 우리는 NCLBGC 공공기록 대조 결과 77건(전부 `source_url` + `checked_at` 보유)을 갖고 있다. Yelp/Angi는 별점만 있다.
- **콜드메일과 같은 물건을 쓴다.** 메일 1통("면허를 확인했고 프로필이 라이브다")이 가리키는 링크가 `?c=` 쿼리 URL이 아니라 색인되는 정식 페이지가 된다. 판매 자료와 SEO 자산이 같은 페이지가 된다.

단 오늘의 시간 예산상 **G1·A2·B3가 먼저다.** G2는 오늘 착수 → 7/28 완료를 기한으로 잡는다 (7/29 발송 전에 링크가 정식 URL이 되게).

## 1.3 오늘 명시적으로 손대지 않는 것

- **도시 페이지 확장(41개 도시 중 31개 미보유)** — 기회는 실재하지만, 업체 수가 적은 도시에 얇은 페이지를 찍으면 구글이 걸러낸다(WO-02에서 이미 세운 원칙). G2 완료 후 "업체 3곳 이상인 도시만 승격" 기준으로 재검토.
- F3(leads-count), F4(슬롯 잔여 UI), F11(로고 이미지 처리), SC 스크래퍼, 신규 콘텐츠 — 전부 유지 보류.

---

# Part 2. 업무지시안

## 2.0 공통 완료 기준

- 루트 `npm test` 그린 + `cd web && npm run build` 그린 (빌드 전 dev 서버 종료)
- 변경 동작 로컬에서 최소 1회 실증
- 커밋 메시지에 `[WO3-G1]` 형식 태그
- 카피 원칙 유지: 공포 마케팅·guarantee 표현 금지, 검증 표기에는 항상 확인 날짜, "verified"는 엄격 기준 7곳에만

## 2.1 태스크 총괄

| # | 태스크 | 담당 | 기한 | 소요 | 우선순위 |
|---|---|---|---|---|---|
| **G1** | **배포 경로 복구 + 미커밋 변경 커밋/푸시** | 개발(+오너 15분) | 오늘 오전 | 45분 | **P0** |
| **A2** | 결제 배관 e2e (test 모드: 커스텀데이터 → 슬롯 활성화 → Featured 노출) | 개발 | 오늘 | 1.5h | **P0** |
| **B3** | 타깃 리스트 49곳 + 사전생성 체크아웃 URL(F2) + 이메일 결측 집계(F5) | 개발 | 오늘 | 1.5h | **P0** |
| **G2** | 업체별 색인 가능 프로필 페이지 (`/pro/[slug]/` 67개) | 개발 | **착수 오늘 / 완료 7/28** | 3–4h | P1 |
| O1 | LS 라이브 심사 상태 확인 (승인 메일/대시보드) | 오너 | 오늘 | 5분 | P0 |
| O2 | GTM 컨테이너에 GA4 태그 존재 여부 확인 (F10 이중 집계) | 오너 | 오늘 | 10분 | P1 |
| O3 | GitHub 레포 생성 여부·권한 결정 (G1 전제) | 오너 | 오늘 오전 | 15분 | P0 |

## 2.2 태스크 상세

### G1 — 배포 경로 복구 (오늘 첫 작업. 이거 전에 다른 커밋 금지)

**순서를 지킬 것. 커밋이 먼저다 — 라이브에만 있는 상태를 먼저 없앤다.**

1. **미커밋 변경 커밋** (레포 루트 `C:\biz\crawlcheck`)
   - 대상: `web/app/layout.tsx`(루트 og title/description/url 제거), `web/app/page.tsx`(홈 전용 og 지정), `web/app/favicon.ico`, `web/app/apple-icon.png` 신규 추가
   - `crawlcheck/maketer/`는 커밋하지 말고 `.gitignore`에 추가 (마케팅 이미지 원본 — 레포 자산이 아니다)
   - `web/public/illustrations/logo-m*`는 **이번 커밋에서 제외** (F11 오너 결정 대기 중 — 결정 전 추가하면 미사용 자산이 레포에 고착된다)
   - 커밋 메시지: `[WO3-G1] Per-page OG tags + favicon/apple-icon (F8/F9, deployed 7/24)`
   - 완료 기준: `git status`에 위 4개 파일이 사라짐

2. **원격 정상화** (O3 결정 후)
   - GitHub 원격 추가: `git remote add origin <repo-url>` → `git push -u origin main`
   - Actions Variables 설정 필수 (없으면 태그가 빠진 채 빌드된다): `NEXT_PUBLIC_SITE_URL`, `NEXT_PUBLIC_CONTACT_EMAIL`, `NEXT_PUBLIC_GA_ID=G-0KT5CD4NCD`, `NEXT_PUBLIC_GSC_VERIFICATION`
   - Secrets: `DEPLOY_SSH_KEY`, `DEPLOY_KNOWN_HOSTS`(= `ssh-keyscan -t ed25519 172.236.235.9`)
   - **비공개 레포로 만들 것** (`.env`는 무추적이지만, ops 설정·서버 IP가 노출될 이유가 없다)
   - 완료 기준: Actions 1회 성공 → 라이브 사이트가 **여전히** 페이지별 og를 갖고 있는지 재확인 (`curl -s https://crawlcred.com/terms/ | grep og:title`). 여기서 홈 og가 나오면 1번 커밋이 빠진 것이다.

3. **GitHub 사용을 오너가 원하지 않는 경우 (대안 경로)**
   - 서버 베어 레포에 post-receive 훅을 두지 **말 것** (서버 빌드 금지 원칙 + 1CPU/2GB에서 Next 빌드는 위험).
   - 대신 로컬 배포 스크립트를 레포에 명문화: `scripts/deploy-web.mjs` — ① `npm test` ② `cd web && npm run build` ③ `web/out/` rsync (deploy.yml의 두 rsync 명령과 동일한 `--delete --exclude '/_next/static'` 순서 유지) ④ `git push server main`을 배포 조건으로 강제.
   - 완료 기준: 스크립트 1회 실행으로 라이브 갱신 성공 + 커밋과 라이브가 동일 커밋 해시에서 나왔음을 기록.

4. **문서 정정 (필수 — 이걸 빼면 같은 사고가 반복된다)**
   - `crawlcheck/CLAUDE.md` 배포 문단: 실제 경로로 수정
   - `CrawlCred-사업기획안.md` §4.3 "웹" 행: 실제 배포 방식·검증 방법으로 수정, §6 의사결정 로그에 "2026-07-26: 배포 파이프라인 미작동 발견 및 복구" 기록

### A2 — 결제 배관 e2e (7/29 발송의 관문)

- 로컬 API 기동 후 `scripts/e2e-billing.mjs`로 웹훅 경로 회귀 확인 (활성화·갱신·중복전달 idempotency·취소·환불·슬롯 희소성). **주의: `E2E-` 프리픽스 마켓에 파괴적** — 실 마켓 토큰으로 돌리지 말 것.
- 그다음 **실제 LS test 체크아웃 1회**: 대시보드 → Get featured → `checkoutUrl()` 생성 링크 → 결제 화면에 **가격이 티어와 일치하는지 확인** (city_t2 $99 / city_t1 $149 / state $199 — UUID 매핑 검증의 핵심) → `FOUNDING` 자동 적용으로 첫 결제 $9.9 표시 확인 → 웹훅 수신 → `featured_slots` 활성화 → 디렉토리/도시 페이지에 Featured 노출까지 **한 줄로 실증**.
- 완료 기준: 전 구간 통과 + 로그 보존. 실패 시 수정 후 재실증. **여기가 막히면 콜드메일 발송을 미룬다** (돈 못 받는 링크를 보내는 게 최악).

### B3 — 타깃 리스트 + 사전생성 체크아웃 URL + 이메일 결측 집계

- 산출물: `C:\biz\CrawlCred-타깃리스트-2026-07-26.csv` (또는 .md 표)
- 컬럼: `slug, name, city, state, email, phone, license_no, license_status, checked_at, profile_url, checkout_url, tier, segment(verified7|rest42), sent_at, replied, claimed, paid`
- `checkout_url`은 `checkoutUrl({tier: tierFor(...), contractorSlug, market: marketToken(...), discountCode:"FOUNDING"})` 로 **업체별 사전 생성** → 콜드메일 2통의 `{checkout_url}`에 그대로 사용 (F2: 로그인 강요 제거)
- `profile_url`은 **G2 완료 후 정식 URL로 일괄 교체**한다. G2 미완 상태로 발송해야 한다면 현행 `/pro/?c=<slug>`를 쓰되, 리스트에 교체 대상임을 표시.
- F5: 이메일 없는 업체 수를 집계해 리스트 맨 위에 기록 (예: "49곳 중 이메일 보유 N곳 — 발송 가능 모수는 N"). 발송 목표 49통은 이 숫자에 맞춰 정정한다. 거짓 목표는 KPI를 오염시킨다.
- 완료 기준: verified 7곳이 리스트 최상단에 개인화 변수(면허번호·상태·확인일) 전부 채워진 상태로 존재.

### G2 — 업체별 색인 가능 프로필 페이지 (오늘 착수 / 7/28 완료)

**설계 (권장안 확정 — 이 구조로 진행):**

- **URL: `/pro/[slug]/`** (정적 export, `generateStaticParams`가 빌드 타임에 `/api/contractors` 조회). 도시 하위(`/north-carolina/charlotte/<slug>/`)는 토픽 관련성이 조금 낫지만 슬러그 이동·중복 canonical 관리 비용이 커진다 → **오늘은 `/pro/[slug]/`, 도시 관련성은 내부링크로 확보.**
- 기존 `/pro/?c=` 쿼리 페이지는 `robots: index:false` 유지 (호환용). 신규 per-slug 페이지에서만 `index`, `canonical=/pro/<slug>/`.
- **페이지 내용** (얇은 페이지 방지 — 이게 승패를 가른다):
  - H1: 회사명 + 도시. title: `Is {Name} licensed in {City}, {ST}? — Verification Report | CrawlCred`
  - 검증 표: kind / status / **detail 원문** / `checked_at` / `source_url` — 우리 데이터의 핵심. `none_found`도 그대로 노출하되 **NC의 $40k 면허 기준 설명을 함께 둔다** (없음 ≠ 불법. 이 정직함이 우리 포지셔닝이다)
  - "무엇을 확인했고 무엇을 확인하지 않았는가" 블록 (methodology 링크)
  - 내부링크 3개+: 해당 도시 페이지 ↔ `/directory/` 필터 ↔ `verify-contractor-license` 가이드
  - CTA 1개: "Get quotes from verified pros in {City}"
  - JSON-LD `LocalBusiness` — **`aggregateRating`은 실제 리뷰가 있을 때만** 출력 (없는데 넣으면 구조화데이터 위반)
  - 미청구(unclaimed) 업체에는 "This is your business? Claim it free." (무료 클레임 유입 = 영업 자산)
- **sitemap.ts**: 67개 프로필 URL 추가, `lastModified`는 해당 업체 **최신 `checked_at`** (빌드 시간 아님 — 날짜 정직성 원칙)
- **빌드 안전장치 (필수)**: 빌드 타임 API 조회가 실패하거나 업체 수가 **10 미만이면 빌드를 실패시킨다.** 조용히 빈 사이트를 배포하면 색인된 67페이지가 한 번에 404가 된다.
- 완료 기준: `web/out/pro/<slug>/index.html` 67개 생성, 무작위 3개 페이지에서 title·canonical·검증 날짜·내부링크 확인, sitemap에 포함, 라이브 배포 후 GSC에 **sitemap 재제출 + 대표 3개 URL 색인 요청**.

### 오너 액션 (O1~O3)

- **O1**: LS 라이브 활성화 심사 상태 확인. 승인 났으면 즉시 공유 → 실카드 $1 검증 일정 확정. 반려면 사유 확인 후 재신청(스프린트 크리티컬 패스).
- **O2**: tagmanager.google.com → GTM-T9S75TGD 컨테이너에 GA4 태그가 있으면 **GTM 쪽 제거** (직접 gtag G-0KT5CD4NCD 유지 권장). 이중 집계 상태로 기록한 KPI는 전부 2배로 부풀어 있게 된다.
- **O3**: GitHub 레포 사용 여부 결정 → 사용 시 비공개 레포 생성 + Actions Variables/Secrets 등록 (G1-2), 미사용 시 로컬 스크립트 경로 선택 (G1-3).

## 2.3 오늘 하지 않는 것

- 도시 페이지 31개 신규 생성 (G2 후 "업체 3곳 이상" 기준으로 재검토)
- F3 leads-count, F4 슬롯 잔여 UI(첫 결제 후 조건부), F11 로고 이미지 정리
- SC 스크래퍼, 신규 콘텐츠, 마켓플레이스 프로모션, 애드센스
- 신규 UI 리디자인 일체

## 2.4 보고 체계

- G1 완료 즉시 보고 (배포 경로가 무엇으로 확정됐는지 한 줄) + `사업기획안` §4.3·§6 정정
- A2 결과는 통과/실패로만 보고. 실패 시 7/29 발송 일정 재조정 판단을 함께 올린다.
- 오늘 종료 시 §4.1 KPI 표에 발송 가능 모수(F5 집계 결과) 기록
- 7/27 (월) 주간 KPI 리뷰 #1 — GSC 색인 수 첫 기록(베이스라인). G2 배포 전 숫자를 반드시 남겨야 효과 측정이 가능하다.

---

# Part 3. 순차 검토 결과 (2026-07-26 오후, 실행 가능성 실사)

Part 2의 각 태스크를 실행 직전 상태로 검토했다. **아래 정정이 Part 2보다 우선한다.**

## 3.1 검토 요약

| # | 태스크 | 검토 판정 | 조치 |
|---|---|---|---|
| G1 | 배포 경로 복구 | ⚠️ **함정 1건 발견** — GitHub 경로만 만들면 백엔드는 영원히 갱신 안 됨 | 3.2 참조, 절차 보강 |
| A2 | 결제 배관 e2e | ⚠️ **분리 필요** — 로컬 DB는 데모 6건, 프로덕션(67건) 재현 불가 | 3.3 참조, A2-a/A2-b로 분리 |
| B3 | 타깃 리스트 | 🚨 **선행 블로커** — 발송할 이메일 주소가 데이터에 없을 가능성이 높다 | 3.4 참조, **최우선으로 확인** |
| G2 | 프로필 색인 페이지 | ✅ 실행 가능 — 빌드 타임 API 조회 가능 확인 | 변경 없음 |
| 베이스라인 | 루트 테스트 | ✅ 31 pass / 0 fail | — |

## 3.2 G1 정정 — 서버의 `origin`은 GitHub가 아니다

**확인된 사실**
- 서버 앱 체크아웃 `/srv/crawlcheck/app`의 원격: `origin → /srv/crawlcheck/repo.git` (**서버 자기 자신의 베어 레포**)
- `deploy.yml`의 백엔드 단계는 서버에 SSH로 들어가 `git fetch origin && git reset --hard origin/main`을 실행한다
- **따라서 GitHub Actions가 돌아도, 서버는 GitHub이 아니라 자기 베어 레포를 fetch한다.** 베어 레포에는 아무도 푸시하지 않으므로 **정적 웹만 갱신되고 API·마이그레이션·nginx 설정은 영구히 정체된다.**
- 현재 서버 앱 HEAD = `411e3a5` (로컬 `bb3a4a4` 대비 2커밋 뒤). 다행히 `411e3a5..bb3a4a4`는 `src/`·`Dockerfile`·`package.json` **무변경**(web 전용)이라 기능상 무해 — 그러나 구조 문제는 그대로다.

**정정된 G1-2 절차 (GitHub 경로 선택 시, 아래 중 하나를 반드시 함께 할 것)**
- (권장) 서버 앱 체크아웃의 원격을 GitHub로 전환: `git remote set-url origin <github-url>` + 서버에 read-only deploy key 등록 → 그 후 Actions 1회 실행해 `deploy complete: <해시>`가 **최신 커밋 해시**로 찍히는지 확인
- (대안) 배포 시 GitHub과 서버 베어 레포 **양쪽에 푸시**: `git push origin main && git push server main` — 사람이 잊는 순간 깨지는 방식이라 비권장
- **완료 기준 추가**: 배포 후 `ssh root@... 'cd /srv/crawlcheck/app && git rev-parse --short HEAD'`가 로컬 HEAD와 일치할 것. 웹만 확인하고 끝내면 이 결함을 또 놓친다.

## 3.3 A2 정정 — 로컬에서는 마지막 구간을 재현할 수 없다

**확인된 사실**
- 로컬 DB는 **Windows 네이티브 PostgreSQL 17.5** (127.0.0.1:5544, 프로덕션 터널 아님 — 프로세스 확인) → e2e 스크립트를 돌려도 프로덕션에 영향 없음 ✅
- 그런데 로컬 DB 내용은 **contractors 6건 / verifications 0건**. 프로덕션은 67건 / 77건 → **"실제 업체 slug로 슬롯 활성화 → 라이브 디렉토리 Featured 노출"은 로컬에서 재현 불가**
- 프로덕션은 PG16 컨테이너, 로컬은 PG17 → 마이그레이션 검증 시 버전 차이 인지할 것

**정정된 A2 절차 — 두 단계로 분리**
- **A2-a (로컬, 안전, 오늘)**: 로컬 API 기동 → `scripts/e2e-billing.mjs`로 웹훅 전 경로 회귀(활성화·갱신·idempotency·취소·환불·슬롯 희소성). `E2E-` 프리픽스 마켓만 사용 — 실 마켓 토큰 금지. 완료 기준: 전 케이스 통과.
- **A2-b (프로덕션, LS test 모드, 오늘)**: 실제 test 체크아웃 1회로 ① 티어별 표시 가격이 $99/$149/$199와 일치(UUID 매핑 검증) ② `FOUNDING` 자동 적용가 표시 ③ 웹훅 수신 → `featured_slots` 활성화 ④ 라이브 디렉토리/도시 페이지에 Featured 노출.
  - **🚨 반드시 정리(cleanup)까지 한 세트로 실행**: 프로덕션 DB에 테스트 슬롯이 남으면 **라이브 사이트에 가짜 Featured 업체가 노출된다.** 검증 직후 해당 슬롯 비활성화 + `billing_events` 테스트 행 표시. 정리 완료 확인(디렉토리에서 Featured 배지 사라짐)까지가 A2-b의 완료 기준.
  - 가능하면 실업체가 아닌 **테스트 목적 slug/market**으로 진행. 실업체 프로필에 Featured가 잠깐이라도 붙는 건 피한다.

## 3.4 B3 선행 블로커 — 콜드메일을 보낼 주소가 없을 가능성

**확인된 사실 (여기가 오늘 검토의 핵심)**
- 시딩 소스 `data/nc-charlotte.json` 등의 필드는 `name, city, state, zip, phone, website, services` — **`email` 필드가 아예 없다** (17건 표본 전수)
- 공개 API `/api/contractors`도 email을 반환하지 않고, `src/contractors.ts`에 `email` 문자열 자체가 없다
- 로컬 DB 6건 중 email 보유 2건 (데모 데이터)
- `src/admin.ts`의 CSV 내보내기는 leads / featured / inquiries / orders / users / claims / reviews / revenue — **contractors(이메일 포함) 내보내기가 없다**
- 라이브 admin API는 활성 상태 (`/api/admin/leads.csv` → 401 = `ADMIN_TOKEN` 설정됨)
- `CrawlCred-콜드메일-시퀀스.md`에도 주소 출처에 대한 언급이 없다

**결론**: WO-03의 "이메일 보유분 49곳"이라는 전제는 **검증되지 않았고, 데이터 계보상 전량 NULL일 가능성이 높다.** 사실이라면 7/29 1차 발송은 불가능하며, **업체 웹사이트 contact 페이지에서 주소를 수동 수집(49곳 × 3~5분 ≈ 2.5~4h)** 하는 작업이 계획에 새로 들어가야 한다. 이건 스프린트 일정을 실제로 바꾸는 항목이다.

**부수 영향**: `contractors.email`은 "로그인 이메일이 리스팅과 자동 연결되는" 기능의 키다(repo CLAUDE.md §7). 이메일이 없으면 **콜드메일 수신자가 로그인해도 자기 리스팅에 자동 연결되지 않는다** → 클레임 동선도 함께 막힌다.

**지금 할 일 (P0로 승격, 다른 개발보다 먼저)**

1. **프로덕션 실측 (5분, 오너 또는 권한 승인 필요)** — 아래 한 줄로 확정된다:
   ```
   ssh root@172.236.235.9 'cd /srv/crawlcheck/app && docker compose exec -T postgres \
     psql -U crawlcheck -d crawlcheck -c "select count(*) total, count(nullif(trim(email),$$$$)) with_email from contractors"'
   ```
   (검토 세션에서 이 조회는 권한 정책에 막혀 실행하지 못했다 — 오너가 직접 실행하거나 권한을 승인해 주면 즉시 확정된다)
2. **`with_email`이 0~5건이면**: B3의 성격이 "리스트 정리"에서 **"주소 수집"**으로 바뀐다. verified 7곳 먼저 수동 수집(30분) → 7/29 1차 발송은 **7곳으로 진행**, 나머지 42곳은 8/1까지 분할 수집. 발송 KPI 49통은 **수집 가능 모수로 정정**(거짓 목표 금지).
3. **`/api/admin/contractors.csv` 추가 (30분, P1)**: slug·name·city·state·phone·website·email·license_no·license_status·checked_at·claimed 내보내기. 수집한 주소를 DB에 채우고 나면 B3 리스트를 매번 손으로 만들 필요가 없어지고, F5 집계도 자동화된다.

## 3.5 정정된 오늘 실행 순서

| 순서 | 태스크 | 소요 | 상태 |
|---|---|---|---|
| 1 | **G1** 배포 경로 복구 + 커밋 + 재배포 검증 | 45분–1h | ✅ **완료 (3.6 참조)** |
| 2 | **A2-a** 로컬 e2e 회귀 | 40분 | ✅ **완료 — 78/78 통과 (3.7)** |
| 3 | **B3 도구화** `/api/admin/contractors.csv` 신설·배포 | 1h | ✅ **완료 — 프로덕션 가동 (3.8)** |
| 4 | **B3-0 프로덕션 이메일 실측** | 5분 | ⏳ **오너 1줄 실행 대기 — 이 숫자가 이번 주 계획을 결정한다 (3.8)** |
| 5 | **A2-b** 프로덕션 test 체크아웃 + **정리** | 50분 | ⏳ 오너(LS 대시보드) 대기 — 정리 누락 금지 |
| 6 | **B3** (실측 결과에 따라 리스트 정리 or 주소 수집) | 1.5–3h | ⏳ B3-0 결과에 따라 성격이 바뀜 |
| 7 | **G2** 업체별 색인 페이지 | 3–4h | ✅ **완료·배포 (7/28 기한 → 당일 완료, 3.9)** |

## 3.6 G1 완료 기록 (2026-07-26 배포 실행)

**커밋**: `405abd3 [WO3-G1] Per-page OG tags + favicon/apple-icon` — `layout.tsx`(루트 og 제거)·`page.tsx`(홈 전용 og)·`favicon.ico`·`apple-icon.png`·`.gitignore`(`maketer/`). `logo-m*.webp` 7개는 F11 오너 결정 대기로 **의도적 미커밋** (코드 참조 0건, 라이브에는 존재).

**배포 경로 (실제로 작동한 절차 — repo `CLAUDE.md` "Deploying"에 명문화)**
1. 루트 `npm test` ✅ 31 pass → `cd web && npm run build` ✅ (dev:3201 idle 확인)
2. `git push server main` → 서버 앱 체크아웃 `git fetch && reset --hard` → **HEAD 일치 확인 `405abd3`**
3. **로컬에 `rsync`가 없음**(Git Bash) → `tar czf - -C web/out . | ssh 'tar xzf - -C /tmp/cc-deploy'` (186 파일)
4. 서버에서 rsync 2단계: `_next/static/` additive 우선 → `--delete --exclude "/_next/static"` 전체
5. 백엔드: `411e3a5..405abd3`이 `src`·`Dockerfile`·`package.json` 무변경이라 **도커 재빌드·마이그레이션 생략** (nginx conf도 무변경)

**사전 안전 확인**
- `--delete` 영향 범위: 웹루트 최상위 목록과 `web/out` 최상위 목록 **완전 일치** 확인 후 실행 (`ads.txt`·`llms.txt`는 `web/public/`에 있어 산출물 포함 — 삭제 위험 없음)
- 환경변수 누락 리스크 없음: `web/lib/site.ts`가 GA(`G-0KT5CD4NCD`)·GTM·GSC 토큰·SITE_URL·CONTACT_EMAIL 전부 하드코딩 폴백 보유 → `web/.env` 없이 빌드해도 태그 유지

**배포 후 검증 (전부 통과)**
- HTTP 200: `/`, `/north-carolina/charlotte/`, `/north-carolina/`, `/directory/`, `/tips/`, `/guides/verify-contractor-license/`, `/pro/`, `/for-contractors/pricing/`, `/favicon.ico`, `/apple-icon.png`, `/llms.txt`, `/ads.txt`, `/sitemap.xml`, `/robots.txt`, `/feed.xml`, `/illustrations/logo-m1.webp`
- 페이지별 og 유지: 홈만 `og:url`, `/terms/`=`Terms of Service | CrawlCred`, charlotte=도시 제목 → **F8 회귀 없음**
- GA 태그 1건, GSC 태그 1건, sitemap 39 URL, `/health` 200, `/api/stats` = 67·7·77·41
- 서버 앱 HEAD == 로컬 HEAD (`405abd3`)

**남은 것**: GitHub 연결 여부(O3)는 미결. 연결 시 **서버 `origin`을 GitHub로 전환**하지 않으면 정적 웹만 갱신되고 API가 영구 정체된다(§3.2).

## 3.7 A2-a 완료 기록 (로컬 결제·마켓플레이스 회귀)

**결과: 78/78 통과** — `scripts/e2e-billing.mjs` 18/18 + `scripts/e2e-marketplace.mjs` 60/60. 로컬 API(3200) + 로컬 PG17(5544, 데모 DB)에서 실행 — 프로덕션 무관.

- 결제 경로 전량 통과: 커스텀데이터→슬롯 활성화, 재전달 idempotency, 갱신 연장, **슬롯 희소성(3칸 채운 뒤 4번째는 held)**, 미지 업체·커스텀데이터 결측→held, 취소(기간 만료까지 유지), 만료, 환불(subid 직접 + 커스텀데이터 폴백), 잘못된 서명→401
- 마켓플레이스 전량 통과: 인증·오퍼·채팅(멤버십 격리·unread·커서·4000자 제한)·주문(전이 규칙·상대 이메일 비노출·터미널 상태)
- **함정 기록**: 첫 실행이 18건 전부 실패했다. 원인은 코드가 아니라 **2일간 떠 있던 로컬 dev API가 옛 `.env`의 웹훅 시크릿으로 기동돼 있었던 것** — 서명 검증만 통과 못 해 전 케이스가 401. 시크릿을 명시(`LEMONSQUEEZY_WEBHOOK_SECRET=test-secret`)해 API를 재기동하니 전량 통과. `node --env-file`은 **셸 환경변수가 파일 값을 이긴다**(확인함) → 재현 시 이 방식 사용.
- **부작용**: e2e가 로컬 dev DB의 데모 업체 4곳을 정리했다(현재 e2e 업체 2곳). 로컬 전용이며 `npm run seed:demo`로 복구 가능 — 미복구 상태.

## 3.8 B3 도구화 — `/api/admin/contractors.csv` 신설 (커밋 `e744413`, 프로덕션 배포 완료)

§3.4의 "주소가 있는지조차 확인할 수 없다"를 구조적으로 해소했다. 매번 psql을 열지 않고 **한 번의 요청으로 실측 + 타깃 리스트를 동시에** 얻는다.

- **컬럼**: slug, name, city, state, zip, phone, website, email, **has_email**, claimed, license_status/detail/checked_at/source_url, insurance_status, cert_status, name_match_status, featured_active, google_rating, google_reviews, profile_url
- **정렬**: license-verified 우선 → has_email 우선 → 주/도시/이름. **파일 맨 위가 곧 1차 발송 배치**가 되게 설계
- 검증: 로컬에서 무토큰 401 / 오토큰 401 / 정토큰 200, kind별 최신 검증행(lateral) 반영 확인. 타입체크·루트 테스트 31 그린
- **프로덕션 배포 완료**: `git push server main` → 서버 체크아웃 정렬 → `docker compose build app migrate` → `migrate`("migrations up to date", 신규 없음) → `up -d app` → `/health` 통과(2회 재시도) → app **healthy**, 서버 HEAD == 로컬 HEAD (`e744413`). 기존 라우트 회귀 없음(`/api/stats` 67·7·77·41, `/api/contractors` 200, `/api/admin/leads.csv` 401)

**오너가 지금 실행할 한 줄 (B3-0 — 이번 주 계획을 결정하는 숫자)**

```bash
curl -H "x-admin-token: $ADMIN_TOKEN" https://crawlcred.com/api/admin/contractors.csv -o contractors.csv
python -c "
import csv
r=list(csv.DictReader(open('contractors.csv',encoding='utf-8-sig')))
print('total', len(r),
      '| with_email', sum(1 for x in r if x['has_email']=='true'),
      '| license_verified+email', sum(1 for x in r if x['has_email']=='true' and x['license_status']=='verified'))"
```

> `grep -c ',true,'`로 세지 말 것 — `has_email,claimed`가 인접 컬럼이라 `,false,true,`(주소 없는데 클레임된 업체)도 함께 잡혀 **과대계상**된다. 위 CSV 파서 방식으로 세야 정확하고, `license_detail`에 쉼표가 들어 있어도 안전하다. (합성 데이터로 검증: grep 3 vs 실제 2)

- **has_email이 40 이상**이면 B3는 원안대로 "리스트 정리"이고 7/29 1차 발송은 일정대로 간다
- **10 미만**이면 B3의 성격이 **"주소 수집"**으로 바뀐다 → verified 7곳 먼저 수동 수집(30분) → 7/29는 **7통으로 진행**, 나머지는 8/1까지 분할. KPI의 "49통"은 수집 가능 모수로 정정할 것 (거짓 목표 금지)
- 어느 쪽이든 이 CSV가 `checkout_url` 컬럼만 붙이면 바로 발송 워크시트가 된다 (F2)
- CSV의 `profile_url`은 **G2 완료로 이미 정식 색인 URL**(`/pro/<slug>/`)이 되었다 — 콜드메일 링크를 그대로 쓸 수 있다

## 3.9 G2 완료 기록 — 업체별 색인 페이지 (커밋 `b394782`, 프로덕션 배포 완료)

**문제**: 업체 프로필이 `/pro/?s=<slug>` 쿼리 페이지 하나뿐이고 `robots:noindex` + sitemap 미포함이었다. 이미 면허를 대조해 둔 67곳이 **계약 직전 검색어**("업체명 license", "is 업체명 legit")에 대해 페이지 0개였고, 콜드메일 1통의 "your verified profile is live" 링크가 **검색엔진에 무시하라고 지시된 URL**을 가리키고 있었다.

**만든 것**
- **`/pro/[slug]/` 67페이지 프리렌더** — 빌드 타임에 API에서 실데이터를 받아 검증 리포트(상태·detail 원문·확인일·출처 링크)를 **HTML에 실어** 생성. 기존 `ProProfile`을 새로 만들지 않고 데이터 주입(prop)만 추가 — 마크업·문구는 단일 소스 유지
- **하이브리드 신선도**: 정적 HTML로 색인되면서, 마운트 시 클라이언트가 재조회해 최신 검증을 보여준다. 재조회 실패 시 프리렌더 사본을 유지(좋은 페이지를 에러로 덮지 않음)
- **`ProfileIndex` — 도시/주 페이지에 서버 렌더 링크 목록.** 기존 리스팅 카드는 클라이언트 렌더라 **크롤러가 볼 내부링크가 0**이었다 → sitemap만 있는 고립 페이지가 될 뻔했다. 지금 Charlotte 7개, NC 48개 링크가 정적 HTML에 존재하며, 도시별 실인벤토리 문장("N listings … M dated checks")이 추가 콘텐츠가 됨
- **빌드 안전장치**: API 불통 또는 업체 10곳 미만이면 **빌드 실패**. 빈 디렉토리를 배포하면 그 빈 상태가 색인되고 다음 정상 배포에서 전 URL이 404가 된다. 주별 조회로 목록 API의 200건 상한도 회피
- **샘플 리스팅 차단**: `demo-*`는 페이지는 유지(디렉토리 링크가 살아 있어야 함)하되 **noindex + sitemap·링크목록 제외**. 가짜 업체 프로필 색인은 "Evidence, not scare tactics" 포지셔닝과 정면 충돌
- **구조화 데이터 분리**: 업체는 `LocalBusiness`, 우리 리포트는 `WebPage`(publisher=CrawlCred) 2노드 → 리치 결과가 우리 검증 주장을 업체 것으로 귀속시키지 않는다. `aggregateRating`은 실제 리뷰가 있을 때만
- `profilePath()` 헬퍼로 컴포넌트 16곳의 하드코딩 URL 통일

**검증 (라이브 기준 전부 통과)**
- 프로필 200, 실업체 `index, follow` + 페이지별 canonical·og / demo `noindex` / 쿼리 페이지 `noindex`
- **sitemap 39 → 105 URL** (실업체 66개 추가, demo 0건), 각 항목 `lastModified`는 해당 업체 **최신 확인일**(빌드 시각 아님)
- 정적 내부링크 Charlotte 7 / NC 48, 검증 리포트 본문 정적 포함 확인
- 타입체크·lint·루트 테스트 31 그린, 백엔드 무변경이라 도커 재빌드 없이 정적 배포만, 서버 HEAD == 로컬 HEAD

**오너 후속 (5분, 색인 가속)**: GSC에서 **sitemap 재제출** + 대표 URL 3개 색인 요청 — 예: `/pro/moisture-loc-charlotte/`, `/pro/a-healthy-home-chapel-hill/`, `/north-carolina/`. 7/27 KPI 리뷰에 "색인 페이지 수" 베이스라인을 남기면 66페이지 투입 효과를 4주 후 측정할 수 있다.

---

# Part 4. 누락·기능성 사후 검토 (2026-07-26 저녁)

당일 작업물을 배포 후 다시 감사했다. 발견 4건 중 3건은 수정·재배포 완료, 1건은 지시 문서의 오류였다.

## 4.1 수정 완료 (커밋 `a3fe05c`, 배포됨)

| # | 발견 | 심각도 | 조치 |
|---|---|---|---|
| H1 | **검증 날짜가 빌드 머신 타임존에 묶여 있었다.** 프리렌더 전에는 전부 클라이언트 렌더라 드러나지 않던 문제 — 정적 페이지가 되면서 KST 빌드 기준으로 날짜가 굳었다. `2026-07-24T02:58Z` 체크가 **구글이 색인하는 HTML에는 "July 24"**, **샬럿 고객 브라우저에서는 하이드레이션 후 "July 23"** 로 표시된다(실측: `America/New_York` → July 23). "확인 날짜"가 전부인 제품에서 같은 체크에 두 개의 답이 나오고, React 하이드레이션 불일치도 동반 | **높음 — 브랜드 핵심 주장 훼손** | `web/lib/dates.ts` 신설, 모든 as-of 날짜를 **UTC 고정** 포맷으로 통일 (ProProfile·RatingLine·ReviewsSection·프로필 메타데이터). 타임존 5종에서 동일 출력 확인 |
| H2 | **sitemap이 `robots:noindex`인 `/pro/`를 포함**하고 있었다 — 무시하라고 지시한 페이지를 크롤하라고 요청하는 모순 (G2 이전부터 존재, 프로필 페이지가 생기며 명확해짐) | 중간 | sitemap에서 `/pro/` 제거. 105 → **104 URL** (실업체 66 + 코어/가이드 38) |
| H3 | **수동 배포 절차에 `_next/static` 정리 단계가 빠져 있었다.** 2단계 rsync는 그 디렉토리를 의도적으로 `--delete` 대상에서 제외하므로, 정리 없이는 옛 청크 세대가 무한 누적된다 (`deploy.yml`에는 있던 단계) | 낮음 | `CLAUDE.md` 배포 절차에 30일 경과 청크 prune + nginx conf 동기화 조건 추가. 배포 시 실행 확인(667 → 667, 30일 경과분 없음) |
| H4 | **llms.txt에 업체별 리포트가 없었다** — GEO 타깃 페이지 66개가 AI 답변 엔진 안내서에서 누락 | 낮음 | "Per-contractor verification reports" 섹션 추가. **"NC에서는 면허 부재가 합법일 수 있다"** 주의를 함께 명시 — 답변 엔진이 공백을 위법으로 읽지 않게 |

## 4.2 지시 문서 오류 정정

- **§3.8의 카운트 명령이 틀렸다.** `grep -c ',true,'`는 `has_email,claimed`가 인접 컬럼이라 `,false,true,`도 잡아 과대계상한다(합성 검증: grep 3 vs 실제 2). CSV 파서 기반 명령으로 교체 완료.

## 4.3 검토했고 문제 없던 항목

| 항목 | 확인 결과 |
|---|---|
| `robots.txt` | `Allow: /` (AI 크롤러 14종 명시 포함) — 신규 페이지를 막는 규칙 없음 |
| `trailingSlash` | `next.config`에 `true` — `profilePath()`의 `/pro/<slug>/`와 일치 |
| 로그인 리다이렉트 | `AccountPanel.safeNext()`가 동일 출처 경로 전체 허용 → `?next=/pro/<slug>/` 정상 |
| 업체 주 분포 | NC 49 + SC 18 = 67, **전부 `STATES` 5개 안** → 빌드에서 누락되는 업체 없음 |
| 미지 slug | `/pro/does-not-exist/` → **404** (정상) |
| 쿼리 폴백 | `ProProfileQuery` 번들 포함 확인 — 구 링크·신규 리스팅 경로 살아 있음 |
| 프로필 내부링크 | cost·directory·guides 9개·methodology(푸터)·account 등 정적 HTML에 존재 |
| 백엔드 영향 | G2·수정 커밋 모두 `src` 무변경 → 도커 재빌드 없이 정적 배포만, `/health` 200 |

## 4.4 남은 미결 (의도적으로 하지 않은 것)

- **F11 `logo-m*` 이미지 7개** — 코드 참조 0건, 라이브에는 존재. 오너 결정 대기로 **커밋하지 않음**(작업 중 `git add -A`로 두 번 섞여 들어가 두 번 제거했다. 결정 전까지 미추적 유지)
- **로컬 dev DB** — A2-a e2e가 데모 업체 4곳을 정리한 상태. 로컬 전용, `npm run seed:demo`로 복구 가능
- **검증 리포트 안의 methodology 링크** — 스펙에 적었던 "무엇을 확인/미확인" 블록의 명시 링크는 미추가(푸터 링크로 대체 가능). 다음 콘텐츠 작업에 묶으면 됨
- **도시 페이지 31개 확장** — G2 이후 "업체 3곳 이상" 기준으로 재검토 예정

---

# Part 5. "어떤 곳은 나오고 어떤 곳은 안 나온다" — 노출 누락 버그 (2026-07-26 밤)

오너 제보로 조사. **원인 2개가 독립적으로 겹쳐 있었고, 둘 다 조용히 실패**했다.

## 5.1 원인 ① 도시 페이지가 zip으로만 조회 — 실업체 66곳 중 20곳이 zip 없음

스크랩 출처가 zip을 잘 안 주기 때문에 **30%가 `zip = null`**이다. 도시 페이지는 zip 프리픽스로만 조회했으므로 그 20곳은 **자기 도시 페이지에도 나올 수 없었다.**

| 도시 | 수정 전 | 수정 후 | 숨어 있던 업체 |
|---|---|---|---|
| Charlotte | 5 | **8** | Economy Exterminators, Green Frog Waterproofing, Mr. Crawl Space |
| Greenville | 1 | **3** | Crawlspaces and More, Encapsulation Professionals |
| **Columbia** | **0** | **1** | VAPOR-X Crawl Spaces |
| Raleigh / Durham / Greensboro / Wilmington / Charleston | 각 −1 | | Carolina Casa, East Coast Crawl Space Solutions, Greensboro Crawl Space Guys, Elite Moisture Solutions, Charleston Crawlspaces |

**Columbia는 "Verification for this area is in progress"를 띄우면서, 같은 페이지 두 섹션 아래(오늘 넣은 정적 목록)에는 검증된 업체가 있었다** — 사이트가 자기 자신과 모순된 상태.

**수정**: `city`와 `zip`이 함께 오면 **OR**로 매칭. 프리픽스는 옆 동네 업체를 끌어오는 역할을 유지하되, zip이 없으면 **도달 범위만 줄고 리스팅이 사라지지는 않는다.** ZIP 검색도 해당 sectional center의 도시명을 함께 넘기므로 zip 없는 업체가 빠지지 않는다.

## 5.2 원인 ② 목록 API 기본 limit 60 — 알파벳 뒤쪽 6곳이 사이트에서 사라져 있었다

웹 클라이언트가 limit을 안 넘겨 서버 기본값 60이 적용됐다. 정렬이 `featured desc, name`이라 **알파벳 뒤쪽 실업체 6곳이 디렉토리·이름검색에서 통째로 누락**: Southeast Foundation and Crawl Space Repair, Tar Heel Basement Systems, The Crawlspace King, Triangle Crawl Space Solutions, Upstate Crawl Spaces, VAPOR-X Crawl Spaces. **아무 곳에도 "잘렸다"는 표시가 없었다.**

**수정**: 클라이언트가 limit을 명시하고, 응답에 **`total`**(limit 적용 전 매칭 수)을 추가해 절단을 감지 가능하게 했다. 빌드 타임 가드도 "정확히 200개 오면 잘린 것"이라는 추정 대신 `total` 비교로 교체.

## 5.3 회귀 방지

필터 조립을 `src/lib/contractor-filter.ts`의 순수 함수로 분리하고 **테스트 5개** 추가 (city+zip OR, city 단독, zip 단독, 무필터, LIKE 와일드카드 이스케이프). 루트 테스트 **31 → 36개 전부 통과**. 순수 함수를 `lib/`에 둔 이유: `npm test`는 DB·환경변수 없이 도는 스위트라 라우터 모듈을 임포트하면 깨진다.

## 5.4 검증 (프로덕션)

도시별 노출 수 위 표대로 회복, `limit` 미지정 시 `반환 60 / total 67`로 절단이 드러남, 웹 재빌드·배포 후 전 경로 200 · sitemap 104 · health 200.

## 5.5 남은 데이터 부채

`zip` 결측 20곳은 **여전히 결측**이다. 지금은 도시명 매칭으로 가려졌을 뿐, 정확한 ZIP 반경 검색·거리 정렬에서는 계속 불리하다. Places 키가 생기면 좌표·주소로 ZIP을 채우는 게 인벤토리 심화(확장 순서 1번)와 같은 작업에 포함된다. **추정으로 채우지 말 것** — 도시 프리픽스를 대입하는 건 데이터 조작이다.

---

# Part 6. 주장 대비 데이터 감사 — "SC는 왜 아무것도 안 나오나" (2026-07-26 밤)

오너 제보("다른 지역은 왜 2/2 VERIFIED가 안 나오며, 누락된 장소도 있음")에서 시작해 **모든 검증 행을 사이트의 주장과 대조**했다. 결과는 이 제품에서 나올 수 있는 최악의 불일치였다 — **사이트가 자기 검증을 과대 주장하고 있었다.**

## 6.1 오너 제보에 대한 답

- **SC 18곳 전부 "Verification in progress"** — 표시 버그가 아니라 **데이터 공백**이다. SC 면허 조회가 한 번도 실행되지 않아 검증 0건. (원인은 6.4에서 뒤집힘)
- **`2/2 VERIFIED`가 안 보이는 이유** — 두 검증이 모두 verified일 때만 뜬다. NC 48곳 중 면허 verified는 **7곳**뿐이고, 39곳은 `none_found`(NC $40k 기준 아래라 합법인 경우가 많다), 2곳은 `expired`. 숫자가 낮은 게 아니라 **기록이 그렇다.**
- **누락된 장소** — Part 5에서 수정 완료(zip 결측 + limit 60).

## 6.2 데이터 정합성 — 무증거 행과 모순 표시

| 발견 | 조치 |
|---|---|
| NC 48곳 중 **17곳이 license 행 2개** — 2026-07-17 시드 플레이스홀더(`unverified`, **source_url 없음**) + 실제 NCLBGC 결과. 프로필이 둘을 동등하게 렌더 → "UNVERIFIED(출처 없음)" 옆에 "NONE FOUND(보드 링크)" | **마이그레이션 0011**: 더 새로운 출처 있는 행이 존재할 때만 무출처 행 삭제 (증거 있는 행은 건드리지 않음). 프로덕션 적용, 15행 삭제 |
| 목록 쿼리가 kind별 **모든 이력**을 반환 → 소비자가 한 검증에 두 답을 받음. **오늘 넣은 FAQ 생성기가 `find(kind==='license')`로 임의의 한 행을 골라** 바로 위 리포트와 모순될 수 있었다 | API가 **kind별 최신 1행**만 반환 (`distinct on`). 이력은 테이블·`/api/verification-log`에 유지 |
| `checks` 카운터가 **행 수**를 셈 → 폐기된 행 포함, 게다가 **90일 재확인 cron이 돌 때마다 증가**해 "증거의 양"이 "우리 활동량"으로 변질 | **보유 검증 수**(리스팅×kind 1개)로 재정의 |
| 프로필 숫자에 **데모 리스팅**이 포함 (`listed: 67`) — 우리가 만들어낸 리스팅을 증거 수치에 포함 | stats에서 데모 제외 |

**결과 (라이브)**: `listed 67 → 66`, `checks 77 → 59`, `verified 7` 유지. **숫자가 내려간 것이 정정이다.**

## 6.3 카피 — "모든 리스팅에 5개 검증"은 사실이 아니었다

실제 보유: **license 49행 + name_match 11행. insurance·certification·discipline은 0행.** SC 18곳은 검증 0건. 그런데 methodology·about·for-contractors 가이드·홈·llms.txt가 모두 "모든 리스팅에 동일한 5개 검증"을 주장하고 있었다.

- **methodology**: "완전한 리포트는 5개 검증" + **날짜 붙은 "지금 실제로 돌고 있는 것" 블록** 신설 (NC 면허 O / name_match는 면허 기록 있을 때 / SC 미시작 / 보험·인증은 서류 확보 시)
- **about·가이드·홈·llms.txt**: 주별 실제 커버리지를 명시. 새 데이터 필드 `STATES.licenseCheckAutomated`로 구동 — 산문이 아니라 데이터가 진실의 출처
- **헤딩 "Verified pros" → "Crawl space pros"**: 검증 0건 리스팅 위에 걸린 헤딩이 카드가 뒷받침할 수 없는 주장을 하고 있었다. 주장은 카드의 배지가 한다
- **llms.txt**: 답변 엔진에게 "리포트에 실제로 5개가 없으면 5개 검증이라고 서술하지 말라"고 명시

## 6.4 SC — 자동화 불가 확정, 수동으로 전환 (정정 완료)

> **이 절의 1차 결론("헤드리스 불필요, 검색 POST만 반복하면 된다")은 틀렸다.** 계속 파보니 검색 폼 자체가 CAPTCHA였다. 아래 6.4-a에 최종 결론, 6.4-b에 원래 조사 기록을 남긴다.

### 6.4-a 최종 결론 (2026-07-26 밤)

- **검색 폼에 CAPTCHA가 있다** — Residential Builders 폼에 `CaptchaIncorrectLabel` 컨트롤. 넓은 검색어("Construction", "Foundation")로도 결과가 0건이었던 이유가 이것이다(포스트백은 실행되지만 검증에서 막힘).
- **우회하지 않는다.** 보드가 자동 검색을 의도적으로 막았고, WO-02 §S1에 오너가 세운 기준("우회가 과도해지면 중단하고 수동 조회")에 그대로 해당한다. **헤드리스 브라우저 계획 폐기.**
- **수동 경로는 이미 존재했다** — `data/sc-licenses.json`에 SC 18곳이 `found: null`로 시드돼 있고, `import-manual-licenses.ts`가 NC와 동일한 형태의 검증 행을 쓴다. **비어 있던 건 사람이 할 18건의 조회뿐이었다.** 그래서 새 도구를 만들지 않고(중복 방지) 기존 것의 결함만 고쳤다:

| 결함 | 조치 |
|---|---|
| `checked_at`이 `now()` — **조회한 날이 아니라 입력한 날**로 찍힘. 일주일 뒤 일괄 입력하면 as-of 날짜 18건이 전부 거짓 | 레코드별 `checkedOn` 필드. 없으면 "오늘 날짜로 찍었다"고 경고 |
| `found: true`인데 면허번호 없음 → **인용 불가능한 검증**을 게시 | 레코드 거부(해당 행만, 종료코드 1). 날짜 형식 오류도 거부 |
| **`data/`가 도커 이미지에 없어 컨테이너에서 ENOENT** — 문서화된 SC 절차가 실행 자체가 안 됐다(실제로 처음 돌렸을 때 실패) | Dockerfile에 `COPY data ./data` (40KB) |
| 검증 로직이 스크립트 안에 있어 테스트 불가 | `src/lib/sc-record.ts`로 분리 + 테스트 3개 (총 39개 통과) |

**오너 작업 (약 40분, 18곳)**: 절차는 repo `RUNBOOK_KO.md` §5-2에 단계별로 있다 — 보드는 **Residential Builders 우선, 없으면 Contractors**, Company 칸과 개인명 칸은 함께 쓰면 안 되고, `checkedOn`에 **기록을 읽은 날**을 적는다. 기입 후 `--write` → 웹 재빌드·재배포. SC는 cron 재확인 대상이 아니므로 90일마다 이 절차를 반복한다(`licenseCheckAutomated: false`가 사이트 카피에서 이 사실을 말한다).

### 6.4-b 조사 기록 (경로 자체는 살아 있다)

기획안·WO-02/03이 계속 "**verify.llronline.com WAF 403 → 헤드리스 브라우저 필요**"를 전제했다. 재조사 결과 **차단이 아니다**:

1. 문서에 적힌 URL(`LicLookup/Regs/LicLookup.aspx`)은 **302 FileNotFound** — 경로가 바뀐 것
2. 쿠키 저장 없이 요청하면 ASP.NET의 `AspxAutoDetectCookieSupport` 리다이렉트에 걸린다. **쿠키 세션을 유지하면 `LicLookup/LookupMain.aspx` → 200**
3. 보드 선택 POST(viewstate 왕복) → **`LicLookup/Contractors/Contractor.aspx?div=69` 200 도달**, 검색 폼 필드 확인: `txt_lastName`·`txt_city`·`txt_state`·`txt_licNum`·`ddl_type`·`btn_find`
4. 크롤스페이스에 해당하는 보드: **Contractors=69, Residential Builders=46**

여기까지는 사실이고 유효하다 — 포털 도달 경로와 보드 ID는 사람이 조회할 때도 그대로 쓴다. 다만 **이 지점에서 CAPTCHA에 막힌다**(6.4-a). **SC 18곳(디렉토리의 27%)이 검증 0건 상태를 벗어나는 것은 여전히 최우선이지만, 수단은 코드가 아니라 오너의 40분이다.**

---
*진행 상태의 단일 기준은 `CrawlCred-사업기획안.md`. 이 문서는 2026-07-26 실사 + 실행 가능성 검토 + 사후 감사 + 노출 누락 버그 + 주장 대비 데이터 감사 반영.*

---

# Part 7. 종일 작업 종료 기록 (2026-07-26)

커밋 **38개**, 서버 HEAD == 로컬 HEAD (`0c524d9`), 테스트 **69 통과**, 전 경로 200.

## 7.1 수치 변화

| | 아침 | 종료 시 |
|---|---|---|
| 리스팅 | 67 | **457** (NC 130 · SC 92 · TN 87 · VA 86 · GA 67) |
| 프로필 페이지 | 0 | **459** |
| 도시 페이지 | 10 | **38** |
| 가이드 | 9 | **11** |
| 씬 사진 | 7 | **19** |
| sitemap | 39 | **525 URL** |
| 면허 검증 자동화 | NC only(수동 실행) | **NC + VA, 매일 cron** |
| 보유 검증 / verified | 77건(중 17건 무증거) / 7곳 | **130건(전부 출처·날짜) / 23곳** |
| 루트 테스트 | 31 | **69** |

## 7.2 밤새 자동으로 도는 것

| 시각(UTC) | 작업 |
|---|---|
| 03:20 | NC 면허 재확인 (기한 초과분, 1회 15건) |
| 03:40 | VA 면허 재확인 (1회 6곳 — DPOR 실측 상한) |
| 04:10 | DB 백업 (14일 보관) |

상태 변경·실패가 있을 때만 `devoh@signpost.kr`로 메일이 간다. NC 잔여 33곳·VA 잔여 78곳이 며칠에 걸쳐 소화된다.
**상태 변경 메일을 받으면 웹을 재빌드·재배포해야 한다** — 프로필 페이지는 프리렌더이므로 DB만 바뀌면 색인된 HTML은 옛 상태로 남는다.

## 7.3 오너 몫 (우선순위 순)

1. **업체 이메일 27곳 채우기** — `CrawlCred-업체이메일-후보-2026-07-26.csv`의 `chosen` 칸.
   **이것 하나가 리드 자동 전달 + 자동 클레임 + 마켓플레이스를 동시에 해제한다.** 지금은 셋 다 잠겨 있다
2. **OpenAI API 키 교체** — 대화 로그에 노출됨. IP·API 제한이 걸려 있어 실질 위험은 낮지만 교체 권장
3. **GA4** 내부 트래픽 필터 + `quote_request_submitted`·`featured_checkout_opened`를 주요 이벤트로 표시 (15분).
   안 하면 7/27 KPI 베이스라인이 오너 본인 세션으로 오염된다
4. **주정부 문의 2통 발송** — `CrawlCred-주정부-데이터문의-2026-07-26.md`. GA는 로스터 구매 요청, TN은 공개기록 요청.
   성공하면 TN 87 + GA 67 = 154곳의 분기 수동 작업이 영구히 사라진다
5. **LS 라이브 심사 확인** → 승인 시 A2-b(실카드 검증)
6. **SC 18곳 / TN 87곳 수동 조회** — 4번이 실패했을 때의 경로
7. **O3 GitHub 연결 결정** — 하면 서버 `origin`도 GitHub로 전환해야 한다(안 하면 정적 웹만 갱신되고 API는 정체)

## 7.4 다음 세션 착수점

- **마켓플레이스가 휴면 상태**다. 기능은 e2e 60건으로 검증됐지만 클레임된 리스팅이 0곳이라 오퍼를 만들 주체가 없다. 원인은 7.3의 1번과 같다
- GA/TN/VA 242곳이 `CHECKS PENDING` — VA는 cron이 소화 중, GA/TN은 7.3의 4번에 달려 있다
- 평점 미확보 25곳 (Places가 확신 매칭 실패 — 추측으로 채우지 않았다)
- 도시 페이지 28개는 데이터 기반이고 **지역 고유 서술이 비어 있다**. 오너가 아는 지역 정보를 주면 연구된 10개 수준으로 승급 가능
