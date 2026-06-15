# 🎮 Pong Remaster — Basys3 RTL Project

> Basys3 FPGA에서 Verilog RTL로 구현한, DQN 기반 AI 대전 모드를 탑재한 Pong 게임

[![Notion](https://img.shields.io/badge/Notion-프로젝트%20상세%20보기-000000?style=for-the-badge&logo=notion&logoColor=white)](https://app.notion.com/p/minseokim-profile/FPGA-339b5d65c68c80cb91f2fc9ec11dd616?source=copy_link)

---

## 1. 개요 (Overview)

| 항목 | 내용 |
|------|------|
| 플랫폼 | Digilent Basys3 (Artix-7 FPGA) |
| 언어 | Verilog HDL |
| 도구 | Vivado |
| 해상도 | 800×480 @ 60Hz |
| 개발 기간 | 2026.04 |
| 팀 구성 | 5인 팀 프로젝트 |

---

## 2. 담당 역할 (My Role)

- Predict 모드 설계 및 구현 — 공의 속도 벡터 기반 도착 위치 예측, 데드존·반응 주기·오차 파라미터로 난이도 제어
- pong_top 통합

---

## 3. 주요 기능 (Key Features)

- **1:1 / AI / Predict 모드** — 두 플레이어 대전, DQN 자동 제어, 궤적 예측 자동 제어 3가지 모드 전환
- **AI → RTL 변환** — Colab DQN 가중치를 Verilog 상수로 내장, 클럭 없는 조합 논리로 신경망 추론
- **실시간 렌더링** — VRAM 없이 h_cnt/v_cnt 기반 매 픽셀 조합 논리로 오브젝트 판단 및 사운드 재현

---

## 4. 기술 스택 및 아키텍처 (Tech Stack & Architecture)

![Verilog](https://img.shields.io/badge/Verilog-FF6B35?style=for-the-badge&logo=v&logoColor=white)
![Vivado](https://img.shields.io/badge/Vivado-E01F27?style=for-the-badge&logo=xilinx&logoColor=white)
![FPGA](https://img.shields.io/badge/Basys3-0096D6?style=for-the-badge&logo=digilent&logoColor=white)

![Pong_Flowchart](Pong_Flowchart.png)

---

## 5. 핵심 구현 및 트러블슈팅 (Key Implementation & Troubleshooting)

### 핵심 구현

**① VRAM 없는 실시간 렌더링**
h_cnt/v_cnt가 모니터 픽셀 위치를 추적하며, 매 픽셀마다 조합 논리로 오브젝트 포함 여부를 즉시 판단해 색상을 출력한다. 별도의 프레임 버퍼 없이 동작한다.

**② AI → RTL 변환 (DQN)**
Google Colab에서 학습한 Deep Q-Network 가중치를 정수 스케일로 변환해 Verilog 상수로 직접 내장한다. 신경망 추론 전체가 클럭 없는 조합 논리 회로로 동작한다.

**③ 공 궤적 예측 (Predict 모드)**
현재 공의 속도 벡터를 기반으로 패들 도달 위치를 사전 계산하여 P2 패들을 이동시킨다. 반응 주기(`REACTION_DIV`), 데드존(`DEADZONE`), 오차(`ERROR_RANGE`) 파라미터로 난이도를 조절한다.

### 트러블슈팅

| 발생 문제 | 발생 원인 | 해결 방안 | 결과 |
|-----------|-----------|-----------|------|
| Predict 모드 패들이 너무 정확해 게임이 성립되지 않음 | 예측 오차 없이 항상 최적 위치 이동 | `ERROR_RANGE` 파라미터로 의도적 오차 주입 | 난이도 조절 가능한 자연스러운 대전 구현 |
| 공 속도 증가 시 패들 반응이 뒤처짐 | `REACTION_DIV` 고정값으로 반응 주기 부족 | 속도 단계별 반응 주기 동적 조정 | 고속 구간에서도 안정적 추적 |

---

## 6. 디렉토리 구조 (Directory Structure)

| 파일 | 역할 |
|------|------|
| `codes.v` | 최상위 모듈 — 클럭 분주, VGA, 게임 물리, 렌더링 통합 |
| `ai.v` | DQN 추론 (5→8→3, 조합 논리) |
| `Pre.v` | 공 궤적 예측 기반 자동 패들 제어 |
| `sound_.v` | 사운드 서브 모듈 |
| `sound.v` | 이벤트별 구형파 사운드 출력 |
