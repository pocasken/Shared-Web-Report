# StyleKorean vs KCOSERA — Top 20 브랜드 비교 대시보드 (v02)

## 개요

`research/Compare StyleKorean and KCOSERA/2026-08-06_StyleKorean_KCOSERA_top20-price-comparison_v01.xlsx`(GPT 작성)를 대체하는 것이 아니라, 같은 원본 데이터를 브라우저에서 브랜드/카테고리로 탐색하고 상품별 상세 비교를 볼 수 있게 만든 인터랙티브 대시보드입니다.

- 원본 엑셀과 데이터 소스는 수정하지 않았습니다.
- 데이터 원본: `working/compare-stylekorean-kcosera-stage1/2026-08-06_top20_matched_v01.json` (엑셀도 이 파일로부터 생성됨, `build_stage1_workbook.mjs` 참고)

## 구성

- **상단 통계**: Brands / Products / 직접 가격 비교 가능 / StyleKorean 매칭 없음 요약.
- **가격 비교 탭**: 브랜드 콤보박스(All Brands + 20개 브랜드) → 선택한 브랜드의 상품 목록(KCOSERA vs StyleKorean 가격·매칭 스펙) → 상품 클릭 시 상세 비교. Match Status·Cheaper Source·가격 비교 가능 필터는 "고급 필터"로 접어 기본 화면을 단순하게 유지.
- **카테고리로 알아보기 탭**: `guidance.category` 기준 11개 카테고리 칩(브랜드와 무관하게 전체 190개 대상). K-Beauty 상품이 익숙하지 않은 사용자가 카테고리별로 실제 상품·효능·성분·피부타입을 보며 학습할 수 있고, 카드에 KCOSERA/StyleKorean 링크를 직접 제공. 클릭 시 동일한 상세 비교 모달로 연결.

## 실행 방법

**v02부터는 로컬 서버가 필요 없습니다.** `index.html`을 더블클릭해서 바로 열면 됩니다.

(v01은 `fetch()`로 `data/products.json`을 불러와 `file://`로 직접 열면 브라우저가 로드를 차단했습니다. v02는 데이터를 `<script src="data/products.js">`로 동기 로드하도록 바꿔 이 문제를 해결했습니다.)

로컬 서버로 열어도 동일하게 동작합니다:

```bash
cd "deliverables/html-tools/stylekorean-kcosera-price-comparison-v01"
python3 -m http.server 8787
# 브라우저에서 http://localhost:8787 접속
```

## 파일 구조

```
index.html                 — 대시보드 UI (마크업+스타일+스크립트 단일 파일)
data/products.js            — 190개 상품 비교 데이터 (앱이 실제로 로드하는 파일, window.PRODUCTS_DATA)
data/products.json          — 동일 데이터의 참고/디버깅용 JSON
images/kcosera/*.png        — KCOSERA 썸네일 (로컬 복사본, 190/190)
images/stylekorean/*.png    — StyleKorean 썸네일 (로컬 다운로드, 163/190)
```

## 데이터 재생성

이 폴더는 배포용 결과물만 담고 있으며, 재생성 스크립트(`build_products_data.mjs`)와 원본 소스는 `working/compare-stylekorean-kcosera-html-v01/`에 있습니다. 원본 stage1 데이터가 갱신되면 그 폴더에서 다시 실행 후 이 폴더로 재복사합니다.

## 알려진 제한 사항

- StyleKorean 썸네일 190개 중 163개는 로컬에 다운로드됨. 나머지 27개는 매칭된 StyleKorean 상품이 없거나(16개, `KCOSERA Only / No reliable match`) 원본에 이미지가 없는 경우(11개, `no_img` placeholder)로, 실제로 표시할 이미지가 없는 것이며 다운로드 실패가 아닙니다.
- 매칭이 불확실한 상품(`Review Needed`, `Volume/Pack Difference`, `KCOSERA Only / No reliable match`)은 목록/상세에서 경고 배지로 구분되며, 가격을 임의로 확정 비교하지 않습니다.
- 가격이 없거나(`null`) 직접 비교 불가능한 경우 "Not available" / "직접 가격 비교 불가"로 명확히 표시됩니다.

## 검증 완료 항목

- [x] **`file://`로 서버 없이 직접 열어도 정상 동작** (v01 버그 수정 확인 — headless Chrome으로 재현·검증)
- [x] 브랜드 콤보박스 21개 옵션(All Brands + 20개) 정상 채움
- [x] 상단 통계(20/190/64/16)가 데이터에서 정확히 계산됨
- [x] 브랜드 선택 시 해당 브랜드 상품만 표시, All Brands로 복귀 시 190개 전체 복원
- [x] 카테고리 탭 11개 칩 + 개수 일치, 칩 클릭 시 해당 카테고리만 필터링(브랜드 무관 전체 190개 기준)
- [x] 가격 비교 탭 카드, 카테고리 탭 학습 카드 모두 클릭 시 상세 비교(KCOSERA vs StyleKorean + Product Guidance) 연결
- [x] 학습 카드의 KCOSERA/StyleKorean 링크가 새 탭으로 열림 (`target="_blank" rel="noopener"`)
- [x] 이미지 445개 중 깨진 이미지 0개, 콘솔 에러 0건
- [x] 모바일 폭에서 상세 비교가 1열로 축소되는지 반응형 확인

## 상태

사용자 검토 및 브라우저 검증(파일 직접 열기 포함) 완료 후 `deliverables/html-tools/stylekorean-kcosera-price-comparison-v01/`에 최종본으로 저장되었습니다.
