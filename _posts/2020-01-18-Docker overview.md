---
layout: docker-post
title: "Docker overview"
date: 2020-01-18
tags: [docker]
excerpt: ""
docker: true
comments: true
---

`번역기사용`

[출처:get-started](https://docs.docker.com/get-started/)  

# Docker overview

Docker는 응용 프로그램 개발, 배송 및 실행을위한 개방형 플랫폼입니다. Docker를 사용하면 애플리케이션을 인프라와 분리하여 소프트웨어를 신속하게 제공 할 수 있습니다. Docker를 사용하면 애플리케이션을 관리하는 것과 동일한 방식으로 인프라를 관리 할 수 ​​있습니다. 코드를 신속하게 배송, 테스트 및 배포하기위한 Docker의 방법론을 활용하면 코드 작성과 프로덕션에서 코드 실행 사이의 지연을 크게 줄일 수 있습니다.

---

~~~
 - 애플리케이션을 인프라와 분리하여 소프트웨어를 신속하게 제공
 - 애플리케이션을 관리하는 것과 동일한 방식으로 인프라를 관리 가능
 - 코드 작성과 프로덕션에서 코드 실행 사이의 지연 감소
~~~



## The Docker platform

Docker는 컨테이너라고하는 느슨하게 격리 된 환경에서 응용 프로그램을 패키지하고 실행할 수 있는 기능을 제공합니다. 격리 및 보안을 통해 지정된 호스트에서 여러 컨테이너를 동시에 실행할 수 있습니다. 컨테이너는 하이퍼 바이저를 추가로 로드 할 필요가 없지만 호스트 시스템의 커널 내에서 직접 실행되므로 경량입니다. 즉, 가상 머신을 사용하는 경우보다 주어진 하드웨어 조합에서 더 많은 컨테이너를 실행할 수 있습니다. 실제로 가상 머신 인 호스트 머신 내에서 Docker 컨테이너를 실행할 수도 있습니다!

Docker는 컨테이너의 수명주기를 관리하기위한 툴링 및 플랫폼을 제공합니다.

 - 컨테이너를 사용하여 애플리케이션 및 지원 컴포넌트를 개발하십시오.
 - 컨테이너는 응용 프로그램을 배포하고 테스트하는 단위가됩니다.
 - 준비가되면 컨테이너 또는 조정 된 서비스로 프로덕션 환경에 응용 프로그램을 배포하십시오. 프로덕션 환경이 로컬 데이터 센터, 클라우드 공급자 또는이 둘의 하이브리드인지에 관계없이 동일하게 작동합니다.

~~~
 - 지정된 호스트에서 여러 컨테이너를 동시에 실행  
 - 가상 머신을 사용하는 경우보다 주어진 하드웨어 조합에서 더 많은 컨테이너를 실행  
 - 가상 머신 인 호스트 머신 내에서도 Docker 컨테이너를 실행 가능
~~~

---

## Docker Engine

Docker Engine은 다음과 같은 주요 구성 요소가 포함 된 클라이언트-서버 응용 프로그램입니다.

 - 데몬 프로세스 (dockerd 명령)라고하는 장기 실행 프로그램 유형의 서버입니다.
 - 프로그램이 데몬과 통신하고 수행 할 작업을 지시하는 데 사용할 수있는 인터페이스를 지정하는 REST API.
 - CLI (명령 줄 인터페이스) 클라이언트 (docker 명령)

![docker](https://docs.docker.com/engine/images/engine-components-flow.png)

CLI는 Docker REST API를 사용하여 스크립팅 또는 직접 CLI 명령을 통해 Docker 데몬을 제어하거나 상호 작용합니다. 다른 많은 Docker 응용 프로그램은 기본 API 및 CLI를 사용합니다.  

데몬은 이미지, 컨테이너, 네트워크 및 볼륨과 같은 Docker 객체를 만들고 관리합니다.  

> **참고** : Docker는 오픈 소스 Apache 2.0 라이센스에 따라 라이센스가 부여됩니다.  


## Docker를 무엇에 사용할 수 있습니까?

**애플리케이션의 빠르고 일관된 제공**  

Docker는 개발자가 응용 프로그램 및 서비스를 제공하는 로컬 컨테이너를 사용하여 표준화 된 환경에서 작업 할 수 있도록함으로써 개발 수명주기를 간소화합니다. 컨테이너는 CI/CD (Continuous Integration and Continuous Delivery(지속적인 통합 및 지속적인 제공)) 워크 플로우에 적합합니다.  

다음 예제 시나리오를 고려하십시오.  

 - 개발자는 **로컬로 코드를 작성** 하고 Docker 컨테이너를 사용하여 동료와 작업을 공유합니다.
 - Docker를 사용하여 애플리케이션을 **테스트 환경으로 푸시** 하고 **자동 및 수동 테스트를 실행** 합니다.  
 - 개발자가 버그를 발견하면 **개발 환경에서 버그를 수정** 하고 테스트 및 검증을 위해 **테스트 환경에 재배포** 할 수 있습니다.  
 - **테스트가 완료** 되면 **업데이트 된 이미지를 프로덕션 환경으로 푸시** 하는 것 만큼 간단하게 고객에게 문제를 해결할 수 있습니다.  


**반응 형 배포 및 확장**  

Docker의 컨테이너 기반 플랫폼은 이식성이 뛰어난 워크로드를 허용합니다. Docker 컨테이너는 개발자의 로컬 랩톱, 데이터 센터의 물리적 또는 가상 컴퓨터, 클라우드 공급자 또는 여러 환경에서 실행될 수 있습니다.  

---

~~~
 - 이식성이 뛰어난 워크로드를 허용
 - 개발자의 로컬 랩톱, 데이터 센터의 물리/가상 컴퓨터, 클라우드 공급자 등 여러 환경에서 실행 가능
~~~

Docker의 휴대 성과 가벼운 특성으로 인해 업무 요구에 따라 거의 실시간으로 작업량을 동적으로 쉽게 관리하고 응용 프로그램과 서비스를 확장 또는 축소 할 수 있습니다.  

**동일한 하드웨어에서 더 많은 워크로드 실행**

Docker는 가볍고 빠릅니다. 하이퍼 바이저 기반 가상 머신 대신 실행 가능하고 비용 효율적인 대안을 제공하므로 더 많은 컴퓨팅 용량을 사용하여 비즈니스 목표를 달성 할 수 있습니다. Docker는 고밀도 환경과 적은 리소스로 더 많은 작업을 수행해야하는 중소 규모 배포에 적합합니다.

~~~
 - Docker는 고밀도 환경과 적은 리소스로 더 많은 작업을 수행해야하는 중소 규모 배포에 적합
~~~

## Docker architecture

Docker는 클라이언트-서버 아키텍처를 사용합니다. Docker 클라이언트는 Docker 데몬과 통신합니다. Docker 데몬은 Docker 컨테이너를 빌드, 실행 및 배포하는 작업을 많이 수행합니다. Docker 클라이언트와 데몬은 동일한 시스템에서 실행되거나 Docker 클라이언트를 원격 Docker 데몬에 연결할 수 있습니다. Docker 클라이언트 및 데몬은 REST 소켓, UNIX 소켓 또는 네트워크 인터페이스를 통해 통신합니다.  

~~~
 - Docker - 클라이언트/서버 아키텍처 사용
 - Docker 클라이언트 - Docker 데몬과 통신
 - Docker 데몬 - 컨테이너 빌드, 실행 및 배포
~~~

![](https://docs.docker.com/engine/images/architecture.svg)

### The Docker daemon

Docker 데몬(`dockerd`)은 Docker API 요청을 수신하고 이미지, 컨테이너, 네트워크 및 볼륨과 같은 Docker 객체를 관리합니다. 데몬은 다른 데몬과 통신하여 Docker 서비스를 관리 할 수도 있습니다.  

### The Docker client

Docker 클라이언트 (`docker`)는 많은 Docker 사용자가 Docker와 상호 작용하는 기본 방법입니다. `docker run`과 같은 명령을 사용하면 클라이언트는 이러한 명령을 `dockerd`로 보내어 이를 실행합니다. `docker` 명령은 Docker API를 사용합니다. Docker 클라이언트는 둘 이상의 데몬과 통신 할 수 있습니다.

~~~
 - 사용자가 Docker와 상호 작용하는 기본 방법
 - Docker 클라이언트는 둘 이상의 데몬과 통신 가능
~~~

### Docker registries

Docker 레지스트리는 Docker 이미지를 저장합니다. Docker Hub는 누구나 사용할 수있는 공용 레지스트리이며 Docker는 기본적으로 Docker Hub에서 이미지를 찾도록 구성되어 있습니다. 당신은 당신의 자신의 개인 레지스트리를 실행할 수 있습니다. Docker Datacenter (DDC)를 사용하는 경우 Docker Trusted Registry (DTR)가 포함됩니다.

~~~
 - Docker 이미지 저장
 - Docker Hub 는 공용 레지스트리
 - 기본적으로 Docker Hub에서 이미지를 찾도록 구성
 - DDC를 사용하면 DTR이 포함
~~~

`docker pull` 또는 `docker run` 명령을 사용하면 필요한 이미지가 구성된 레지스트리에서 가져와 집니다. `docker push` 명령을 사용하면 이미지가 구성된 레지스트리로 푸시됩니다.  

### Docker objects

Docker를 사용하면 이미지, 컨테이너, 네트워크, 볼륨, 플러그인 및 기타 객체를 생성 및 사용하게됩니다. 이 섹션은 이러한 개체 중 일부에 대한 간략한 개요입니다.

#### IMAGES
이미지는 Docker 컨테이너를 만드는 지침이 포함 된 읽기 전용 템플릿입니다. 종종 이미지는 다른 이미지를 기반으로 하며 추가 사용자 정의가 있습니다. 예를 들어, `ubuntu` 이미지를 기반으로하는 이미지를 만들 수 있지만 Apache 웹 서버와 응용 프로그램 및 응용 프로그램을 실행하는 데 필요한 구성 세부 정보를 설치합니다.  

~~~
 - 컨테이너를 만드는 지침이 포함된 읽기 전용 템플릿
~~~

자신의 이미지를 만들거나 다른 사람이 만들고 레지스트리에 게시 한 이미지 만 사용할 수 있습니다. 자신의 이미지를 작성하려면 이미지를 작성하고 실행하는 데 필요한 단계를 정의하기위한 간단한 구문으로 Dockerfile을 작성하십시오. Dockerfile의 각 명령어는 이미지에 레이어를 만듭니다. Dockerfile을 변경하고 이미지를 다시 빌드하면 변경된 레이어 만 다시 빌드됩니다. 이것은 다른 가상화 기술과 비교할 때 이미지를 매우 작고 빠르게 만드는 이유 중 하나입니다.

~~~
 - 자신의 이미지와 다른 사람이 만들고 레지스트리에 게시한 이미지만 사용가능
 - Dockerfile의 각 명령어는 이미지에 레이어를 만듬
 - Dockerfile을 변경하고 이미지를 다시 빌드하면 변경된 레이어만 다시 빌드
~~~

#### docker run 명령 예
다음 명령은 `ubuntu` 컨테이너를 실행하고 로컬 명령 줄 세션에 대화식으로 연결 한 다음 `/bin/bash` 를 실행합니다.  

~~~linux
$ docker run -i -t ubuntu /bin/bash
~~~

이 명령을 실행하면 다음이 발생합니다 (기본 레지스트리 구성을 사용한다고 가정).  

1. 로컬에 `ubuntu` 이미지가 없으면 `Docker pull ubuntu`를 수동으로 실행 한 것처럼 Docker가 구성된 레지스트리에서 이미지를 가져옵니다.  
2. `docker container create` 명령을 수동으로 실행 한 것처럼 Docker가 새 컨테이너를 만듭니다.  
3. Docker는 읽기-쓰기 파일 시스템을 컨테이너에 최종 레이어로 할당합니다. 이를 통해 실행중인 컨테이너가 로컬 파일 시스템에서 파일과 디렉토리를 작성하거나 수정할 수 있습니다.  
4. Docker는 네트워킹 옵션을 지정하지 않았으므로 컨테이너를 기본 네트워크에 연결하기위한 네트워크 인터페이스를 만듭니다. 여기에는 컨테이너에 IP 주소 할당이 포함됩니다. 기본적으로 컨테이너는 호스트 시스템의 네트워크 연결을 사용하여 외부 네트워크에 연결할 수 있습니다.  
5. Docker는 컨테이너를 시작하고 `/bin/bash`를 실행합니다. 컨테이너가 대화식으로 실행되고 터미널에 연결되어 있기 때문에 (`-i` 및 `-t` 플래그로 인해) 출력이 터미널에 기록되는 동안 키보드를 사용하여 입력을 제공 할 수 있습니다.  
6. `exit`를 입력하여 `/bin/bash` 명령을 종료하면 컨테이너가 중지되지만 제거되지는 않습니다. 다시 시작하거나 제거 할 수 있습니다.  

~~~
 - 구성된 레지스트리에서 이미지를 가져옴
 - Docker가 새 컨테이너를 만듬
 - 읽기-쓰기 파일 시스템을 컨테이너에 최종 레이어로 할당
 - 기본 네트워크에 연결하기 위한 인터페이스를 만듬
 - 호스트 시스템의 네트워크 연결을 사용하여 외부 네트워크에 연결 가능
~~~

#### SERVICES

서비스를 사용하면 여러 Docker 데몬에서 컨테이너를 확장 할 수 있으며, 여러 Docker 데몬은 모두 여러 관리자 및 작업자와 함께 떼로 작동합니다. swarm의 각 구성원은 Docker 데몬이며 데몬은 모두 Docker API를 사용하여 통신합니다. 서비스를 사용하면 특정 시점에 사용 가능해야하는 서비스 복제본 수와 같은 원하는 상태를 정의 할 수 있습니다. 기본적으로 서비스는 모든 작업자 노드에서로드 밸런싱됩니다. 소비자에게는 Docker 서비스가 단일 응용 프로그램 인 것처럼 보입니다. Docker Engine은 Docker 1.12 이상에서 swarm 모드를 지원합니다.

~~~
 - 여러 Docker 데몬에서 컨테이너 확장 가능
 - swarm의 각 구성원은 Docker 데몬
 - 데몬은 모두 Docker Api를 사용하여 통신
 - 소비자에게는 Docker 서비스가 단일 응용 프로그램인 것처럼 보임
~~~

## The underlying technology (기본 기술)

Docker는 Go로 작성되었으며 Linux 커널의 여러 기능을 활용하여 기능을 제공합니다.

### Namespaces

Docker는 `namespaces`라는 기술을 사용하여 컨테이너라는 격리 된 작업 공간을 제공합니다. 컨테이너를 실행할 때 Docker는 해당 컨테이너에 대한 네임 스페이스 집합을 만듭니다.  

~~~
 - 컨테이너 실행 시 해당 컨테이너에 대한 namespaces 집합을 만듬
~~~

이러한 네임 스페이스는 격리 계층을 제공합니다. 컨테이너의 각 측면은 별도의 네임 스페이스에서 실행되며 해당 네임 스페이스로 액세스가 제한됩니다.  

Docker Engine은 Linux에서 다음과 같은 네임 스페이스를 사용합니다.
 - **The `pid` namespace**: 공정 분리 Process isolation (PID: Process ID).
 - **The `net` namespace**: 네트워크 인터페이스 관리 Managing network interfaces (NET: Networking).
 - **The `ipc` namespace**: IPC 리소스에 대한 액세스 관리 Managing access to IPC resources (IPC: InterProcess Communication).
 - **The `mnt` namespace**: 파일 시스템 마운트 지점 관리 Managing filesystem mount points (MNT: Mount).
 - **The `uts` namespace**: 커널 및 버전 식별자를 격리 Isolating kernel and version identifiers (UTS: Unix Timesharing System).

### Control groups

Linux의 Docker Engine은 또한 제어 그룹 (`cgroup`)이라는 다른 기술에 의존합니다. cgroup은 응용 프로그램을 특정 리소스 집합으로 제한합니다. Control groups을 통해 Docker Engine은 사용 가능한 하드웨어 자원을 컨테이너와 공유하고 선택적으로 한계 및 제한 조건을 시행 할 수 있습니다. 예를 들어 특정 컨테이너에 사용 가능한 메모리를 제한 할 수 있습니다.

~~~
 - 응용프로그램을 특정 리소스 집합으로 제한
 - Docker Engine은 Control groups을 통해 특정 컨테이너에 사용 가능한 메모리를 제한 가능
~~~

### Union file systems

UnionFS (Union File System)는 계층을 만들어서 매우 가볍고 빠르게 만드는 파일 시스템입니다. Docker Engine은 UnionFS를 사용하여 컨테이너의 빌딩 블록을 제공합니다. Docker Engine은 AUFS, btrfs, vfs 및 DeviceMapper를 포함하여 여러 UnionFS 변형을 사용할 수 있습니다.

~~~
 - 계층을 만들어서 매우 가볍고 빠르게 만드는 파일 시스템
 - 컨테이너의 빌딩 블록 제공
 - Docker Engine은 다양한 UnionFS 사용 가능
~~~

### Container format

Docker Engine은 네임 스페이스, 컨트롤 그룹 및 UnionFS를 Container format이라는 래퍼로 결합합니다. 기본 컨테이너 형식은 `libcontainer`입니다. 앞으로 Docker는 BSD Jails 또는 Solaris Zones와 같은 기술과 통합하여 다른 컨테이너 형식을 지원할 수 있습니다.

~~~
 - Docker Engine 아래 오브젝트를 Container format이라는 래퍼로 결합
  - namespaces
  - control groups
  - UnionFS
~~~
