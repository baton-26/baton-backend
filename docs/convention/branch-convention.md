# Branch Convention

코히어와 같은 개인 fork 기반 작업 방식과 병합 전략을 사용한다.
개인 fork에서 개발하고 원본 저장소에 PR로 반영한다.

연계 문서: [커밋 컨벤션](./commit-convention.md), [협업 컨벤션](./collaboration-convention.md)

## 1. 저장소와 주요 브랜치

| 이름 | 역할 |
| --- | --- |
| `upstream` | 원본 저장소를 가리키는 로컬 remote 이름 |
| `origin` | 개인 fork를 가리키는 로컬 remote 이름 |
| 원본 `develop` | 개발 통합 브랜치이자 원본 저장소의 기본 브랜치 |
| 원본 `release` | 배포할 변경을 반영하고 CD를 실행하는 고정 브랜치 |
| 개인 작업 브랜치 | 이슈별 코드와 문서를 변경하는 브랜치 |

배포 브랜치는 이름이 정확히 `release`인 브랜치 하나로 운영한다.
`release/1.0.0`처럼 버전별 브랜치를 만드는 방식을 기본 규칙으로 사용하지 않는다.
`main` 등 다른 공용 브랜치를 운영하는 경우에도 공용 브랜치 간 병합 규칙을 따른다.

## 2. 작업 시작

1. 기능 개발이나 수정 작업 전에 원본 저장소에 이슈를 등록한다.
2. 원본 `develop`의 최신 변경을 확인하고 개인 fork의 개발 기준을 맞춘다.
3. 최신 원본 `develop`을 기준으로 개인 fork에 이슈 번호가 포함된 작업 브랜치를 만든다.
4. 작업을 마친 뒤 개인 fork에 push하고 원본 `develop`으로 PR을 보낸다.

개인 fork의 기본 브랜치 이름과 관계없이, 작업 기준은 항상 원본 `develop`이다.
원본 브랜치에 직접 push하거나 원본에 개인 작업 브랜치를 만들어 개발하지 않는다.

## 3. 작업 브랜치 이름

```text
<type>/<issue-number>-<short-description>
```

- 이슈 번호는 필수이며 숫자만 사용한다.
- 설명은 영어 소문자와 하이픈을 사용하고, 2~4단어를 권장한다.
- 공백, 한글, 대문자, 밑줄, `#`을 사용하지 않는다.

| 타입 | 용도 | 예시 |
| --- | --- | --- |
| `feature` | 기능 추가 | `feature/12-login-api` |
| `fix` | 오류 수정 | `fix/15-email-validation` |
| `refactor` | 구조 개선 | `refactor/18-user-service` |
| `docs` | 문서 작성과 수정 | `docs/7-commit-convention` |
| `test` | 테스트 추가와 수정 | `test/21-login-validation` |
| `chore` | 빌드, 의존성, 설정 등 유지보수 | `chore/9-ci-setup` |

이 형식은 개인 작업 브랜치에 적용한다. 고정 공용 브랜치인 `develop`, `release`에는 적용하지 않는다.
브랜치의 `feature`와 커밋의 `feat`처럼 브랜치 타입과 커밋 타입은 서로 다를 수 있다.

## 4. PR 방향과 병합 방식

| PR 방향 | 병합 방식 | 목적 |
| --- | --- | --- |
| 개인 작업 브랜치 → 원본 `develop` | **Squash and merge** | 작업 내용을 하나의 논리적 커밋으로 정리 |
| 원본 `develop` → 원본 `release` | **Create a merge commit** | 기존 작업 이력을 유지하며 배포 브랜치에 반영 |
| 그 외 공용 브랜치 간 병합 | **Create a merge commit** | 두 브랜치가 기존 커밋 이력을 공유하도록 유지 |

### 작업 PR

- 기능과 수정 PR은 하나의 이슈와 하나의 논리적 변경을 중심으로 작성한다.
- Squash는 코드 변경을 모두 반영하면서 대상 브랜치에 커밋 하나를 남긴다.
- PR 제목은 [커밋 컨벤션](./commit-convention.md)을 따른다.
- 원본 GitHub 설정에서 Squash 기본 메시지를 PR 제목과 본문을 사용하도록 맞춘다.
- 병합 전 최종 커밋 메시지와 호환성 변경 표시를 확인한다.
- PR 본문에 `Closes #번호`를 적고, 기본 브랜치인 원본 `develop`에 병합할 때 이슈가 종료되도록 한다.

### 배포 PR

- `develop`에서 검증된 변경을 원본 `release`로 PR한다.
- 배포 PR에는 포함된 작업 PR, 변경 사항, 검증 결과를 정리한다.
- 배포 PR에서도 필수 CI 검사를 통과해야 한다.
- 기존 커밋을 유지하는 Create a merge commit으로 병합한다.
- `release`에 변경이 반영되면 CD가 실행된다. CD 성공을 확인해야 배포 완료로 판단한다.
- 개발 이슈는 `develop` 병합 시 종료한다. 이슈 종료와 배포 완료를 같은 시점으로 간주하지 않는다.

계속 사용하는 공용 브랜치 사이를 Squash하면 기존 커밋과 다른 새 커밋이 만들어진다.
이후 PR에서 이미 반영한 커밋이 다시 보이거나 충돌 처리가 복잡해질 수 있으므로 공용 브랜치 사이에는 Merge commit을 사용한다.
[GitHub 병합 방식 안내](https://docs.github.com/en/pull-requests/reference/pull-request-merges)를 참고한다.

## 5. 병합 후 브랜치 관리

- 병합한 개인 작업 브랜치는 코히어와 동일하게 삭제하지 않고 보존한다.
- Squash 전의 세부 작업 커밋은 개인 작업 브랜치에서 확인할 수 있다.
- 병합한 작업 브랜치는 다음 작업에 재사용하지 않는다. 최신 원본 `develop`에서 새 이슈의 브랜치를 만든다.
- 병합 후 원본 `develop`과 개인 fork의 개발 기준을 다시 동기화한다.
- 원본의 공용 브랜치를 삭제하거나 강제 push하지 않는다.

## 6. 전체 흐름

```text
원본 이슈 생성
→ 최신 원본 develop에서 개인 작업 브랜치 생성
→ 작업과 커밋
→ 개인 fork에 push
→ 원본 develop으로 PR
→ 필수 CI 통과 및 본인 검토
→ Squash and merge
→ 연결된 이슈 종료, 개인 작업 브랜치 보존
→ 배포할 때 원본 develop에서 release로 PR
→ 필수 CI 통과 및 본인 검토
→ Create a merge commit
→ CD 실행 및 성공 확인
```

## 체크리스트

- [ ] 작업 이슈가 있고 작업 브랜치 이름에 이슈 번호를 포함했다.
- [ ] 원본 `develop`의 최신 상태를 기준으로 작업했다.
- [ ] 작업 브랜치는 개인 fork에 두었다.
- [ ] 작업 PR의 대상은 원본 `develop`이며 `Closes #번호`를 작성했다.
- [ ] 필수 CI가 성공했고 변경 내용을 직접 검토했다.
- [ ] 작업 PR은 Squash, 공용 브랜치 간 PR은 Merge commit을 선택했다.
- [ ] 병합한 작업 브랜치를 보존하고 다음 작업에는 새 브랜치를 사용한다.
- [ ] 배포 작업이라면 CD 성공까지 확인했다.
