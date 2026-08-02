# WO-03 B2 — 창립 파트너 콜드메일 시퀀스 (발송용 최종 카피)

| 항목 | 내용 |
|---|---|
| 산출물 | WO-03 §2.3 콜드메일 3통 + 발송 규칙 |
| 발신 | hello@crawlcred.com (답장 수신 가능 확인됨) |
| 상태 | 발송 준비 완료 — 오너 최종 검토 후 사용 |

## 개인화 변수 (발송 전 치환 — B3 타깃 리스트에서)

- `{first_name}` — 담당자 이름 (모르면 인사말을 "Hi there,"로)
- `{company}` — 업체명
- `{license_no}` — NCLBGC 면허번호 (예: L.54441)
- `{license_status}` — ACTIVE 등
- `{city}` — 주력 도시
- `{profile_url}` — 해당 업체 프로필 URL
- `{checked_date}` — 검증 수행일 (예: July 24, 2026)
- `{checkout_url}` — **업체별 사전 생성 결제 링크** (B3 리스트의 checkout_url 컬럼 — FOUNDING 코드 자동 적용, 로그인 불필요). 절대 범용 가격 페이지 링크로 대체하지 말 것

## 발송 규칙

- 일 10통 이하, 수동 발송 (도메인 평판 보호 — 자동 대량 발송 금지)
- 1차: license-verified 7곳만. 2차(42곳)는 메일 1의 면허 문장을 상태에 맞게 조정 ("no license required for jobs under $40k in NC — your listing says so, honestly")
- 응답 오면 시퀀스 중단, 사람 대 사람으로 전환
- 모든 발송·응답을 스프레드시트(또는 §4.1 KPI)에 기록

---

## 메일 1 — 검증 통지 (Day 0, 파는 것 없음)

**제목 (A/B 중 택1):**
- A: `Your NC license checked out clean — {license_no}`
- B: `We verified {company} against the NCLBGC (no charge, no catch)`

```
Hi {first_name},

I run CrawlCred, a directory of crawl space contractors that verifies
public records instead of collecting star ratings.

Last week we checked all 49 NC crawl space companies against the
NC Licensing Board. {company} came back clean: license {license_no},
{license_status}, checked on {checked_date} — with a link to the
board's own record, so homeowners can see it's real.

Your verified profile is already live here:
{profile_url}

You can claim it free (takes 2 minutes — edit your services, phone,
and photos): just log in with this email address.

No charge, no catch. We only make money if contractors later choose
a featured spot — and that's entirely optional.

Either way: your license record looked good, and we thought you
should know it's working for you online.

Best,
{서명 — 오너 이름}
CrawlCred — Evidence, not scare tactics
hello@crawlcred.com
```

**원칙**: 첫 메일에서 팔지 않는다(상호성 — 원칙 17). 실제 면허번호가 첫 문단에(원칙 14·18).

---

## 메일 2 — 창립 오퍼 (Day 3, 미응답자에게)

**제목:** `Founding rate for {company} — $10 first month, locked before we grow`

```
Hi {first_name},

Quick follow-up. Your verified profile on CrawlCred is live and
claimable (free): {profile_url}

One more thing, and I'll be straight with you: we're a new site.
Traffic is growing from zero. That's exactly why founding pricing
exists —

  Featured placement in {city}: $10 for the first month,
  then $99/mo. Cancel anytime, full refund within 14 days.

Why bother while we're small? Because featured slots are capped at
3 per city, and founding partners keep priority as traffic grows.
We're issuing 10 founding codes total, then it's $99 like everyone
else.

If the site doesn't bring you value, cancel — the refund policy is
real and posted publicly.

Claim your free profile first, and if the featured spot makes sense:
{checkout_url}

Best,
{서명}
```

**원칙**: 신생임을 먼저 자백 → 그것이 곧 오퍼의 근거(정직한 희소성 — 원칙 2). 첫 달 $10 = 손실 문턱 제거. 슬롯 3개·코드 10개는 실제 제한만 언급.

---

## 메일 3 — 마감 통지 (Day 7~10, 미응답자에게)

**제목:** `Founding codes close Aug 24 — then {city} goes to $99`

```
Hi {first_name},

Last note from me, I promise.

The 10 founding codes ($10 first month, price locked) close on
August 24. After that, featured placement in {city} is $99/mo for
everyone — including the 3-slot cap.

Your verified profile stays live and free either way:
{profile_url}

If featured isn't for you right now, no hard feelings — claiming
your free profile still helps homeowners find a contractor whose
license actually checks out.

Best,
{서명}
```

**원칙**: 손실 회피(원칙 10)를 실제 마감으로만. 마지막 문장은 관계 보존 — 못 팔아도 클레임은 남긴다.

---

## 금지 사항 (전 메일 공통)

- 트래픽·방문자 수 부풀리기, "고객들이 찾고 있다" 류 암시 금지
- guarantee/보증 표현 금지
- 8/24 마감과 10코드 한정은 **시스템적으로 실제로 지킬 것** — 어기는 순간 브랜드 원칙 전체가 무너진다
