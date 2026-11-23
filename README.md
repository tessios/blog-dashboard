# 블로그 현황 · 홈판 키워드 5선

3곳(부계정·본계정·티스토리)의 발행 현황과 네이버 홈판 추천 키워드 5선을 한 화면에서 보는 정적 웹앱입니다.

- 주소: https://tessios.github.io/blog-dashboard/
- 구조: `index.html`(화면) + `data.json`(데이터) — 그 외 의존성 없음
- 갱신: **매일 07:45 자동화가 `data.json`만 새로 써서 push** 하면 GitHub Pages가 자동 반영합니다. `index.html`은 건드리지 않습니다.

## 갱신 방법 (자동화용)

1. 07:45 홈판 스캔이 끝나면 `data.json`을 같은 스키마로 다시 씁니다.
2. `updated_at`, `updated_display`, `headline`, `blogs`, `keywords`, `pipeline`, `trend`를 채웁니다.
3. `git add data.json && git commit -m "data: YYYY-MM-DD" && git push` (main)
4. Pages 배포 후 `https://tessios.github.io/blog-dashboard/data.json` 이 새 값을 주는지 확인합니다.

화면은 `data.json`을 `cache: no-store`로 읽으므로 push만으로 반영됩니다.

## 스키마 요약

```
schema       숫자, 스키마 버전(현재 1)
updated_at   ISO8601 (KST 오프셋 포함)
updated_display  화면에 보일 시각 "11/24 (화) 07:52"
refresh      갱신 주기 문구
basis        수집 조건 한 줄
headline     { today_posts, today_label, sample, sample_label, sample_sub,
               last, last_label, last_sub, surge, surge_label, surge_sub }
blogs[]      { id, label, role, accent, published_total|null, published_label,
               published_sub, today_posts|null, last{title,at,category},
               dist[{name,count}], dist_note, metrics[{label,value,note}],
               status[{state:"ok"|"wait"|"hold", text, sub}], recent[{at,title,category}], notes[] }
keywords[]   { rank, keyword, short, category, signal, tone:"up"|"flat"|"weak",
               evidence, series[5], series_label, competition, reason,
               title_example, action, risk, meta }
pipeline[]   { tag, title, when, note }
trend        { hot_topics[{name,tag?}], readouts[{n,title,text}],
               cohort{dates[5], axes[{name,values[5],verdict}]},
               top_posts[{title,reaction,tab}] }
```

- `published_total`/`today_posts`가 `null`이면 화면은 "미연동"으로 표시합니다. 확인되지 않은 수치를 임의로 넣지 않습니다.
- `series`는 최근 5일 등장 수(과거→오늘)입니다. 값이 없으면 축을 빼고 `cohort`만 줄입니다.
- `state`: `ok` 완료/정상, `wait` 대기, `hold` 보류.

## 지켜야 할 것

- **ID·이메일·계정 로그인 정보 금지.** 블로그는 `부계정 (리뷰·관심사)`, `본계정 (기술·품질)`, `티스토리 (글로벌·여행)` 라벨로만 부릅니다. 블로그 URL·글 번호도 넣지 않습니다.
- **확인된 수치만.** 조회·유입은 관리자 캡처를 받았을 때만 채우고, 확인 시점을 함께 적습니다.
- **회사·현장·일정 정보 금지.** 여행 소재는 "겨울에 다녀왔다" 수준으로만 씁니다.
- **로그인·발행 없음.** 홈판·RSS는 비로그인 공개 읽기만 사용합니다.
- 이 저장소는 공개입니다. `robots.txt`로 검색 노출을 막고 `index.html`에 `noindex`를 넣었지만, 공개 저장소라는 전제로 데이터를 넣습니다.

## 파일

| 파일 | 역할 |
|---|---|
| `index.html` | 화면 전체(스타일·스크립트 포함, 외부 CDN 없음) |
| `data.json` | 유일한 데이터 소스 |
| `robots.txt` | 검색 비노출 |
| `.nojekyll` | GitHub Pages Jekyll 처리 생략 |
