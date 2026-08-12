# 오늘의 건강

하루 생활습관을 한 화면에서 기록하는 개인용 대시보드.
**HTML 파일 하나**로 끝나고, 더블클릭하면 바로 열린다. 로그인도 서버도 없다.

![10점 만점 · 표정 표시](https://img.shields.io/badge/score-10%EC%A0%90%20%EB%A7%8C%EC%A0%90-E3ECA6)

## 쓰는 법

`헬스케어_대시보드.html` 을 브라우저로 열면 끝. 설치도 빌드도 없다.

## 기능

| 영역 | 내용 |
|---|---|
| **식단** | 끼니(아침·점심·저녁·간식)를 고르고 먹은 것을 적은 뒤 Enter |
| **운동** | 유산소 / 웨이트로 나눠 기록. 종류별 합계와 총합을 함께 표시 |
| **수분** | 잔 단위로 +/− 또는 잔 아이콘 직접 클릭 (목표 8잔) |
| **수면** | 취침·기상 시각을 넣으면 수면 시간 자동 계산 (자정 넘김 처리) |
| **메모** | 컨디션·식욕·몸 상태 기록. `Ctrl(⌘) + Enter` 로 저장 |
| **체크리스트** | 기본 4종 제공. 추가·삭제·토글, 진행률 표시 |
| **오늘의 점수** | 수면·운동·식단·수분을 각 2.5점으로 환산해 10점 만점 |

점수 구간에 따라 표정이 함께 바뀐다 — 8점 이상 😄 / 5~8점 😐 / 5점 미만 😢

자정이 지나면 오늘 기록은 초기화되고, 체크 항목과 메모는 그대로 남는다.

## 설계

- **순수 HTML / CSS / JS.** 프레임워크·빌드 도구·CDN 없음
- 저장은 **localStorage** (`health-dashboard-v2`)
- 사용자 입력은 전부 `textContent` 로 삽입 (`innerHTML` 문자열 조립 금지)
- 라이트 / 다크 모드 (OS 설정 자동 감지 + 수동 토글)
- 이미지는 **인라인 SVG** — 네트워크 요청이 0이라 오프라인에서도 그대로 뜬다

디자인은 [DESIGN-airbnb.md](./DESIGN-airbnb.md) 를 따른다.
흰 캔버스 + ink + Rausch 단일 액센트, 상·하단은 루미너리 그린(`#E3ECA6`) 밴드.

## 검증

`_health_test_inject.js` 를 원본 HTML에 주입한 사본을 브라우저로 열면
화면 하단에 통과 여부가 표시된다. 자격증명이 필요 없다.

```powershell
$src = "헬스케어_대시보드.html"; $inj = "_health_test_inject.js"
$tmp = "$env:TEMP\healthtest"; New-Item -ItemType Directory -Force $tmp | Out-Null
$html = [IO.File]::ReadAllText($src, [Text.Encoding]::UTF8)
$js   = [IO.File]::ReadAllText($inj, [Text.Encoding]::UTF8)
[IO.File]::WriteAllText("$tmp\test.html",
  $html.Replace('</body>', "<script>$js</script></body>"),
  (New-Object Text.UTF8Encoding($false)))
# 생성된 test.html 을 브라우저로 열면 결과가 나온다
```

> 이전 실행의 localStorage가 남으면 결과가 오염된다.
> 재실행 전에 개발자도구에서 `localStorage.clear()` 를 먼저 실행할 것.

**현재 38 / 38 통과** (Edge, 라이트·다크 확인)

## 문서

- [PRD_헬스케어.md](./PRD_헬스케어.md) — 요구사항 · 화면 구성 · 점수 산식 · 알려진 제약
- [TASKS_헬스케어.md](./TASKS_헬스케어.md) — 태스크별 진행 상황과 검증 결과
- [DESIGN-airbnb.md](./DESIGN-airbnb.md) — 디자인 기준 문서

## 알려진 제약

- 기록이 **이 브라우저에만** 남는다. 캐시를 지우거나 시크릿 모드로 열면 사라진다
- 기기 간 동기화가 없다
- 지난 날 기록은 남지 않는다 (추이 조회는 백로그)

> 계정별 저장(Supabase)을 붙였다가 되돌린 이력이 있다.
> 이유는 [PRD 8항](./PRD_헬스케어.md)에 정리돼 있다.
