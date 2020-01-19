---
layout: docker-post
title: "Orientation and setup"
date: 2020-01-18
tags: [docker]
excerpt: ""
docker: true
comments: true
---

`번역기사용`

[출처:get-started](https://docs.docker.com/get-started/)  

# Orientation and setup

어서 오십시오! Docker를 배우게되어 기쁩니다. Docker Community QuickStart는 다음을 수행하는 방법을 알려줍니다.

1. Docker 환경 설정 (이 페이지에서)
2. 이미지를 빌드하고 하나의 컨테이너로 실행
3. 개발 머신에서 Kubernetes 환경 설정 및 사용
4. 개발 머신에서 Swarm 환경 설정 및 사용
5. Docker Hub에서 컨테이너화 된 응용 프로그램 공유

## Docker concepts(개념)

Docker는 개발자와 시스템 관리자가 컨테이너를 사용하여 응용 프로그램을 `build`, `share` 및 `run`할 수있는 플랫폼입니다. 컨테이너를 사용하여 응용 프로그램을 배포하는 것을 컨테이너화라고합니다. 컨테이너는 새로운 것이 아니지만 응용 프로그램을 쉽게 배포하는 데 사용됩니다.  

~~~
 - 컨테이너화 : 컨테이너를 사용하여 응용 프로그램을 배포하는 것
~~~


컨테이너는 다음과 같은 이유로 컨테이너화가 점차 대중화되고 있습니다.

 - Flexible(융통성) : 가장 복잡한 응용 프로그램도 컨테이너화 할 수 있습니다.
 - Lightweight(경량) : 컨테이너는 호스트 커널을 활용하고 공유하므로 가상 시스템보다 시스템 리소스 측면에서 훨씬 효율적입니다.
 - Portable(휴대용) : 로컬로 구축하고 클라우드에 배포하며 어디에서나 실행할 수 있습니다.
 - Loosely coupled(느슨한결합) : 컨테이너는 자체적으로 충분하고 캡슐화되어 있으므로 다른 컨테이너를 방해하지 않고 컨테이너를 교체하거나 업그레이드 할 수 있습니다.
 - Scalable(확장가능) : 데이터 센터에 컨테이너 복제본을 늘리고 자동으로 배포 할 수 있습니다.
 - Secure(안전한) : 컨테이너는 사용자가 구성 할 필요없이 공격적인 제약과 격리를 프로세스에 적용합니다.

Docker는 응용 프로그램 개발, 배송 및 실행을위한 개방형 플랫폼입니다. Docker를 사용하면 애플리케이션을 인프라와 분리하여 소프트웨어를 신속하게 제공 할 수 있습니다. Docker를 사용하면 애플리케이션을 관리하는 것과 동일한 방식으로 인프라를 관리 할 수 ​​있습니다. 코드를 신속하게 배송, 테스트 및 배포하기위한 Docker의 방법론을 활용하면 코드 작성과 프로덕션에서 코드 실행 사이의 지연을 크게 줄일 수 있습니다.

## Images and containers

기본적으로 컨테이너는 실행중인 프로세스 일 뿐이며 호스트 및 다른 컨테이너와 격리하기 위해 캡슐화 기능이 추가되어 있습니다. 컨테이너 격리의 가장 중요한 측면 중 하나는 각 컨테이너가 자체 개인 파일 시스템과 상호 작용한다는 것입니다. 이 파일 시스템은 Docker 이미지에 의해 제공됩니다. 이미지에는 응용 프로그램을 실행하는 데 필요한 모든 것 (코드 또는 이진, 런타임, 종속성 및 기타 필요한 파일 시스템 개체)이 포함됩니다.

~~~
 - 컨테이너 : 실행중인 프로세스일 뿐
 - 컨테이너 격리 : 각 컨테이너가 자체 개인 파일 시스템과 상호 작용
 - 이미지 : 응용프로그램을 실행하는 모든 것을 포함
~~~

## Containers and virtual machines

컨테이너는 Linux에서 기본적으로 실행되며 호스트 시스템의 커널을 다른 컨테이너와 공유합니다. 별도의 프로세스를 실행하여 다른 실행 파일보다 더 많은 메모리를 사용하지 않아 경량화됩니다.

반대로 가상 머신 (VM)은 하이퍼 바이저를 통해 호스트 리소스에 가상으로 액세스 할 수 있는 완전한 "게스트"운영 체제를 실행합니다. 일반적으로 VM은 응용 프로그램 논리에서 소비하는 것보다 많은 오버 헤드를 발생시킵니다.

~~~
 - 컨테이너는 별도의 프로세스를 실행하여 다른 실행 파일보다 적은 메모리를 사용해 경량화
 - 가상머신(VM)은 응용 프로그램 논리에서 소비하는 것보다 많은 오버 헤드 발생
~~~

![](https://docs.docker.com/images/Container%402x.png)


## Orchestration
컨테이너화 된 프로세스의 이식성과 재현성은 클라우드 및 데이터 센터에서 컨테이너화 된 애플리케이션을 이동하고 확장 할 수 있는 기회를 의미합니다. 컨테이너는 이러한 애플리케이션이 어디에서나 동일한 방식으로 실행되도록 효과적으로 보장하므로 이러한 모든 환경을 빠르고 쉽게 활용할 수 있습니다. 또한 애플리케이션을 확장함에 따라 애플리케이션의 유지 관리를 자동화하고 실패한 컨테이너를 자동으로 교체하고 수명주기 동안 해당 컨테이너의 업데이트 및 재구성 롤아웃을 관리 할 수있는 툴링이 필요합니다.  

컨테이너화 된 응용 프로그램을 관리, 확장 및 유지 관리하는 도구를 오케 스트레이터라고하며 가장 일반적인 예로는 Kubernetes 및 Docker Swarm이 있습니다. 이 두 오케 스트레이터의 개발 환경 배포는 Docker Desktop에서 제공하며 이 가이드 전체에서, 첫 번째 오케스트레이션 된 컨테이너화 된 응용 프로그램을 만드는 데 사용됩니다.

~~~
 - orchestrators : 컨테이너화 된 응용 프로그램을 관리, 확장 및 유지 관리하는 도구  
 예) Kubernetes 및 Docker Swarm  
 - Kubernetes 및 Docker Swarm의 개발 환경 배포는 Docker Desktop에서 제공
~~~


# Install Docker Desktop

컨테이너화 된 애플리케이션 개발을 시작하는 가장 좋은 방법은 OSX 또는 Windows 용 Docker Desktop을 사용하는 것입니다. Docker Desktop을 사용하면 로컬 개발 컴퓨터에서 Kubernetes 또는 Swarm을 쉽게 설정할 수 있으므로 응용 프로그램을 개발중인 오케 스트레이터의 모든 기능을 클러스터없이 바로 사용할 수 있습니다. 운영 체제에 적합한 설치 지시 사항을 따르십시오.  

# Enable Kubernetes

Docker Desktop은 Kubernetes를 빠르고 쉽게 설정할 수 있습니다. 운영 체제에 적합한 설정 및 유효성 검사 지침을 따르십시오.  

## WINDOWS

1. Docker Desktop을 설치하면 시스템 트레이에 Docker 아이콘이 나타납니다. 마우스 오른쪽 버튼으로 클릭하고 **Settings -> Kubernetes** 를 탐색하십시오.  
2. Kubernetes 사용 확인란을 선택하고 **Apply** 을 클릭하십시오. Docker Desktop이 자동으로 Kubernetes를 설정합니다. 이 작업에는 상당한 시간이 소요될 수 있습니다 (20 분). 메뉴 표시 줄에서 Docker 아이콘을 마우스 오른쪽 버튼으로 클릭하고 **Settings** 을 클릭 한 다음 'Kubernetes is running'옆에 녹색 표시등이 표시되면 모든 것이 성공적으로 완료된 것입니다.  
3. Kubernetes가 시작되어 실행 중인지 확인하려면 다음 내용으로 `pod.yaml`이라는 텍스트 파일을 만듭니다.

~~~
apiVersion: v1
kind: Pod
metadata:
  name: demo
spec:
  containers:
  - name: testpod
    image: alpine:3.5
    command: ["ping", "8.8.8.8"]
~~~

간단한 핑을 8.8.8.8로 격리하는 단일 컨테이너가있는 포드를 설명합니다.  
4. powershell에서 `pod.yaml`을 생성 한 위치로 이동하여 포드를 만듭니다.  
~~~
 kubectl apply -f pod.yaml
~~~
5. 포드가 작동하고 실행 중인지 확인하십시오.  
~~~
 kubectl get pods
~~~
다음과 같이 보일 것입니다 :  
~~~
NAME      READY     STATUS    RESTARTS   AGE
demo      1/1       Running   0          4s
~~~
6. 핑 프로세스에 필요한 로그가 있는지 확인하십시오.  
~~~
 kubectl logs demo
~~~
정상적인 핑 프로세스의 결과가 표시되어야합니다.  
~~~
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: seq=0 ttl=37 time=21.393 ms
64 bytes from 8.8.8.8: seq=1 ttl=37 time=15.320 ms
64 bytes from 8.8.8.8: seq=2 ttl=37 time=11.111 ms
...
~~~
7. 마지막으로 테스트 포드를 분리하십시오.  
~~~
 kubectl delete -f pod.yaml
~~~

# Enable Docker Swarm

Docker Desktop은 기본적으로 Swarm을 실행하는 데 필요한 모든 것을 갖춘 Docker Engine에서 실행됩니다. 운영 체제에 적합한 설정 및 검증 지침을 따르십시오.  

## WINDOWS

1. powershell을 열고 Docker Swarm 모드를 초기화하십시오.  
~~~
 docker swarm init
~~~
모두 제대로 작동하면 다음과 유사한 메시지가 나타납니다.  
~~~
Swarm initialized: current node (tjjggogqpnpj2phbfbz8jd5oq) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3e0hh0jd5t4yjg209f4g5qpowbsczfahv2dea9a1ay2l8787cf-2h4ly330d0j917ocvzw30j5x9 192.168.65.3:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
~~~
2. 알파인 기반 파일 시스템을 사용하고 핑을 8.8.8.8로 격리하는 간단한 Docker 서비스를 실행하십시오.  
~~~
 docker service create --name demo alpine:3.5 ping 8.8.8.8
~~~
3. 서비스가 하나의 컨테이너를 작성했는지 확인하십시오.  
~~~
 docker service ps demo
~~~
다음과 같이 보일 것입니다 :  
~~~
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE           ERROR               PORTS
463j2s3y4b5o        demo.1              alpine:3.5          docker-desktop      Running             Running 8 seconds ago
~~~
4. 핑 프로세스에 필요한 로그가 있는지 확인하십시오.  
~~~
 docker service logs demo
~~~
정상적인 핑 프로세스의 결과가 표시되어야합니다.  
~~~
demo.1.463j2s3y4b5o@docker-desktop    | PING 8.8.8.8 (8.8.8.8): 56 data bytes
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=0 ttl=37 time=13.005 ms
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=1 ttl=37 time=13.847 ms
demo.1.463j2s3y4b5o@docker-desktop    | 64 bytes from 8.8.8.8: seq=2 ttl=37 time=41.296 ms
...
~~~
5. 마지막으로 테스트 서비스를 해체하십시오.  
~~~
 docker service rm demo
~~~

# Conclusion

이제 개발 시스템에 Docker Desktop을 설치했으며 Kubernetes 및 Swarm에서 간단한 컨테이너화 된 워크로드를 실행할 수 있음을 확인했습니다. 다음 섹션에서는 첫 번째 컨테이너화 된 응용 프로그램 개발을 시작합니다.
