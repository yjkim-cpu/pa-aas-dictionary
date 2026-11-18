# PA-AAS 공통 데이터 사전 v0 (초안)

전북 피지컬AI 과제의 PA-AAS 서브모델 팩에서 쓰는 **Common data dictionary(공통 데이터 사전)**이다. 한 줄이 값의 종류 하나이고, 그 한 줄이 AAS의 ConceptDescription(개념설명) 하나다.
어느 공정 · 위치 · 채널의 값인지는 사전이 아니라 AAS 안의 자리(상위 묶음 · 요소 이름)와 AIMC · 채널 프로파일이 맡는다.

작성: 한국정보통신기술협회(TTA) AI혁신표준팀 · 생성일 2026-11-18 · 상태: **초안 — 합의 전. 식별자와 정의가 바뀔 수 있다.**
원천: 전북대 P존 AASX 2026-09-29 전체본 86개. 원천 식별자 531종(전북대 자체 · KOSMO 경로)을 값의 종류 기준으로 283줄로 정리했다.

> ⚠ **TTA 주소 `https://aas.tta.or.kr/`는 가안이다**(TTA 전산 확정 전). 확정되면 식별자가 바뀔 수 있으므로 시스템에 고정해 넣지 말 것.

## 들어 있는 것

| 파일 | 내용 | 대응하는 규격 |
|---|---|---|
| `dictionary.csv` | Data dictionary table(데이터 사전 테이블, 283줄) | IDTA-01003-a (Part 3a) — 한 줄이 IEC 61360 데이터 사양을 쓰는 개념설명 하나 |
| `values.csv` | Value list(값 목록, 29줄 · 항목 11개) — 코드형 항목의 값과 뜻, 값마다 식별자 | Part 3a §6.2의 `valueList` (값과 `valueId`의 짝) |
| `units.csv` | Unit table(단위 표, 15종) — 단위 기호마다 ECLASS · IEC 62720 · QUDT · UCUM | Part 3a §6.2의 `unit` · `unitId` (제약 AASc-3a-009: 측정형이면 단위가 있어야 한다) |
| `mapping.csv` | Mapping table(대응표, 540줄) — Source identifier(원천 식별자, 전북대 · KOSMO 531종) → Dictionary identifier(사전 식별자) | 규격 파일은 아님 — IDTA-01001 (Part 1)의 `isCaseOf`(같은 뜻의 다른 정의를 가리키는 칸)에 해당하는 정보를 표로 편 것 |
| `external_refs.csv` | External references(외부 참조, 81종) — 새로 만들지 않고 그대로 쓰는 국제 · 외부 식별자 | IDTA-01001 (Part 1)의 `semanticId` 외부 참조 — IDTA 서식 · ECLASS · IEC CDD(IEC 61360) · W3C WoT |
| `concept-descriptions.json` | Concept descriptions(개념설명 전체, 305건) | IDTA-01002 (Part 2) §7.4 CD 저장소 — GetAllConceptDescriptions, `GET /concept-descriptions` 의 응답 모양 |
| `concept-descriptions/<base64url>.json` | Concept description(개념설명 한 건) — 파일 이름 = 식별자의 base64url | Part 2 §7.4 — GetConceptDescriptionById, `GET /concept-descriptions/{cdIdentifier}` 의 응답 모양 |
| `environment.json` | AAS Environment(AAS 환경 파일) — 개념설명 전체를 담은 것 | IDTA-01001 (Part 1)의 Environment, JSON 직렬화 — CD 저장소 서버 · AASX Package Explorer에 그대로 싣는다 |
| `index.json` | Index(색인) — 식별자 → 이름 · 단위 · 값 · 상태 · 파일, `aliases`: Source identifier(원천 식별자) → Dictionary identifier(사전 식별자) | 규격 파일은 아님 — Part 2 §7.4의 조건 조회(…ByIdShort · …ByIsCaseOf)를 정적 파일로 대신 |

개념설명의 내용은 IDTA-01003-a(Part 3a, IEC 61360 데이터 사양)를 따르고 aas-core3.0으로 검증했다(위반 0건).
이 폴더는 정적 파일이다. Part 2 조회 API의 경로와 응답 모양을 그대로 따랐으므로, CD 저장소 서버를 세우면 `environment.json`을 올려 같은 경로로 제공할 수 있다.

## 수량

- 사전 283줄 — 값 228 · 묶음 51 · 한정자 4 / 상태: 초안 276 · 정의 대기 7
- 분류: 구조 묶음 51 · 구성·사양 49 · 이산 I/O 신호 37 · 장치 기능 신호 34 · 측정값 31 · 생산·작업 정보 21 · 물류·적재 21 · 설비 상태·이벤트 14 · 제어 지령 9 · 연결·식별 9 · 한정자 4 · 시계열 레코드 3
- 개념설명 파일 305건 (항목 276 + 값 29)
- 대응표 540줄 (원천 식별자 531종) · 값 목록 29줄 · 단위 15종 · 외부 식별자 81종

## 식별자 원칙

1. **IDTA 서식 · ECLASS · IEC CDD · W3C 등 국제 식별자는 그대로 쓴다.** 이 사전에 다시 싣지 않는다(`external_refs.csv`).
2. **전북대 자체 식별자와 KOSMO 경로의 식별자는 모두 TTA 사전 항목으로 정리한다**: `https://aas.tta.or.kr/ids/cd/<이름>/<판>/<개정>`
   - 여러 원천이 같은 값의 종류이면 한 줄로 묶는다. 어느 원천이 어느 줄로 갔는지는 `mapping.csv`.
   - KOSMO 공개본에 같은 뜻의 식별자가 있으면 개념설명의 `isCaseOf`에 적는다. KOSMO 재사용 여부는 과제 총괄의 결정 사항이며, 결정에 따라 바뀔 수 있다.
3. **같은 판에서는 내용을 바꾸지 않는다.** 뜻 · 단위 · 값 종류가 바뀌면 판을 올려 새 식별자로 낸다. 오타 · 번역 보강은 개정만 올린다. 폐기해도 지우지 않는다.
4. 값 목록의 값은 `<항목 식별자>/value/<값>` 으로 식별한다.

## 열 (dictionary.csv)

| 열 | 뜻 | 개념설명의 칸 |
|---|---|---|
| semanticId | 식별자 | `id` |
| idShort · name_en · name_ko | 이름 | `idShort` · `preferredName` |
| definition_en · definition_ko | 뜻풀이 | `definition` |
| kind | 값 / 묶음 / 한정자 | `category` (PROPERTY / COLLECTION / QUALIFIER_TYPE) |
| group | 분류 (11가지 + 시계열 레코드) | (표에만) |
| dataType | 값 종류 (IEC 61360) | `dataType` |
| unit | 단위 기호 | `unit` |
| unitId · unit_ucum · unit_qudt | 단위의 ECLASS 식별자 · UCUM · QUDT (`units.csv`에서 붙인 것) | `unitId` (ECLASS) |
| valueList | 허용 값 (식별자는 `values.csv`) | `valueList` |
| sourceOfDefinition | 정의의 출처 | `sourceOfDefinition` |
| isCaseOf | 같은 뜻의 외부 식별자 (KOSMO 공개본) | `isCaseOf` |
| version | 판.개정 | `administration` |
| status | 초안 / 정의 대기 / 확정 / 폐기 | (표에만) |

`정의 대기`는 뜻 · 값 종류 · 단위 가운데 정하지 못한 것이 있어 개념설명 파일로 내지 않은 항목이다.
단위의 ECLASS 식별자는 근거를 확인한 것만 채웠다(`units.csv`의 `basis`).
