# 🏎️ IR-Responsive Autonomous Line-Following System

**적외선(IR) 원격 제어 인터페이스와 센서 피드백 루프를 결합한 임베디드 제어 시스템 구현 프로젝트**

단순한 주행을 넘어, **상태 머신(State Machine)** 기반의 동작 제어와 **PWM(Pulse Width Modulation)**을 활용한 정밀 조향 알고리즘을 설계하고 검증했습니다.

---

## 📺 Project Demonstration

> **폴더 내의 영상과 사진 파일 경로를 아래에 맞춰 수정해 주세요.**

| 시스템 외관 (Photo) | 주행 시연 (Demo Video/GIF) |
| :---: | :---: |
| <img src="./images/Line_Tracer.jpg" width="380"> | <img src="./images/demo_video.gif" width="380"> |


---

## 🛠 Embedded System Architecture

본 시스템은 데이터 획득(Sensing), 판단(Logic), 제어(Actuation)의 세 가지 핵심 레이어로 구성되어 있습니다.

### 1. Control Interface (NEC IR Protocol)
* **Status Control:** 적외선 통신을 통해 시스템의 활성화 상태(`isStartLineTracing`)를 결정합니다. 
* **State Mapping:** 특정 커맨드(`0x16`, `0x0D`)를 해석하여 실시간으로 주행 모드와 대기 모드를 전환하는 인터럽트 기반 인터페이스를 구축했습니다.

### 2. Sensing & Data Acquisition
* **Analog Sensing:** 좌/우 IR 반사 센서로부터 유입되는 아날로그 신호를 실시간으로 획득합니다.
* **Threshold Filtering:** `REF_VALUE_LINE_DETECT` 임계값을 설정하여 노이즈를 제거하고, 경로 이탈 여부를 이진화(Binary)하여 판단하는 데이터 전처리 로직을 적용했습니다.

### 3. Differential Steering Algorithm
센서 피드백에 따라 좌우 모터의 출력 차이를 발생시켜 부드러운 경로 추종을 수행합니다.

| 상황 (Scenario) | 좌측 모터 출력 | 우측 모터 출력 | 동작 상태 |
| :--- | :---: | :---: | :--- |
| **Both on Line** | 180 | 180 | 직진 (Forward) |
| **Left Deviation** | 160 | 50 | 우측 교정 (Steer Right) |
| **Right Deviation** | 50 | 160 | 좌측 교정 (Steer Left) |

---

## 📂 Logic Flow Analysis

제공된 소스 코드는 모듈화(Modularity)를 극대화하여 설계되었습니다.

* **`updateLineSensor()`**: 센서 하드웨어로부터 데이터를 읽어오는 추상화 계층.
* **`updateMotorSpeed()`**: 센서 데이터를 기반으로 목표 속도를 산출하는 제어 로직 계층.
* **`processMotor()`**: 산출된 속도값을 PWM 신호로 변환하여 H-Bridge 드라이버를 제어하는 액추에이터 계층.

---

## 🔌 Hardware Pin Mapping

| Peripheral | Connection Pin | Function |
| :--- | :---: | :--- |
| **IR Receiver** | D3 | Remote Signal Input |
| **Line Sensor (L/R)** | A1 / A0 | Analog Path Detection |
| **Motor Left (F/B)** | D9 / D10 | PWM Speed Control |
| **Motor Right (F/B)** | D5 / D6 | PWM Speed Control |

---

