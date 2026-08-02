# CrawlCred 씬 이미지 생성 — 프롬프트 + 레퍼런스 사용법

| 항목 | 내용 |
|---|---|
| 목적 | 도시/주 페이지용 씬 사진 6장 추가 (현재 7장이 38개 도시 페이지에서 반복됨 → 13장) |
| 레퍼런스 | `C:\biz\9.jpg` (곰팡이·수분 피해 장선), `C:\biz\11.png` (완성된 인캡슐레이션 광각) |
| 출력 | `C:\biz\` 에 PNG로 저장 → 내가 webp 변환·씬 등록·배포 |
| 근거 문서 | `crawlcheck/CLAUDE.md` 브랜드 상수 / `methodology` 페이지 자체 선언 |

---

## 0. 레퍼런스를 어떻게 쓰는가 (이게 이 문서의 핵심)

레퍼런스는 **피사체와 재질 현실감**만 가져온다. **구도·앵글·조명·연출은 전부 다시 쓴다.**
그대로 베끼면 두 가지가 동시에 깨진다 — 남의 사진과 구별되지 않고, 우리 포지셔닝을 위반한다.

| 레퍼런스 | 가져올 것 | **바꿀 것** |
|---|---|---|
| `11.png` | 20mil 백색 라이너의 밝기, 라이너로 감싼 피어, 제습기, 단열 덕트, 실제 헤드룸(약 90cm) | **대칭 광각 복도 아이레벨** → **바닥 로우앵글**, 테이프 이음선을 **대각선 오프센터**로. 스톡사진 느낌 제거 |
| `9.jpg` | 목재 장선의 착색·수분 흔적이 실제로 어떻게 보이는지, PVC 배관·자재 질감 | **곰팡이 클로즈업(공포 소구)** → **"측정 중인 증거"**. 장갑 낀 손이 수분 측정기를 장선에 대는 프레임. 착색은 사실대로, 선정적이지 않게 |

**왜 9.jpg를 그대로 못 쓰는가**: 우리 포지셔닝은 `"Evidence, not scare tactics"`이고
`CLAUDE.md` 브랜드 상수에 *"copy never uses fear marketing"* 이 박혀 있다. 곰팡이 확대 사진을
히어로로 쓰는 순간 우리가 경쟁사를 비판하는 근거가 우리에게 돌아온다.

**우리 스타일의 출처**: `methodology` 페이지가 스스로 선언한 문장 —
*"제대로 된 인캡슐레이션은 판매 브로슈어가 아니라 시공 도면처럼 보인다."*
사진도 같아야 한다. **측량 기록 사진**, 마케팅 렌더가 아니다.

---

## 1. Codex CLI로 실행 (레퍼런스 첨부)

```bash
cd C:\biz
codex exec -i 9.jpg -i 11.png "$(cat CrawlCred-이미지생성-프롬프트-2026-07-26.md)"
```

`-i`로 두 레퍼런스를 첨부하면 모델이 재질·밝기·비율을 실제 사진에서 읽는다.
**첨부와 동시에 아래 지시를 반드시 함께 전달해야 한다** — 안 그러면 레퍼런스를 그대로 재현한다.

> Use the two attached photos as reference for MATERIAL REALISM ONLY — liner
> brightness, timber grain, staining, pipe and duct appearance, real crawl-space
> proportions. Do NOT reproduce their composition. The wide symmetrical corridor
> view and the mould close-up are both explicitly rejected. Re-author angle,
> framing, staging and light per each numbered brief below.

## 2. ChatGPT / 이미지 도구에 직접 붙일 프롬프트

### HOUSE STYLE (6장 전부 적용)

```
Documentary architectural-inspection photography, full-frame camera with a wide
prime. Bright, even, cool-neutral white balance; clean whites with no colour
cast; realistic construction materials and honest wear. Calm and factual, like a
surveyor's record photo — NOT a marketing render, NOT dramatic. No vignette, no
lens flare, no HDR crunch. No text, no logos, no watermarks, no signage. No
faces, no people beyond a gloved hand where specified. Photorealistic, sharp,
realistic crawl-space proportions (about 90 cm headroom). Teal (#0e9e94) may
appear ONLY as a tool or equipment accent — never as a colour filter or grade.
3:2 landscape.
```

### 1 · `scene-seam-lowangle`

```
Camera placed on the floor of a finished encapsulated residential crawl space,
looking along a taped seam in a bright white 20-mil vapour barrier that runs
diagonally off-centre toward the right third of the frame. In the middle distance
a small white dehumidifier sits with its condensate line run to a drain.
Overhead, unpainted wooden floor joists and one insulated duct. A compact work
light with a teal housing rests on the liner in the near foreground, throwing
soft even light. Shallow depth of field on the seam tape.
```
alt: `A low view along a taped seam in the white vapor barrier of a finished crawl space, with a dehumidifier and its drain line in the middle distance`

### 2 · `scene-inspection-meter` — 9.jpg 재연출

```
Close documentary detail of a crawl-space inspection in progress: a gloved hand
(glove only, no face, no body) presses a small handheld pin-type moisture meter
against the underside of a wooden floor joist. The joist shows honest,
matter-of-fact staining and a dry water line — evidence being measured, not a
horror image. The meter body has a teal accent. Behind, a bright white vapour
barrier reflects soft light up onto the framing. Tight composition, meter and
joist grain in sharp focus.
```
alt: `A gloved hand holding a moisture meter against a stained floor joist during a crawl space inspection`

### 3 · `scene-pier-wrap-detail`

```
Tight detail, camera about 40 cm from the subject: the base of a concrete-block
support pier inside an encapsulated crawl space, wrapped in white vapour-barrier
liner with a neat butyl seam tape band, the liner turned up the pier and
terminated in a straight line. Floor liner sweeps away out of focus behind. Even
soft light, crisp material texture — block, plastic sheet, tape adhesive edge.
```
alt: `Detail of a concrete pier wrapped in white vapor barrier liner with a taped seam in an encapsulated crawl space`

### 4 · `scene-rim-foam-detail`

```
Detail of a crawl-space rim joist band sealed with rigid foam board, the foam cut
clean to the bay and sealed at the edges, sill plate visible below, framing
lumber above. Camera slightly below, looking up at an angle so the bay recedes to
the left. Cool even light, no shadows crushed. Construction-record framing that
shows workmanship quality.
```
alt: `A crawl space rim joist bay sealed with cut and edge-sealed rigid foam board`

### 5 · `scene-vent-sealed`

```
From inside a crawl space, a former foundation vent opening in a concrete-block
wall, now permanently sealed with a fitted insulated block and edge sealant, the
white wall liner lapped over it. A thin line of daylight traces the outside edge
of the old frame. Off-centre composition, wall filling the left two thirds, liner
floor at the bottom edge. Quiet, factual light.
```
alt: `A former foundation vent sealed and lapped over with wall liner, seen from inside an encapsulated crawl space`

### 6 · `scene-dehum-drain`

```
Three-quarter view of a compact white crawl-space dehumidifier standing on a
bright white vapour barrier, its flexible condensate line run neatly along the
liner to a small sealed drain fitting in the near foreground. Ducted supply hose
curves away behind. Camera at unit height, unit placed left of centre with clean
liner to the right. Even light, no reflections blown out.
```
alt: `A crawl space dehumidifier on a white vapor barrier with its condensate line run to a sealed floor drain`

---

## 3. 받은 뒤 내가 하는 일

1. PNG → webp 변환 (기존 자산과 동일 포맷·용량대)
2. `web/public/illustrations/` 에 배치
3. `components/SceneImage.tsx` 의 `SCENES` 배열에 alt와 함께 등록 (7 → 13장)
4. 빌드·배포 — 38개 도시 페이지와 5개 주 페이지의 씬 반복이 줄어든다

## 4. 검수 기준 (이 중 하나라도 걸리면 재생성)

- [ ] 텍스트·로고·워터마크·간판이 있는가 → 즉시 폐기
- [ ] 얼굴이나 사람 전신이 있는가 → 폐기 (2번의 장갑 낀 손만 허용)
- [ ] 곰팡이가 화면을 지배하거나 어둡고 극적인가 → 포지셔닝 위반, 폐기
- [ ] 티일이 색보정/필터로 쓰였는가 → 장비 액센트만 허용
- [ ] 헤드룸이 사람이 서 있을 만큼 높은가 → 크롤스페이스가 아니라 지하실, 폐기
- [ ] `11.png`의 대칭 복도 구도를 재현했는가 → 폐기
