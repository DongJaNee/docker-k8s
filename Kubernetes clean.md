```
#!/bin/bash

echo " Kubernetes 및 containerd 초기화 시작"

# 1. kubeadm 초기화 (클러스터 설정 제거)
sudo kubeadm reset -f

# 2. CNI 플러그인, etcd, config 파일 제거
sudo rm -rf /etc/cni /etc/kubernetes /var/lib/etcd /var/lib/kubelet /var/lib/cni
sudo rm -rf ~/.kube

# 3. containerd 설정 초기화
sudo systemctl stop containerd
sudo apt purge -y containerd
sudo rm -rf /etc/containerd /var/lib/containerd /var/run/containerd

# 4. kubelet 제거 및 초기화
sudo systemctl stop kubelet
sudo apt purge -y kubelet kubeadm kubectl
sudo apt autoremove -y

# 5. Docker도 초기화 (선택적)
read -p "Docker도 함께 삭제하시겠습니까? [y/N]: " answer
if [[ "$answer" == "y" || "$answer" == "Y" ]]; then
    sudo systemctl stop docker
    sudo apt purge -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    sudo rm -rf /var/lib/docker /etc/docker /var/run/docker.sock
    echo " Docker 삭제 완료"
else
    echo " Docker는 유지됨"
fi

echo " Kubernetes, containerd, kubelet 초기화 완료"
```
