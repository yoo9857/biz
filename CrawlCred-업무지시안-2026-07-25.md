# CrawlCred 업무지시안 — WO-02 (Phase 2 진입)

| 항목 | 내용 |
|---|---|
| 문서 번호 | WO-2026-0725-02 |
| 발행일 | 2026-07-24 (금) |
| 대상 기간 | 2026-07-25 (토) ~ 2026-08-09 (일) — 2주 |
| 발행 | 기획/마케팅 (marketer) → 오너·개발 |
| 근거 문서 | `CrawlCred-사업기획안.md` §3 / 완료보고서 2026-07-24 (WO-01 개발 항목 전량 조기 완료 확인) |
| 상태 | **정정 1차 (2026-07-24 저녁) — 아래 정정표가 우선** |

## 정정표 (2026-07-24 저녁, 레포 실사 기준)

| # | 태스크 | 정정 후 상태 |
|---|---|---|
| U1 | hello@ 수신 복구 | ✅ 완료 (오너, 7/24) |
| U2 | GA4 | ✅ 완료 — G-0KT5CD4NCD 직접 연결 + GTM(GTM-T9S75TGD) 설치 (커밋 5085d02) |
| U3 | LS Tier1/State UUID | ⏳ 미완 (featured.ts에 TODO 잔존) — **WO-03으로 이관, 우선순위 상향** |
| U4 | Resend sending-only 키 | ⏳ 미완 — 유지 |
| U5 | Google Places 키 | ⏳ 미완 — 유지 |
| U6 | UptimeRobot | ❌ **취소** (오너 결정 7/24 — 리스크 인지 하에 제외) |
| C1/C3 | Charlotte/Raleigh 비용 가이드 | 🔄 **대체 완료** — 도시 페이지에 지역화 비용표+FAQ LD 탑재(커밋 27bab63)로 목적 달성. 별도 가이드는 중복이라 취소, 신규 주제로 교체(WO-03) |
| C2 | NC 검증 방법 가이드 | ✅ 기존재 — `verify-contractor-license` 가이드 (강화 키트 적용됨) |
| C4 | Vapor barrier vs encapsulation | ✅ 기존재 — `encapsulation-vs-vapor-barrier` 가이드 |
| R1 | 주간 KPI 리뷰 | 유지 (7/27 첫 기록) |
| S1 | SC 스크래퍼 | 유지 (8월, P2) |
| S2 | 실결제 커스텀데이터 e2e | ⏳ 미완 — **WO-03으로 이관, P0 승격** (판매 전 필수 관문) |

> **WO-02는 이 정정으로 사실상 종결.** 잔여 항목(U3·U4·U5·S1·S2·콘텐츠 신규 주제)은 WO-03(수익 스프린트)으로 이관·재우선순위화. |

---

# Part 1. 제안서 (이번 2주의 초점)

## 1.1 상황 판단

WO-01의 개발 항목(히어로 숫자·자가진단 연결·인라인 카드·stats 엄격화·가이드 키트)이 계획 대비 5일 조기 완료됐다. 이제 코드가 병목이 아니다. 남은 병목은 정확히 두 가지:

1. **오너만 할 수 있는 외부 연결 6건** — DNS·GA4·LS 상품·API 키·모니터. 전부 합쳐 반나절 분량인데, 이 중 GA4가 없으면 **KPI 대시보드가 영원히 빈 표**로 남는다. 측정 없는 실행은 실행이 아니다.
2. **콘텐츠 0편/주.** 사이트는 완성됐지만 유입 엔진(주 2편 루틴)이 아직 첫 발도 안 뗐다. 지금부터 발행하는 글이 8~10월 트래픽을 결정한다 — SEO는 심은 날과 수확하는 날이 다르다.

## 1.2 이번 2주의 원칙

- **개발 신규 기능 동결.** 완료보고서 §5의 잔여 항목과 콘텐츠 외에 새 기능을 만들지 않는다. (원칙: 병목 아닌 곳의 개선은 낭비)
- **콘텐츠는 "우리만 쓸 수 있는 글"만.** 아무나 쓰는 일반론("crawl space가 뭔가요")이 아니라, 우리 검증 데이터(49곳 대조, 면허 기준 $40k, expired 2곳)를 인용해 경쟁자가 복제 못 하는 글을 쓴다. (원칙 14 구체적 숫자, 18 권위)
- **도시 비용 가이드는 템플릿 복제 금지.** 도시명만 갈아끼운 얇은 글은 구글이 걸러낸다. 도시별로 실제 다른 데이터(해당 지역 업체 수·검증 현황·기후 특성)를 반드시 포함.

---

# Part 2. 업무지시안

## 2.0 공통 완료 기준

- 루트 `npm test` + `cd web && npm run build` 그린 (빌드 전 dev 종료), 변경 동작 로컬 확인
- 카피 원칙: 공포 마케팅·guarantee 표현 금지, 검증 표기에 날짜 유지, "verified"는 엄격화 기준(7곳)과 일치하게만 사용
- 완료 시 `CrawlCred-사업기획안.md` §3 체크 + 커밋 메시지 `[WO2-번호]` 표기

## 2.1 태스크 총괄

| # | 태스크 | 담당 | 기한 | 소요 | 우선순위 |
|---|---|---|---|---|---|
| U1 | hello@ 수신 복구 (forwardemail MX+TXT) | 오너 | 7/25 (토) | 20분 | P0 |
| U2 | GA4 생성 → ID 주입·재배포 | 오너→개발 | 7/27 (월) | 1h | **P0 최우선** |
| U3 | LS Tier1($149)/State($199) 상품 생성 → UUID 연동 | 오너→개발 | 7/28 (화) | 1h | P1 |
| U4 | Resend sending-only 키 교체 + 구키 폐기 | 오너→개발 | 7/28 (화) | 30분 | P1 |
| U5 | Google Places 키 발급 → 평점 66개 일괄 반영 | 오너→개발 | 7/29 (수) | 1h | P1 |
| U6 | UptimeRobot 무료 모니터 2개 등록 | 오너 | 7/25 (토) | 15분 | P1 |
| C1 | 콘텐츠 #1: Charlotte 비용 가이드 | 개발/에이전트 | 7/28 (화) | 2h | P0 |
| C2 | 콘텐츠 #2: NC 업체 검증 방법 가이드 | 개발/에이전트 | 7/30 (목) | 2h | P0 |
| C3 | 콘텐츠 #3: Raleigh 비용 가이드 | 개발/에이전트 | 8/4 (화) | 2h | P0 |
| C4 | 콘텐츠 #4: Vapor barrier vs. encapsulation | 개발/에이전트 | 8/6 (목) | 2h | P0 |
| R1 | 주간 KPI 리뷰 #1·#2 (GSC 색인 첫 기록) | 오너 | 7/27, 8/3 (월) | 각 30분 | P0 |
| S1 | SC 면허 헤드리스 스크래퍼 + SC 업체 시딩 | 개발 | 8/9 (일) | 4–6h | P2 |
| S2 | 실 LS 체크아웃 커스텀데이터→슬롯 활성화 최종 확인 | 개발 | U3 후 8/9까지 | 1h | P2 |

## 2.2 태스크 상세

### U2 — GA4 (최우선. 이거 없으면 KPI 표가 계속 빈 칸)
- (오너) analytics.google.com에서 GA4 속성 생성(crawlcred.com) → 측정 ID(G-XXXX) 전달
- (개발) 빌드 환경 `NEXT_PUBLIC_GA_ID` 주입(Analytics.tsx 훅 기구축) → 재배포 → 실시간 보고서에서 본인 방문 확인
- **완료 기준**: GA4 실시간에 방문 잡힘. 7/27 KPI 리뷰부터 "주간 유기 방문" 기록 시작.

### U1·U6 — 인프라 연결 (토요일 35분 묶음 처리)
- U1: Linode DNS에 MX `mx1/mx2.forwardemail.net` + TXT `forward-email=hello:devoh@signpost.kr` → 외부 메일로 수신 테스트
- U6: UptimeRobot 무료 — `https://crawlcred.com` + API 헬스 엔드포인트(실경로 확인) 5분 간격, 알림 devoh@signpost.kr

### U3·U4·U5 — 수익화·신뢰 배관 (화·수)
- U3: LS 대시보드에서 Tier1 $149/State $199 생성(커스텀데이터 필드 Tier2와 동일 구성) → buy UUID를 `lib/featured.ts`에 입력, "설정 중" 제거 → 3개 티어 test 체크아웃 열림 확인
- U4: Resend sending-only 키 발급→서버 .env 교체→로그인 코드 실발송 1회 확인→**구키 폐기는 발송 확인 후에만**
- U5: Google Places API 키 발급(무료 한도 내, 66건 1회 배치) → `GOOGLE_PLACES_API_KEY` 설정 → import 스크립트 실행 → 프로필에 구글 평점(출처+확인일 표기) 노출 확인

### C1~C4 — 콘텐츠 4편 (유일한 성장 엔진, 화·목 고정)

공통 스펙:
- 가이드 강화 키트(GuideChrome: 스티키 TOC·Key Takeaways·FAQ LD·출처·편집자) 적용
- 내부 링크 필수 3개+: 해당 도시 페이지 ↔ 디렉토리 필터 ↔ quote-check
- CTA 1개만: "Get quotes from verified pros in {city}" 또는 quote-check
- 우리 데이터 인용 최소 1곳(예: "Of 49 NC contractors we checked against the state licensing board…")
- 비용 수치는 외부 출처 표기 + as-of 날짜. 단정 대신 범위.

| # | 제목(안) | 타깃 검색어 | 차별화 포인트 |
|---|---|---|---|
| C1 | Charlotte Crawl Space Encapsulation Cost (2026) | "charlotte crawl space encapsulation cost" | 샬럿 지역 업체 수·검증 현황 + NC $40k 면허 기준 설명 |
| C2 | How to Verify a Crawl Space Contractor in North Carolina | "verify crawl space contractor license nc" | NCLBGC 조회 절차 스크린샷급 상세 + 우리가 49곳 돌린 실결과(expired 2곳 존재) |
| C3 | Raleigh Crawl Space Encapsulation Cost (2026) | "raleigh crawl space encapsulation cost" | C1 구조 재사용하되 롤리 데이터로 차별화 (복제 금지 원칙) |
| C4 | Vapor Barrier vs. Encapsulation: Which Does Your Crawl Space Need? | "vapor barrier vs encapsulation" | 판단 기준표 + "둘 다 필요 없을 수도 있다" 정직 포지션 (경쟁사가 못 쓰는 글) |

- **완료 기준**: 발행 후 URL이 sitemap에 포함, GSC 색인 요청 1회.

### R1 — 주간 KPI 리뷰 (월요일 루틴 개시)
- §4.1 표에 기록: GSC 색인 수·노출·클릭 / GA4 주간 방문(U2 후) / 리드 건수 / 콘텐츠 누적 / 검증 업체 수
- 7/27 첫 기록이 베이스라인. 기록 후 다음 주 우선순위 1개 결정 + 사업기획안 "최종 갱신" 날짜 갱신

### S1 — SC 진출 (8월 1주차, WAF 이슈)
- verify.llronline.com이 스크립트 요청에 403(WAF) → Playwright 등 헤드리스 브라우저로 전환
- **준수 사항**: 공개 면허 조회 목적의 정상 열람 자동화다 — 요청 간격 스로틀(NC 스크립트의 쿨다운 방식 유지), 대량·반복 부하 금지, robots/이용약관 확인 후 진행. 우회가 과도해진다 판단되면 중단하고 수동 조회(18곳이면 수동도 2~3h)로 전환.
- SC 업체 18곳 시딩 + 면허 대조 → source_url+checked_at 필수 → /api/stats·홈 숫자 라인 자동 반영 확인
- **완료 기준**: SC 페이지에 검증 체크 노출, dry-run 검수 후 --write 1회.

### S2 — 실결제 배관 최종 확인 (U3 이후)
- test 모드에서 대시보드 "Get featured" → 커스텀데이터(slug+market) 실린 체크아웃 → 웹훅 → 슬롯 활성화 → 디렉토리 Featured 노출까지 실증 1회, 로그 보존
- **완료 기준**: 전 구간 통과. 실패 시 수정 후 재실증. (Phase 3 개시 전 필수 관문)

## 2.3 이번 지시에서 명시적으로 하지 않는 것

- Featured 콜드영업, 마켓플레이스 프로모션 (트리거: 월 방문 1,000)
- 신규 기능 개발 (사진 포트폴리오·영업시간·메시지 차단 등 — 백로그 유지)
- 애드센스 신청 (색인 안착 후)

## 2.4 보고 체계

- 완료 시: 사업기획안 §3 체크 + 커밋에 `[WO2-번호]`
- 8/9 (일) 기간 종료 시 **완료보고서 제출** → marketer 검토 → WO-03 발행 (Phase 2 루틴 지속 + KPI 첫 추세 반영)
- 지시와 실제 제약 충돌 시 임의 변경 금지, 본 문서에 메모 후 조율

---
*진행 상태의 단일 기준은 `CrawlCred-사업기획안.md`. 이 문서는 발행 시점 스냅샷.*
