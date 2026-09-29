# atlas-odyssey

CUVIC 바이브 코딩 대회 개인 출품작. 대회 규칙은 `docs/competition-rules.md`. 주제·기술 스택은 미정.

## 작업 흐름

작업은 스킬 5개로 단계별로 진행한다. 각 스킬은 자기 단계만 하고 멈춘다. 사용자 지시 없이 다음 스킬로 넘어가지 않는다.

`flow-start` → `flow-work` → `flow-ship` → `flow-review` → `flow-finish`

- 브랜치 이름·커밋 컨벤션·머지 방식·릴리스: `CONTRIBUTING.md`
- 개발 방식: TDD (실패하는 테스트 → 구현 → 리팩토링)
- 보고서: `docs/reports/N-<요약>.md` (파일명 영어, 내용 한국어)
- `docs/commands.md`, `docs/dev-workflow.md`는 아카이빙 예정. 참조하거나 의존하지 않는다

## 문서 갱신 규칙

문서 갱신은 작업의 일부다. 코드를 바꾸면 관련 문서·설정을 같은 브랜치·같은 PR에서 갱신한다.

도구를 추가할 때(예: Python 포매터) 해당하는 것을 전부 갱신한다:

- 의존성 목록 (`requirements.txt`, `pyproject.toml` 등)
- `.vscode/settings.json`, `.vscode/extensions.json`
- `.editorconfig`
- `.gitignore`
- CI 워크플로
- 문서 (설치·실행 방법)

## GitHub

- 원격: `github.com/seren-wib/atlas-odyssey` (public)
- 기본 브랜치 `develop`. squash merge 비활성
- 룰셋: `develop`(rebase·merge commit 허용), `main`(merge commit만). 둘 다 삭제·force push 금지, PR 필수, 승인 0명
- `develop`·`main`에 직접 커밋·푸시하지 않는다. 필요해 보이면 멈추고 사용자에게 알린다

## 배포 (잠정)

Oracle Cloud Always Free (ARM A1, 서울 리전, 재고 없으면 춘천). 예비 GCP e2-micro. `main` 머지 시 GitHub Actions로 배포. 서버 설정은 레포에 코드로 남긴다. 기술 스택 결정 후 확정.

## 환경

- WSL(Ubuntu)과 macOS를 오가며 작업한다
- 줄바꿈은 `.gitattributes`가 LF로 고정한다. `core.autocrlf`는 건드리지 않는다
- gh: WSL은 GitHub 공식 apt 저장소의 최신판(Ubuntu 기본 저장소의 2.45는 `gh pr edit`가 실패함), macOS는 `brew install gh`

### 셸

- `gh ... --body "..."`에 백틱이 들어가면 명령 치환된다. 본문은 따옴표 친 heredoc(`<<'EOF'`)으로 `--body-file -`, 커밋 메시지는 `git commit -F -`. 임시 파일을 레포에 만들지 않는다
