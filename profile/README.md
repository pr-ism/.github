# PR-ism

> 당신의 PR이 프리즘을 통과하면 다채로운 인사이트가 됩니다.

PR-ism은 GitHub Pull Request와 리뷰 과정에서 발생하는 다양한 협업 이벤트를 수집하고,  
이를 팀 단위의 통계와 시각화된 지표로 제공하는 협업 분석 서비스입니다.

코드 리뷰는 단순한 승인 절차가 아니라,  
팀의 속도, 협업 방식, 병목, 그리고 품질 문화를 보여주는 중요한 흐름이라고 생각했습니다.

PR-ism은 리뷰 요청부터 승인, 수정 요청, 머지까지의 과정을 데이터로 추적하여  
리뷰 지연을 줄이고, 병목 구간을 빠르게 파악하며,  
팀이 더 건강한 협업 문화를 만들어갈 수 있도록 돕습니다.

[🌐 서비스 바로가기](https://www.pr-ism.site/)

---

## Why PR-ism

협업 프로젝트를 진행하다 보면 이런 문제를 자주 겪게 됩니다.

- 리뷰 요청이 전달되었지만 놓쳐서 PR이 오래 방치되는 경우
- 어떤 PR이 어디에서 막혀 있는지 한눈에 파악하기 어려운 경우
- 팀 내 리뷰 참여가 특정 인원에게 편중되는 경우
- "협업이 잘 되고 있는가?"를 감각이 아니라 데이터로 확인하고 싶은 경우

PR-ism은 이런 문제를 해결하기 위해 만들어졌습니다.

- 리뷰 요청, 예약, 리마인드 기능으로 리뷰 지연을 줄이고
- PR 흐름 데이터를 기반으로 병목 구간을 분석하며
- 개인/팀 단위의 협업 활동을 지표로 수치화해
- 팀이 실제 협업 상태를 더 명확하게 이해할 수 있도록 합니다.

---

## System Architecture

PR-ism은 GitHub 협업 이벤트를 수집하고, Slack 알림과 통계 서버를 통해 데이터를 처리한 뒤, 웹 대시보드에서 이를 시각화하는 구조로 동작합니다.

<div align="center">
<img width="2048" height="998" alt="4" src="https://github.com/user-attachments/assets/8e0bc1e8-126e-4a61-a679-7d4eb0f6065f" />

</div>

| 구성 요소 | 역할 |
|:---|:---|
| **Front-End Server** | 사용자에게 협업 지표와 분석 결과를 시각적으로 제공 |
| **Slack Bot Server** | 리뷰 요청, 승인, 수정 요청, 리마인드 알림 담당 |
| **Statistics Server** | PR 및 리뷰 메타데이터를 수집·가공하여 통계 계산 |
| **RDB (MySQL)** | 협업 이벤트와 분석 결과 저장 |
| **Monitoring / APM** | 서비스 상태와 성능 모니터링 |

---

## Core Features

### 1. 종합 통계 대시보드

PR 처리 현황, 리뷰 건전성, 팀 협업 활동을 한눈에 파악할 수 있는 대시보드를 제공합니다.

<div align="center">
<img width="1266" height="888" alt="1번" src="https://github.com/user-attachments/assets/daf0d10f-8647-4c9c-b321-4d96031ba39c" />
</div>

- **병목 구간 분석** — PR 생성부터 머지까지의 Cycle Time을 리뷰 대기 / 리뷰 진행 / 병합 대기로 세분화
- **팀 협업 지수** — 리뷰어 수, Round Trip, Gini 계수로 리뷰 분배 균형도 측정
- **리뷰 건전성 진단** — Review Rate, First Review Approve 비율, Changes Requested 비율 등

---

### 2. 팀 협업 지표

작성자와 리뷰어 간의 상호작용 속도, 리뷰 집중도 및 지연 유발 요인을 분석합니다.

<div align="center">

<img width="1138" height="798" alt="2" src="https://github.com/user-attachments/assets/b559402b-7689-4da1-ac46-addbf7d9c99f" />

</div>

- **Review Coverage / Reviewer Concentration** — 리뷰 커버리지와 상위 리뷰어 집중도
- **작성자별 리뷰 대기 시간** — 누구의 PR이 오래 방치되는지 시각화
- **리뷰어 응답 속도 × 작업량 Scatter** — 리뷰어별 부하와 응답 속도 상관관계

---

### 3. 리뷰 처리 속도 분석

PR 대기 시간의 분포와 업무 시간 내 처리 비율을 통해 팀의 코드 리뷰 민첩성을 분석합니다.

<div align="center">
<img width="1266" height="887" alt="3" src="https://github.com/user-attachments/assets/6769e1e4-0fd7-484f-8950-557d63726053" />
</div>

- **P50 / P90 대기 시간** — 중앙값과 롱테일 딜레이 구간을 분리해서 확인
- **Same-Day Review / Core Time Sync** — 당일 처리율과 코어타임 내 처리율
- **승인 후 병합 대기 시간** — 승인 이후 머지까지의 불필요한 지연 감지

---

### 4. Slack 알림 및 리뷰 예약

리뷰어가 지정되면 Slack을 통해 리뷰 요청 알림을 전송하고,  
리뷰어는 "지금 바로", "15분 뒤", "30분 뒤", "1시간 뒤"와 같은 옵션으로  
리뷰 시점을 예약할 수 있습니다.

| 알림 유형 | 동작 |
|:---|:---|
| **리뷰 요청 알림** | 리뷰어 지정 시 Slack으로 즉시 알림 + 리뷰 예약 옵션 제공 |
| **리뷰 완료 알림** | Approve / Request Changes 시 PR 작성자에게 알림 |
| **리뷰 예약 리마인드** | 예약 시간 5분 전 리마인더 발송 (커스텀 가능) |

---

## What We Analyze

PR-ism은 단순히 PR 개수만 보여주는 도구가 아니라,  
리뷰 과정 전체를 분석합니다.

주요 분석 관점은 다음과 같습니다.

- 리뷰가 얼마나 빠르게 시작되는가
- 어느 구간에서 가장 오래 대기하는가
- 같은 날 처리되는 리뷰 비율은 어느 정도인가
- 팀 내 리뷰 참여가 고르게 분산되는가
- 특정 리뷰어에게 업무가 집중되고 있지는 않은가
- 승인 이후 실제 병합까지 또 지연이 발생하는가

이를 통해 팀은 감각이 아니라 데이터 기반으로 협업 방식을 돌아볼 수 있습니다.

---

## Tech Stack

| 영역 | 기술 |
|:---|:---|
| **Frontend** | TypeScript, Next.js, Vercel |
| **Backend** | Java, Spring Boot |
| **Database** | MySQL (AWS RDS) |
| **Infra** | AWS EC2 |
| **Monitoring** | Grafana, Pinpoint (APM) |
| **CI/CD** | GitHub Actions |
| **Integration** | GitHub Webhook, Slack API |

---

## Repositories

| Repository | Description |
|:---|:---|
| [**client**](https://github.com/pr-ism/client) | PR-ism 웹 대시보드 클라이언트 |
| [**statistics-server**](https://github.com/pr-ism/statistics-server) | PR 및 리뷰 통계 분석 서버 |
| [**slack-bot-server**](https://github.com/pr-ism/slack-bot-server) | Slack 알림 및 상호작용 처리 서버 |
| [**github-actions**](https://github.com/pr-ism/github-actions) | GitHub 이벤트 수집 및 자동화 workflow |
| [**coderabbit**](https://github.com/pr-ism/coderabbit) | CodeRabbit 설정 관리 저장소 |

---

## Target Users

PR-ism은 다음과 같은 팀을 위해 설계되었습니다.

- 체계적인 리뷰 문화를 만들고 싶은 소규모 스타트업
- PR 리뷰 병목을 체감하고 있지만 객관적 지표가 없는 개발팀
- 팀 리뷰 현황을 파악하고 개선하려는 팀장 / PM
- 협업 경험을 쌓고 싶은 사이드 프로젝트 / 부트캠프 팀

---


<div align="center">

**PR-ism**과 함께 팀의 코드 리뷰를 더 빠르고, 더 투명하게 만들어보세요.

[서비스 시작하기 →](https://www.pr-ism.site/)

</div>
