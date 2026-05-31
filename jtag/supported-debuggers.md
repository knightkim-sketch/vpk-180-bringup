# VPK-180 지원 디버거 목록

VPK-180 (Versal Premium VP1802) 보드에서 사용 가능한 JTAG 디버거 목록입니다.
AMD hw_server를 통해 Vivado / Vitis와 연동됩니다.

---

## 지원 디버거 목록

### AMD 공식 케이블

| 제품명 | Part Number | 비고 |
|---|---|---|
| **SmartLynq+ Module** | HW-SMARTLYNQ-PLUS-G | Versal 권장, HSDP 지원 |
| SmartLynq Data Cable | HW-SMARTLYNQ-G (DLC20) | JTAG 전용 |
| Platform Cable USB II | DLC10 | 범용 JTAG |
| Platform Cable USB | DLC9G / DLC9LP / DLC9 | 구형, 기본 JTAG |

### Digilent 케이블

| 제품명 | 비고 |
|---|---|
| JTAG-HS3 | 고속, 권장 |
| JTAG-HS2 | 범용 |
| JTAG-HS1 | 범용 |
| JTAG-SMT2 | 표면실장 모듈 |
| JTAG-SMT1 | 표면실장 모듈 |

### 비호환 제품

| 제품명 | 사유 |
|---|---|
| AIJI System Openice-A1000 | Versal JTAG 체인 미지원, hw_server 연동 불가, ARM 모바일폰 전용 |

---

## VPK-180 JTAG 커넥터

| 커넥터 | 사양 | 용도 |
|---|---|---|
| **J36** | 2mm 2×7 shrouded 헤더 | 외부 JTAG 포드 연결 |
| **J369** | USB-C | 온보드 FT4232HL USB-JTAG 브리지 / HSDP |
| **J37** | JTAG MUX 점퍼 | 내부(FT4232) ↔ 외부 포드 선택 |

**J37 점퍼 설정:**
- 핀 2-3 단락: 온보드 FT4232 (USB-C) 사용
- 핀 1-2 단락: 외부 JTAG 포드 (J36) 사용

---

## SmartLynq+ Module 상세

### 개요

SmartLynq+ Module(HW-SMARTLYNQ-PLUS-G)은 Versal Adaptive SoC를 위한 AMD 공식 고속 디버그/트레이스 모듈입니다.
HSDP(High-Speed Debug Port)를 통해 표준 JTAG 대비 최대 100배 빠른 디버깅이 가능합니다.

### 하드웨어 사양

| 항목 | 내용 |
|---|---|
| Part Number | HW-SMARTLYNQ-PLUS-G |
| HSDP 속도 | 최대 10 Gb/s |
| 트레이스 메모리 | 14 GB |
| 동작 온도 | 10°C ~ 26°C |

**호스트 측 인터페이스:**
- USB 3.0
- Gigabit Ethernet (권장)
- DC 전원 잭
- GPIO 8-bit

**타겟 측 인터페이스:**
- USB-C (HSDP 연결)
- 14-pin JTAG 커넥터
- MICTOR-38 커넥터
- PC4 커넥터

### VPK-180 연결 방법

#### HSDP 모드 (권장)

```
SmartLynq+ [USB-C] ──USB-C 케이블──> VPK-180 [J369]
SmartLynq+ [Ethernet] ──────────────> 호스트 PC (Ethernet)
```

1. SmartLynq+의 USB-C 포트 → VPK-180 J369 연결
2. SmartLynq+의 Ethernet 포트 → 호스트 PC 연결
3. J37 점퍼: 핀 2-3 단락 (FT4232 모드)
4. SmartLynq+ 전원 인가 후 디스플레이에 IP 주소 표시될 때까지 대기 (약 2분)

#### JTAG 전용 모드

```
SmartLynq+ [14-pin JTAG] ──리본 케이블──> VPK-180 [J36]
SmartLynq+ [Ethernet or USB] ────────────> 호스트 PC
```

1. SmartLynq+의 14-pin JTAG → VPK-180 J36 연결
2. J37 점퍼: 핀 1-2 단락 (외부 포드 모드)

### 소프트웨어 설치

#### Windows

1. SmartLynq+를 USB-B 케이블로 PC에 연결
2. 장치 관리자에서 RNDIS 장치 확인
3. 우클릭 → "드라이버 업데이트" → "목록에서 직접 선택"
4. 제조사: Microsoft / 모델: "USB RNDIS Adapter" 선택
5. 설치 완료 후 모듈 IP: `10.0.0.2` 로 접근 가능

#### Linux

```bash
# 네트워크 인터페이스 확인
ifconfig

# SmartLynq+ 인터페이스 설정 (eth1은 실제 인터페이스명으로 변경)
sudo ifconfig eth1 10.0.0.1 netmask 255.255.0.0

# SmartLynq+: 10.0.0.2 로 접근
```

#### MicroSD 카드 이미지

SmartLynq+에 microSD 카드 이미지를 최신 버전으로 플래시해야 합니다.

1. [AMD Wiki](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/1145634826/SmartLynq+Module)에서 최신 이미지 다운로드
2. microSD 카드에 플래시
3. SmartLynq+에 삽입 후 전원 인가
4. 설치 완료까지 약 2분 대기 (전원 절대 차단 금지)

### Vivado Hardware Manager 연결

```tcl
# Tcl 콘솔에서 직접 연결
connect_hw_server -url TCP:<SmartLynq+_IP>:3121
```

또는 GUI:

1. Vivado → Hardware Manager → "Open Target" → "Open New Target"
2. "Connect to": **Remote Server** 선택
3. Host name: SmartLynq+ 디스플레이에 표시된 IP 주소 입력
4. Port: `1534`
5. "Next" → 연결된 Versal 디바이스 확인

### Vitis IDE 디버그 연결

1. 소프트웨어 프로젝트 우클릭 → "Debug As" → "Debug Configurations"
2. 타겟 프로세서 선택:
   - APU: `ARM Cortex-A72`
   - RPU: `ARM Cortex-R5F`
   - 소프트 코어: `MicroBlaze`
3. Connection: SmartLynq+ IP 주소 입력
4. "Debug" 클릭하여 세션 시작

**UART 접근 (SSH):**
```bash
ssh <SmartLynq+_IP>
# 접속 후 minicom으로 UART 콘솔 접근
minicom
```

### HSDP 설계 활성화 (Vivado)

HSDP 모드를 사용하려면 Versal CIPS IP 코어에 HSDP를 활성화해야 합니다.

1. Versal CIPS IP 설정 → Debug 탭 → HSDP 섹션
2. "High-Speed Debug Port (HSDP)" 활성화
3. Aurora 설정:
   - Pathway: `AURORA`
   - GT Selection: `HSDP1 GT`
   - GT Refclk Selection: `REFCLK1`
   - GT Refclk Freq: `156.25 MHz`
   - Line rate: `10.0 Gb/s`
4. 디자인 재생성

### JTAG 부트 모드 디버깅

Versal은 JTAG 부트 시 Cortex-A72 / R5F 코어가 리셋 상태로 유지됩니다.
디버깅 전 XSDB에서 수동으로 리셋을 해제해야 합니다.

```tcl
# XSDB Tcl 콘솔
connect
targets
# 타겟 ID 확인 후
target <ID>
rst -processor
```

### 지원 디버그 기능

| 기능 | 지원 여부 |
|---|---|
| APU Cortex-A72 디버그 | O |
| RPU Cortex-R5F 디버그 | O |
| MicroBlaze 디버그 | O |
| 멀티코어 동시 디버그 | O |
| 브레이크포인트 / 워치포인트 | O |
| 메모리 뷰 | O |
| QSPI / OSPI 플래시 프로그래밍 | O |
| eMMC 프로그래밍 | O |
| Tcl 스크립트 자동화 | O |
| HSDP 고속 다운로드 | O (설계 활성화 필요) |

---

## 참고 문서

- [UG1582 VPK180 Evaluation Board User Guide](https://docs.amd.com/r/en-US/ug1582-vpk180-eval-bd)
- [UG1514 SmartLynq+ Module User Guide](https://docs.amd.com/r/en-US/ug1514-smartlynq-plus-module)
- [UG908 Vivado Programming and Debugging - JTAG Cables](https://docs.amd.com/r/en-US/ug908-vivado-programming-debugging/JTAG-Cables-and-Devices-Supported-by-hw_server)
- [AMD Wiki: SmartLynq+ Module](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/1145634826/SmartLynq+Module)
- [Versal Embedded Design Tutorial - HSDP with SmartLynq+](https://xilinx.github.io/Embedded-Design-Tutorials/docs/2023.1/build/html/docs/Introduction/Versal-EDT/docs/6-system-design-example-HSDP.html)
