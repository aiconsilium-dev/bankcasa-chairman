# PRD — 뱅크법률집사 (이사장 전용 앱)

## 1. Product Overview

| 항목 | 내용 |
|------|------|
| **서비스명** | 뱅크법률집사 — 이사장 (BankCasa Chairman) |
| **한 줄 설명** | 신협·단위농협·새마을금고 이사장이 경영 대시보드, 연체·법적조치 현황, 조합원 관리를 수행하는 전용 앱. bankcasa-demo와 동일 코드베이스에서 `role=chairman`으로 진입. |
| **대상 사용자** | 신협·단위농협·새마을금고 이사장 |

## 2. Tech Stack

| 구분 | 기술 |
|------|------|
| 프레임워크 | React 19 + TypeScript |
| 빌드 도구 | Vite 8 |
| 스타일링 | Tailwind CSS |
| 라우팅 | HashRouter (react-router-dom) |
| 상태 관리 | Context API (AppProvider) |
| 저장소 | localStorage |
| 배포 | GitHub Pages (gh-pages 브랜치) |

> **참고:** bankcasa-demo와 동일 코드베이스. URL 파라미터 `role=chairman`으로 진입.

## 3. Architecture

```
SPA (bankcasa-demo 동일 코드베이스)
├── URL 진입: #/?role=chairman&inst=xxx&name=xxx
├── AppProvider → role='chairman' 설정
├── AppLayout
│   ├── 좌측 사이드바 (이사장 전용 메뉴)
│   └── 메인 콘텐츠 영역
├── ChairmanDashboard
│   ├── KPI 카드 4종
│   └── 연체 테이블
└── Pages/ (bankcasa-demo와 공유)
```

## 4. Pages & Routes

### 사이드바 메뉴 (이사장 전용)

| 메뉴 | 경로 | 설명 |
|------|------|------|
| 집변 법률상담 | 외부 링크 | `homelawyer.kr` — 검정 강조 |
| 경영 대시보드 | `/` | KPI + 연체 현황 |
| 연체·법적조치 현황 | `/report` | 법적 조치 리포트 |
| AI 법령·지침 검색 | `/regulation-search` | 법령/내규/지침 검색 |
| 조합원 게시판 | `/board` | 게시판 열람 + **댓글 작성 가능** |
| 공지 발송 | `/notice` | 조합원 대상 공지 발송 |
| 채권계산기 | `/calculator` | 채권 계산 |
| 마이페이지 | `/mypage` | 사용자 정보 |

### 경영 대시보드 상세

#### KPI 카드 (4종)

| KPI | 설명 |
|-----|------|
| 이번달 법적조치 | 당월 법적 조치 건수 |
| 연체 90일+ | 90일 이상 연체 건수 |
| 변호사비용 절감 | AI 서류 자동화로 절감한 비용 |
| BEP 달성률 | 손익분기점 달성 비율 |

#### 연체 테이블

| 컬럼 | 설명 |
|------|------|
| 조합원번호 | 조합원 식별 번호 |
| 성명 | 조합원 이름 |
| 연체일수 | 연체 경과 일수 |
| 잔액 | 연체 잔액 |
| 담보 | 담보물 유형 |
| 권고조치 | AI 권고 법적 조치 |
| [처리] | 처리 액션 버튼 |

## 5. Data Models

bankcasa-demo와 동일 + 이사장 전용 데이터:

```typescript
interface User {
  institution: string;
  name: string;
  role?: 'teller' | 'member' | 'chairman';
}

// 연체 테이블 행
interface OverdueRecord {
  memberNo: string;     // 조합원번호
  name: string;         // 성명
  overdueDays: number;  // 연체일수
  balance: number;      // 잔액
  collateral: string;   // 담보
  recommendation: string; // 권고조치
}

// KPI 데이터
interface ChairmanKPI {
  legalActionsThisMonth: number;
  overdueOver90: number;
  costSaving: number;
  bepRate: number;
}
```

## 6. Key Features

### 6.1 경영 대시보드
- KPI 4종 카드 요약
- 연체 현황 테이블 (정렬/필터 가능)
- 각 행에서 처리 액션 실행

### 6.2 연체·법적조치 현황 리포트
- 전체 연체 현황 상세 뷰
- 법적 조치 진행 상태 트래킹
- 기간별 통계

### 6.3 조합원 게시판
- 게시글 열람
- **댓글 작성 가능** (이사장 권한)
- 조합원 소통 채널

### 6.4 공지 발송
- 조합원 대상 공지사항 작성·발송

### 6.5 AI 법령·지침 검색 / 채권계산기
- 행원 앱과 동일 기능

## 7. Design System

| 속성 | 값 |
|------|-----|
| 레이아웃 | 좌측 사이드바 + 메인 콘텐츠 (데스크탑 최적화) |
| 배경색 | `#ffffff` |
| 텍스트색 | `#000000` |
| 구분선 | `1px solid #e5e5e5` |
| KPI 카드 | 4열 그리드, 숫자 강조 |
| 연체 테이블 | 행 호버 효과, 처리 버튼 우측 |
| 폰트 | Pretendard |

## 8. External Integrations

| 서비스 | URL | 연동 방식 |
|--------|-----|-----------|
| 집변 (법률 상담) | `homelawyer.kr` | `window.open()` — 사이드바 최상단, 검정 강조 |
| 법령정보 | `law.go.kr` | 외부 링크 |

## 9. Deployment

| 항목 | 값 |
|------|-----|
| 호스팅 | GitHub Pages |
| 브랜치 | `gh-pages` |
| URL | `https://{owner}.github.io/bankcasa-chairman/` |
| 진입 | URL 파라미터 `#/?role=chairman&inst=xxx&name=xxx` |

## 10. Known Limitations

- **bankcasa-demo와 동일 코드베이스** — URL 파라미터로 역할 분기
- **모든 데이터는 목업** — KPI, 연체 테이블 모두 하드코딩
- **AI 상담은 정적 의사결정 트리** — LLM 미연동
- **공지 발송은 UI만** — 실제 발송 기능 미구현
- **게시판 댓글은 localStorage** — 서버 저장 없음
- **인증/인가 없음**
