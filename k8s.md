## k8s(쿠버네티스)란?
- 컨테이너화 된 애플리케이션을 배포, 확장, 관리를 자동화하는 오픈 소스 플랫폼.

### 쿠버네티스와 도커의 관계
- 도커는 컨테이너 기술을 제공하는 플랫폼이고, 쿠버네티스는 도커를 포함한 다양한 컨테이너 런타임을 사용하여 컨테이너를 오케스트레이션하는 도구.
- 즉, 도커는 컨테이너를 만들고 실행하는 역할을 하고, 쿠버네티스는 이렇게 만들어진 컨테이너들을 관리하고 운영하는 역할.

## CRI-O란? 
- 레드햇이 개발한 CRI-O 는 Kubernetes 용 Open Container Initiative (OCI) 컨테이너 런타임.
- CRI-O는 오픈 소스 커뮤니티 중심으로한 컨테이너 엔진으로 Kubernetes 에서 주로 사용되었던 Docker 를 대체하는 것에 목적
- CRI-O는 Kubernetes 의 CRI (Container Runtime Interface) 표준 컴포넌트를 최소한의 런타임으로 구현하는 것으로, 특히 Kubernetes와의 통합을 염두에 두고 설계

## Network Plug-In이란?
- 본체 프로그램에 없던 어떤 기능을 더해 넣는(add-in) 컴퓨터 프로그램
- 기본 소프트웨어를 지원해서 특수한 기능을 확장할 수 있도록 설계된 부속 프로그램

## Kubelet이란?
- 쿠버네티스 클러스터에서 각 노드에 설치되어 실행되는 에이전트.
- 노드에서 파드(Pod) 내의 컨테이너들이 정상적으로 동작하도록 관리하는 핵심적인 역할 수행.
-  파드 스펙(PodSpec)에 정의된 대로 컨테이너가 실행되고 관리되는지 확인하고,
 노드의 상태를 클러스터의 컨트롤 플레인에 보고하며, 필요한 경우 컨테이너를 시작, 중지, 업데이트하는 등의 작업을 수행

## Pod란?
- 쿠버네티스에서 배포 가능한 가장 작은 단위, 하나 이상의 컨테이너를 묶어서 관리 
- 파드 내의 컨테이너들은 함께 배치되고, 공유된 네트워크와 스토리지를 사용하며, 동일한 호스트에서 실

-----
참고자료 

Portainer : https://docs.portainer.io/start/install-ce/server/kubernetes/baremetal#deploy-using-yaml-manifests

Kubernetes : https://kubernetes.io/docs/tutorials/cluster-management/kubelet-standalone/#prepare-the-system

