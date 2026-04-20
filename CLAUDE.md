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

**배포 도메인:** `astro.config.mjs`의 `site` 값을 실제 도메인으로 변경 후 배포.

**폰트:** Atkinson 로컬 폰트 (`src/assets/fonts/`) — `astro.config.mjs`에서 설정.

**스타일:** `src/styles/global.css`에 전역 CSS. 컴포넌트는 scoped CSS 사용. CSS 프레임워크 없음. 흑백 테마 적용.

**이미지:** Astro `<Image>` 컴포넌트로 최적화. 포스트 내 Unsplash 외부 이미지는 마크다운 `![]()` 문법 사용.

**RSS 피드:** `/rss.xml`로 자동 생성 (`src/pages/rss.xml.js`).

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
pubDate: 'YYYY-MM-DD'
heroImage: '../../assets/blog-placeholder-1.jpg'
---
```

> heroImage는 로컬 placeholder 이미지를 사용한다 (Astro 스키마가 로컬 이미지만 허용).
> 본문 이미지는 아래 이미지 규칙에 따라 외부 URL을 마크다운으로 삽입한다.

**글 작성 규칙:**
1. **분량:** 최소 5,000자 이상
2. **주제:** 이전에 작성한 글과 겹치지 않는 새로운 식재료 관련 주제 선택
3. **SEO 구조:**
   - H2, H3 소제목으로 목차 구조화
   - 핵심 키워드를 제목·도입부·소제목에 자연스럽게 배치
4. **이미지:** 본문 중간에 **1개 이상** 삽입
   - 형식: `![설명](이미지URL)`
   - 아래 3개 사이트 중 검색 결과 URL을 바로 사용 (이미지 검색에 과도한 시간/토큰 낭비 금지)
     - https://unsplash.com/ko
     - https://www.pexels.com/ko-kr/
     - https://pixabay.com/ko/
   - 검색 1회로 URL을 찾지 못하면 즉시 placeholder 텍스트로 대체하고 글 작성을 계속 진행한다
5. **말투:** 블로그 글체 (구어체 혼용), 독자에게 말 걸듯 자연스럽게
6. **구성:**
   - 도입부 → H2/H3 본론 → 이미지 → 팁/주의사항 → 마무리
