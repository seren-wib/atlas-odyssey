# docs: write project plan (#17)

## 작업 내용

- 주제가 학습 지도로 돌아온 것을 반영해 중간 제출 문서를 작성했다. 문서 원본은 Google Drive `atlas-odyssey` 폴더의 Google Docs다.
  - 프로젝트 계획서: 문제 정의, 목표, 범위, 개발 방식, 일정, 위험
  - 요구사항 명세서(SRS) 초안: 기능 요구사항 FR-1~6, 비기능 요구사항, 데이터 요구사항, 단위 테스트 계획, 미결 사항
  - AI 워크플로우 및 환경 설정: 도구 선정, flow 스킬 5단계, 보고서와 규칙 개선 사례, 환경, 프롬프트 전략
  - 중간 발표 문서: 위 세 문서를 발표 양식 네 항목으로 요약 (PDF 5장 이내)
- 저장소에는 `README.md`를 새로 만들어 문서 링크를 달았다.
- `CLAUDE.md`의 "주제·기술 스택은 미정"을 현재 주제와 문서 위치로 바꿨다.
- `docs/notes/2026-10-15.md` 맨 위에 주제 변경으로 계획이 폐기됐다는 안내를 붙였다.

## 변경 파일

- `README.md` (신규)
- `CLAUDE.md`
- `docs/notes/2026-10-15.md`
- `docs/reports/17-project-plan.md` (신규)

## 테스트

- 코드 변경 없음. 문서 작업이라 테스트 대상이 없다.
- 중간 발표 문서를 PDF로 내보내 페이지 수(5장 이내)와 표·제목 배치를 확인했다.

## 트러블슈팅

| 증상 | 원인 | 해결 |
| --- | --- | --- |
| HTML로 올린 문서의 표 열 너비가 모두 같게 나와 긴 설명 열이 좁게 눌림 | Drive의 HTML 변환이 열 너비를 균등 분배함 | Docs API `updateTableColumnProperties`로 열마다 고정 너비를 주고, 머리글 행에 배경색과 굵게를 적용 |
| HTML의 `page-break-before`가 무시됨 | Drive 변환이 이 CSS를 페이지 나눔으로 바꾸지 않음 | 제목에 `keepWithNext`를 걸어 제목이 페이지 끝에 혼자 남지 않게 함 |
| `update_file`로 기존 문서 내용을 바꿀 수 없음 | Drive 커넥터의 `update_file`은 메타데이터만 바꿈 | 제출 문서를 새로 만들었다. 먼저 만든 "atlas-odyssey 중간 기획안" 문서는 삭제 여부를 사용자에게 확인 |

## 남은 과제

- Drive 문서 공유 설정: GitHub 저장소가 public이므로 README의 링크를 심사자가 열려면 "링크가 있는 사용자 보기"로 바꿔야 한다.
- SRS 확정(요구사항 인터뷰), SDD 작성, 기술 스택 결정
- README를 readme-style 형식으로 다시 쓰기
