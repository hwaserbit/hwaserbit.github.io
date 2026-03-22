---
title: "홈랩 아키텍처 설계: Ubuntu Desktop vs Server, 무엇을 선택해야 할까?"
date: 2026-03-26 23:16:00 +0900
categories: [OS]
tags: [HomeLab, Ubuntu, Architecture, Netplan, Troubleshooting]
series: 홈랩 아키텍처 구축기
description: "홈랩 아키텍처 설계의 첫 단추, 시스템 안정성과 네트워크 통제권 확보를 위한 Ubuntu Server LTS 도입 및 선택 기준을 분석합니다."

# 2. 고급 설정 (필요할 때만 true 또는 경로 입력)
pin: false                   # true로 설정하면 메인화면 최상단에 고정됨 (공지사항 등)
math: true                   # 수식($$)을 사용할 경우 true
mermaid: true                # 다이어그램(flowchart)을 그릴 경우 true
toc: true                    # 오른쪽 목차 표시 여부 (기본값 true)
comments: false               # 댓글 기능 켜기/끄기

# 3. 이미지/배너 설정
image:
  # 배너 이미지 경로
  path: /assets/img/banners/2026-03-26-ubuntu-desktop-vs-server.png
  alt: "그래픽 인터페이스(GUI)를 갖춘 우분투 데스크탑과 명령어(CLI) 기반 터미널 환경인 우분투 서버의 특징을 좌우로 대비시킨 비교 이미지" # 시각장애인용 설명 (SEO)

---

> **TL;DR (요약 로그)**
> 1. 홈랩을 구성하는 4개의 노드(스토리지, AI 연산, 모니터링, 로그 수집) OS로 **Ubuntu Server LTS**를 채택했습니다.
> 2. 서버 환경(브리지, VLAN, 고정 IP)에서는 NetworkManager보다 **`systemd-networkd`** 렌더러가 훨씬 예측 가능하고 안정적입니다.
> 3. 불필요한 GUI 데몬이 없어야 자원 오버헤드와 보안 취약점(Attack Surface)을 최소화할 수 있습니다.
{: .prompt-info }

<br>

```bash
root@hwaserbit:~# cat /etc/os-release | grep PRETTY_NAME
PRETTY_NAME="Ubuntu 24.04 LTS"
```

## 1. Init System: 홈랩 하드웨어 롤(Role) 정의



안정적인 홈랩(Home Lab) 인프라를 구축하기 위한 첫걸음은, 각 물리/가상 노드의 역할(Role)을 명확히 정의하고 그에 맞는 운영체제(OS)를 베이스로 까는 것입니다.

현재 제가 설계하고 구축 중인 홈랩의 하드웨어와 역할은 다음과 같습니다.

| 노드(하드웨어) | 핵심 역할 (Role) | 요구사항 | 
| :---: | :---: | :---: | 
| **TOPTON Intel 8505 i226-V 2.5G 6 lans** | Pfsense + Gateway | 높은 RAM 용량, 저전력 |
| **ODROID-H4 PLUS** | 스토리지 | 저전력, 안정성 |
| **i5-13600k + A770 16GB** | AI 연산 + 고성능 프로젝트 | 높은 RAM, VRAM 용량, 높은 연산 성능 및 최신 GPU 기술, 전성비 |

각 노드는 목적이 다르지만, 공통적인 특징이 있습니다. 모두 모니터 없이 원격으로 제어되는 **헤드리스(Headless)** 운영을 전제로 하며, 복잡한 네트워크(브리지, VLAN)와 하드웨어 가속(HWA)을 사용한다는 점입니다.

이러한 요구사항을 분석한 결과, 모든 노드의 기반 OS로 **Ubuntu Server LTS**를 채택했습니다. 왜 Desktop 버전이 아닌 **Server 버전**을 선택했는지, 인프라 운영 관점에서 그 이유를 3가지로 분석했습니다.

## 2. 네트워크 렌더러의 차이: 안정성 vs 편의성

Ubuntu Desktop과 Server의 가장 치명적인 차이는 바로 **기본 네트워크 렌더러(Renderer)**에 있습니다. 두 OS 모두 겉으로는 **netplan**을 사용하여 네트워크를 설정하지만, 그 설정값을 실제로 시스템에 적용하는 **백엔드 엔진**이 다릅니다.

Ubuntu Desktop **(NetworkManager)**: Wi-Fi 연결, 잦은 IP 변경 등 동적이고 모바일 친화적인 환경에 맞춰져 있습니다. GUI 환경에서 클릭 몇 번으로 설정하기는 편하지만, 서버급의 복잡한 라우팅이나 브리지 설정 시 돌발 변수가 발생할 확률이 높습니다.

Ubuntu Server **(systemd-networkd)**: 오직 서버(Headless) 환경을 위해 디자인되었습니다. 브리지(Bridge), VLAN, 고정 IP 라우팅을 선언적인(Declarative) 방식으로 완벽하게 통제하며, 재부팅 시에도 100% 예측 가능한 상태를 보장합니다.

**H4 PLUS**나 **13600k** 노드처럼 다수의 가상화 컨테이너, 가상환경(docker, KVM)가 브리지 네트워크를 물고 통신해야 하는 환경에서는, 변동성이 큰 NetworkManager 대신 systemd-networkd를 사용하는 것이 시스템 장애를 예방하는 첫 번째 방어선입니다.

```bash
# 현재 적용된 렌더러 확인 방법
netplan get | grep renderer
```

```bash
# 결과
renderer: networkd
```

---

## 3. 자원 오버헤드와 공격 표면(Attack Surface) 최소화

서버 인프라 설계의 핵심은 **"꼭 필요한 것만 남기고 전부 제거한다"**는 원칙입니다.

Desktop 버전을 설치하면 gnome-shell, gdm3 같은 무거운 GUI 데몬뿐만 아니라 파일 인덱서(tracker), 프린터 서비스(cups), 블루투스 데몬 등 서버 구동에 전혀 쓸모없는 백그라운드 프로세스들이 상시 실행됩니다.

이는 단순히 RAM이나 CPU를 낭비하는 `오버헤드`의 문제를 넘어섭니다.

업데이트 폭발: GUI 관련 패키지들이 얽혀 있어 패키지 업데이트 주기가 잦아지고, 업데이트 시 예기치 않은 의존성 충돌로 서버가 다운될 위험이 커집니다.

보안 취약점 증가: 백그라운드에서 도는 포트와 데몬이 많아질수록 외부 공격자가 침투할 수 있는 표면적(Attack Surface)이 넓어집니다.

**H4 PLUS**에서 NFS와 SMB, webdav등 을 돌리고, **13600k**에서 A770 GPU로 AI 연산/추론과 인코딩 연산을 수행하는 데 GUI는 전혀 필요하지 않습니다. 철저하게 통제된 CLI 환경에서 컨테이너 기반으로 서비스를 올리는 것이 압도적으로 안전합니다.

---

## 4. "Desktop을 깔고 GUI만 끄면 되지 않나요?"

초기 인프라 설계 시 많이들 하는 실수 중 하나가 *"일단 Desktop을 깔고, systemctl set-default multi-user.target으로 GUI만 내리고 쓰자"*는 접근입니다.

하지만 이 방법은 인프라 관점에서 매우 위험합니다. GUI 프로세스만 멈췄을 뿐, 수백 개의 Desktop 패키지와 의존성 툴킷들은 여전히 시스템에 남아있습니다. 추후 커널이나 드라이버(특히 GPU 드라이버)를 업데이트할 때 이 잔여 패키지들이 꼬이면서 원인 모를 부팅 불량(Kernel Panic)을 일으킬 수 있습니다.

애초에 군더더기 없이 최소 패키지만 설치되는 Server 버전을 선택하는 것이 시스템의 수명을 늘리는 정석입니다.

---

## 5. 서버 엔지니어의 생명줄: 안전한 네트워크 변경

원격(Headless)으로 서버를 관리할 때 가장 무서운 순간은 네트워크 설정을 바꿨다가 연결이 끊겨버리는 '셀프 격리' 상황입니다. Ubuntu Server 환경에서는 netplan의 강력한 롤백 기능을 통해 이를 방어할 수 있습니다.

```bash
# 네트워크 설정 테스트 (120초 내에 승인하지 않으면 자동 원상복구)
root@hwaserbit:~# netplan try --timeout 120
```

저는 여기에 더해 모든 노드에 관리 전용 NIC(물리 랜포트)를 별도의 고정 IP로 할당하고, 어떠한 경우에도 이 포트의 설정은 건드리지 않는 '백도어(Backdoor)' 정책을 설계했습니다. 하지만 최악의 경우도 고려하여 PiKVM을 통해 콘솔에 직접 붙어 하드 롤백을 수행할 수 있는 최후의 안전벨트도 마련해 두었습니다.

---

## 6. 결론: 편의성 대신 완벽한 통제권을 선택하다

홈랩(HomeLab) 구축은 단순히 남는 하드웨어에 운영체제를 설치하는 것을 넘어, 서비스 목적에 맞는 아키텍처를 스스로 설계하고 검증하는 과정입니다. 리눅스를 처음 접하거나 빠른 설정을 원할 경우 직관적인 GUI를 제공하는 Desktop 버전이 매력적인 선택지일 수 있습니다.

하지만 시스템 엔지니어링 관점에서 이는 **초기의 편의성과 인프라 통제권 사이의 트레이드오프(Trade-off)**입니다. 이번 홈랩 설계에서 GUI의 편리함을 덜어내고 철저히 CLI 기반의 Ubuntu Server LTS를 채택함으로써, 다음과 같은 세 가지 핵심 인프라 요건을 확보했습니다.

1. **예측 가능성(Predictability):** `NetworkManager`의 변동성 대신 `systemd-networkd`를 사용하여 브리지, VLAN 등 복잡한 네트워크 환경에 대한 선언적이고 확실한 통제권 확보
2. **자원 최적화(Resource Optimization):** 불필요한 데몬과 백그라운드 프로세스를 제거하여, 스토리징 및 AI 연산 등 핵심 워크로드에 컴퓨팅 자원 집중
3. **보안성(Security):** 불필요한 패키지 및 포트 오픈을 원천 차단하여 시스템의 공격 표면(Attack Surface) 최소화

가장 밑바탕이 되는 OS 아키텍처를 단단하고 가볍게 구축하는 것은 향후 발생할 수 있는 수많은 장애 포인트를 사전에 제거하는 정석적인 접근입니다. 

기반 인프라의 뼈대가 완성된 만큼, 앞으로 이 환경 위에서 전개될 다양한 가상화/컨테이너 서비스 도입 및 트러블슈팅 여정을 계속해서 기록해 나가겠습니다.

```bash
root@hwaserbit:~# uptime -p
up 3 days, 23 hours, 49 minutes
root@hwaserbit:~# reboot
```
