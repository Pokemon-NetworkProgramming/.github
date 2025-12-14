# ⚔️ Java Socket 1v1 포켓몬 배틀 게임

## Java Swing + Socket 프로그래밍 기반 실시간 턴제 전략 게임

![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Swing](https://img.shields.io/badge/Swing-GUI-blue?style=for-the-badge)
![Socket](https://img.shields.io/badge/Socket-Networking-green?style=for-the-badge)
![JSON](https://img.shields.io/badge/JSON-Data-lightgrey?style=for-the-badge)

## 📋 목차

- [프로젝트 소개](#-프로젝트-소개)
- [팀원 및 역할](#-팀원-및-역할)
- [주요 화면](#-주요-화면)
- [시스템 아키텍처](#-시스템-아키텍처)
- [주요 기능](#-주요-기능)
  - [멀티 스레드 소켓 서버](#1--멀티-스레드-소켓-서버)
  - [정교한 배틀 시스템](#2-️-정교한-배틀-시스템)
  - [GUI 애니메이션 & 연출](#3--gui-애니메이션--연출)
- [기술 스택](#-기술-스택)
- [프로젝트 구조](#-프로젝트-구조)
- [통신 프로토콜](#-통신-프로토콜)
- [실행 방법](#️-실행-방법)

---

## 📌 프로젝트 소개

본 프로젝트는 **Java 소켓 통신(TCP/IP)**과 **Swing GUI**를 활용하여 구현한 1대1 실시간 네트워크 포켓몬 배틀 게임입니다.
중앙 집중식 서버를 통해 다수의 클라이언트가 접속하여 대기실(로비)에서 방을 생성하고 매칭을 진행하며, 포켓몬의 스탯(Speed), 타입 상성, 기술(Move) 등을 고려한 정교한 턴제 전투를 경험할 수 있습니다.

### 🎯 개발 목표

- **네트워크 심화:** `ServerSocket`과 `Thread`를 활용한 1:N 비동기 통신 및 동기화 처리 학습
- **객체 지향 설계:** 데이터 모델(Pokemon, Move)과 로직(BattleManager), 뷰(Swing Panel)의 분리
- **실시간 상호작용:** 턴제 게임의 상태 동기화(HP, PP, 교체 등) 및 예외 처리(연결 끊김, 기절 등) 구현

---

## 👥 팀원 및 역할

|       이름 (학번)       | 기여도 | 담당 역할 및 주요 업무                                                                                                                                                                               |                                                         GitHub                                                          |
| :---------------------: | :----: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------: |
| **홍성환**<br>(2171115) |  50%   | **Full-stack Developer**<br>• 서버 아키텍처 설계 (`ServerApp`, `GameRoom`)<br>• 배틀 로직 구현 (`BattleManager`, `DamageCalculator`)<br>• 네트워크 통신 모듈 구현 (`NetworkClient`, `ClientHandler`) |   [![GitHub](https://img.shields.io/badge/-Github-181717?style=flat-square&logo=github)](https://github.com/12hsh12)    |
| **박준민**<br>(2171114) |  50%   | **Full-stack Developer**<br>• 데이터 구조 설계 및 로더 구현 (`DataLoader`, JSON 파싱)<br>• 로비 및 포켓몬 선택 UI 구현<br>• 클라이언트 UI/UX 디자인 및 애니메이션 구현                               | [![GitHub](https://img.shields.io/badge/-Github-181717?style=flat-square&logo=github)](https://github.com/davidpark300) |

---

## 🎥 주요 화면

|   **1. 게임 로비 (Lobby)**    |     **2. 포켓몬 선택 (Selection)**     |
| :---------------------------: | :------------------------------------: |
|                               |                                        |
| 방 생성, 방 목록 갱신 및 입장 | 랜덤 배정된 6마리 중 3마리 엔트리 선택 |

|     **3. 배틀 진행 (Battle)**      |   **4. 기술 이펙트 & 결과 (Effect)**   |
| :--------------------------------: | :------------------------------------: |
|                                    |                                        |
| HP바 애니메이션, 스킬/교체 선택 UI | 타입별 공격 모션, 로그 출력, 승패 판정 |

---

## 🏗 시스템 아키텍처

```bash
┌─────────────────────────────────────────────────────────────┐
│                       Game Server                           │
│                                                             │
│  [ServerApp] ◀───────── [ClientHandler] ◀──┐              │
│       │                       ↕              │              │
│  [GameLobby] ──────────▶ [GameRoom]         │              │
│       │                       │              │              │
│       ▼                       ▼              │              │
│  (Room List)           [BattleManager]       │              │
│                               │              │              │
│                       [DamageCalculator]     │              │
└──────────────────────────────────────────────┼──────────────┘
                                               │ TCP/IP Socket
                                               │
┌─────────────────────────┐           ┌────────┴────────────────┐
│       Client A          │           │        Client B         │
│                         │           │                         │
│   [MainFrame (GUI)]     │           │   [MainFrame (GUI)]     │
│          │              │           │          │              │
│    [NetworkClient] ─────┼─────────▶│    [NetworkClient]      │
│                         │           │                         │
│      [UI Panels]        │           │      [UI Panels]        │
│   - LobbyPanel          │           │   - LobbyPanel          │
│   - PokemonSelectPanel  │           │   - PokemonSelectPanel  │
│   - BattlePanel         │           │   - BattlePanel         │
│                         │           │                         │
└─────────────────────────┘           └─────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                     Shared Core (Common)                    │
│                                                             │
│   [DataLoader] ──▶ [JSON DB (Pokemon/Move/Type)]           │
│        │                                                    │
│        ▼                                                    │
│   [Data Models] (Pokemon, Move, TypeChart)                  │
└─────────────────────────────────────────────────────────────┘
```

### 계층별 역할

#### 🖥️ Server (`com.pokemon.game.server`)

- **GameRoom:** 두 클라이언트 간의 게임 세션을 관리하며, `handleBattleAction`을 통해 턴을 동기화합니다.
- **BattleManager:** 실제 데미지 계산, 선공/후공 판정, 기절(Faint) 처리, 승패 판정 등 핵심 로직을 수행합니다.
- **ClientHandler:** 각 클라이언트와 별도 스레드로 통신하며 메시지를 송수신합니다.

#### 🎮 Client (`com.pokemon.game.client`)

- **MainFrame:** `CardLayout`을 사용하여 로비, 선택, 배틀 화면을 전환합니다.
- **UI Panels:** `BattleTopScreen`(애니메이션), `BattleBottomScreen`(컨트롤), `BattleMiddleScreen`(로그)로 구성되어 있습니다.
- **NetworkClient:** 서버로부터 수신된 패킷(`UPDATE_HP`, `LOG` 등)을 파싱하여 UI 이벤트를 트리거합니다.

---

## ✨ 주요 기능

### 1. 🔄 멀티 스레드 소켓 서버

- **방(Room) 기반 매칭:** `GameLobby`에서 방을 생성하고 관리하며, 2명이 모이면 자동으로 게임이 시작됩니다.
- **동시성 처리:** `synchronized` 키워드를 사용하여 채팅 및 게임 상태 동기화 시 스레드 간 충돌을 방지했습니다.
- **연결 관리:** 유저가 강제 종료하더라도 `exitUser` 메서드를 통해 상대방에게 부전승을 알리고 방을 정리합니다.

### 2. ⚔️ 정교한 배틀 시스템

- **데미지 공식:** `(공격력 / 방어력) * 위력` 기반에 **타입 상성(2.0x, 0.5x)**, **자속 보정(1.5x)**, **급소(1.5x)**, **랜덤 난수(0.85~1.0)**가 모두 적용됩니다.
- **Shift Rule (교체 룰):** 상대 포켓몬이 기절했을 때, 다음 포켓몬을 꺼내기 전 교체할 기회를 부여합니다.
- **PP (Power Point):** 기술 사용 시 PP가 차감되며, 0이 되면 사용할 수 없습니다.
- **상태 이상 & 기절:** HP가 0이 되면 `FAINT` 패킷을 전송하고 강제 교체 화면(`FORCE_SWITCH`)으로 전환됩니다.

### 3. 🎨 GUI 애니메이션 & 연출

- **리소스 프리로딩:** 게임 시작 시 `BattleTopScreen`에서 이미지 리소스를 미리 캐싱하여 끊김 없는 연출을 제공합니다.
- **HP 바 애니메이션:** 데미지를 입었을 때 `Timer`를 사용하여 체력이 서서히 줄어드는 효과를 구현했습니다.
- **콜백(Callback) 구조:** `공격 모션` → `HP 감소` → `로그 출력("효과가 굉장했다!")` 순서가 꼬이지 않도록 `Runnable` 콜백 체인을 사용했습니다.

---

## 🛠 기술 스택

|     분류     |        기술        | 비고                                              |
| :----------: | :----------------: | :------------------------------------------------ |
| **Language** | **Java (JDK 11+)** | 핵심 로직 및 전체 구현                            |
|   **GUI**    |   **Java Swing**   | Custom Component, CardLayout, Timer               |
| **Network**  |  **Socket (TCP)**  | `java.net.Socket`, `java.io.PrintWriter`          |
|   **Data**   |      **JSON**      | Google GSON 라이브러리 사용                       |
|  **Design**  |  **MVC Pattern**   | Model(Data), View(UI), Controller(Server/Manager) |

---

## 📂 프로젝트 구조

```
src/main/java/com/pokemon/game/
├── core/                # (공통) 데이터 모델 및 로더
│   ├── data/            # DataLoader, TypeChart, FontLoader
│   ├── model/           # Pokemon, Move (데이터 객체)
│   └── utils/           # TypeUtils (상성 계산), KoreanUtil (한글 조사 처리)
│
├── client/              # (클라이언트) GUI 및 네트워크
│   ├── ui/              # MainFrame, BattlePanel, BattleTop/BottomScreen 등
│   ├── ClientApp.java   # 클라이언트 실행 진입점
│   └── NetworkClient.java # 서버 통신 모듈
│
└── server/              # (서버) 게임 로직 및 스레드 관리
    ├── ServerApp.java   # 서버 실행 진입점
    ├── GameRoom.java    # 1:1 배틀 세션 및 패킷 처리
    ├── BattleManager.java # 턴 계산, 데미지 로직, 로그 생성
    ├── DamageCalculator.java # 데미지 공식 계산기
    └── ClientHandler.java # 클라이언트별 스레드
```

---

## 📡 통신 프로토콜

서버와 클라이언트는 문자열 기반의 커스텀 프로토콜(`Command:Payload`)을 사용합니다.

| Command            | Payload 예시           | 설명                                    |
| :----------------- | :--------------------- | :-------------------------------------- |
| `ACTION`           | `ATTACK:0`             | 0번 스킬로 공격 요청                    |
| `ACTION`           | `SWAP:2`               | 2번 포켓몬으로 교체 요청                |
| `LOG`              | `0:피카츄의 10만볼트!` | 전투 로그 전송 (0: 플레이어 인덱스)     |
| `UPDATE_HP`        | `OPP:0:50`             | 상대(OPP) 0번 포켓몬의 HP를 50으로 갱신 |
| `SERVER_UPDATE_PP` | `0:1:14`               | 0번 플레이어의 1번 스킬 PP를 14로 갱신  |
| `FAINT`            | `1:25`                 | 1번 플레이어의 포켓몬(ID 25) 기절       |
| `GAME_OVER`        | `VICTORY`              | 게임 승리 알림                          |

---

## ▶️ 실행 방법

1.  **데이터 파일 확인:** `src/main/resources` 폴더에 `pokemon_db.json`, `moves_db.json`, `type_chart.json` 및 이미지 리소스가 있는지 확인합니다.
2.  **서버 실행:** `com.pokemon.game.server.ServerApp.java`를 실행하여 포트(예: 30000)를 엽니다.
3.  **클라이언트 실행:** `com.pokemon.game.client.ClientApp.java`를 실행합니다. (테스트를 위해 2개 실행)
4.  **게임 진행:**
    - 클라이언트 1: "방 만들기" -> 방 이름 입력 -> 대기
    - 클라이언트 2: 방 목록에서 해당 방 더블 클릭 -> 입장
    - 양쪽에서 포켓몬 3마리 선택 후 "선택 완료" 클릭 -> 배틀 시작!

---

## 📄 라이선스

이 프로젝트는 교육 목적으로 제작되었으며, 포켓몬스터의 저작권은 **Nintendo, Creatures, GAME FREAK**에 있습니다.
