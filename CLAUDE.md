# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
pnpm dev        # 개발 서버 시작 (localhost:4321)
pnpm build      # 프로덕션 빌드 → ./dist/
pnpm preview    # 빌드된 사이트 미리보기
```

## Architecture

Astro 6 기반 정적 블로그. 파일 기반 라우팅과 Content Collections 사용.

**라우팅:** `src/pages/` 폴더 구조가 URL에 그대로 매핑됨. `blog/[...slug].astro`가 개별 포스트를 렌더링하는 동적 라우트.

**Content Collections:** 블로그 포스트는 `src/content/blog/`에 `.md` 또는 `.mdx` 파일로 저장. 스키마는 `src/content.config.ts`에서 Zod로 검증. 필수 필드: `title`, `description`, `pubDate` / 선택 필드: `updatedDate`, `heroImage`.

**전역 상수:** 사이트 타이틀·설명은 `src/consts.ts`에서 관리.

**배포 도메인:** `https://ian-papa.com` (확정). `astro.config.mjs`의 `site` 값으로 설정되어 있음.

**폰트:** Atkinson 로컬 폰트 (`src/assets/fonts/`) — `astro.config.mjs`에서 설정.

**스타일:** `src/styles/global.css`에 전역 CSS. 컴포넌트는 scoped CSS 사용. CSS 프레임워크 없음. 흑백 테마 적용.

**이미지:** Astro `<Image>` 컴포넌트로 최적화. 포스트 내 외부 이미지는 마크다운 `![]()` 문법 사용.

**RSS 피드:** `/rss.xml`로 자동 생성 (`src/pages/rss.xml.js`).

**애드센스:** `src/components/BaseHead.astro` 하단에 스크립트 삽입됨 (publisher ID: ca-pub-3153802080373437). 모든 페이지에 자동 적용.

## 배포 구조

- **GitHub → Cloudflare Pages** 자동 연동. `master` 브랜치에 push하면 즉시 빌드·배포.
- **GitHub Actions cron** (`.github/workflows/scheduled-deploy.yml`)이 매시 7분(`7 * * * *`)에 Cloudflare Deploy Hook(`CLOUDFLARE_DEPLOY_HOOK` secret)으로 POST 요청을 보내 재빌드를 트리거함 — git 커밋은 발생하지 않음.
- Cloudflare는 정적 빌드 기반이라 "실시간 예약 발행" 아님. 빌드 시점에 `pubDate`가 지난 글만 노출됨.

---

## 블로그 운영 규칙 (식재료 정보 수익화 블로그)

### 기본 방침
- 이 블로그는 **식재료 관리·보관법·제철음식 전문 수익화 블로그**다.
- 주제 범위: 식재료 보관법, 손질법, 제철 식재료, 식재료 효능, 식재료 조합, 냉장/냉동 보관 팁 등
- 모든 글은 SEO(검색엔진 최적화)를 최우선으로 고려해서 작성한다.
- 글 말투는 자연스럽고 친근하게 — AI가 쓴 것처럼 딱딱하거나 나열식으로 쓰지 않는다.

### "새글 작성" 명령어
사용자가 **"새글 작성"** 이라고 입력하면, 아래 규칙에 따라 즉시 마크다운 포스트 파일을 생성한다.

**파일 위치:** `src/content/blog/[주제-영문슬러그].md`

**프론트매터 형식:**
```yaml
---
title: '제목 (SEO 키워드 포함, 30자 내외)'
description: '메타 디스크립션 (검색결과에 표시될 요약, 120자 내외, 핵심 키워드 포함)'
pubDate: 'YYYY-MM-DDTHH:MM:SS+09:00'
heroImage: 'https://images.pexels.com/photos/[ID]/pexels-photo-[ID].jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1'
draft: false
---
```

- `heroImage`: Pexels/Unsplash 외부 URL 사용 (주제와 직접 연관된 사진)
- **`pubDate`는 반드시 `+09:00` 한국시간(KST) 오프셋 포함** — Cloudflare 빌드 서버는 UTC 기준이라 타임존 없이 쓰면 KST보다 9시간 늦게 노출됨
- `pubDate`를 미래 날짜로 설정하면 그 날 이후 빌드 시점부터 발행됨
- `draft: true`는 완전 숨김용 (목록·빌드에서 제외). 예약발행은 `draft: false` + 미래 `pubDate` 조합으로 처리

**글 작성 규칙:**
1. **분량:** 최소 5,000자 이상
2. **주제:** 이전에 작성한 글과 겹치지 않는 새로운 식재료 관련 주제 선택
3. **SEO 구조 (애드센스 승인 고려):**
   - H2, H3 소제목으로 목차 구조화
   - 핵심 키워드를 제목·도입부·소제목에 자연스럽게 배치
   - 광고보다 콘텐츠 비중이 압도적으로 높아야 함 (텍스트 중심 구성)
   - 전문성·신뢰성이 느껴지도록 구체적인 수치·이유·근거 포함
4. **이미지 규칙 (반드시 준수):**
   - **최대 2개** — heroImage 1개 + 본문 이미지 1개
   - **주제와 직접 연관된 사진만 사용** — 글이 쌀이면 쌀, 참기름이면 참기름 사진
   - **관련 이미지를 못 찾으면 넣지 않는다** — 억지로 비슷한 사진 붙이지 말 것
   - **중복 금지** — 이미 다른 포스트에 쓴 Pexels 사진 ID를 재사용하지 않는다. 새 글 작성 전 `grep -rh "heroImage\|!\[" src/content/blog/*.md | grep -oE "photos/[0-9]+"` 로 기존 ID 확인
   - **이미지 검색 순서:** Pexels → Unsplash → Pixabay (1회 검색, 없으면 건너뜀)
   - 이미지 URL에서 파일명 확인 — "olive-oil", "gift-box" 같이 주제와 다른 파일명은 즉시 제외
   - 형식: `![구체적인 설명](이미지URL)`
5. **말투:** 블로그 글체 (구어체 혼용), 독자에게 말 걸듯 자연스럽게
6. **구성:** 도입부(공감) → H2/H3 본론 → 본문 이미지 1개 → 팁/주의사항 → 마무리

### 예약발행 방법
- 즉시 발행: `draft: false` + `pubDate: 'YYYY-MM-DDTHH:MM:SS+09:00'` (현재 시각)
- 예약발행: `draft: false` + `pubDate: 'YYYY-MM-DDTHH:MM:SS+09:00'` (미래 시각) → git push 후 GitHub Actions cron이 주기적으로 재빌드해 pubDate가 지난 글 자동 노출
- 임시저장: `draft: true` → 빌드해도 목록에 나타나지 않음

### 애드센스 승인 유의사항
- 이미지보다 텍스트(고품질 정보) 비중이 높아야 함
- 각 글은 독립적으로 완결된 정보를 제공해야 함
- 글 수가 최소 10편 이상 쌓여야 승인 가능성이 높아짐
- 개인정보처리방침 페이지 추가 권장
