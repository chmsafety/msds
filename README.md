# MSDS 관리 앱

CHM 사업장 화학물질(MSDS) 관리 웹앱 — Supabase + 단일 index.html (GitHub Pages).

## 기능
- **MSDS 자료관리**: PDF 업로드 → 브라우저에서 텍스트 추출 → 규칙 기반 자동 분류(제품명·그림문자·신호어·H/P 문구·구성성분·특검/관리/허가대상) → 검수 → 사업장별 보유 제품 관리. 스캔본·비표준 양식은 "AI로 보완 추출"(Edge Function).
- **소분용기 경고표지**: 용량 입력 → 고시 별표3 규격(450/300/180/90㎠, 5L 미만은 표면적 5%)으로 표지 크기 자동 계산 → 100ml 이하는 간이표시(4항목) → A4 출력.
- **공정별 관리요령**: 기존 엑셀 양식 그대로 자동 작성(그림문자·신호어·보호구 아이콘 포함), 출력 크기 조절(A4/A3/70/50%).
- **특수건강검진 관리**: 근로자·배치 등록, 배치전 검진 체크, 배치후 검진 예정일 자동 계산, 대시보드 D-7 알림.

## 구조
```
index.html                      ← 배포 파일 (build.sh 로 생성)
src/part1.html / part2.js / part3.js   ← 소스 조각
build.sh [URL] [KEY]            ← 합치기 + Supabase 설정 주입
supabase/migrations/001_init.sql       ← 테이블·RLS·Storage
supabase/functions/msds-extract/index.ts ← AI 보완 추출 Edge Function
```

## 배포 순서
1. Supabase 프로젝트 `msds` 생성(서울 리전) → SQL Editor 에서 `001_init.sql` 실행
2. Authentication → Providers → Email: "Confirm email" 끄기(사내용)
3. Edge Function 배포: `supabase functions deploy msds-extract` → Secrets 에 `ANTHROPIC_API_KEY` 추가
4. `./build.sh https://xxxx.supabase.co sb_publishable_xxx` → index.html 을 GitHub 저장소(chmsafety/msds)에 올리고 Pages 활성화
5. 앱에서 첫 가입자 = 자동 관리자. 설정 → 사업장 등록, 가입 신청자 승인

## 법적 근거(자동 판단 참고용)
- 특수건강진단 대상 유해인자: 산업안전보건법 시행규칙 별표22 (성분 CAS 매칭은 참고용 — 담당자 확인 필요)
- 배치후 첫 검진 시기: 별표23 (유해인자별 1~12개월, 앱에서 선택)
- 경고표지 규격·소량용기 간이표시: 화학물질의 분류·표시 및 MSDS에 관한 기준 별표3, §6
