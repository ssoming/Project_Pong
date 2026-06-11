# Pong Remaster — Basys3 RTL Project
 
> Basys3 FPGA에서 Verilog RTL로 구현한 Pong 게임. DQN 기반 AI 대전 모드 탑재.
 
[![Notion](https://img.shields.io/badge/Notion-프로젝트%20상세%20보기-000000?style=for-the-badge&logo=notion&logoColor=white)](https://app.notion.com/p/minseokim-profile/FPGA-339b5d65c68c80cb91f2fc9ec11dd616?source=copy_link)
 
## 개요
 
| 항목 | 내용 |
|------|------|
| 플랫폼 | Digilent Basys3 (Artix-7 FPGA) |
| 언어 | Verilog HDL |
| 도구 | Vivado |
| 해상도 | 800×480 @ 60Hz |
| 개발 기간 | 2026.04 |
| 팀 구성 | 5인 |
 
## 게임 모드
 
- **1:1 모드** — 두 플레이어가 버튼으로 패들을 직접 조작
- **AI 모드** — Google Colab에서 학습한 DQN 가중치를 Verilog 상수로 내장해 P2를 자동 제어
- **Predict 모드** — 공의 속도 벡터로 도착 위치를 예측해 P2 패들을 이동. 파라미터로 난이도 조절 가능
## 파일 구성
 
| 파일 | 역할 |
|------|------|
| `codes.v` | 최상위 모듈 — 클럭 분주, VGA 타이밍, 게임 물리, 렌더링 통합 |
| `ai.v` | DQN 추론 (5→8→3, 조합 논리) |
| `Pre.v` | 공 궤적 예측 기반 자동 패들 제어 |
| `sound.v` | 이벤트별 구형파 사운드 출력 |
 
## 주요 구현
 
- **VRAM 없는 실시간 렌더링** — h_cnt/v_cnt로 매 픽셀마다 조합 논리로 오브젝트 판단
- **하드웨어 난수** — 33.3MHz로 순환하는 카운터의 득점 순간 값을 공 스폰 위치로 사용
- **AI → RTL 변환** — Colab DQN 가중치를 정수 스케일로 변환 후 Verilog 상수로 직접 내장
- **오리지널 Pong 사운드** — v_cnt 비트를 오디오 출력에 직결해 구형파 생성 (아타리 방식 재현)
- **8구역 반사각** — 패들 타격 위치(rel_y)에 따라 dy를 -4~+4로 결정
