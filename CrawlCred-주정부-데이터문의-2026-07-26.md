# 주정부 면허 데이터 문의 초안 (TN · GA)

| 항목 | 내용 |
|---|---|
| 목적 | TN 87곳 · GA 67곳의 수동 면허 조회(합 약 5시간, 90일마다 반복)를 **공식 데이터 경로로 영구 대체** |
| 발송 | 오너 (hello@crawlcred.com) |
| 비용 | TN 미지 / GA 로스터 추정 $25~100 (1회) |
| 근거 | GA는 "Residential and General Contractors" 로스터 판매 확인. TN은 공개 조회는 있으나 데이터 제공 경로 미확인 |

**왜 이 문의가 수동 작업보다 나은가**: 90일 재확인 약속이 인벤토리에 선형 비례한다. TN 87 + GA 67 = 154곳을 분기마다 손으로 읽는 것은 지속되지 않는다. 공식 파일 하나면 로더가 대신하고, NC 스크레이퍼보다 오히려 안정적이다 — 세션 워밍도, 스로틀도, 페이지 구조 변경도 없다.

---

## 1. Tennessee — Department of Commerce & Insurance

**수신**: Board for Licensing Contractors (Tennessee Department of Commerce & Insurance)
**참고**: 공개 조회는 `verify.tn.gov`. 문의 목적은 **대량 조회를 대체할 공식 경로**.

```
Subject: Public records request — bulk licensee data for the Board for Licensing Contractors

Hello,

I operate CrawlCred (crawlcred.com), an independent directory of crawl space and
moisture contractors in the Southeast. For each business we list, we check the
state licence record and publish the result with the date we read it — including
when no licence is on file, alongside the context that this can be lawful below a
state's threshold.

We currently list 87 contractors with Tennessee addresses, and we re-check every
listing on a 90-day cycle. Reading each record individually through the public
search is workable but not sustainable at that cadence, and it puts avoidable
load on your search system.

I would like to ask whether the Board makes licensee data available in bulk on any
of the following terms:

  1. A downloadable or purchasable roster of active licensees for the Board for
     Licensing Contractors (and, separately, Home Improvement Contractors) —
     licence number, business name, city, status, expiration date. Fields matching
     what the public search already displays; no personal contact details needed.
  2. A documented API or data feed intended for third-party use.
  3. A public records request under the Tennessee Public Records Act, if that is
     the appropriate channel for a one-off extract.

If a fee applies, please let me know the amount and how to pay it. If none of the
above is available, I would appreciate confirmation of that too, along with any
guidance on an acceptable request rate against the public search so that we stay
within your expectations.

For context on how the data is used and presented, our methodology page is at
https://crawlcred.com/methodology/ — every check we publish carries its source and
the date it was read.

Thank you,
[이름]
CrawlCred · hello@crawlcred.com
```

## 2. Georgia — Secretary of State, Professional Licensing Boards Division

**수신**: Licensing Division, 237 Coliseum Drive, Macon, GA 31217-3858 (Mon–Fri 8am–5pm)
**참고**: 로스터 판매가 이미 확인됨. 이건 **구매 요청**이지 가능 여부 문의가 아니다.

```
Subject: Roster purchase — Residential and General Contractors

Hello,

I would like to purchase the licensee roster for Residential and General
Contractors.

I operate CrawlCred (crawlcred.com), an independent directory of crawl space and
moisture contractors in the Southeast. We publish the state licence record for each
business we list, with the date we read it, and we re-check on a 90-day cycle. The
roster would let us do that from your published data rather than by repeated
individual lookups.

Could you confirm:

  - the current price and how to pay,
  - the format supplied (CSV or similar) and the fields included — we need licence
    number, business name, city, status and expiration date; no personal contact
    details are required,
  - how often the roster is refreshed, and whether a repeat purchase on a
    quarterly or semi-annual basis is possible.

Thank you,
[이름]
CrawlCred · hello@crawlcred.com
```

---

## 3. 답이 오면

| 답변 | 조치 |
|---|---|
| 로스터/피드 제공 | `src/lib/manual-boards.ts`에서 해당 주를 제거하고 **파일 로더**를 붙인다. `boards.ts`의 `BoardClient` 시그니처를 그대로 만족시킬 수 있다 — 네트워크 호출 없이 |
| 요청 간격 안내만 | 그 간격을 `boards.ts`에 넣고 자동화 대상으로 승격 |
| 제공 없음 | 수동 유지. `licenseCheck: "not-started"` 문구가 이미 그 사실을 말하고 있으므로 사이트 변경은 없다 |

## 4. 보내지 말아야 할 것

- **내부 API 접근 여부를 묻지 말 것.** TN 포털의 문서화되지 않은 엔드포인트를 언급하는 것은 우리가 그것을 조사했다는 사실을 알리는 것이고, 문의 목적은 공식 경로를 얻는 것이다
- 접근 권한이나 예외 대우를 요청하지 말 것. 우리가 원하는 것은 **누구나 쓸 수 있는 공개 데이터**다
