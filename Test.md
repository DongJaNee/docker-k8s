 1. kubectl 바이너리와 체크섬 파일 다운로드

# 최신 버전 변수 가져오기
K8S_VERSION=$(curl -L -s https://dl.k8s.io/release/stable.txt)

# 바이너리 다운로드
curl -LO "https://dl.k8s.io/${K8S_VERSION}/bin/linux/amd64/kubectl"

# 체크섬 파일 다운로드
curl -LO "https://dl.k8s.io/${K8S_VERSION}/bin/linux/amd64/kubectl.sha256"

 2. 체크섬 검증 명령어

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

kubectl 파일에 실행 권한을 부여하려면:

chmod +x kubectl
sudo mv kubectl /usr/local/bin/

sudo kubeadm init --pod-network-cidr=10.244.0.0/16

2. Kubernetes 설치 (minikube or k3s)

# 필요한 도구 설치
sudo apt install -y conntrack
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Minikube 시작 (docker 드라이버로)
minikube start --driver=docker
