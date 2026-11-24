# Collaboration Convention

바톤은 개발자 1명이 개인 fork에서 작업하고 원본 저장소에 PR로 반영한다.
코히어의 작업 저장소, PR, 브랜치 보호, 병합 방식을 유지하되 다른 사람의 필수 승인 인원은 0명으로 운영한다.

연계 문서: [작업 지침](../../AGENTS.md), [커밋 컨벤션](./commit-convention.md), [브랜치 컨벤션](./branch-convention.md)

## 1. 운영 기준과 적용 상태

이 문서는 합의한 운영 기준이다. 문서 작성만으로 GitHub 설정이나 Actions 워크플로가 적용되는 것은 아니다.
실제 원본·fork 저장소 연결, 브랜치 준비, Ruleset, CI/CD 워크플로는 별도로 구성하고 검증해야 한다.

| 항목 | 기준 |
| --- | --- |
| 개발 인원 | 1명 |
| 작업 저장소 | 개인 fork |
| 변경 반영 대상 | 원본 저장소 |
| 원본 기본 브랜치 | `develop` |
| 배포 브랜치 | 이름이 정확히 `release`인 고정 브랜치 |
| 개발 PR | 개인 작업 브랜치 → 원본 `develop` |
| 배포 PR | 원본 `develop` → 원본 `release` |
| 필수 승인 인원 | 0명 |
| 병합 조건 | 필수 CI 통과, 충돌 없음, 검토 대화 해결, 본인 검토 |
| 배포 완료 조건 | `release` 반영 후 CD 성공 확인 |

## 2. 개인 fork에서 작업

- 원본 저장소를 개인 계정에 fork하고, 개인 fork를 로컬 작업 저장소에 연결한다.
- 로컬 remote 이름은 개인 fork를 `origin`, 원본을 `upstream`으로 구분한다.
- 작업 이슈는 원본에 등록하고 작업 브랜치는 개인 fork에 만든다.
- 원본에 직접 push하지 않고 PR로 변경을 반영한다.
- 작업 기준과 PR 대상은 원본 `develop`이다.
- 저장소 소유자도 같은 절차를 따른다. 관리자 권한으로 보호 규칙을 우회하는 것을 기본 절차로 사용하지 않는다.

브랜치 이름, 생성 기준, 병합 후 보존 규칙은 [브랜치 컨벤션](./branch-convention.md)을 따른다.

## 3. PR 작성

### 제목

PR 제목은 [커밋 컨벤션](./commit-convention.md)과 같은 형식을 사용한다.
Squash 최종 커밋 제목으로 사용할 수 있도록 변경 내용을 구체적으로 적는다.

```text
feat(auth): 로그인 API 추가
```

### 본문

- 변경 목적, 주요 변경 사항, 실제 검증 결과를 적는다.
- 개발 PR은 원본 이슈를 `Closes #번호`로 연결한다.
- 원본 기본 브랜치를 `develop`으로 설정해야 해당 브랜치에 병합할 때 종료 키워드가 작동한다.
- 다른 저장소의 이슈를 연결해야 한다면 `Closes 소유자/저장소#번호`로 대상을 명시한다.
- 배포 PR은 포함된 작업과 검증 결과를 정리한다. 개발 이슈는 이미 `develop` 병합 시 종료된 상태일 수 있다.
- 호환성이 깨지는 변경은 PR 제목의 `!` 또는 본문의 `BREAKING CHANGE:`로 표시하고 필요한 앱·웹 수정도 설명한다.
- 수행하지 않은 검사나 실패한 검사를 성공으로 적지 않는다.
- AI가 사용자에게 이슈나 PR 본문을 제시할 때는 본문 전체를 하나의 코드 블록으로 제공해 한 번에 복사할 수 있게 한다.

개발 PR 본문 예시는 다음과 같다. 이슈 번호와 검증 결과는 실제 내용으로 바꾼다.

```markdown
## 변경 목적

변경이 필요한 이유를 작성한다.

## 주요 변경 사항

- 주요 변경 내용을 작성한다.

## 검증 결과

- 실제로 실행한 검사와 결과를 작성한다.
- 미실행 또는 실패한 검사가 있으면 이유를 작성한다.

## 관련 이슈

Closes #12
```

[GitHub 이슈 연결 안내](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue)를 참고한다.

## 4. 1인 개발의 검토와 병합

1. PR의 변경 파일을 직접 읽고 이슈의 요구 사항을 충족하는지 확인한다.
2. 관련 없는 변경이나 비밀값이 포함되지 않았는지 확인한다.
3. GitHub Actions의 필수 CI 검사 결과를 확인한다.
4. 충돌과 남아 있는 검토 대화를 해결한다.
5. PR 방향에 맞는 병합 방식을 선택하고 최종 메시지를 확인한 뒤 병합한다.

다른 사람의 승인을 필수로 요구하지 않는다. GitHub에서는 작성자가 자신의 PR을 승인할 수 없으므로 필수 승인 인원은 0명으로 둔다.
팀원 간 리뷰 응답 기한이나 리뷰어 배정 규칙은 두지 않는다.

## 5. 원본 저장소의 브랜치 보호

GitHub의 branch Ruleset으로 원본 브랜치를 보호한다.
이 규칙은 원본 저장소의 보호 기준이며, 개인 fork에서 작업 브랜치를 갱신하는 절차와 구분한다.

| 설정 | 기준 |
| --- | --- |
| Ruleset 이름 | `Protect branch` |
| Enforcement status | `Active` |
| Target branches | 원본의 모든 브랜치, All branches (`~ALL`) |
| 제외 브랜치 | 없음 |
| Bypass list | 비움 |
| Require a pull request before merging | 사용 |
| Required approvals | 0명 |
| Require conversation resolution before merging | 사용 |
| Require status checks to pass before merging | 사용, 빌드·테스트를 포함한 실제 CI 검사 등록 |
| Block force pushes | 사용 |
| Restrict deletions | 사용 |
| Require linear history | 사용하지 않음. 공용 브랜치 간 Merge commit을 허용해야 함 |
| 허용 병합 방식 | Squash and merge, Create a merge commit |

PR 생성은 필수이지만 다른 사람의 승인은 필수가 아니다.
Code Owner 승인이나 마지막 push에 대한 타인의 승인 등 별도 승인 조건도 필수로 설정하지 않는다.

보호를 적용하기 전에 원본의 `develop`, `release`와 기본 브랜치 설정을 준비한다.
Ruleset 적용 후에는 원본에 직접 push하거나, 필수 검사가 실패한 PR을 병합할 수 없는지 확인한다.
[GitHub Ruleset 안내](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets)를 참고한다.

## 6. CI: 병합 전 빌드와 테스트

- 원본으로 들어오는 개발 PR과 배포 PR에서 GitHub Actions CI를 실행한다.
- 빌드와 테스트가 성공해야 병합할 수 있도록 필수 상태 검사로 등록한다.
- 검사 이름은 실제 워크플로 실행 결과에 표시되는 이름을 확인해 등록한다. 아직 없는 검사 이름을 임의로 지정하지 않는다.
- 포맷 검사 등 추가 검사를 도입하면 해당 도구와 워크플로 설정을 함께 관리한다.
- CI 실행 설정과 CI 성공을 병합 조건으로 강제하는 Ruleset 설정을 모두 확인한다.
- 필수 검사가 실패하거나 아직 끝나지 않았다면 원인을 해결하고 검사가 성공한 뒤 병합한다.

## 7. CD: release 병합 후 배포

- 원본 `release`에 변경이 반영되는 `push` 이벤트를 배포 실행 조건으로 사용한다.
- 원본 직접 push는 보호 규칙으로 제한하므로 정상 배포 흐름은 PR 병합을 통해 진행한다.
- 배포 PR의 CI가 성공하면 Create a merge commit으로 `release`에 병합한다.
- 이후 실행되는 CD의 성공을 확인해야 배포 완료로 판단한다.
- CD가 실패하면 배포 완료로 보고하지 않고 원인을 확인한다.
- 병합 후에 실행되는 이 CD 작업을 같은 PR의 병합 전 필수 상태 검사로 지정하지 않는다.

```text
개발 PR → CI 성공 → develop 병합 → 이슈 종료
배포 PR → CI 성공 → release 병합 → CD 실행 → CD 성공 → 배포 완료
```

## 8. 병합 메시지와 브랜치 보존

- 원본에서 Squash 기본 메시지를 PR 제목과 본문을 사용하도록 설정한다.
- 실제 병합 화면에서 최종 커밋 메시지가 컨벤션에 맞는지 확인한다.
- 작업 PR은 Squash, 공용 브랜치 간 PR은 Merge commit을 사용한다.
- 병합한 개인 작업 브랜치는 보존하며 자동 삭제 대상에 두지 않는다.
- 저장소에 두 병합 방식을 허용했다고 PR 방향에 맞는 방식이 자동 선택되는 것은 아니므로 병합할 때 확인한다.

## 체크리스트

- [ ] 원본 기본 브랜치가 `develop`이고 배포 브랜치가 `release`다.
- [ ] 개인 fork에서 작업하고 원본으로 PR을 보냈다.
- [ ] 개발 PR에 연결할 이슈와 `Closes #번호`가 있다.
- [ ] 필수 승인 인원은 0명이며, 변경 내용을 직접 검토했다.
- [ ] 실제 CI 검사가 필수 상태 검사로 등록되어 있고 모두 성공했다.
- [ ] 충돌과 남아 있는 검토 대화를 해결했다.
- [ ] PR 방향에 맞는 병합 방식과 최종 메시지를 확인했다.
- [ ] 병합 후 개인 작업 브랜치를 보존했다.
- [ ] 배포 작업이라면 CD 성공을 확인했다.
