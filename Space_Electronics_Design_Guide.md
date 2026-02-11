# 우주용 전자 시스템 설계 가이드

## 목차
- [1. 개요](#1-개요)
- [2. 우주 환경 이해](#2-우주-환경-이해)
- [3. PCB 설계](#3-pcb-설계)
- [4. 커넥터 및 연결 방법](#4-커넥터-및-연결-방법)
- [5. 보드 고정 시스템](#5-보드-고정-시스템)
- [6. 열관리](#6-열관리)
- [7. 전원 시스템](#7-전원-시스템)
- [8. 방사선 대응](#8-방사선-대응)
- [9. 표준 및 인증](#9-표준-및-인증)
- [10. 주요 제조업체](#10-주요-제조업체)
- [11. CubeSat 특수 사례](#11-cubesat-특수-사례)
- [12. 설계 체크리스트](#12-설계-체크리스트)

---

## 1. 개요

### 1.1 우주용 vs 방산용 핵심 차이

**우주용 전자 시스템의 특징**
- **극한 환경**: -180°C ~ +150°C, 고진공
- **방사선**: 총 선량 100+ krad, SEU/SEL
- **무게**: 극도로 중요 (kg당 $10k-$50k)
- **신뢰성**: No-failure 설계 원칙
- **냉각**: 전도/복사만 가능 (대류 불가)

### 1.2 설계 철학

**방산 설계**
- Modular, field-replaceable
- Cost-performance balance
- Standard connectors

**우주 설계**
- Ultra-reliable, redundant
- Reliability > cost
- Custom/qualified components
- Mass optimization 최우선

### 1.3 적용 범위

본 가이드는 다음 우주 시스템에 적용됩니다:
- 인공위성 전자 시스템
- 우주 탐사선
- CubeSat / SmallSat
- 우주정거장 장비

---

## 2. 우주 환경 이해

### 2.1 환경 조건

| 항목 | 지상/방산 | 우주 |
|------|-----------|------|
| **온도** | -40°C ~ +85°C | -180°C ~ +150°C |
| **압력** | 대기압 | 10^-6 ~ 10^-15 Torr |
| **방사선** | 거의 없음 | 100+ krad (TID) |
| **진동** | MIL-STD-810 | NASA-STD-7001 |
| **충격** | 수십 G | 수백 G (발사 시) |

### 2.2 주요 위험 요소

**진공 (Vacuum)**
- Outgassing: 휘발성 물질 방출
- 광학계 오염 위험
- 대류 냉각 불가

**극저온 (Cryogenic)**
- 재료 수축/균열
- 솔더 조인트 파손
- 전기적 특성 변화

**열 사이클 (Thermal Cycling)**
- 궤도당 90분 주기
- 일식/일조 반복
- CTE 불일치 문제

**방사선 (Radiation)**
- Total Ionizing Dose (TID)
- Single Event Effects (SEE)
- Displacement Damage

**발사 진동/충격**
- 수백 G 충격
- 광대역 진동 (20-2000 Hz)
- Acoustic loading

---

## 3. PCB 설계

### 3.1 재질 선택

**FR-4 사용 불가 이유**
- Outgassing (TML > 1%)
- 극저온 균열
- 방사선 내성 낮음

**우주 인증 재질**

#### 1. Polyimide (Kapton 기반)

**제조사**: DuPont
**제품**: Pyralux, Kapton

**특성**
- Outgassing: TML < 1%, CVCM < 0.1%
- 온도: -269°C ~ +400°C
- 방사선 내성: 우수
- 유연성: Flex/Rigid-flex 가능

**단점**
- 고비용
- 제조 난이도

#### 2. Cyanate Ester (CE)

**제조사**: Rogers, Isola
**제품**: Rogers RO4000 series

**특성**
- Low outgassing
- 저유전손실 (Df < 0.005)
- 낮은 CTE
- Rigid 구조

**용도**: 고주파 RF 보드

#### 3. PTFE (Teflon)

**제조사**: Rogers, Taconic
**제품**: RT/duroid, TLY

**특성**
- Extreme low outgassing
- 화학적 안정성
- 고주파 성능 우수

**단점**: 기계적 강도 낮음

#### 4. Ceramic Substrate

**재질**: Alumina (Al2O3), AlN

**특성**
- 방사선 완전 면역
- 열전도 우수 (AlN: 170 W/m·K)
- 극저온 안정성
- CTE 매칭 (Si와 유사)

**용도**: 고신뢰성 전원, CPU

**제조**: Kyocera, CoorsTek

### 3.2 Layer Stack-Up

**8-10층 권장** (방산 14-16층 대비 감소)

**이유**
- Via reliability 극대화
- 무게 절감
- Outgassing 최소화

**예시 (10층)**
```
Layer 1:  Signal (High-Speed Differential)
Layer 2:  GND Plane (2oz copper)
Layer 3:  Signal (Mid-Speed)
Layer 4:  PWR Plane (Split: 3.3V / 5V)
Layer 5:  GND Plane
Layer 6:  Signal (High-Speed Differential)
Layer 7:  GND Plane
Layer 8:  PWR Plane (12V / 28V)
Layer 9:  Signal (Low-Speed)
Layer 10: GND Plane (2oz copper)
```

**특징**
- Symmetrical stack-up 필수 (warping 방지)
- Thicker copper: 2-3 oz
- Impedance tolerance: ± 5% (방산 ± 10%)

### 3.3 Via 설계

**Through-hole via 선호**
- Blind/buried via 신뢰성 문제
- 열 사이클 취약

**Via 보강**
- Via plugging: Epoxy fill + copper cap
- Thermal stress relief: Tear-drop pad
- Redundant via: Critical net 2개 이상

**IPC-6012 Class 3/A**
- Via barrel: 최소 25 μm (방산 18 μm)
- Aspect ratio: < 8:1

### 3.4 임피던스 제어

**Tighter tolerance**
- ± 5% (방산 ± 10%)
- 온도 변화 고려
- TDR 전수 검사

---

## 4. 커넥터 및 연결 방법

### 4.1 왜 VPX를 사용하지 않는가?

**문제점**
- Press-fit/solder 신뢰성 문제
- 무게 과다
- Thermal cycling 취약
- Backplane PCB 무게

### 4.2 우주용 표준 커넥터

#### 1. Micro-D Connectors

**제조사**: Glenair, Cinch

**특징**
- 극소형, 경량
- 고밀도 (50-100 pins)
- Hermetic seal 옵션
- Screw-lock 메커니즘

**용도**: Board-to-board, board-to-harness

**가격**: $100-300 (25-pin)

#### 2. Nano-D / Nano-Miniature

**제조사**: Omnetics Connector Corporation

**특징**
- 초소형 (세계 최소 D-sub)
- 무게: < 1g
- High cycle life (500+)

**용도**: CubeSat

**가격**: $150-400 (25-pin)

#### 3. MIL-DTL-38999 (Circular)

**제조사**: Amphenol, TE Connectivity

**특징**
- 우주 검증 완료
- Hermetic, environmentally sealed
- High power capability

**용도**: 전원, 신호 혼합

**가격**: $300-800

#### 4. CCSDS Standards

**용도**: SpaceWire, CAN bus

### 4.3 연결 방법론

**Point-to-Point Harness 선호**

**이유**
1. 신뢰성: Mating/unmating 최소화
2. 무게: Backplane PCB 제거
3. 열관리: 각 보드 독립 냉각
4. 유연성: 배선 자유도

**구조**
```
[보드 A]---[Micro-D]---[Harness]---[Micro-D]---[보드 B]
   |                                              |
[Chassis mount]                            [Chassis mount]
```

**Harness 고정**
- Lacing cord: Nomex, Kevlar
- Tie-down: 50-75mm 간격
- 공진 방지

---

## 5. 보드 고정 시스템

### 5.1 Wedge Lock 사용 금지

**이유**
- 발사 시 수백 G에서 이탈 가능
- 궤도 진동으로 loosening
- 신뢰성 부족

### 5.2 Direct Bolt Mounting (표준)

**구성**
```
     보드 PCB
   [=========]
   |    |    |
   볼트 볼트 볼트
   |    |    |
   [Chassis]
```

**부품**

**Standoff**
- 재질: 알루미늄, 티타늄 (경량)
- 제조: PEM, Accurate Screw Machine

**Bolts**
- 표준: NAS (National Aerospace Standard)
- 재질: Stainless steel, Titanium
- Locking: Safety wire 또는 Lock nut

**Thermal Interface**
- Thermal grease/pad
- 제조: Chomerics, Bergquist
- Conduction cooling 필수

**Bolt Pattern**
- 4-6 points (균등 분포)
- Torque specification 준수
- Torque stripe (도색 검증)

### 5.3 Edge Clamping (보조)
```
    [Clamp bar]
    |         |
    | ======= | ← PCB
    |         |
    [Chassis rail]
```

- 보드 엣지 압착
- Thermal conduction 동시 제공
- 큰 보드에 볼트와 병행

### 5.4 Potting / Conformal Coating

**Potting**
- Epoxy encapsulation
- 재질: Stycast, Hysol (space-qualified)
- 용도: 소형 모듈, 센서

**Conformal Coating**
- Parylene, silicone
- 습기/오염 방지
- Outgassing 시험 필수

### 5.5 Lacing & Tie-Down

**용도**: Harness 고정

**재질**
- Lacing cord: Nomex, Kevlar
- Tie: Velcro (space-grade), cable ties

**간격**: 50-75mm마다

---

## 6. 열관리

### 6.1 우주에서의 열전달

**대류 냉각 불가**
- 진공 환경
- 전도/복사만 가능

**Conduction Path**
```
[Component] → [Thermal via] → [PCB] → [Interface] → [Chassis] → [Radiator]
```

### 6.2 Thermal Vias

**설계**
- High-power 부품 아래 via array
- Via fill: copper or thermal epoxy
- 밀도: 0.5mm pitch grid

### 6.3 Metal Core PCB (MCPCB)

**구조**
- Aluminum base (1-2mm)
- Dielectric layer
- PCB layer

**특성**
- 열전도: > 3 W/m·K
- 제조: Bergquist, Laird

### 6.4 Chassis Interface

**Thermal Gap Filler**
- 제품: Chomerics T-pli, Bergquist Gap Pad
- 두께: 0.5-2mm
- 열전도: 3-8 W/m·K
- Compression 고려

### 6.5 Heat Pipes (고급)

**용도**: Hot spot → Radiator 이송

**제조**
- Advanced Cooling Technologies
- Thermacore

---

## 7. 전원 시스템

### 7.1 전원 버스

**28VDC 표준**
- Nominal: 28V
- Range: 22-34V
- Point-of-Load conversion

### 7.2 Redundancy

- Dual redundant bus
- Cross-strapping
- Current limiting (latch-up 보호)

### 7.3 Capacitors

**Space-qualified**
- Tantalum: AVX TRR/TRS
- Ceramic: Kemet, Johanson (C0G/X7R)

**De-rating**
- 50% voltage
- 60% temperature
- 예: 50V cap을 25V max 사용

### 7.4 전원 분배

**구조**
- 각 보드 독립 PoL converter
- Local regulation
- Filtering

---

## 8. 방사선 대응

### 8.1 방사선 효과

**Total Ionizing Dose (TID)**
- LEO: 10-100 krad
- GEO: 100+ krad
- Deep space: 더 높음

**Single Event Effects (SEE)**
- SEU: Single Event Upset
- SEL: Single Event Latch-up
- SEB: Single Event Burnout

### 8.2 대응 방법

**부품 선택**
- Radiation-hardened IC
- TID testing
- SEE characterization

**회로 설계**
- EDAC (Error Detection and Correction)
- TMR (Triple Modular Redundancy)
- Watchdog timer

**Shielding**
- Aluminum box (제한적)
- Tantalum foil (beta 차단)

### 8.3 시험

**시험 기관**
- NASA GSFC Radiation Effects Facility
- CERN
- 한국원자력연구원

**시험 종류**
- TID: Co-60 gamma
- SEE: Heavy ion, proton

---

## 9. 표준 및 인증

### 9.1 NASA Standards

**NASA-STD-8739**: Workmanship
- 8739.1: Crimping
- 8739.3: Soldering
- 8739.4: Wire and cable

**NASA-STD-7001**: Vibroacoustic test

**MSFC-STD-3012**: Stress corrosion

### 9.2 ESA Standards

**ECSS-Q-ST-70**: Materials and processes

**ECSS-E-ST-50**: Communications

### 9.3 Military (우주 적용)

**MIL-PRF-38534**: Hybrid microcircuits

**MIL-PRF-38535**: Integrated circuits

**MIL-STD-883**: Test methods

### 9.4 Outgassing Test (ASTM E595)

**기준**
- TML (Total Mass Loss): < 1.0%
- CVCM: < 0.1%

**조건**
- 온도: 125°C
- 진공: 5 × 10^-5 Torr
- 시간: 24 hours

**데이터베이스**
- https://outgassing.nasa.gov

---

## 10. 주요 제조업체

### 10.1 PCB Fabrication

**미국**
- TTM Technologies (Space division)
- Sanmina
- IEC Electronics

**유럽**
- Thales Alenia Space
- Airbus Defence and Space

**특징**
- IPC-6012 Class 3/A
- Cleanroom (Class 10,000)
- Full traceability
- 가격: 일반의 10-50배

### 10.2 커넥터

**Glenair, Inc.**
- 웹사이트: https://www.glenair.com
- 제품: Micro-D, Nano-D, Circular
- 특화: Hermetic sealing

**Omnetics**
- 웹사이트: https://www.omnetics.com
- 제품: Nano-miniature
- 특화: CubeSat

**TE Connectivity**
- 제품: MIL-DTL-38999
- 특화: High reliability

**Samtec**
- 제품: Rugged header/socket
- 특화: PC/104 stack

### 10.3 Fasteners

**PEM (Penn Engineering)**
- 웹사이트: https://www.pemnet.com
- 제품: Space standoffs, inserts

**NAS Standards**
- NAS 1351: Hex head bolt
- NAS 679: Drilled head (safety wire)

### 10.4 Thermal Management

**Boyd Corporation (Aavid)**
- Thermal interface materials

**Laird Technologies**
- Gap fillers, phase change

**Advanced Cooling Technologies**
- Heat pipes, vapor chambers

---

## 11. CubeSat 특수 사례

### 11.1 PC/104 Standard

**Form Factor**: 90mm × 96mm

**Connector**
- 104-pin header (0.1" pitch)
- 제조: Samtec, Harwin

**Stacking**
```
     [보드 3]
        |
     [Spacer]
        |
     [보드 2]
        |
     [Spacer]
        |
     [보드 1]
        |
   [Base plate]
```

**고정**
- 4 corner bolts: M3 또는 4-40
- Thru-hole rods
- Nut & lock washer

### 11.2 CubeSat 제조업체

**Pumpkin (CubeSat Kit)**
- 웹사이트: https://www.cubesatkit.com
- PC/104 backplane, structure

**Clyde Space**
- 웹사이트: https://www.aac-clyde.space
- Power, communication boards

**ISIS**
- 웹사이트: https://www.isispace.nl
- Antenna, solar deployment

---

## 12. 설계 체크리스트

### 재질 선택
- [ ] Outgassing test (TML < 1%, CVCM < 0.1%)
- [ ] 온도 범위 검증
- [ ] 방사선 내성 확인

### PCB 설계
- [ ] Symmetrical stack-up
- [ ] Via reliability (< 8:1 aspect ratio)
- [ ] Thermal via array
- [ ] Impedance ± 5%

### 커넥터
- [ ] Space-qualified 선택
- [ ] Hermetic seal 고려
- [ ] Screw-lock 메커니즘

### 기구
- [ ] Direct bolt mounting
- [ ] Safety wire 적용
- [ ] Torque specification
- [ ] Thermal interface

### 열관리
- [ ] Conduction path 설계
- [ ] Thermal analysis
- [ ] Radiator sizing

### 전원
- [ ] 28V bus 설계
- [ ] Redundancy
- [ ] Capacitor de-rating (50%)

### 방사선
- [ ] TID budget
- [ ] SEE mitigation
- [ ] Shielding analysis

### 시험
- [ ] Thermal vacuum
- [ ] Vibration (NASA-STD-7001)
- [ ] TID/SEE testing
- [ ] Outgassing test

### 문서
- [ ] As-built documentation
- [ ] Material certifications
- [ ] Test reports
- [ ] Traceability

---

## 부록

### A. 비용 예시

**PCB (12층)**
- 방산: $500-1,000
- 우주: $5,000-15,000

**Micro-D (25-pin)**
- 방산: $10-20
- 우주: $100-300

**총 시스템 (5-board)**
- 방산 Backplane: $5,000-10,000
- 우주 Point-to-point: $20,000-50,000

### B. 설계 프로세스

**PDR** (Preliminary Design Review)
- 개념 설계 완료

**CDR** (Critical Design Review)
- 상세 설계 완료

**TRR** (Test Readiness Review)
- 시험 준비 완료

**FRR** (Flight Readiness Review)
- 발사 준비 완료

### C. 참고 자료

**표준 문서**
- NASA-HDBK-4001: Structural Design
- ESA PSS-01-301: Outgassing test
- ECSS-Q-ST-70-02: Thermal vacuum

**교육**
- NASA APPEL
- ESA Training courses

**컨퍼런스**
- SmallSat Conference (Logan, Utah)
- AIAA Space Conference

---

**문서 버전**: 1.0  
**최종 수정**: 2025-02-11  
**작성자**: 나무
