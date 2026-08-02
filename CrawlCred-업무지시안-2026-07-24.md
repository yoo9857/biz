# CrawlCred 개발 제안서 & 업무지시안

| 항목 | 내용 |
|---|---|
| 문서 번호 | WO-2026-0724-01 |
| 발행일 | 2026-07-24 (금) |
| 발행 | 기획/마케팅 (marketer) → 개발 |
| 근거 문서 | `CrawlCred-사업기획안.md` §3 일정 / marketer 사이트 평가 리포트 (2026-07-24) |
| 상태 | **발행됨 — 착수 대기** |

---

# Part 1. 제안서 (왜 이 작업들인가)

## 1.1 검토 배경

2026-07-24 marketer 종합 평가 결과 **7.0/10**. 제품 완성도(8.5)에 비해 사업 성과를 막는 병목은 코드 품질이 아니라 다음 세 가지다:

1. **가진 무기를 안 보여준다.** NC 49곳 전수 대조·검증 체크 77개라는 실증 데이터가 DB에만 있고 홈·도시 페이지 첫 화면에 없다. 방문자는 5초 안에 "빈 사이트인지"를 판단한다. (원칙 12 사회적 증거, 14 구체적 숫자, 15 첫 3초)
2. **첫 방문자가 새는 구멍이 열려 있다.** 도시 페이지에 업체 카드가 인라인 노출되지 않아 "업체가 없어 보이는" 문제, 의미 불명 CTA("Launch coverage"), CTA 과다. (원칙 6 단순함, 7 선택지 축소)
3. **수익화 배관이 반만 연결돼 있다.** 결제는 Tier2만 연동, 커스텀데이터→슬롯 활성화 미검증, hello@ 수신 불가, GA4 부재로 성과 측정 불가. 트래픽이 도달했을 때 돈이 흐를 배관을 지금 완성해둬야 한다.

## 1.2 기대 효과

- **활성화(Activation) 개선**: 히어로 숫자 라인 + 도시 페이지 인라인 카드 → 첫 방문 이탈률 감소, 홈→디렉토리 클릭률 상승 (GA4 도입 후 측정)
- **측정 가능성 확보**: GA4 + GSC로 §4.1 KPI 대시보드가 실제로 돌아가기 시작
- **수익화 준비 완료**: Phase 3 트리거(월 방문 1,000) 도달 즉시 영업 개시 가능한 상태
- **신뢰 자산 보호**: hello@ 수신 복구(사이트·Organization LD에 노출 중인 주소가 불통인 상태는 "Evidence" 포지셔닝과 정면 모순)

## 1.3 하지 않기로 한 것 (명시적 제외)

- Featured 콜드영업용 기능, 마켓플레이스 추가 기능(사진 포트폴리오·영업시간·메시지 차단) — 수요 발생 전까지 백로그 유지
- 유료 도구·광고 도입 — 무예산 원칙
- 대규모 리팩토링 — 전환·측정·배관에 직결되지 않는 코드 정리는 이번 지시 범위 밖

---

# Part 2. 업무지시안 (무엇을, 어떻게, 언제까지)

## 2.0 공통 완료 기준 (모든 태스크에 적용)

- 루트 `npm test` 그린, web 변경 시 `cd web && npm run build` 그린 (빌드 전 dev 서버 종료)
- 변경 동작 로컬에서 1회 이상 실제 확인
- 카피 변경은 브랜드 원칙 준수: 공포 마케팅 금지, 보증성 표현(guarantee) 금지, 검증 표기엔 날짜 유지
- 완료 시 `CrawlCred-사업기획안.md` §3 체크박스 갱신

## 2.1 태스크 목록 (우선순위순)

| # | 태스크 | 담당 | 기한 | 소요 | 우선순위 |
|---|---|---|---|---|---|
| T1 | hello@ 수신 복구 (DNS) | 오너 | 7/24 (금) | 20분 | P0 |
| T2 | 홈 히어로 개편 (숫자 라인 + CTA 정리) | 개발 | 7/25 (토) | 1–2h | P0 |
| T3 | 자가진단 CTA 개선 + 견적 연결 | 개발 | 7/26 (일) | 1–2h | P0 |
| T4 | GA4 생성 + 주입 | 오너+개발 | 7/27 (월) | 1h | P1 |
| T5 | 도시/주 페이지 인라인 업체 카드 | 개발 | 7/29 (수) | 4–8h | P1 |
| T6 | 홈 Featured 가격 앵커 + LS UUID 완성 | 오너+개발 | 7/31 (금) | 2h | P1 |
| T7 | 결제→슬롯 활성화 e2e 검증 | 개발 | 8/2 (일) | 2h | P2 |
| T8 | Resend sending-only 키 교체 | 오너+개발 | 8/2 (일) | 30분 | P2 |
| T9 | 외부 업타임 모니터 (무료) | 오너 | 8/2 (일) | 15분 | P2 |
| T10 | 서비스 목록 중복 정의 단일화 | 개발 | 8월 중 | 1–2h | P3 |
| T11 | SC 면허 대조 스크립트 | 개발 | 8월 중 | 4h | P3 |

## 2.2 태스크 상세

### T1 — hello@crawlcred.com 수신 복구 `P0 · 오너 · 7/24`
- **문제**: apex MX 레코드 없음. contact 페이지와 Organization JSON-LD에 노출 중인 주소가 수신 불가.
- **지시**: Linode DNS Manager에 forwardemail.net 무료 플랜 설정 — MX `mx1.forwardemail.net`, `mx2.forwardemail.net` + TXT `forward-email=hello:devoh@signpost.kr`
- **완료 기준**: 외부 메일함에서 hello@crawlcred.com 발송 → devoh@signpost.kr 수신 확인.

### T2 — 홈 히어로 개편 `P0 · 개발 · 7/25`
- **근거**: 원칙 12·14·15. 히어로에 사회적 증거 숫자 0개.
- **지시**:
  1. 헤드라인 유지: "Hire a pro with a clean record."
  2. 서브헤드 1문장으로 축소: "We check every contractor's license, insurance and IICRC cert against public records — and show you the date. No scare tactics, no pay-to-rank."
  3. 숫자 라인 추가 (✓ 스타일, 모노 칩): "✓ 49 North Carolina contractors checked against the state board · 77 dated record checks · re-checked every 90 days" — **하드코딩 금지, /api/stats에서 동적으로** (SC 추가 시 자동 반영). 라벨은 "verified"가 아니라 "checked" 사용(stats의 verified 집계는 관대함 — 과장 금지).
  4. 화면 첫 뷰포트의 지배 CTA는 ZIP/도시 검색(HeroSearch) 하나만. "For contractors"는 하단 한 줄 배너로 강등.
- **완료 기준**: 첫 뷰포트에 CTA 1개 + 실데이터 숫자 라인 렌더. 숫자는 API 값과 일치.

### T3 — 자가진단 CTA 개선 `P0 · 개발 · 7/26`
- **근거**: 원칙 6·3. "Launch coverage"는 의미 전달 실패.
- **지시**:
  1. CTA 문구 → "Check my crawl space (60 sec) →"
  2. 진단 결과 화면 하단에 다음 단계 연결: "Get quotes from verified pros near you" → 견적요청(바텀시트) 또는 해당 지역 디렉토리로.
- **완료 기준**: 진단 완료 → 견적요청까지 클릭 2회 이내.

### T4 — GA4 생성 + 주입 `P1 · 오너+개발 · 7/27`
- **지시**: 오너가 GA4 속성 생성 → 측정 ID를 서버 빌드 환경 `NEXT_PUBLIC_GA_ID`에 주입(훅은 Analytics.tsx로 이미 구축됨) → 재배포.
- **완료 기준**: 라이브 사이트에서 GA4 실시간 보고서에 방문 잡힘. 이후 §4.1 KPI 표의 "주간 유기 방문" 기록 가능.

### T5 — 도시/주 페이지 인라인 업체 카드 `P1 · 개발 · 7/29`
- **근거**: 원칙 12. 도시 페이지가 "비어 보임" → 첫 방문 이탈. 평가 리포트 미비 B.
- **지시**:
  1. 각 도시/주 페이지에 해당 지역 업체 카드 3개 인라인 노출 (검증 체크 수 상위순, 기존 디렉토리 카드 컴포넌트 재사용).
  2. 헤더: "3 of N pros serving {city} — each checked against the NC Licensing Board, dated."
  3. 하단 링크: "See all N pros in {city} →" (해당 필터 걸린 디렉토리로).
  4. 정적 export 제약 준수 — 빌드 타임 fetch(generateStaticParams 경로) 또는 클라이언트 fetch 중 기존 패턴 따를 것.
- **완료 기준**: 전 도시/주 페이지 일괄 적용, 업체 0곳인 도시는 "인근 지역 업체" 폴백 노출, 빌드 그린.

### T6 — Featured 가격 앵커 + LS 상품 완성 `P1 · 오너+개발 · 7/31`
- **근거**: 원칙 13. 홈/for-contractors에 가격이 앵커 없이 노출. 평가 리포트 미비 D.
- **지시**:
  1. (개발) Featured 소개 문구에 앵커 추가: "From $99/mo. Angi runs $200–550/mo plus shared leads. One booked encapsulation covers a full year. Max 3 slots per market."
  2. (오너) LS에서 Tier1($149)/State($199) 상품 생성 → buy UUID 전달 → (개발) `lib/featured.ts`에 입력, "설정 중" 표시 제거.
- **완료 기준**: 3개 티어 모두 체크아웃 열림(test 모드), 가격 문구에 앵커 포함.

### T7 — 결제→슬롯 활성화 e2e `P2 · 개발 · 8/2`
- **문제**: 순정 링크 결제만 테스트됨. custom data(contractor_slug+market) 실린 결제로 슬롯이 실제 활성화되는 흐름 미검증. Phase 3 개시 전 필수 배관.
- **지시**: test 모드에서 custom data 포함 체크아웃 → 웹훅 수신 → 슬롯 활성화 → 디렉토리 Featured 노출까지 전 구간 1회 실증. 실패 지점 있으면 수정.
- **완료 기준**: e2e 1회 통과 기록(스크린샷 또는 로그) + billing 테스트 스위트 그린 유지.

### T8 — Resend 키 교체 `P2 · 오너+개발 · 8/2`
- **문제**: 서버 .env에 full-access 키. 유출·폐기 시 메일 전면 중단 리스크.
- **지시**: (오너) Resend에서 sending-only 키 발급 → (개발) 서버 .env 교체 → 로그인 코드 메일 실발송 1회 확인.
- **완료 기준**: sending-only 키로 실발송 성공, full-access 키 폐기.

### T9 — 외부 업타임 모니터 `P2 · 오너 · 8/2`
- **지시**: UptimeRobot 무료 — `https://crawlcred.com` + `https://crawlcred.com/api/health`(경로는 실제 헬스 엔드포인트 확인) 5분 간격, 알림 devoh@signpost.kr.
- **완료 기준**: 테스트 알림 수신.

### T10 — 서비스 목록 단일화 `P3 · 개발 · 8월 중`
- **문제**: 서비스 카테고리 목록이 코드 4곳에 중복 정의(현재 값 일치, 드리프트 위험).
- **지시**: 단일 소스(shared 상수)로 통합, 나머지는 import. ORDER_TRANSITIONS 방식과 동일하게 API/web 각 1곳씩(정적 export 제약상 web은 미러 유지 시 주석으로 상호 참조 명시).
- **완료 기준**: 정의 지점 최소화 + 테스트 그린.

### T11 — SC 면허 대조 스크립트 `P3 · 개발 · 8월 중`
- **지시**: `verify.llronline.com` 대상, NC(`import-nc-licenses.ts`) 구조 복제. 스로틀 쿨다운 포함, `--write` 플래그, source_url+checked_at 필수.
- **완료 기준**: SC 시드 업체 대상 dry-run 출력 검수 후 --write 1회 실행, /api/stats에 SC 반영.

## 2.3 보고 체계

- 태스크 완료 시: 사업기획안 §3 체크 + 커밋 메시지에 `[T번호]` 표기
- 매주 월요일 리뷰에서 미완 태스크는 사유와 함께 재일정 또는 §6 백로그로 이동
- 지시 내용과 실제 코드/제약이 충돌하면 **임의 변경하지 말고** 이 문서에 메모 후 기획(marketer)과 조율

---
*이 문서는 발행 시점 스냅샷이다. 진행 상태의 단일 기준은 `CrawlCred-사업기획안.md`.*
