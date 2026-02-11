# 방산용 통신 장비 Backplane 설계 가이드

## 목차
- [1. 개요](#1-개요)
- [2. PCB 레이어 스택업 설계](#2-pcb-레이어-스택업-설계)
- [3. VPX 커넥터 및 슬롯 배치](#3-vpx-커넥터-및-슬롯-배치)
- [4. 고속 신호 라우팅](#4-고속-신호-라우팅)
- [5. 전원 분배 네트워크](#5-전원-분배-네트워크)
- [6. EMI/EMC 설계](#6-emiemc-설계)
- [7. 기계적 설계](#7-기계적-설계)
- [8. Card Guide 및 고정 시스템](#8-card-guide-및-고정-시스템)
- [9. 시뮬레이션 및 검증](#9-시뮬레이션-및-검증)
- [10. 제조 고려사항](#10-제조-고려사항)
- [11. 주요 제조업체](#11-주요-제조업체)
- [12. 설계 체크리스트](#12-설계-체크리스트)

---

## 1. 개요

### 1.1 적용 범위
본 가이드는 다음 인터페이스를 포함하는 방산용 통신 장비 Backplane 설계에 적용됩니다:
- 전원 공급
- RS-422/485 시리얼 통신
- 1GbE (Gigabit Ethernet)
- 10GbE (10 Gigabit Ethernet)

### 1.2 설계 목표
- **신호 무결성**: 10GbE 고속 신호의 안정적 전송
- **EMI/EMC 성능**: MIL-STD-461 준수
- **신뢰성**: 방산 환경 (-40°C ~ +85°C, 진동/충격)
- **모듈성**: VPX 표준 기반 확장 가능 구조

### 1.3 주요 표준
- **VITA 46 (VPX)**: 커넥터 및 Backplane 표준
- **IEEE 1101.10/11**: Wedge lock 및 기구 표준
- **MIL-STD-461**: EMI/EMC 요구사항
- **MIL-STD-810**: 환경 시험
- **IPC-6012 Class 3**: PCB 제조 품질

---

## 2. PCB 레이어 스택업 설계

### 2.1 권장 레이어 구성

#### 14~16층 구조 (10GbE 포함)
```
Layer 1:  Top Signal (High-Speed Differential Pairs)
Layer 2:  GND Plane
Layer 3:  Signal (Mid-Speed, RS-422/485)
Layer 4:  PWR Plane (3.3V)
Layer 5:  GND Plane
Layer 6:  Signal (10GbE Differential)
Layer 7:  GND Plane
Layer 8:  Signal (10GbE Differential)
Layer 9:  GND Plane
Layer 10: Signal (1GbE, Control)
Layer 11: PWR Plane (5V, 12V)
Layer 12: GND Plane
Layer 13: Signal (Low-Speed, Power Distribution)
Layer 14: Bottom Signal (Component Side)
```

#### 12층 구조 (비용 절감형)
```
Layer 1:  Top Signal
Layer 2:  GND Plane
Layer 3:  Signal (High-Speed)
Layer 4:  GND Plane
Layer 5:  Signal (High-Speed)
Layer 6:  PWR Plane
Layer 7:  GND Plane
Layer 8:  Signal (Mid-Speed)
Layer 9:  GND Plane
Layer 10: Signal (Low-Speed)
Layer 11: PWR Plane
Layer 12: Bottom Signal
```

### 2.2 스택업 설계 원칙

**신호 레이어와 플레인 배치**
- 모든 고속 신호 레이어는 반드시 인접한 GND 플레인과 쌍을 이루어야 함
- Differential pair는 GND 플레인 사이에 샌드위치 구조로 배치
- PWR 플레인과 GND 플레인을 인접 배치하여 decoupling capacitance 확보

**유전체 두께**
- 고속 신호 레이어: 4-6 mil (100-150 μm) - 임피던스 제어
- PWR/GND 플레인: 3-4 mil - 높은 커패시턴스
- 외부 레이어: 8-10 mil - 기계적 강도

### 2.3 재질 선택

**표준 FR-4**
- Dk: 4.2-4.5 (1GHz)
- Df: 0.02
- 용도: 1GbE까지 적합
- 비용: 저렴

**고속 재질 (Megtron 6, Rogers)**
- Dk: 3.6-3.8 (안정적)
- Df: 0.005-0.01
- 용도: 10GbE 필수
- 비용: 높음

**권장: Hybrid Stack-up**
- 10GbE 레이어: Megtron 6
- 나머지 레이어: FR-4

---

## 3. VPX 커넥터 및 슬롯 배치

### 3.1 VPX 표준 개요

**VITA 46 주요 사양**
- **커넥터**: 7-row high-density
- **Pin Pitch**: 2mm
- **Data Rate**: 최대 10Gbps (Gen3)
- **전원 공급**: 슬롯당 최대 200W

### 3.2 슬롯 간격

**표준 피치**
- **0.8" (20.32mm)**: VPX 표준 권장
- **1" (25.4mm)**: 고밀도 구성 (냉각 주의)
- **커스텀**: 방열 요구사항에 따라 조정

### 3.3 슬롯 배치 전략

**전원 공급 슬롯**
- Backplane 중앙 또는 양 끝 배치
- 전원 분배 손실 최소화

**고속 통신 슬롯**
- 10GbE: 중앙부 배치 (신호 라우팅 길이 균등)
- Fabric 스위치: 중앙 배치

**저속 I/O 슬롯**
- 주변부 배치 가능

**예시 배치 (7-Slot)**
```
[PSU] [1GbE] [10GbE] [Switch] [10GbE] [RS-422/485] [Control]
 Slot1  Slot2   Slot3   Slot4    Slot5     Slot6      Slot7
```

---

## 4. 고속 신호 라우팅

### 4.1 10G Ethernet 설계 규칙

**임피던스**
- Differential: 100Ω ± 10%
- Single-ended: 50Ω ± 5%

**트레이스 설계**
- Width: 4-6 mil
- Spacing (diff pair): 4-5 mil
- Length matching: ± 5 mil (0.127mm)

**Via 설계**
- Back-drilling 필수: Stub < 10 mil
- Via diameter: 8-10 mil (드릴)
- Pad: 18-20 mil
- Differential via 대칭 배치

**라우팅 원칙**
- Layer 변경 최소화
- 90도 코너 금지 → 45도 또는 arc
- Serpentine spacing: 최소 3× trace width
- Reference plane 연속성 유지

### 4.2 1G Ethernet 설계

**임피던스**: 100Ω ± 10%

**트레이스**
- Width: 5-7 mil
- Length matching: ± 10 mil

**라우팅**
- Layer 전환 허용
- 10GbE와 최소 30 mil 이격

### 4.3 RS-422/485 설계

**임피던스**: 120Ω ± 10%

**트레이스**
- Width: 8-10 mil
- Spacing: 8-10 mil
- Length matching: ± 50 mil

**라우팅**
- 고속 신호와 격리 (최소 50 mil)
- 별도 signal layer 권장

### 4.4 신호 간섭 방지

**Layer 분리**
```
Layer 6: 10GbE Lane 0-3
Layer 8: 10GbE Lane 4-7
Layer 10: 1GbE, RS-422/485
```

**Guard Trace/GND Stitching**
- 고속 신호 양측에 GND guard trace (10 mil)
- Via stitching: 100 mil 간격
- Cross-talk 목표: -40dB 이상

---

## 5. 전원 분배 네트워크

### 5.1 전원 플레인 설계

**전압별 플레인 분리**
- 12V: Main power (Layer 11)
- 5V: Logic/IO (Layer 11, split plane)
- 3.3V: Core logic (Layer 4)
- 1.8V/1.2V: 필요 시 추가

**Copper Weight**
- Power planes: 2 oz (70 μm) 이상
- Signal layers: 0.5-1 oz

**전원 분배 토폴로지**
- **Star topology**: 전압 안정성 우수
- **Bus topology**: 전류 균등 분배

### 5.2 Decoupling 전략

**Bulk Capacitance**
- 슬롯당 220-470 μF (Tantalum/Electrolytic)
- VPX 커넥터 근처 배치

**Bypass Capacitance**
- 10 μF (X7R): 슬롯당 4-6개
- 1 μF: 슬롯당 10-15개
- 0.1 μF: 모든 전원 핀 근처 (100 mil 이내)
- 10 nF: 고속 신호 핀 근처

**배치 원칙**
- Via inductance 최소화
- Multiple via 사용 (병렬 저항/인덕턴스 감소)

### 5.3 전압 강하 계산

**목표**: < 3% (worst-case)

**계산식**
```
V_drop = I × R
R = ρ × L / (W × T)

ρ (구리): 1.68 × 10^-8 Ω·m
```

**예시**
- 12V, 10A, 경로 300mm, 2oz, 폭 50mm
- R = 1.44 mΩ
- V_drop = 14.4 mV (0.12% ✓)

---

## 6. EMI/EMC 설계

### 6.1 GND 플레인 설계

**Multi-Point Ground 권장**
- 모든 GND 플레인 low-impedance 연결
- Slot-to-slot GND via: 200 mil 간격

**Split GND 주의**
- 가급적 GND 분리 금지
- 불가피한 경우: 신호가 경계 넘지 않도록

### 6.2 Shield 및 차폐

**VPX 커넥터**
- Metal housing을 chassis GND 연결
- EMI gasket 사용

**고속 신호**
- Differential pair 주변 GND via fence
- Spacing: λ/10 (10GHz → 3mm)

**External EMI**
- Edge via stitching (100 mil 간격)
- Metal enclosure 360도 연결

### 6.3 필터링

**Common-Mode Choke**
- 10GbE: 고주파 대응
- 1GbE: 표준 CMC
- RS-422/485: 저주파 CMC

**TVS Diode**
- 외부 포트 ESD/Surge 보호
- Clamping voltage < 신호 레벨

---

## 7. 기계적 설계

### 7.1 PCB 두께 및 강도

**두께**
- 표준: 2.4mm (0.094")
- 최대: 3.2mm (VPX 표준)

**보강**
- Stiffener bars (> 400mm 길이)
- Metal core insert (고전류 구간)

### 7.2 VPX 커넥터 고정

**Through-hole mount 권장**
- Press-fit 또는 soldered
- 기계적 stress 대응

**추가 고정**
- Screw mount bracket
- Epoxy underfill (진동 환경)

---

## 8. Card Guide 및 고정 시스템

### 8.1 Card Guide (카드 가이드)

**역할**
- 보드 양측 지지
- 슬롯 삽입 시 가이드

**재질**
- 알루미늄: 경량, 열전도 우수
- 스테인리스: 고강도
- 플라스틱: 저비용

**규격**
- Slot width: PCB 두께 + 0.2~0.5mm
- 표준 2.4mm PCB → 2.6~2.9mm slot

### 8.2 Wedge Lock / Injector-Ejector

**IEEE 1101.10 표준**

**작동 방식**
- 레버 회전 → 웨지가 card guide 밀어내며 보드 삽입
- 역방향 → 보드 탈거

**사양**
- 3U/6U 표준 크기
- 회전 각도: 90도
- 삽입력: 20-50 lbs

### 8.3 조립 절차

1. Card guide를 섀시에 장착
2. 보드에 wedge lock 장착
3. 보드를 card guide에 삽입
4. Wedge lock 체결
5. 전면 패널 나사 체결 (옵션)

---

## 9. 시뮬레이션 및 검증

### 9.1 Signal Integrity

**도구**
- Cadence Sigrity
- Keysight ADS
- Ansys HFSS

**검증 항목**
- Eye diagram: Opening > 60%
- S-parameters (S21, S11)
- Impedance: ± 10%
- TDR

### 9.2 Power Integrity

**검증**
- DC voltage drop
- AC impedance < target
- PDN resonance

### 9.3 EMC Pre-compliance

**Radiated Emission**
- Near-field scanning (10GHz)

**Conducted Emission**
- Power rail noise < 100 mV pk-pk

---

## 10. 제조 고려사항

### 10.1 제조 능력

**Layer alignment**: ± 3 mil
**Impedance control**: ± 10%
**Via registration**: ± 2 mil

### 10.2 테스트 포인트

- 고속 differential pair: test coupon
- Critical net: test pad

### 10.3 Assembly

**Reflow profile**
- Lead-free: 260°C peak

**Inspection**
- X-ray: BGA, VPX soldering
- AOI: placement

---

## 11. 주요 제조업체

### 11.1 VPX 커넥터 및 기구

**nVent Schroff (구 Calmark)**
- 웹사이트: https://schroff.nvent.com
- 제품: VPX card guides, wedge locks
- Part 예시:
  - 20817-302 (3U wedge lock)
  - 20817-101 (Card guide)

**Elma Electronic**
- 웹사이트: https://www.elma.com
- 제품: VPX/VME systems

**Pixus Technologies**
- 웹사이트: https://www.pixustechnologies.com
- 제품: VITA 46 compliant systems

**Curtiss-Wright**
- 웹사이트: https://www.curtisswrightds.com
- 제품: VPX 전체 시스템

**Mercury Systems**
- 웹사이트: https://www.mrcy.com
- 제품: OpenVPX 섀시

### 11.2 커넥터

**3M**
- VPX high-speed connectors

**TE Connectivity**
- MIL-spec connectors

**Hybricon**
- 웹사이트: https://www.hybricon.com
- VPX backplane connectors

### 11.3 PCB 제조

**TTM Technologies**
**Sanmina**
**IEC Electronics**

---

## 12. 설계 체크리스트

### Layout 단계
- [ ] 10GbE 임피던스 100Ω ± 10%
- [ ] Via stub < 10 mil
- [ ] Diff pair length ± 5 mil
- [ ] GND plane 연속성
- [ ] Power drop < 3%
- [ ] Decoupling cap 최적화
- [ ] EMI via fence
- [ ] Test point 배치

### Fabrication
- [ ] Stack-up 승인
- [ ] Impedance control 사양
- [ ] Via back-drilling
- [ ] Surface finish (ENIG)

### Assembly
- [ ] Reflow profile
- [ ] X-ray inspection
- [ ] TDR/S-parameter test

### System
- [ ] Power-up sequence
- [ ] 10GbE link test
- [ ] EMC test (MIL-STD-461)
- [ ] Environmental stress

---

## 부록

### A. 용어 정리

- **VPX**: VITA 46 표준 고속 Backplane
- **Wedge Lock**: 보드 고정/탈거 메커니즘
- **Card Guide**: 보드 슬롯 가이드 레일
- **TDR**: Time Domain Reflectometry
- **PDN**: Power Distribution Network

### B. 참고 자료

- VITA 46 Specification
- IEEE 1101.10/11
- MIL-STD-461
- IPC-6012 Class 3

---

**문서 버전**: 1.0  
**최종 수정**: 2025-02-11  
**작성자**: 나무
