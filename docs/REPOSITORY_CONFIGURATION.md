## Repository 운영 구조

프로젝트는 역할에 따라 여러 Repository로 분리하여 관리합니다. 각 Repository는 독립적으로 코드와 Issue를 관리하지만, 동일한 개발 흐름과 Repository 설정을 사용합니다.

Repository는 Private을 기본으로 운영하며, 보안 및 민감 정보에 관련된 이슈가 없을 경우 Public으로 전환이 가능합니다.

Repository를 Public으로 전환하게 될 경우 Collaborators와 Branch Protection Rule을 점검합니다.

프로젝트 전체의 작업 현황은 각 Repository가 아닌 Organization의 **프로젝트 별 공용 Project**에서 통합하여 관리합니다.

> **작업을 시작하기 전에 다음 구조를 먼저 이해해주세요.**
> - 코드와 Issue는 각 프로젝트의 Repository에서 관리합니다.
> - 일반적인 개발은 `develop` 브랜치를 `source`로 진행합니다.
> - 작업 브랜치에서 개발한 내용은 PR을 통해 `develop`에 반영합니다.
> - 배포 가능한 상태가 되면 `develop`의 변경사항을 `main`에 반영합니다.
> - Repository 접근 권한은 개인이 아닌 Organization Team을 기준으로 관리합니다.

## Branch

브랜치는 `main`과 `develop`을 중심으로 운영합니다.

```text
main
  └─ develop (default)
       ├─ feat/12/login
       ├─ feat/13/signup
       └─ fix/14/token
```

`main`은 실제 배포가 가능한 코드를 유지하는 **Production 브랜치**입니다.

`develop`은 평소 개발 내용을 통합하는 **Development 브랜치**입니다. 최초 Repository 생성 후 `main`을 Source로 생성하며, Repository의 Default Branch로 지정합니다.

따라서 새로운 기능을 개발하거나 버그를 수정할 때 `main`에서 직접 작업하지 않습니다. `develop`을 기준으로 새로운 작업 브랜치를 생성합니다.

예를 들어 Issue `#12`의 로그인 기능을 구현한다면 다음과 같이 작업할 수 있습니다.

```text
develop
  └─ feat/12/login
```

작업이 완료되면 해당 브랜치에서 `develop`을 Base로 Pull Request를 생성합니다.

```text
feat/12/login
      ↓ PR
   develop
```

여러 작업이 `develop`에 통합되고 배포 가능한 상태가 되면 `develop → main` Pull Request를 생성합니다.

결과적으로 개발한 코드가 이동하는 흐름은 다음과 같습니다.

```text
feat/* → develop → main
```

브랜치를 생성하는 방향과 변경사항을 병합하는 방향을 구분하면 다음과 같습니다.

```text
브랜치 생성
main → develop(default) → feat/*

변경사항 병합
feat/* → develop → main
```

## 작업 명명 규칙

Issue, Branch, Commit, Pull Request는 같은 작업 유형을 기준으로 이름을 작성합니다. 작업의 주된 목적에 가장 가까운 유형 하나를 선택합니다.

| 유형 | Issue 접두사 | 사용 기준 |
| --- | --- | --- |
| `feat` | `[Feat]` | 새로운 기능 추가 |
| `fix` | `[Fix]` | 버그 수정 |
| `design` | `[Design]` | UI 및 디자인 변경 |
| `refactor` | `[Refactor]` | 기능 변경 없는 코드 구조 개선 |
| `test` | `[Test]` | 테스트 추가 및 수정 |
| `docs` | `[Docs]` | 문서 작성 및 수정 |
| `discussion` | `[Discussion]` | 정책·기획·설계안을 리뷰로 논의하고 확정 |
| `chore` | `[Chore]` | 빌드, 설정, 의존성 등의 기타 작업 |
| `infra` | `[Infra]` | 인프라 및 배포 환경 변경 |

### Issue 제목

```text
[<타입>] <작업 내용>
```

예: `[Feat] 회원 가입 기능 구현`

Issue Template을 사용하는 경우 유형에 맞는 접두사가 자동으로 입력됩니다. 제목에는 구현할 작업이 드러나도록 작성하고 끝에 마침표를 붙이지 않습니다.

### Branch 이름

```text
<타입>/<이슈번호>/<작업 요약>
```

예: `feat/12/signup`

Branch 이름의 타입은 소문자를 사용하고, 이슈 번호에는 `#`을 붙이지 않습니다. 작업 요약은 짧은 영문 소문자와 하이픈을 사용합니다.

정책·기획·설계안을 Pull Request 리뷰로 논의하는 경우에는 `discussion` 타입을 사용합니다.

```text
discussion/<이슈번호>/<작업 요약>
```

예: `discussion/14/policy-review`

### Commit 메시지

```text
<타입>: <변경 내용 요약>
```

예: `feat: 회원 가입 이메일 중복 검증 추가`

Commit 타입은 소문자를 사용하고 제목 끝에 마침표를 붙이지 않습니다. 작업 브랜치에서는 변경 과정을 의미 단위로 나누어 Commit할 수 있습니다.

### Pull Request 제목

```text
[<타입>/#<이슈번호>] <변경 내용 요약>
```

예: `[Feat/#12] 회원 가입 기능 추가`

Pull Request 타입은 Issue 접두사와 같은 표기를 사용하며, 관련 Issue 번호를 제목 앞에 함께 작성합니다.

## **Pull Request**

모든 개발 내용은 Pull Request를 통해 통합하는 것을 기본 흐름으로 사용합니다.

Merge 방식은 **Squash Merge**로 통일합니다. 작업 브랜치에서는 개발 과정에 따라 여러 Commit을 생성할 수 있지만, PR을 Merge할 때 하나의 Commit으로 합쳐 `develop` 또는 `main`에 반영합니다.

Squash된 Commit의 Message는 **PR Title**을 사용합니다. 따라서 PR Title은 해당 작업의 내용을 명확하게 표현하도록 작성합니다.

Pull Request는 Organization Project에 추가하지 않습니다. 대신 PR 본문의 `Closes #<이슈번호>`를 통해 해당 Repository의 Issue를 연결하며, PR이 Merge되면 연결된 Issue가 닫히도록 합니다.

정책·기획·설계 문서는 Draft Pull Request로 먼저 공유합니다. 리뷰 댓글에서 쟁점을 논의하고, 합의된 내용을 문서에 반영한 뒤 `Ready for review`로 전환합니다. 리뷰가 끝나기 전에는 정책을 `Accepted`로 표시하지 않습니다.

정책 토의 절차는 다음과 같이 진행합니다.

```text
Issue 생성
  ↓
discussion/<이슈번호>/<작업 요약> 브랜치 생성
  ↓
Draft Pull Request 생성
  ↓
리뷰 댓글로 쟁점 논의
  ↓
문서 수정 및 결정 내용 기록
  ↓
Ready for review 전환
  ↓
승인 후 Squash Merge
```

GitHub Discussions를 사용하지 않고 Pull Request 리뷰를 토의 공간으로 사용합니다. 정책이 확정되면 PR을 병합한 커밋을 기준 문서의 확정 시점으로 기록합니다.

현재 기본으로 사용중인 Repository 들의 Pull Request 설정은 다음과 같습니다.

- Merge Commit: OFF
- Squash Merge: ON
- Rebase Merge: OFF
- Squash Commit Message: PR Title
- Update Branch 제안: ON
- Auto Merge: OFF
- Merge 후 작업 브랜치 자동 삭제: ON

작업 중 Base Branch인 `develop`에 새로운 변경사항이 추가되면 GitHub의 `Update branch` 기능을 이용해 최신 변경사항을 작업 브랜치에 반영할 수 있습니다.

PR이 Merge되면 사용이 끝난 작업 브랜치는 자동으로 삭제됩니다.

## **Branch Protection**

Private Repository를 기본으로 사용하고 있어, Branch Protection Rule은 적용하지 않습니다.

따라서 시스템적으로 직접 Push나 Merge를 제한하지는 않지만, `main`과 `develop`에 직접 작업하지 않고, 작업 브랜치를 생성하여 Pull Request를 통해 변경사항을 반영하는 흐름을 사용합니다.

## **Issue와 Project**

Issue와 Project는 서로 다른 범위에서 관리합니다.

Issue는 실제 코드가 존재하는 **각 Repository에서 생성하고 관리**합니다.

반면 Repository마다 별도의 Project를 만들지는 않습니다. 여러 Repository에서 발생하는 작업을 한곳에서 확인할 수 있도록 Organization의 공용 Project인 **`TomorrowWorks/2`**를 함께 사용합니다.

```text
Meta Repository ─────┐
                     │
App Repository ──────┤
                     ├─→ TomorrowWorks/2
Backend Repository ──┤
                     │
Infra Repository ────┘
```

개발자는 자신이 작업하는 Repository에서 Issue를 생성하고 해당 Issue를 `TomorrowWorks/2`에 추가합니다. Issue Template의 `projects` 설정도 `TomorrowWorks/2`를 사용하여 생성된 Issue가 공용 Project에 연결되도록 구성합니다.

Project의 작업 관리 단위는 Issue입니다. Pull Request는 Project에 별도로 추가하지 않고 관련 Issue에 연결하며, 전체 프로젝트의 진행 상황과 우선순위는 공용 Project에 등록된 Issue를 기준으로 확인합니다.

이를 통해 Repository는 코드의 책임 영역에 따라 분리하면서도 프로젝트 전체의 작업 흐름은 하나의 공간에서 관리할 수 있습니다.

## 문서 및 GitHub Template 배치

전체 문서 구조와 문서별 역할은 [docs/README.md](README.md)를 기준으로 확인합니다.

Repository별 실제 문서와 Template 배치는 다음과 같습니다. 아래 경로는 Repository Root를 기준으로 합니다.

| Repository | 문서 또는 Template | 배치 위치 |
| --- | --- | --- |
| `motimate` | 프로젝트 개요 | `docs/PROJECT_OVERVIEW.md` |
| `motimate` | Repository 운영 규칙 | `docs/REPOSITORY_CONFIGURATION.md` |
| `motimate` | 문서 인덱스 | `docs/README.md` |
| `motimate` | 기능 문서 인덱스 | `features/README.md` |
| `motimate` | Backend 구현 참고 문서 | `docs/backend/` |
| `motimate` | Frontend 구현 참고 문서 | `docs/frontend/` |
| `motimate` | Issue Template | `.github/ISSUE_TEMPLATE/*.yml` |
| `motimate` | Pull Request Template | `.github/PULL_REQUEST_TEMPLATE.md` |
| `motimate-backend` | 프로젝트 소개 및 실행 방법 | `README.md` |
| `motimate-backend` | Pull Request Template | `.github/PULL_REQUEST_TEMPLATE.md` |
| `motimate-app` | 프로젝트 소개 및 실행 방법 | `README.md` |
| `motimate-app` | Pull Request Template | `.github/PULL_REQUEST_TEMPLATE.md` |
| `motimate-infra` | 인프라 소개 및 운영 방법 | `README.md` |

README에는 Repository별 핵심 정보와 문서 링크를 두고, 상세 설계 및 개발 지침은 별도 문서(./docs)로 관리합니다.

## **Access**

Repository 접근 권한은 구성원 개인에게 하나씩 부여하는 방식보다 **Organization Team 단위로 관리**합니다.

각 Team에는 담당하는 Repository에 `Write` 권한을 부여합니다.

```text
Organization
└─ Development Team
   ├─ App Repository      → Write
   ├─ Backend Repository  → Write
   └─ Infra Repository    → Write
```

새로운 개발자가 프로젝트에 참여하면 각 Repository에 개별적으로 권한을 추가하기보다 해당 Team에 구성원을 추가합니다.

이를 통해 Repository가 늘어나거나 구성원이 변경되더라도 Team을 기준으로 일관된 접근 권한을 유지할 수 있습니다.

## **개발 흐름**

새로운 작업을 시작했다면 기본적으로 다음 순서로 진행합니다.

```text
1. 담당 Repository에서 Issue 확인 또는 생성
        ↓
2. develop을 기준으로 작업 브랜치 생성
        ↓
3. 기능 개발 및 Commit
        ↓
4. 작업 브랜치 → develop PR 생성
        ↓
5. Squash Merge
        ↓
6. 작업 브랜치 자동 삭제
        ↓
7. 배포 가능한 상태가 되면 develop → main PR
```

즉, 일반적인 개발에서는 `develop`을 중심으로 작업하고 `main`은 배포 가능한 상태를 유지하는 것을 기본 원칙으로 합니다.


## Repository 설정
새로운 Repository를 생성할 경우 기본적으로 아래의 설정을 따릅니다.

#### General
- **Default branch**
	- `develop`
- **Features**
	- Issues: `ON`
	- Discussions: `OFF`
	- Projects: `OFF`
	- Pull requests: `ON`
		- Pull request permissions →` Collaborators only`
- **Pull Requests**
	- Merge Commit: `OFF`
	- Squash Merge: `ON`
		- `Squash Commit Message: PR Title`
	- Rebase Merge: `OFF`
	- Always suggest updating pull request branches: `ON`
	- Auto Merge: `OFF`
	- Automatically delete head branches: `ON`
#### Commits
- Allow comments on individual commits: `ON`
#### Issues
- Auto-close issues with merged linked pull requests: `ON`
