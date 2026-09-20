---
name: requirements-policy
description: Motimate의 기능 요구사항과 서비스 정책을 도출·문서화하고 변경 시 추적성과 일관성을 유지한다. 새 기능 정의, 기존 기능 문서 유지보수, 요구사항·정책 검토에 사용하며 API 명세만 작성하는 요청에는 사용하지 않는다.
---

# Requirements & Policy

사용자의 서비스 설명과 프로젝트 문서를 바탕으로 기능 요구사항과 서비스 정책을 분리해 작성한다. 결정되지 않은 정책은 임의로 확정하지 않고, Requirement와 Policy 사이의 양방향 추적성을 유지한다.

## 작업 전 프로젝트 기준 확인

모든 경로는 저장소 루트를 기준으로 해석한다.

1. `docs/PROJECT_OVERVIEW.md`와 `features/README.md`를 읽는다.
2. 대상 도메인의 기존 Requirement와 Policy가 있으면 두 문서를 모두 읽는다.
3. 문서가 직접 참조하는 자료 중 용어나 확정된 결정에 영향을 주는 문서를 확인한다.

정보가 충돌하면 다음 순서를 우선한다.

1. 사용자가 현재 요청에서 명시한 내용
2. `docs/PROJECT_OVERVIEW.md`
3. 대상 도메인의 기존 Requirement와 Policy
4. User Scenario, User Flow, 화면 설계 등 제품 자료
5. 현재 구현 코드 — 참고 자료일 뿐 정책의 근거로 자동 확정하지 않는다.

코드와 제품 문서가 다르면 불일치로 알린다. 기존 용어, 확정된 정책, 문서 상태를 우선하며 사용자의 명시적 요청 없이 바꾸지 않는다.

## 핵심 원칙

- Requirement는 사용자가 수행할 수 있어야 하는 행동 또는 시스템이 제공해야 하는 관찰 가능한 동작을 정의한다.
- Policy는 기능이 동작하는 규칙, 조건, 기본값, 권한, 예외와 경계를 정의한다.
- 두 문서에 같은 내용을 불필요하게 중복하지 않는다.
- 업계 관행이나 추측을 Motimate의 정책으로 확정하지 않는다.
- 결정되지 않은 횟수, 기간, 권한, 삭제·보존 규칙은 사용자에게 확인한다.
- 기술 구현인 API URL, DTO, DB 컬럼, 클래스, UI 컴포넌트를 제품 정책으로 혼동하지 않는다.
- MVP 범위를 벗어난 기능을 임의로 추가하지 않는다.

## 작업 모드

### Initialize

새 도메인이나 문서가 없는 기능을 정의한다. 먼저 Domain을 식별하고 하나의 Domain 단위로 진행한다. `references/discovery-process.md`를 읽고 다음 순서를 따른다.

`목적 → 행위자 → 정상 흐름 → 상태 → 제약 → 예외 → 경계 → 확정`

질문은 현재 단계에 필요한 소수만 묶어서 하고, 이미 프로젝트 자료에서 확인한 내용은 다시 묻지 않는다. 정상 흐름을 이해하기 전에 세부 예외로 넘어가지 않는다.

### Maintain

기존 문서를 변경하거나 기능을 추가한다.

1. 관련 Requirement와 Policy를 먼저 읽는다.
2. 요청이 기존 결정과 참조에 미치는 영향을 찾는다.
3. 새 정책 결정이 필요한지 확인하고, 미결정 사항만 질문한다.
4. 확정된 범위만 수정하고 양방향 참조와 기능 인덱스를 갱신한다.
5. 관련 없는 내용과 기존 문서 상태는 보존한다.

기존 문서에 ID가 없다면 해당 도메인을 자동 변환하지 않는다. ID 기반 형식으로 전체 마이그레이션할지 사용자에게 먼저 확인하고, 동의하지 않으면 기존 형식을 보존한다.

### Review

문서 검토 요청에서는 `references/review-checklist.md`를 읽고 다음을 검사한다.

- Requirement 또는 Policy 누락, 중복, 충돌
- 양방향 참조 누락 또는 존재하지 않는 ID
- 정의되지 않은 용어와 모호한 표현
- Requirement와 Policy의 책임 혼재
- 상태 전이, 예외, 경계 조건 누락
- 구현 세부사항의 불필요한 혼입
- 미결정 사항을 확정된 것처럼 표현한 내용

먼저 문제와 영향 범위를 설명하고, 사용자의 결정이 필요한 사항과 바로잡을 수 있는 문서 문제를 구분한다. 검토만 요청받았다면 파일을 수정하지 않는다.

## ID와 문서 위치

새 도메인은 다음 ID를 사용한다.

- Requirement: `{DOMAIN}-REQ-{NUMBER}` (예: `FRIEND-REQ-001`)
- Policy: `{DOMAIN}-POL-{NUMBER}` (예: `FRIEND-POL-001`)

Domain 내에서 001부터 증가시키고 삭제된 번호를 재사용하지 않는다. Requirement에는 관련 Policy ID를, Policy에는 관련 Requirement ID를 기록한다.

문서는 다음 위치에 작성한다.

```text
features/<slug>/<slug>_requirement.md
features/<slug>/<slug>_policy.md
```

- `<slug>`는 대상 도메인의 소문자 kebab-case 이름이다.
- 새 문서는 `Proposed` 상태로 시작한다.
- 허용 상태는 `Proposed`, `Accepted`, `Deprecated`이다.
- 기존 문서의 상태는 사용자가 변경을 요청하지 않는 한 유지한다.
- 문서는 사용자가 다른 언어를 요청하지 않는 한 한국어로 작성한다.
- Requirement는 `templates/requirement-template.md`, Policy는 `templates/policy-template.md`를 사용한다.

## 기능 인덱스 동기화

문서를 생성하거나 상태를 변경하면 `features/README.md` 표의 행을 추가하거나 갱신한다.

```markdown
| <Domain> | [요구사항](<slug>/<slug>_requirement.md) | [정책](<slug>/<slug>_policy.md) | `<Status>` |
```

중복 행을 만들지 않고 기존 순서를 보존한다. 두 문서의 상태가 다르면 임의로 통일하지 말고 사용자에게 확인한다.

## 확정과 작성

문서를 쓰기 전에 다음 세 영역을 보여준다.

1. 확정 가능한 Requirement 후보
2. 사용자가 결정한 Policy 후보
3. 아직 결정되지 않은 사항

사용자가 확정한 내용만 최종 문서에 반영한다. 사용자가 명시적으로 요청한 경우에만 미결정 사항을 `TBD`로 표시해 문서에 남긴다.

## 최종 검증

작성 또는 수정 후 `references/review-checklist.md`로 검토하고 다음을 확인한다.

- Requirement와 Policy의 양방향 참조가 실제 ID를 가리킨다.
- 두 문서와 `features/README.md`의 상태 및 링크가 일치한다.
- 정책 규칙의 중복, 충돌, 임의 결정이 없다.
- 구현 세부사항이나 범위 밖 기능을 추가하지 않았다.
- 기존 문서 수정 시 관련 없는 내용을 보존했다.

저장소에 Markdown 검증 도구가 있으면 실행하고, 없으면 상대 링크와 중복 인덱스 행을 직접 검사한다. 완료 시 변경된 문서와 명시적인 가정 또는 보류 결정을 요약한다.
