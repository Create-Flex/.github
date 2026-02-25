# Create-Flex 프로젝트 종합 보고서
---

## 1. 프로젝트 개요

**Create-Flex**는 크리에이터 매니지먼트 전문 플랫폼으로, 크리에이터와 담당 직원(매니저/관리자)이 하나의 시스템에서 협업할 수 있도록 설계된 인트라넷 서비스입니다.

- **서비스 유형**: B2B 사내 크리에이터 관리 시스템
- **사용자 역할**: EMPLOYEE(일반 직원), MANAGER(매니저), ADMINISTRATOR(관리자), CREATOR(크리에이터)
- **핵심 가치**: 역할별 맞춤 뷰 제공, 실시간 협업, 크리에이터 생애주기 통합 관리

---

## 2. 기술 스택

### 2-1. 프론트엔드

| 항목 | 기술 |
|------|------|
| UI 프레임워크 | React 18 |
| 스타일링 | styled-components |
| 상태 관리 | Zustand |
| 실시간 통신 | STOMP over SockJS (WebSocket), SSE |
| 드래그앤드롭 | @hello-pangea/dnd |
| HTTP 클라이언트 | Axios |
| 아이콘 | lucide-react |
| 알림 | react-hot-toast |
| 빌드 도구 | Vite |
| 총 소스 파일 | 약 167개 (JS/JSX) |

### 2-2. 백엔드

| 항목 | 기술 |
|------|------|
| 언어 / 프레임워크 | Java 17 / Spring Boot 3.5.9 |
| 보안 | Spring Security + JWT (Access Token: Body, Refresh Token: HttpOnly Cookie) |
| ORM | Spring Data JPA + MySQL 8.0 |
| 캐시 / 세션 | Redis |
| 파일 스토리지 | AWS S3 (Presigned URL) |
| 실시간 통신 | STOMP WebSocket (칸반), SSE (알림) |
| AI | Spring AI + OpenAI |
| 이메일 | Spring Mail (JavaMail) |
| API 문서 | Swagger (springdoc-openapi 2.8.5) |
| 빌드 도구 | Gradle |
| 총 소스 파일 | 약 247개 (Java) |

---

## 3. 시스템 아키텍처

![파이널_프로젝트_시스템_아키텍처_최종](https://github.com/user-attachments/assets/fd0b0143-b845-43f1-b92f-85d5605c18f6)


### 인증 흐름

```
로그인 요청 (사번 + 비밀번호)
    └── JWT 발급
         ├── Access Token  → Response Body (프론트: Zustand/메모리 보관)
         └── Refresh Token → HttpOnly Cookie (XSS 방어)
```

---

## 4. 도메인 및 기능 목록

### 4-1. 백엔드 도메인 (20개)

| 도메인 | 설명 | 주요 엔티티 |
|--------|------|------------|
| `auth` | 인증 (로그인/로그아웃/토큰 재발급) | - |
| `member` | 회원 기본 정보, 역할/상태 관리 | Member, MemberProfile, MemberCreatorDetail, MemberEmployeeDetail |
| `creator` | 크리에이터 정보, 담당 매니저 연결 | - |
| `creatorTodo` | 칸반 보드 (컬럼/카드 CRUD + WebSocket) | CreatorTodoColumn, CreatorTodo |
| `vacation` | 연차/반차/병가/경조사/워케이션 신청·승인 | Vacation, VacationFamily, VacationSick, VacationWorkation |
| `attendance` | 출퇴근 기록 관리 | Attendance |
| `health` | 건강검진 결과, 크리에이터 정신건강 관리 | Health, CreatorMentalHealth, CheckupSummanary |
| `schedule` | 일정 캘린더 (크리에이터/직원) | Schedule, ScheduleVisitor |
| `chat` | 채팅방 생성·참여, 메시지 (Redis) | ChatRoom, ChatRoomMember, ChatMessage |
| `notification` | SSE 실시간 알림, 읽음 처리 | Notification |
| `advertisement` | 광고 캠페인 제안 및 승인 | CreatorPromotion |
| `contract` | 크리에이터 계약 관리 | CreatorContract |
| `legalTax` | 법률/세무 연결 지원 | CreatorLegalTax |
| `ai` | AI 챗 (OpenAI 기반 크리에이터 분석) | - |
| `department` | 부서 관리 | Department |
| `team` | 팀 구성, 업무 릴레이 | Team, TeamRelay |
| `employee` | 직원 프로필 조회 | - |
| `image` | 이미지 업로드 (S3 Presigned URL) | - |
| `QnA` | Q&A 게시판 | QABoard |
| `notification` | 알림 SSE 스트림 | Notification |

### 4-2. 프론트엔드 기능 (역할별)

#### 공통
- JWT 인증/로그아웃
- 실시간 알림 (SSE)
- 채팅 (WebSocket/Redis)
- 프로필 조회·수정 (아바타/커버 이미지 업로드)
- 비밀번호 변경

#### EMPLOYEE / MANAGER
- 담당 크리에이터 목록 조회
- 크리에이터 상세 → **칸반 보드** (실시간 동기화)
- 일정 캘린더 (크리에이터 일정 통합)
- 광고 캠페인 관리
- 법률/세무 연결 지원
- 크리에이터 건강 모니터링

#### CREATOR
- 내 일정 캘린더
- 건강검진 결과 조회
- 마이페이지 → **칸반 보드** (본인 업무 현황)

#### ADMINISTRATOR
- 전체 크리에이터/직원 관리
- 연차 신청 승인·반려
- 건강 정보 통합 관리
- 부서/팀 관리

---

## 5. 프로젝트 폴더 구조

### 5-1. 프론트엔드 (`src/`)

```
src/
├── api/                        # 공통 Axios 인스턴스
├── app/                        # 라우팅, 앱 진입점
├── assets/                     # 정적 자원 (이미지 등)
├── components/                 # 공용 UI 컴포넌트
├── features/                   # 기능별 모듈 (Feature-Sliced 구조)
│   ├── ai/                     # AI 챗
│   ├── attendance/             # 출퇴근
│   ├── auth/                   # 인증 (로그인/로그아웃)
│   ├── calendar/               # 일정 캘린더
│   ├── chat/                   # 채팅
│   ├── creator/                # 크리에이터 관리
│   ├── creator-todo/           # 칸반 보드
│   ├── employee/               # 직원 프로필
│   ├── health/                 # 건강 관리
│   ├── notification/           # 알림 (SSE)
│   ├── organization/           # 조직도
│   ├── qa_board/               # Q&A 게시판
│   ├── support/                # 법률/세무 지원
│   └── vacation/               # 연차 관리
└── shared/                     # 전역 공유 모듈
    ├── constants/              # 열거형, 상수 (enums.js)
    ├── model/                  # 전역 상태 (useUIStore)
    └── utils/                  # 공통 유틸 (mapper 등)
```

각 feature 모듈은 `api/`, `model/`, `ui/` 로 계층 분리:
- `api/` : Axios 호출 함수
- `model/` : Zustand 스토어
- `ui/` : React 컴포넌트 및 styled-components

---

### 5-2. 백엔드 (`src/main/java/com/mcn/in4/`)

```
com.mcn.in4/
├── In4Application.java
├── config/                     # AWS S3, JavaMail 설정
├── global/
│   ├── aws/                    # S3 업로드 유틸
│   ├── config/                 # Security, WebSocket, Redis, Swagger, AI 설정
│   ├── error/                  # 공통 예외 처리 (CustomException, ErrorCode)
│   ├── security/               # JWT Filter, Token Provider
│   ├── sse/                    # SseEmitters (알림 연결 풀)
│   └── util/                   # Cookie 유틸
└── domain/                     # 비즈니스 도메인 (20개)
    └── {domain}/
        ├── controller/         # REST 컨트롤러 + API 인터페이스 (Swagger)
        ├── dto/
        │   ├── request/        # 요청 DTO
        │   └── response/       # 응답 DTO
        ├── entity/             # JPA 엔티티
        ├── repository/         # Spring Data JPA Repository
        └── service/            # 서비스 인터페이스 + 구현체
```

---

## 6. 주요 API 엔드포인트

### 인증 (`/api/auth`)
| Method | Path | 설명 |
|--------|------|------|
| POST | `/api/auth/login` | 로그인 (Access Token 반환, Refresh Token Cookie 설정) |
| POST | `/api/auth/logout` | 로그아웃 |
| POST | `/api/auth/reissue` | Access Token 재발급 |

### 크리에이터 (`/api/creators`)
| Method | Path | 설명 |
|--------|------|------|
| GET | `/api/creators/{creatorId}` | 크리에이터 단건 조회 |
| PUT | `/api/creators/{creatorId}` | 크리에이터 정보 수정 |
| DELETE | `/api/creators/{creatorId}` | 크리에이터 삭제 |
| GET | `/api/creators/manager/{managerId}` | 담당 매니저의 크리에이터 목록 |
| GET | `/api/creators/my` | 내 크리에이터 정보 |

### 칸반 보드 (`/api/creator-todo` + WebSocket)
| Method | Path | 설명 |
|--------|------|------|
| GET | `/api/creator-todo/{creatorId}` | 보드 전체 조회 (컬럼+카드) |
| DELETE | `/api/creator-todo/{todoId}` | 카드 삭제 |
| WS | `/pub/todo/move` | 카드 이동 (STOMP 발행) |
| WS | `/pub/todo/create` | 카드 생성 알림 |
| WS | `/pub/todo/update` | 카드 수정 알림 |
| WS | `/pub/todo/delete` | 카드 삭제 알림 |
| SUB | `/sub/creator-todo/{creatorId}` | 보드 실시간 구독 |

### 연차 (`/api/admin/vacations`, `/api/vacations`)
| Method | Path | 설명 |
|--------|------|------|
| GET | `/api/vacations/my` | 내 연차 신청 목록 |
| GET | `/api/vacations/my/remainder` | 잔여 연차 조회 |
| GET | `/api/vacations/my/stats` | 연차 통계 |
| PUT | `/api/admin/vacations/{vacationId}/approve` | 연차 승인 (관리자) |
| PUT | `/api/admin/vacations/{vacationId}/reject` | 연차 반려 (관리자) |

### 출퇴근 (`/api/attendance`)
| Method | Path | 설명 |
|--------|------|------|
| GET | `/api/attendance/dashboard/my` | 내 출퇴근 현황 |
| GET | `/api/attendance/dashboard/company` | 전사 출퇴근 현황 |
| POST | `/api/attendance/check-in` | 출근 |
| POST | `/api/attendance/check-out` | 퇴근 |

### 일정 (`/api/schedules`)
| Method | Path | 설명 |
|--------|------|------|
| GET | `/api/schedules/me` | 내 일정 목록 |
| GET | `/api/schedules/creator` | 크리에이터 일정 목록 |
| POST | `/api/schedules/` | 일정 등록 |
| DELETE | `/api/schedules/{scheduleId}` | 일정 삭제 |

### 채팅 (`/chat`)
| Method | Path | 설명 |
|--------|------|------|
| POST | `/chat/room` | 채팅방 생성 |
| GET | `/chat/rooms/my` | 내 채팅방 목록 |
| GET | `/chat/room/{roomId}/messages` | 채팅 메시지 조회 |
| PUT | `/chat/room/{roomId}/name` | 채팅방 이름 변경 |

### 알림 (`/api/notifications`)
| Method | Path | 설명 |
|--------|------|------|
| GET | `/api/notifications/subscribe` | SSE 알림 구독 |

---

## 7. 주요 기술 구현 사항

### 7-1. 칸반 보드 실시간 동기화 (WebSocket STOMP)

- STOMP `/pub/todo/move` 발행 → 백엔드 DB 업데이트 후 `/sub/creator-todo/{creatorId}` 브로드캐스트
- 발신자: 드래그 완료 즉시 Optimistic Update (서버 응답 대기 없음)
- 수신자: `MOVE_SUCCESS` 수신 시 `todoId`, `columnId`, `newPosition` 파싱 후 로컬 상태 직접 반영
- `clientUuid`로 자신이 보낸 메시지 필터링 (중복 업데이트 방지)
- 카드 생성(`TODO_CREATED`), 수정(`TODO_UPDATED`), 삭제(`TODO_DELETED`)도 동일한 브로드캐스트 방식으로 실시간 동기화

---

### 7-2. 인증 보안 설계

- **Access Token**: Response Body 전달 → Zustand(메모리)에 보관, 새로고침 시 재발급
- **Refresh Token**: `HttpOnly Cookie` → XSS 공격으로부터 토큰 보호
- **STOMP 인증**: `StompHandler`가 WebSocket 연결 시 JWT 검증 (인터셉터 방식)

---

### 7-3. 파일 업로드 (AWS S3 Presigned URL)

```
1. 프론트 → 백엔드: 업로드 요청 (POST /api/image/...)
2. 백엔드 → 프론트: Presigned URL 반환
3. 프론트 → S3: Presigned URL로 직접 파일 PUT
```
백엔드 서버를 통하지 않고 S3에 직접 업로드하여 서버 부하 최소화

---

### 7-4. SSE 실시간 알림

- 백엔드: `SseEmitters`로 연결 풀 관리
- 프론트: SSE 연결 timeout 시 자동 재연결 처리
- 알림 이벤트: 휴가 신청/승인, 새 메시지 등

---

### 7-5. AI 기능 (Spring AI + OpenAI)

- 크리에이터 로그인 시 AI 자동 분석
- `AiChatController`를 통한 AI 채팅 기능

---

## 8. 최근 주요 작업 이력 (Git 기반)

| PR # | 내용 |
|------|------|
| #194 | 칸반 보드 WebSocket 생성 기능 추가 |
| #191 | SSE timeout 처리 및 사이드바 연차 pending 인디케이터 제거 |
| #188 | 채팅방 제목 수정 및 참가자 조회 구현 |
| #187 | 연차 필터링 페이지네이션 버그 수정 |
| #184 | 건강검진 모달 전역 변수 오류 수정 |
| #182 | 채팅 읽음 처리 숫자 구현 (Zustand 상태 관리) |
| #180 | N+1 쿼리 최적화 |
| #178 | 크리에이터 AI 분석 수정 |
| #177 | SSE 알림 기능 구현 |
| #176 | 연차 관리 통계 카드 업데이트 |
| #173 | Q&A 게시판 기능 완료 |
| #170 | 채팅 UI 및 기능 구현 |

---

## 9. 프로젝트 통계

| 항목 | 수치 |
|------|------|
| 프론트엔드 소스 파일 | ~167개 (JS/JSX) |
| 백엔드 소스 파일 | ~247개 (Java) |
| 백엔드 도메인 | 20개 |
| 프론트엔드 기능 탭/뷰 | 20개 이상 |
| REST API 컨트롤러 | 21개 |
| WebSocket 컨트롤러 | 1개 (CreatorTodoWebSocketController) |
---

*본 보고서는 `create-flex-frontend` 및 `create-flex-backend` 소스코드 직접 분석을 기반으로 작성되었습니다.*
