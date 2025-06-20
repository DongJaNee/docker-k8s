## 1. Git and Visual Studio Code download 
https://teddynote.com/10-RAG%EB%B9%84%EB%B2%95%EB%85%B8%ED%8A%B8/%ED%99%98%EA%B2%BD%20%EC%84%A4%EC%A0%95%20(Windows)/

## 2. wsl Install / Update

참고자료 : https://pezpqicf.gensparkspace.com/

1) BIOS에서 System Configuration -> Processor Setting -> VT-X / VT-D 활성화 

2) 검색에 %userprofile% -> .wslconfig 파일 생성 (text파일)
```
.wslconfig 파일
[wsl2]
memory=400GB       # 시스템 메모리의 약 78%, Llama 3.3 70B 요구사항 충족
processors=48      # CPU 코어의 75%, 병렬 처리 최적화
swap=64GB          # 메모리 부족 시 스왑 공간
localhostForwarding=true  # 네트워크 성능 개선
```
3) Docker Desktop Install 

4) NVIDIA Tool Kit Install
```
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
```

```
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | sudo tee/etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```
sudo apt update
```

```
sudo apt install -y nvidia-container-toolkit
```

```
sudo nvidia-ctk runtime configure --runtime=docker
```

```
docker run --rm --gpus all nvidia/cuda:12.0.0-base-ubuntu22.04 nvidia-smi
```

5) Docker Container Install
```
git clone https://github.com/n8n-io/self-hosted-ai-starter-kit.git
```

```
cd self-hosted-ai-starter-kit
```

