# Ubuntu 환경에서 CLI 환경으로 docker, 쿠버네티스 구성 및 Portainer GUI로 관리 

## 1. 구성 
PC : 사내 노트북 PC 

Ubuntu Version : 22.04 LTS

Volume : 95G

## 2. 진행
### 1. CLI에서 Docker 설치
```
# Ubuntu 기준
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

# Docker 권한 설정 
sudo usermod -aG docker $USER
newgrp docker
```

### 2. Kubernetes 설치 (minikube or k3s)
```
# 필요한 도구 설치
sudo apt install -y conntrack
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Minikube 시작 (docker 드라이버로)
minikube start --driver=docker
```

### 3. Portainer 설치
```
# Docker용 Portainer 설치
docker volume create portainer_data

docker run -d \
  -p 9000:9000 -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce
```

### 4. k3s에 Portainer Agent 설치 

1. CLI에서 nodeport.yaml적용 
```
kubectl apply -f https://downloads.portainer.io/ce2-27/portainer-agent-k8s-nodeport.yaml
```

2. Agent NodePort주소 확인
```
kubectl get svc -n portainer
```

<img width="849" height="107" alt="image" src="https://github.com/user-attachments/assets/51737ade-b5bb-49ac-ae74-94dc4d745251" />

3. yaml 파일에서 127.0.0.1과 같은 Loopback 주소를 자신의 IP주소로 변경
```
sudo nano /etc/rancher/k3s/k3s.yaml
```

<img width="849" height="195" alt="image" src="https://github.com/user-attachments/assets/6f8065b5-c819-49c0-b39f-fae880f6bf57" />

4. hostname -I로 확인 

### 5. Portainer 접속 Kubernetes 관리 기능 추가
1. URL에 자신의 IP와 설정한 Port번호 : 9000 번을 이용하여 접속

<img width="1914" height="850" alt="image" src="https://github.com/user-attachments/assets/6ae374f6-ef53-4fed-b66b-8020af7a16f0" />

2. 계정 생성 후 Portainer 접속 

<img width="1914" height="1173" alt="image" src="https://github.com/user-attachments/assets/5996cd57-fcd0-4878-8e6a-3bfa7d60be87" />

3. Environment-related -> Environments에서 Add environment 클릭

<img width="1914" height="1173" alt="image" src="https://github.com/user-attachments/assets/3566a833-61a8-429b-b5db-5732192e722b" />


4. Kubernetes를 선택하고 Start Wizard 클릭

<img width="1920" height="1062" alt="image" src="https://github.com/user-attachments/assets/66324d5d-9ed9-4e74-aa66-18dbcf0ddebf" />

5 Name에 사용할 이름을 적고 Environment address에 4번에서 확인했던 IP주소와 Port번호를 입력 후 Connect 하면 완료.


## 참고 
Portainer : https://docs.portainer.io/start/install-ce/server/kubernetes/baremetal#deploy-using-yaml-manifests

Kubernetes : https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-linux/
