# Goal API 예정안

상태: `Proposed`

이 문서는 [Goal 정책](../../features/goal/goal_policy.md)과 [Goal 요구사항](../../features/goal/goal_requirement.md)을 Backend에 적용하기 위한 API 예정안입니다. 실제 구현 전 Draft PR 리뷰를 통해 확정합니다.

## 범위

- 이 문서에서는 인증된 사용자가 자신의 목표를 생성·조회·수정·삭제하는 API만 다룹니다.
- 친구의 현재 목표 조회와 공개 권한·응답 필드는 Friend 기능의 API 문서에서 별도로 정의합니다.

## 기존 Backend 규칙

- Base path는 `/api/v1`을 사용합니다.
- 현재 사용자는 인증 정보로 식별합니다.
- 본문이 있는 성공 응답과 실패 응답은 `ApiResponse<T>`로 감쌉니다.
- 응답 본문이 없는 요청은 `204 No Content`를 사용할 수 있습니다.
- Controller 요청·응답 객체는 Entity와 분리합니다.

## Endpoint

| Method | Endpoint | 설명 |
| --- | --- | --- |
| `POST` | `/api/v1/users/me/goal` | 현재 사용자의 목표 생성 |
| `GET` | `/api/v1/users/me/goal` | 현재 사용자의 목표 조회 |
| `PATCH` | `/api/v1/users/me/goal` | 현재 사용자의 목표 수정 |
| `DELETE` | `/api/v1/users/me/goal` | 현재 사용자의 목표 삭제 |

Goal은 인증된 사용자에게 종속된 단일 리소스입니다. 서버는 인증 정보로 사용자를 식별하며 요청에 `userId`나 `goalId`를 받지 않습니다.

## 현재 목표 조회

목표가 있으면 `200 OK`와 함께 단일 객체를 반환합니다.

```json
{
  "success": true,
  "data": {
    "content": "정보처리기사",
    "goalDate": "2026-12-31"
  },
  "error": null
}
```

무기한 목표는 `goalDate`를 `null`로 반환합니다.

목표가 없으면 `200 OK`와 `data: null`을 반환합니다. 목표 미등록은 신규 등록 화면으로 이어지는 정상 상태로 처리합니다.

```json
{
  "success": true,
  "data": null,
  "error": null
}
```

## 목표 생성 및 수정

생성과 수정 요청은 다음 필드를 사용합니다.

```json
{
  "content": "정보처리기사",
  "goalDate": "2026-12-31"
}
```

- `content`는 앞뒤 공백을 제거한 뒤 2~20자인지 검증합니다. 한글, 영문, 숫자, 공백만 허용하며, 공백만 입력한 값과 줄바꿈, 특수문자, 이모지는 허용하지 않습니다.
- `goalDate`는 nullable이며, 값이 있으면 ISO 8601 날짜 형식을 사용합니다.
- 생성 요청에서 `goalDate`를 생략하거나 `null`로 보내면 무기한 목표로 저장합니다.
- 수정 요청에서 `goalDate`를 `null`로 보내면 기존 목표일을 제거하고 무기한으로 변경합니다.
- 날짜를 입력하면 오늘부터 최대 3년 이내여야 하며 과거 날짜는 허용하지 않습니다.

### Response

- 생성은 `201 Created`, 수정은 `200 OK`를 반환합니다.
- 생성 응답의 `Location`은 `/api/v1/users/me/goal`을 가리킵니다.
- 응답의 `data`는 `content`, nullable `goalDate`를 포함합니다.
- `dDay`는 표시용 파생 값이므로 응답하지 않습니다.
- 클라이언트는 `goalDate`가 없으면 `무기한`으로 표시하고, 값이 있으면 `D-N`, `D-Day`, `D+N`을 계산합니다.

현재 사용자에게 이미 목표가 있으면 생성 요청을 `409 Conflict`로 거부합니다. 목표가 없는 상태에서 수정을 요청하면 `404 Not Found`를 반환합니다.

## 목표 삭제

- 삭제 전 재확인은 클라이언트에서 처리합니다.
- 서버는 인증된 사용자의 현재 목표를 조회한 뒤 삭제합니다.
- 목표와 연결된 할 일과 집중 기록은 삭제하지 않고 연결만 해제합니다.
- 삭제에 성공하면 `204 No Content`를 반환합니다.
- 목표가 없으면 `404 Not Found`를 반환합니다.

## 데이터 제약

- 사용자 식별자를 기준으로 목표가 1개를 초과하지 않도록 데이터베이스 유일성 제약을 적용합니다.
- 생성 시 기존 목표 확인과 삽입은 하나의 트랜잭션으로 처리합니다.
- 데이터베이스 제약 위반은 중복 목표 생성 오류로 변환합니다.

## 회원 정보 연동

Backend에는 현재 회원 정보를 반환하는 `GET /api/v1/users/me`가 구현되어 있습니다.

```json
{
  "success": true,
  "data": {
    "name": "user",
    "tag": "ABCDE",
    "profileImage": null,
    "status": "ACTIVE"
  },
  "error": null
}
```

- Goal API는 인증 정보에서 사용자 ID를 확인하며 요청에 `userId`나 `goalId`를 받지 않습니다.
- Goal 응답에 회원 정보를 중복해서 포함하지 않습니다.
- 화면에서 회원 정보와 목표가 함께 필요하면 User API와 Goal API를 각각 조회합니다.

확인한 Backend 코드:

- `motimate-backend/src/main/java/org/tomorrowworks/motimate/domain/auth/controller/UserController.java`
- `motimate-backend/src/main/java/org/tomorrowworks/motimate/domain/auth/controller/vo/UserResponse.java`
- `motimate-backend/src/main/java/org/tomorrowworks/motimate/global/response/ApiResponse.java`
- `motimate-backend/src/main/java/org/tomorrowworks/motimate/global/security/JwtAuthenticationFilter.java`

## 구현 시 확정할 항목

- Request/Response VO 이름
- `PATCH` 요청에서 필드 생략과 명시적인 `null`을 구분하는 Request 모델 및 부분 수정 방식
- 오늘과 최대 3년 경계를 판정할 날짜·시간대 기준
- 중복 생성 및 목표가 없는 수정·삭제 응답의 애플리케이션 오류 코드
- 사용자당 목표 1개를 보장하기 위한 데이터베이스 제약과 동시성 처리 구현 방식
