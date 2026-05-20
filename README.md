# branch_monitor

여러 GitHub 저장소의 브랜치 / 커밋 / PR 상태를 **한 페이지**에서 모니터링하는 정적 사이트입니다.

- 호스팅: **Vercel** (Public 저장소, 푸시마다 자동 재배포)
- 그래프 렌더러: [@gitgraph/js](https://gitgraph.js.org/) (jsDelivr CDN)
- 인증: 브라우저 측 GitHub Personal Access Token (저장소별 분리 저장)
- 대상 저장소 추가: `repos.json` 에 항목 1개 추가 → commit → push

## 파일 구조

```
branch_monitor/
├── index.html       랜딩 — 등록된 저장소를 카드로 나열
├── branches.html    그래프 뷰 — ?owner=X&repo=Y 진입
├── repos.json       대상 저장소 레지스트리 (한글 설명 / commit override 포함)
├── vercel.json      Vercel 정적 호스팅 설정
└── README.md        본 문서
```

## 사용

```
https://branch-monitor.vercel.app/                                    # 랜딩
https://branch-monitor.vercel.app/?owner=doroper98&repo=osint_generator  # 상세
```

## 저장소 추가

`repos.json` 의 `repos` 배열에 항목 1개 append:

```json
{
  "owner": "doroper98",
  "repo": "new_project",
  "description": "한국어 설명을 여기에",
  "private": false,
  "branches": {
    "main": "기본 브랜치 한국어 설명"
  },
  "commits": {
    "abc1234": "영어로 된 과거 commit 의 한국어 override"
  }
}
```

`branches` / `commits` 는 옵션. 비워두면 미등록 안내가 뜨고 commit subject 는 원문 그대로.

## PAT 토큰

각 저장소마다 별도 토큰을 권장합니다 (Fine-grained PAT, 권한: Contents/Metadata/PR Read-only).
토큰은 브라우저 localStorage 의 분리된 key 에 저장됩니다:

```
branch_monitor.token.<owner>/<repo>.v1
```

Public 저장소는 토큰 없이도 동작하지만 GitHub API 의 시간당 60 요청 제한을 받습니다.
