# 방산용 vs 우주용 전자 시스템 설계 비교

## 목차
- [1. 개요](#1-개요)
- [2. 환경 조건 비교](#2-환경-조건-비교)
- [3. 설계 철학 비교](#3-설계-철학-비교)
- [4. PCB 설계 비교](#4-pcb-설계-비교)
- [5. 연결 방법 비교](#5-연결-방법-비교)
- [6. 고정 시스템 비교](#6-고정-시스템-비교)
- [7. 열관리 비교](#7-열관리-비교)
- [8. 전원 시스템 비교](#8-전원-시스템-비교)
- [9. 비용 비교](#9-비용-비교)
- [10. 표준 비교](#10-표준-비교)
- [11. 제조업체 비교](#11-제조업체-비교)
- [12. 선택 가이드](#12-선택-가이드)

---

## 1. 개요

### 1.1 문서 목적

본 문서는 방산용(Defense/Military)과 우주용(Space) 전자 시스템 설계의 차이점을 명확히 비교하여, 프로젝트에 적합한 설계 접근법을 선택할 수 있도록 돕습니다.

### 1.2 핵심 차이점 요약

| 구분 | 방산용 | 우주용 |
|------|--------|--------|
| **주요 목표** | 신뢰성 + 비용 효율 | 신뢰성 > 모든 것 |
| **구조** | Backplane 기반 | Point-to-point |
| **무게** | 중요하지 않음 | 최우선 고려 |
| **교체** | Field-replaceable | 불가능 |
| **냉각** | 대류/강제 공랭 | 전도/복사만 |
| **비용** | 중간 | 매우 높음 (10-50배) |

---

## 2. 환경 조건 비교

### 2.1 상세 비교표

| 환경 요소 | 방산용 (MIL-STD-810) | 우주용 (NASA-STD-7001) |
|-----------|---------------------|----------------------|
| **온도** | -40°C ~ +85°C | -180°C ~ +150°C |
| **온도 변화율** | 점진적 | 급격함 (90분 주기) |
| **압력** | 대기압 (1 atm) | 10^-6 ~ 10^-15 Torr |
| **습도** | 0-95% | 0% (진공) |
| **방사선** | 거의 없음 | TID 100+ krad, SEE |
| **진동** | 수십 G | 수백 G (발사 시) |
| **충격** | 수십 G | 수백 G |
| **수명** | 5-20년 | 5-15년 (궤도) |
| **유지보수** | 가능 | 불가능 |

### 2.2 환경별 주요 도전 과제

#### 방산용
- **EMI/EMC**: 전자파 간섭
- **진동/충격**: 차량, 항공기 운용
- **온도 변화**: 사막 ~ 극지방
- **습도**: 부식, 곰팡이

#### 우주용
- **진공**: Outgassing, 대류 냉각 불가
- **극저온**: 재료 수축, 균열
- **방사선**: TID, SEE
- **열 사이클**: 일식/일조 반복
- **발사 환경**: 극심한 진동/충격

---

## 3. 설계 철학 비교

### 3.1 방산용 설계 철학

**핵심 원칙**
1. **Modularity**: 표준 모듈 교체 가능
2. **COTS 활용**: Commercial Off-The-Shelf
3. **Field Repair**: 현장 수리 가능
4. **Cost-Performance**: 비용 대비 성능

**예시**
- VPX 표준 카드
- Hot-swap 가능
- Standard connectors
- MTBF 기반 신뢰성

### 3.2 우주용 설계 철학

**핵심 원칙**
1. **No-Failure Design**: 단 한 번의 고장도 허용 안 됨
2. **Mass Optimization**: 무게 1kg = $10k-$50k
3. **Redundancy**: 모든 critical 기능 이중화
4. **Qualification**: 모든 부품 우주 인증

**예시**
- Custom 설계
- Dual redundant systems
- Space-qualified components
- Full traceability

### 3.3 설계 프로세스 비교

#### 방산용
```
개념 설계 → 상세 설계 → 프로토타입 → 양산
(6-12개월)
```

#### 우주용
```
개념 설계 → PDR → 상세 설계 → CDR → 
부품 검증 → EM 제작 → 환경 시험 → FM 제작 → FRR
(2-5년)
```

**주요 차이**
- 우주용은 Review 단계가 많음
- 부품 하나하나 검증
- 환경 시험 엄격함
- 개발 기간 훨씬 김

---

## 4. PCB 설계 비교

### 4.1 재질 비교

| 항목 | 방산용 | 우주용 |
|------|--------|--------|
| **표준 재질** | FR-4 | Polyimide, Cyanate Ester |
| **고속 재질** | Megtron 6, Rogers | Rogers, PTFE, Ceramic |
| **Dk (1GHz)** | 4.2-4.5 (FR-4) | 3.6-3.8 (stable) |
| **Df** | 0.02 | 0.005-0.01 |
| **Tg** | 130-170°C | 170-250°C |
| **Outgassing** | 고려 안 함 | TML<1%, CVCM<0.1% |
| **비용 (m²)** | $50-200 | $500-2,000 |

**핵심 차이**
- 방산: FR-4 기본, 고속에만 특수 재질
- 우주: FR-4 사용 불가, 모두 특수 재질

### 4.2 Layer Stack-Up 비교

#### 방산용 (14-16층)

**특징**
- 많은 레이어 (신호 분리)
- Mixed signal/power planes
- Decoupling 용이

**장점**
- 라우팅 자유도 높음
- EMI 성능 우수

**단점**
- 무게 증가
- 비용 증가

#### 우주용 (8-10층)

**특징**
- 적은 레이어 (신뢰성)
- Symmetrical stack-up 필수
- Via 최소화

**장점**
- Via reliability 높음
- 무게 절감
- Warping 방지

**단점**
- 라우팅 제약
- 설계 난이도

### 4.3 Via 설계 비교

| 항목 | 방산용 | 우주용 |
|------|--------|--------|
| **Via 종류** | Through, Blind, Buried | Through-hole only |
| **Barrel 두께** | 18 μm | 25 μm |
| **Aspect Ratio** | < 10:1 | < 8:1 |
| **Back-drilling** | 고속 신호만 | 권장 |
| **Via fill** | 선택적 | 권장 (thermal stress) |
| **Redundancy** | 필요 없음 | Critical net은 2+ |

### 4.4 임피던스 제어 비교

| 항목 | 방산용 | 우주용 |
|------|--------|--------|
| **10GbE Diff** | 100Ω ± 10% | 100Ω ± 5% |
| **1GbE Diff** | 100Ω ± 10% | 100Ω ± 5% |
| **검증** | Coupon 샘플링 | 전수 TDR 검사 |
| **온도 보상** | 고려 안 함 | 필수 |

---

## 5. 연결 방법 비교

### 5.1 Backplane vs Point-to-Point

#### 방산용: VPX Backplane

**구조**
```
[Slot1] [Slot2] [Slot3] ... [SlotN]
   |       |       |          |
   +-------+-------+----------+
        Backplane PCB
```

**장점**
- 표준화 (VITA 46)
- Hot-swap 가능
- 확장 용이
- Field replaceable

**단점**
- Backplane 무게
- 커넥터 신뢰성
- 높은 커넥터 비용

**적용**
- 지상 장비
- 항공기 avionics
- 함정 시스템

#### 우주용: Point-to-Point Harness

**구조**
```
[보드A]--[Harness]--[보드B]
   |                    |
[Chassis]          [Chassis]
```

**장점**
- 무게 절감 (Backplane 제거)
- 각 보드 독립 열관리
- 유연한 배선
- 커넥터 신뢰성

**단점**
- 표준화 어려움
- Harness 복잡
- 조립 시간 증가
- 유지보수 불가

**적용**
- 인공위성
- 우주 탐사선
- 우주정거장 실험 장비

### 5.2 커넥터 비교

| 항목 | 방산용 | 우주용 |
|------|--------|--------|
| **표준** | VPX (VITA 46) | Micro-D, Nano-D |
| **Pin 수** | 100-300+ | 9-100 |
| **Pin Pitch** | 2mm | 1.27mm (Micro-D) |
| **고정 방식** | Press-fit, Solder | Screw-lock |
| **Seal** | 선택적 | Hermetic seal |
| **Mating Force** | 중간 | 낮음 (반복 고려) |
| **가격** | $50-200 | $100-800 |
| **제조사** | 3M, Tyco, Samtec | Glenair, Omnetics |

### 5.3 제조업체 비교

#### 방산용 VPX
- **nVent Schroff**
- **Elma Electronic**
- **Pixus Technologies**
- **Curtiss-Wright**

#### 우주용 Micro-D
- **Glenair**
- **Omnetics**
- **TE Connectivity**
- **Amphenol**

---

## 6. 고정 시스템 비교

### 6.1 방산용: Card Guide + Wedge Lock

**Card Guide**
```
┌─────────────┐
│   Chassis   │
│  ┌───────┐  │
│  │ Slot  │  │ ← U-shaped channel
│  └───────┘  │
└─────────────┘
```

**Wedge Lock**
- IEEE 1101.10/11 표준
- 레버 회전 → 보드 삽입
- 탈거 용이

**장점**
- 빠른 교체
- Tool-less (일부)
- 표준화

**단점**
- 진동에서 loosening 가능
- 발사 환경 부적합

**제조사**
- Schroff: Part# 20817-302 (3U)
- Elma: Card guide sets

**가격**
- Card guide: $50-100/set
- Wedge lock: $30-50/pc

### 6.2 우주용: Direct Bolt Mounting

**구조**
```
     보드 PCB
   [=========]
   |    |    |
  볼트 볼트 볼트
   |    |    |
  [Chassis]
```

**Standoff**
- 재질: Al, Ti (경량)
- 제조: PEM

**Bolts**
- NAS 표준
- Safety wire
- Torque stripe

**Thermal Interface**
- Gap filler
- Thermal grease

**장점**
- 극한 신뢰성
- Thermal conduction
- 진동 내성

**단점**
- 교체 불가
- 조립 시간
- Tooling 필요

**제조사**
- PEM: Standoffs
- NAS: Bolts
- Chomerics: Thermal interface

**가격**
- Standoff: $5-20/pc
- NAS bolt: $1-5/pc
- Thermal pad: $10-50/sheet

### 6.3 추가 고정 방법 비교

| 방법 | 방산용 | 우주용 |
|------|--------|--------|
| **Potting** | 선택적 | 소형 모듈 |
| **Conformal Coating** | 선택적 | 필수 |
| **Lacing/Tie-down** | 케이블만 | Harness 필수 |
| **Edge Clamp** | 드물음 | 큰 보드에 사용 |

---

## 7. 열관리 비교

### 7.1 냉각 방법 비교

| 항목 | 방산용 | 우주용 |
|------|--------|--------|
| **주 냉각** | 대류 (공랭) | 전도 |
| **보조 냉각** | 강제 공랭 (팬) | 복사 |
| **Heat sink** | Finned aluminum | Smooth plate |
| **Thermal interface** | 선택적 | 필수 |
| **Radiator** | 없음 | 필수 |

### 7.2 방산용: 대류 냉각

**자연 대류**
- 저전력 (<50W)
- Heat sink with fins
- 간단한 설계

**강제 대류**
- 고전력 (>50W)
- Fan cooling
- Air duct 설계

**장점**
- 설계 단순
- 비용 저렴
- 효율적

**단점**
- Fan 고장 가능
- Dust 문제
- 소음

### 7.3 우주용: 전도/복사 냉각

**Conduction Path**
```
Component → Thermal via → PCB → 
Interface → Chassis → Radiator
```

**설계 요소**
1. **Thermal vias**: 0.5mm pitch array
2. **MCPCB**: Metal core
3. **Gap filler**: 3-8 W/m·K
4. **Heat pipes**: Hot spot 이송
5. **Radiator**: 우주 공간으로 방출

**장점**
- Fan 없음 (신뢰성)
- 진공 적합
- Passive cooling

**단점**
- 설계 복잡
- 무게 증가
- 비용 높음

### 7.4 Thermal Interface 비교

| 항목 | 방산용 | 우주용 |
|------|--------|--------|
| **재질** | Thermal paste | Gap filler, phase change |
| **열전도** | 1-3 W/m·K | 3-8 W/m·K |
| **Compression** | 고려 안 함 | 중요 (볼트 토크) |
| **진공 적합성** | 불필요 | Outgassing test 필수 |
| **제조사** | Arctic Silver | Chomerics, Bergquist |
| **가격** | $5-20 | $50-200 |

---

## 8. 전원 시스템 비교

### 8.1 전원 전압 비교

| 항목 | 방산용 | 우주용 |
|------|--------|--------|
| **Main Bus** | 12V, 24V, 48V | 28V (22-34V) |
| **Logic** | 5V, 3.3V | 5V, 3.3V |
| **Low Voltage** | 1.8V, 1.2V | 1.8V, 1.2V |
| **분배 방식** | Backplane bus | Point-to-point |
| **Redundancy** | 선택적 | 필수 (dual bus) |

### 8.2 Decoupling 비교

#### 방산용
- Bulk: 100-220 μF/slot
- Bypass: 0.1 μF, 1 μF
- De-rating: 20-30%
- 배치: 일반적

#### 우주용
- Bulk: 220-470 μF/board
- Bypass: 0.1 μF, 1 μF, 10 μF
- **De-rating: 50% voltage, 60% temp**
- 배치: 매우 엄격 (via inductance)

**예시**
- 방산: 50V cap을 40V에 사용
- 우주: 50V cap을 25V에 사용

### 8.3 Capacitor 선택 비교

| 타입 | 방산용 | 우주용 |
|------|--------|--------|
| **Tantalum** | 일반 등급 | AVX TRR/TRS |
| **Ceramic** | X7R, X5R | C0G, X7R (space) |
| **Electrolytic** | 일반 | 사용 최소화 |
| **가격** | 표준 | 5-10배 |
| **시험** | 불필요 | TID, outgassing |

### 8.4 전원 분배 구조

#### 방산용: Backplane Bus
```
     [PSU]
       |
  [Backplane]
   |  |  |  |
  B1 B2 B3 B4
```

**특징**
- 중앙 집중식
- 병렬 분배
- Hot-swap 고려

#### 우주용: Distributed PoL
```
[28V Bus]
   |
   +--[PoL1]--[Board 1]
   |
   +--[PoL2]--[Board 2]
   |
   +--[PoL3]--[Board 3]
```

**특징**
- 분산형
- 각 보드 독립 regulation
- Redundant bus

---

## 9. 비용 비교

### 9.1 PCB 제조 비용

| 항목 | 방산용 | 우주용 | 비율 |
|------|--------|--------|------|
| **12층 PCB** | $500-1,000 | $5,000-15,000 | 10x |
| **리드타임** | 2-4주 | 8-16주 | 4x |
| **최소 주문** | 5 pcs | 1 pc | - |
| **재작업** | 가능 | 매우 제한적 | - |

### 9.2 커넥터 비용

| 제품 | 방산용 | 우주용 | 비율 |
|------|--------|--------|------|
| **VPX / Micro-D (25-pin)** | $50-200 | $100-300 | 2-5x |
| **Card guide set** | $50-100 | N/A | - |
| **Wedge lock** | $30-50 | N/A | - |
| **Standoff + bolt** | N/A | $10-30 | - |
| **Thermal interface** | $10-20 | $50-200 | 10x |

### 9.3 시스템 총 비용 (예시)

#### 방산용 7-Slot VPX System
- Backplane PCB: $2,000
- 7× VPX connectors: $1,400
- Card guides: $350
- Wedge locks: $350
- Chassis: $1,000
- **총계: ~$5,000-10,000**

#### 우주용 5-Board System
- 5× PCB (custom): $25,000
- 10× Micro-D connectors: $5,000
- Harness (custom): $10,000
- Standoffs/bolts: $500
- Thermal interfaces: $1,000
- Assembly/test: $10,000
- Qualification: $20,000
- **총계: ~$70,000-100,000**

**비율: 약 10-20배**

### 9.4 개발 비용

| 단계 | 방산용 | 우주용 |
|------|--------|--------|
| **설계** | $50k-100k | $200k-500k |
| **프로토타입** | $20k-50k | $100k-300k |
| **시험** | $30k-70k | $200k-500k |
| **인증** | $20k-50k | $100k-300k |
| **총계** | $120k-270k | $600k-1,600k |

---

## 10. 표준 비교

### 10.1 적용 표준

#### 방산용
- **VITA 46**: VPX standard
- **IEEE 1101.10/11**: Wedge lock
- **MIL-STD-461**: EMI/EMC
- **MIL-STD-810**: Environmental
- **IPC-6012 Class 3**: PCB quality
- **MIL-HDBK-217**: Reliability

#### 우주용
- **NASA-STD-8739**: Workmanship
- **NASA-STD-7001**: Vibration
- **ECSS-Q-ST-70**: Materials
- **ASTM E595**: Outgassing
- **IPC-6012 Class 3/A**: PCB (space)
- **MIL-PRF-38535**: ICs

### 10.2 환경 시험 비교

| 시험 | 방산용 (MIL-STD-810) | 우주용 (NASA-STD-7001) |
|------|---------------------|----------------------|
| **온도** | -40°C ~ +85°C | -180°C ~ +150°C |
| **Thermal cycle** | 100 cycles | 1000+ cycles |
| **진동** | Random, Sine | Random + Acoustic |
| **충격** | 수십 G | 수백 G |
| **압력** | 대기압 변화 | Thermal vacuum |
| **방사선** | 없음 | TID, SEE |
| **Outgassing** | 없음 | ASTM E595 |

### 10.3 품질 인증

#### 방산용
- ISO 9001
- AS9100 (항공)
- MIL-STD-2164 (환경)
- IPC-A-610 Class 2/3

#### 우주용
- ISO 9001
- AS9100
- **NASA Parts Approval**
- **ESA ESCC**
- IPC-A-610 Class 3
- **Full traceability**

---

## 11. 제조업체 비교

### 11.1 시스템 통합업체

#### 방산용
| 업체 | 웹사이트 | 전문 분야 |
|------|---------|----------|
| **Curtiss-Wright** | curtisswrightds.com | VPX systems |
| **Mercury Systems** | mrcy.com | OpenVPX |
| **Kontron** | kontron.com | Embedded systems |

#### 우주용
| 업체 | 웹사이트 | 전문 분야 |
|------|---------|----------|
| **Thales Alenia Space** | thalesaleniaspace.com | Satellite systems |
| **Airbus Defence** | airbus.com | Spacecraft |
| **Lockheed Martin** | lockheedmartin.com | Deep space |

### 11.2 커넥터 업체

#### 방산용
- **nVent Schroff**: VPX systems
- **3M**: High-speed connectors
- **TE Connectivity**: MIL-spec
- **Samtec**: High-density

#### 우주용
- **Glenair**: Micro-D, hermetic
- **Omnetics**: Nano-D, ultra-light
- **TE Connectivity**: Space-grade
- **Amphenol**: Circular

### 11.3 PCB 제조

#### 방산용
- **TTM Technologies**
- **Sanmina**
- **IEC Electronics**
- 리드타임: 2-4주
- 비용: 중간

#### 우주용
- **TTM Space Division**
- **Sanmina Aerospace**
- **IEC Space**
- 리드타임: 8-16주
- 비용: 매우 높음

---

## 12. 선택 가이드

### 12.1 방산용을 선택해야 할 때

**적합한 경우**
- ✓ 지상 또는 대기권 내 운용
- ✓ 현장 유지보수 가능
- ✓ 모듈 교체 필요
- ✓ 비용 제약이 중요
- ✓ 개발 기간 제한 (< 1년)
- ✓ 표준화된 시스템
- ✓ 대류 냉각 가능

**응용 분야**
- 지상 통신 장비
- 차량 탑재 시스템
- 항공기 avionics
- 함정 전자 장비
- 지휘통제 시스템

**장점**
- 낮은 개발 비용
- 빠른 개발 기간
- 표준 부품 사용
- Field service 용이
- 풍부한 ecosystem

**단점**
- 무게 제약 대응 어려움
- 극한 환경 부적합
- 진공 환경 불가
- 방사선 내성 제한적

### 12.2 우주용을 선택해야 할 때

**적합한 경우**
- ✓ 우주 환경 운용
- ✓ 유지보수 불가
- ✓ 극한 신뢰성 필요
- ✓ 무게 제약 중요
- ✓ 방사선 환경
- ✓ 진공 환경
- ✓ 장기 임무 (5-15년)

**응용 분야**
- 인공위성
- 우주 탐사선
- 우주정거장 실험
- 행성 탐사 로버
- 우주 망원경

**장점**
- 극한 환경 대응
- 최고 신뢰성
- 무게 최적화
- 방사선 내성
- 진공/극저온 적합

**단점**
- 매우 높은 비용
- 긴 개발 기간
- 제한적인 공급망
- 수정 어려움
- 복잡한 인증

### 12.3 Hybrid 접근법

**고고도 항공기, 성층권 기구**

일부 우주 기술 + 방산 기술
- Backplane 구조 유지
- 일부 우주급 재질
- Conduction cooling 추가
- 진동 대응 강화

**예시**
- U-2, SR-71 (고고도 정찰기)
- 성층권 통신 플랫폼
- 고고도 장기체공 무인기

### 12.4 의사결정 플로우차트
```
시작
  |
  v
진공 환경? ----YES---> 우주용
  |
  NO
  |
  v
방사선 노출? --YES---> 우주용
  |
  NO
  |
  v
무게 < 1kg? ---YES---> 우주용 고려
  |
  NO
  |
  v
현장 수리? ---YES---> 방산용
  |
  NO
  |
  v
비용 제약? ---YES---> 방산용
  |
  NO
  |
  v
개발 기간? --<1년--> 방산용
  |
  NO
  |
  v
우주용 또는 Hybrid
```

### 12.5 핵심 질문 체크리스트

**환경**
- [ ] 진공 환경에서 운용?
- [ ] 온도 범위: < -100°C or > 100°C?
- [ ] 방사선 노출?
- [ ] 발사 환경 (수백 G)?

**운용**
- [ ] 유지보수 불가?
- [ ] 임무 수명 > 10년?
- [ ] 단 한 번의 고장도 허용 안 됨?

**제약**
- [ ] 무게 제약 중요? (kg당 $10k+)
- [ ] 비용 제약? (>$100k 가능?)
- [ ] 개발 기간? (>2년 가능?)

**기술**
- [ ] 대류 냉각 불가?
- [ ] 표준 커넥터 부적합?
- [ ] Outgassing 문제?

**YES가 3개 이상 → 우주용**
**NO가 대부분 → 방산용**

---

## 부록

### A. 용어 비교표

| 용어 | 방산용 | 우주용 |
|------|--------|--------|
| **Backplane** | VPX, VME, cPCI | 거의 사용 안 함 |
| **Card** | VPX card, Blade | Board, Module |
| **고정** | Wedge lock, Card guide | Bolt mount, Potting |
| **냉각** | Air cooling, Fan | Conduction, Radiation |
| **신뢰성** | MTBF | No-failure design |
| **테스트** | MIL-STD-810 | NASA-STD-7001 |

### B. 추가 참고 자료

**방산용**
- VITA Standards: https://www.vita.com
- Schroff: https://schroff.nvent.com
- MIL-STD-810: Environmental Engineering

**우주용**
- NASA Standards: https://standards.nasa.gov
- ESA ECSS: https://ecss.nl
- NASA Outgassing DB: https://outgassing.nasa.gov

### C. 주요 컨퍼런스

**방산용**
- AUSA (Association of the US Army)
- I/ITSEC (Interservice/Industry Training)
- DSEI (Defence & Security Equipment)

**우주용**
- SmallSat Conference (Logan, Utah)
- AIAA Space Conference
- IAC (International Astronautical Congress)

---

## 결론

### 핵심 메시지

**방산용**
- 표준화, 모듈화, 비용 효율
- Backplane 기반 확장 구조
- Field-replaceable
- 적용: 지상/대기권 내

**우주용**
- 극한 신뢰성, 무게 최적화
- Point-to-point 커스텀 설계
- No-failure design
- 적용: 우주 환경

### 최종 권고사항

1. **환경 분석 우선**: 진공, 방사선 여부
2. **운용 개념 확인**: 유지보수 가능성
3. **제약 조건 검토**: 무게, 비용, 기간
4. **기술 역량 평가**: 내부 전문성
5. **공급망 조사**: 부품 가용성

**잘못된 선택의 결과**
- 방산용 → 우주: 임무 실패
- 우주용 → 방산: 불필요한 과도 설계

**올바른 선택이 프로젝트 성공의 50%입니다.**

---

**문서 버전**: 1.0  
**최종 수정**: 2025-02-11  
**작성자**: 나무  
**관련 문서**:
- Defense_Backplane_Design_Guide.md
- Space_Electronics_Design_Guide.md
