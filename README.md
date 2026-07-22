# hackathon-lecture-service-dev

기상·기후 AI 해커톤 2026 본선 진출 15팀 대상 교육 세션 강의안 — "서비스 개발: 사용자에서 시작하기"

## 구성

- `index.html` — 강의 슬라이드 (16:9, 21장). 브라우저로 열면 세로 스크롤로 슬라이드가 이어지고, 인쇄(Cmd+P)하면 슬라이드당 1페이지로 출력됩니다.
- `talk.html` — 발표용 슬라이드 (16:9, 21장). 키워드 중심 큰 글씨 버전으로, 구성과 페이지 순서는 `index.html`과 1:1 동일합니다.
- `index.pdf` / `talk.pdf` — 위 두 파일을 헤드리스 브라우저로 인쇄 렌더링한 PDF본(각 21페이지).

## PDF 재생성

`index.html`/`talk.html`을 수정한 뒤에는 PDF도 함께 재생성해야 합니다. 헤드리스 Chromium(Playwright 번들 등)으로 아래처럼 렌더링합니다.

```
chrome --headless=new --disable-gpu --no-sandbox \
  --ssl-version-max=tls1.2 \
  --virtual-time-budget=15000 --run-all-compositor-stages-before-draw \
  --print-to-pdf=index.pdf --print-to-pdf-no-header --no-margins \
  file://$(pwd)/index.html
```

두 플래그가 모두 필요합니다 — 하나라도 빠지면 Pretendard 웹폰트 없이(시스템 폴백 폰트로) 렌더링된 PDF가 만들어지고, 겉보기엔 정상 생성된 것처럼 보이므로 주의가 필요합니다.

- `--ssl-version-max=tls1.2` — 프록시를 경유하는 샌드박스 환경에서 헤드리스 Chromium이 TLS 1.3으로 협상을 시도하면 프록시와의 핸드셰이크 도중 연결이 끊깁니다(net::ERR_CONNECTION_RESET). 이 때문에 `pretendard.min.css`(jsDelivr CDN) 로딩이 실패해도 에러 없이 조용히 시스템 폰트로 대체됩니다. TLS 1.2로 협상을 강제하면 정상 로딩됩니다.
- `--virtual-time-budget=15000` (`--run-all-compositor-stages-before-draw`와 함께) — 이 플래그가 없으면 CDN 요청 자체는 성공해도 `--print-to-pdf`가 웹폰트(@font-face) 다운로드가 끝나기 전에 페이지를 캡처해버려, 역시 폴백 폰트로 인쇄됩니다.
- 재생성 후에는 임베드 폰트를 확인해 실제 Pretendard가 들어갔는지 검증하는 것을 권장합니다: `strings index.pdf | grep BaseFont` → `Pretendard-*` 서브셋(Regular/SemiBold/Bold/ExtraBold/Black 등)이 보여야 정상이고, `WenQuanYiZenHei`나 `LiberationSans`가 보이면 폴백 폰트로 렌더링된 것입니다.

## 강의 정보

- 강사: 김영욱 (AIoIA 공동대표 · CTO)
- 대상: 본선 진출 15팀
- 시간: 60분 내외
- 주최 기상청 · 주관 기상기후인재개발원
