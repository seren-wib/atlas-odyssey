# 단계별 명령어

[개발 흐름](dev-workflow.md) 11단계에서 실제로 쓰는 명령어. 모두 WSL(bash)과 macOS(zsh) 터미널에서 똑같이 동작한다.

`N`은 이슈 번호, `P`는 PR 번호, `<요약>`은 브랜치 이름용 짧은 영어 요약(예: `login-form`).

## 최초 1회 (레포 부트스트랩)

PR을 받을 브랜치가 있어야 하므로 이때만 `main`/`develop`에 직접 푸시한다.

```bash
git commit --allow-empty -m "chore: initial commit"
git push -u origin main
git switch -c develop
git push -u origin develop
```

## 1. 이슈 생성

```bash
gh issue create                                  # 대화형: 제목 입력 후 에디터에서 본문 작성
gh issue create --title "..." --body-file issue.md
```

본문에 백틱(`` ` ``)이 들어가면 `--body` 대신 `--body-file`을 쓴다 ([트러블슈팅](#본문의-백틱이-명령어로-실행됨)).

## 2. 브랜치 생성

```bash
git switch develop
git pull --prune
git switch -c feature/N-<요약>
```

## 3. 작업 (TDD + 문서 갱신)

테스트 실행 명령은 기술 스택 결정 후 추가.

## 4. 보고서 생성

보고서 위치 결정 후 추가.

## 5. 커밋

```bash
git add <파일>
git commit -m "<type>: <summary>"
```

관심사별로 나눠서 커밋한다. 형식은 [CONTRIBUTING](../CONTRIBUTING.md#커밋-컨벤션--conventional-commits).

## 6. 푸시

```bash
git push -u origin feature/N-<요약>
```

## 7. PR 생성

```bash
gh pr create --base develop                      # 대화형: PR 템플릿이 에디터에 채워져 나온다
gh pr create --base develop --title "<type>: <summary>" --body-file pr.md
```

기본 브랜치가 `develop`이라 `--base`를 빼도 develop으로 가지만, 실수 방지로 명시한다. release PR만 `--base main`.

## 8. CI

```bash
gh pr checks P --watch
```

CI 워크플로 작성 후 사용.

## 9. PR diff 리뷰

```bash
gh pr view P --json headRefOid -q .headRefOid    # git rev-parse HEAD와 같은지 먼저 확인
gh pr diff P
```

푸시 직후에는 PR diff가 이전 커밋 기준일 수 있다 ([트러블슈팅](#푸시-직후-gh-pr-diff가-이전-내용을-보여줌)).

Claude Code에서는 `/code-review P`도 돌린다. 단, 결과가 비어 와도 직접 리뷰는 생략하지 않는다 ([트러블슈팅](#code-review-결과가-비어-옴)).

## 10. develop에 rebase merge

```bash
gh pr merge P --rebase
```

## 11. 정리

```bash
git push origin --delete feature/N-<요약>
git switch develop
git pull --prune
git cherry develop feature/N-<요약>              # 전부 '-'면 develop에 다 들어간 것
git branch -D feature/N-<요약>
git cherry develop pr<P>                         # /code-review가 남긴 브랜치가 있으면. 전부 '-'일 때만 아래 줄 실행, '+'가 있으면 지우지 말 것
git branch -D pr<P>
gh issue view N --json state -q .state           # CLOSED인지 확인
```

- `git branch -d`가 아니라 `-D`인 이유: [트러블슈팅](#git-branch--d가-not-fully-merged로-거부됨)
- 이슈가 OPEN이면: [트러블슈팅](#closes-n을-넣었는데-이슈가-안-닫힘)

## 릴리스

아직 해보지 않음. 첫 릴리스 때 추가.

---

# 트러블슈팅

## 본문의 백틱이 명령어로 실행됨

**증상**: ``gh issue create --body "... `.gitignore` ..."`` 실행 시 `.gitignore: command not found`가 뜨고 본문에서 해당 부분이 사라진다.

**원인**: 큰따옴표 안의 백틱은 셸(bash, zsh 모두)에서 명령 치환이다. 백틱 사이 문자열이 명령어로 실행되고 결과로 바뀐다.

**해결**: 본문을 파일로 쓰고 `--body-file`로 넘긴다. 커밋 메시지도 마찬가지로 백틱이 필요하면 `git commit -F msg.txt`.

## git branch -d가 "not fully merged"로 거부됨

**증상**: rebase merge 후 `git branch -d feature/...`가 `error: the branch '...' is not fully merged`로 실패한다.

**원인**: GitHub의 rebase merge는 커밋을 develop 위에 다시 만들기 때문에 해시가 바뀐다. 내용은 같아도 git은 원래 커밋이 머지되지 않았다고 판단한다.

**해결**: `git cherry develop feature/...`로 확인한다. 모든 줄이 `-`면 같은 내용이 develop에 있으니 `git branch -D`로 지운다. `+`가 있으면 develop에 없는 커밋이 있다는 뜻이니 지우지 않는다.

## Closes #N을 넣었는데 이슈가 안 닫힘

**증상**: PR 본문에 `Closes #1`을 넣고 develop에 머지했는데 이슈가 OPEN 그대로다.

**원인**: 키워드 자동 종료는 **기본 브랜치**에 머지될 때만 동작한다. 당시 기본 브랜치가 `main`이었고 PR은 `develop`으로 갔다.

**해결**: GitHub 기본 브랜치를 `develop`으로 변경했다 (2026-09-21). 그래도 OPEN이면 PR 본문에 `Closes #N`이 있는지 확인하고 `gh issue close N --comment "Merged via #P"`로 닫는다.

## gh pr edit가 "Projects (classic) is being deprecated"로 실패

**증상**: `gh pr edit`가 `GraphQL: Projects (classic) is being deprecated ...` 에러로 실패한다.

**원인**: Ubuntu 기본 저장소의 gh가 오래된 버전(2.45)이라 이미 없어진 API를 호출한다.

**해결**: GitHub 공식 apt 저장소에서 최신 gh를 설치한다 ([설치 안내](https://github.com/cli/cli/blob/trunk/docs/install_linux.md)). WSL은 2.101.0으로 올렸다 (2026-09-21). `gh --version`이 2.45면 다시 설치.

macOS는 `brew install gh`로 최신 버전이 설치되므로 해당 없음.

## /code-review 결과가 비어 옴

**증상**: Claude Code에서 `/code-review P`(low)를 돌리면 결과가 `(none)`으로 온다. PR #4에서는 마크다운 코드 스팬이 깨진 버전을 리뷰했는데도 빈 결과였다.

**원인**: 확인하지 못했다. 같은 low 리뷰가 PR #6에서는 발견 사항을 텍스트로 돌려줬으므로 결과 전달 경로의 문제는 아닌 것으로 보인다. PR #4의 버그를 놓친 이유는 알 수 없다.

**해결**: 빈 결과를 통과로 보지 않는다. `gh pr diff P`로 직접 리뷰하는 것이 실제 관문이다. 또한 `/code-review`는 `git fetch origin pull/P/head:prP`로 로컬 브랜치 `prP`를 남기므로 정리 단계에서 지운다.

## 푸시 직후 gh pr diff가 이전 내용을 보여줌

**증상**: 리뷰 지적을 고쳐 푸시한 직후 `gh pr diff P`를 실행했더니 고치기 전 내용이 나왔다 (PR #6). 잠시 후 다시 실행하니 정상.

**원인**: GitHub이 새 커밋을 PR에 반영하기까지 짧은 지연이 있다.

**해결**: 리뷰 전에 `gh pr view P --json headRefOid -q .headRefOid`가 `git rev-parse HEAD`와 같은지 확인한다. 다르면 잠시 후 다시 확인한다.

## Windows 쪽에서 WSL 명령을 실행할 때만 생기는 문제

WSL 터미널이나 macOS 터미널에서 직접 실행하면 생기지 않는다. PowerShell·Git Bash에서 `wsl.exe`로 넘길 때만 해당.

- **`$변수`가 비어서 들어감**: `wsl.exe -- bash -lc '...'`의 인자를 WSL이 한 번 더 해석해 변수가 먼저 풀린다. → 명령을 스크립트 파일로 만들고 `wsl.exe -- bash /mnt/c/.../script.sh`로 실행.
- **`/mnt/c/...` 경로가 `C:/Program Files/Git/mnt/c/...`로 바뀜**: Git Bash가 `/`로 시작하는 인자를 Windows 경로로 자동 변환한다. → `MSYS_NO_PATHCONV=1 wsl.exe ...`
- **`fatal: detected dubious ownership`**: Windows Git이 WSL 파일의 소유자를 다른 사용자로 본다. → WSL 안의 git을 쓴다. Windows Git을 꼭 써야 하면 `git config --global --add safe.directory <경로>`.

## macOS에서 처음 클론할 때

```bash
brew install gh
gh auth login
git clone https://github.com/seren-wib/atlas-odyssey.git
cd atlas-odyssey
git switch develop
```

줄바꿈은 `.gitattributes`가 LF로 고정하므로 `core.autocrlf` 설정은 건드리지 않아도 된다.
