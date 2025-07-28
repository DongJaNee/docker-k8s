# 1. Open WebUI에서 GPU를 사용하지않고 CPU 를 사용하는 경우. 

## Docker 및 WSL2에서 Ollama가 내 GPU를 사용하도록 하는 방법

<img width="758" height="424" alt="image" src="https://github.com/user-attachments/assets/e128f15e-9e04-4db5-bccd-ac94c0b05c37" />

```
services:

webui:

image: ghcr.io/open-webui/open-webui:main

container_name: webui

ports:

- 7000:8080/tcp

volumes:

- open-webui:/app/backend/data

extra_hosts:

- host.docker.internal:host-gateway

depends_on:

- ollama

restart: unless-stopped

ollama:

image: ollama/ollama

container_name: ollama

deploy:

resources:

reservations:

devices:

- driver: nvidia

count: 1

capabilities:

- gpu

environment:

- TZ=Asia/Seoul

- gpus=all

expose:

- 11434/tcp

ports:

- 11434:11434/tcp

healthcheck:

test: ollama --version || exit 1

volumes:

- ollama:/root/.ollama

restart: unless-stopped

volumes:

ollama: null

open-webui: null

networks: {}
```

### NVIDIA GPU
**NVIDIA Container Toolkit**을 설치 하세요 .

Apt로 설치

1. 저장소 구성
```
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey \
    | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list \
    | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' \
    | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt-get update
```

2. NVIDIA Container Toolkit 패키지 설치
```
sudo apt-get install -y nvidia-container-toolkit
```

3. Yum 또는 Dnf로 설치
1) 저장소 구성
```
curl -s -L https://nvidia.github.io/libnvidia-container/stable/rpm/nvidia-container-toolkit.repo \
    | sudo tee /etc/yum.repos.d/nvidia-container-toolkit.repo
```

2) NVIDIA Container Toolkit 패키지 설치
```
sudo yum install -y nvidia-container-toolkit
```

Docker를 구성하여 Nvidia 드라이버를 사용하세요
```
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```
컨테이너를 시작하세요
```
docker run -d --gpus=all -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
```

---

## Docker 빠른 시작
※ Open WebUI가 제대로 작동하려면 WebSocket 지원이 필요. 네트워크 구성에서 WebSocket 연결이 허용되는지 확인.

**Ollama가 컴퓨터에 설치되어 있다면** 다음 명령 사용 
```
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

**Nvidia GPU 지원으로 Open WebUI를 실행하려면** 다음 명령 사용 
```
docker run -d -p 3000:8080 --gpus all --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:cuda
```

**Ollama와 함께 제공되는 Open WebUI**
이 설치 방법은 Open WebUI와 Ollama를 번들로 제공하는 단일 커네팅너 이미지를 사용하므로 단일 명령으로 간편하게 설치할 수 있다. 하드웨어 설정에 따라 적절한 명령을 선택
- **GPU 지원** : 다음 명령을 실행하여 GPU 리소스 활용
```
docker run -d -p 3000:8080 --gpus=all -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:ollama
```
- **CPU에만 해당** : GPU를 사용하지 않는 경우 대신 다음 명령 사용
```
docker run -d -p 3000:8080 -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:ollama
```
두 명령 모두 Open WebUI와 Ollama를 기본으로 간편하게 설치하여 모든 것을 신속하게 작동시킬 수 있다.

**Open**
Open WebUI 컨테이너를 쉽게 업데이트하려면 다음 단계를 따르세요.

**수동**
Watchtower를 사용하여 Docker 컨테이너를 수동으로 업데이트
```
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --run-once open-webui
```

**자동**
5분마다 컨테이너를 자동으로 업데이트
```
docker run -d --name watchtower --restart unless-stopped -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --interval 300 open-webui
```

### 수동
Open WebUI를 설치하고 실행하는 두 가지 주요 방법이 있다. uv 런타임 관리자를 사용하거나 Python을 사용하는 것 pip.
두 방법 모두 효과적이지만, 환경 관리가 간소화되고 잠재적 충돌이 최소화되므로. NET Framework를 사용하는 것을 강력히 권장.

**uv**
런타임 관리자 uv는 Open WebUI와 같은 애플리케이션의 Python 환경 관리를 원활하게 해준다. 시작하려면 다음 단계를 따르세요.

1. **uv**
운영 체제에 맞는 설치 명령을 선택
- **윈도우** :
```
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```
- **macOS/리눅스**
```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

2. Open
설치가 완료되면 **uv** Open WebUI를 실행하는 것은 매우 간단하다. 아래 명령을 사용하여 **DATA_DIR** 데이터 손실을 방지하고 환경 변수를 설정. 각 플랫폼에 대한 예시 경로는 다음과 같다.
- **윈도우** :
```
$env:DATA_DIR="C:\open-webui\data"; uvx --python 3.11 open-webui@latest serve
```

- **macOS/리눅스**
```
DATA_DIR=~/.open-webui uvx --python 3.11 open-webui@latest serve
```

**pip**
Python 패키지 관리자를 사용하여 Open WebUI를 설치하는 사용자는 **pip** 와 같은 Python 런타임 관리자를 사용하는 것이 좋다. **uv**, **conda**.
이러한 도구는 Python 환경을 효과적으로 관리하고 충돌을 방지하는 데 도움이 된다.

개발 환경은 Python 3.11입니다. Python3.12는 작동하는 것으로 보이지만 아직 충분히 테스트되지 않았습니다. Python 3.13은 아직 완전히 테스트되지 않았습니다.

1. **Open WebUI설치**
터미널을 열고 다음 명령을 실행.
```
pip install open-webui
```

2. Open WebUI 시작
설치가 완료되면 다음을 사용하여 서버를 시작
```
open-webui serve
```

**Open**
최신 버전으로 업데이트하려면 다음을 실행
```
pip install --upgrade open-webui
```


## 비슷한 사례 1

<img width="747" height="847" alt="image" src="https://github.com/user-attachments/assets/8091db03-f5b9-4087-882e-ccdb3c9021eb" />


- 해결 방안 : Nvidia의 APT 저장소에서 최신 드라이버를 설치

**Linux용 Windows 하위 시스템 (WSL)**
1) 파일 시스템에 로컬 저장소 설치 
```
# dpkg -i cuda-repo-<distro>-X-Y-local_<version>*_amd64.deb
```
2) 임시 공개 GPG키 등록
```
# cp /var/cuda-repo-<distro>-X-Y-local/cuda-*-keyring.gpg /usr/share/keyrings/
```
3) CUDA 저장소의 우선순위를 지정하기 위해 핀 파일 추가
```
$ wget https://developer.download.nvidia.com/compute/cuda/repos/<distro>/x86_64/cuda-<distro>.pin
# mv cuda-<distro>.pin /etc/apt/preferences.d/cuda-repository-pin-600
```
4) 네트워크 저장소 패키지 설치
```
$ wget https://developer.download.nvidia.com/compute/cuda/repos/<distro>/x86_64/cuda-keyring_1.1-1_all.deb
# dpkg -i cuda-keyring_1.1-1_all.deb
```
5) Apt 저장소 캐시 업데이트
```
# apt update
```
6) CUDA SDK 설치
```
# apt install cuda-toolkit
```
7) NVIDIA Container Runtime을 지정
```
docker run --gpus all \
  --runtime=nvidia \
  -e NVIDIA_DRIVER_CAPABILITIES=compute,utility \
  -e NVIDIA_VISIBLE_DEVICES=all \
  ollama/ollama
```

## 비슷한 사례 2

<img width="745" height="863" alt="image" src="https://github.com/user-attachments/assets/fb0981e3-93a5-40c2-b885-0404763cfb22" />

<img width="750" height="539" alt="image" src="https://github.com/user-attachments/assets/3fe42ecd-1233-467d-9f9c-fabf4f47345c" />

### **log**

<img width="662" height="443" alt="image" src="https://github.com/user-attachments/assets/6690e177-e37d-4ef5-af66-b5c18a1b325b" />

- 해결 방안 : 
```
1)sudo modprobe nvidia 실행 
```
2) 오류가 발생하면 BIOS로 가서 secure Boot - turned off 
3) 다시 시작한 후 sudo modprobe nvidia 확인 
4) 환경 변수 설정 (bashrc 편집)
```
export CUDA_VISIBLE_DEVICES=0,1,2,3 
```
5) 컨테이너에서 GPU 확인
```
docker exec -it <라마 컨테이너 이름> nvidia-smi 확인
```

## 비슷한 사례 3

<img width="748" height="624" alt="image" src="https://github.com/user-attachments/assets/ab75cba5-1286-46e9-ba56-3f06cd111444" />

- 해결 방안 
1) Windows 11에서 통합 그래픽 비활성화
- 장치관리자 -> 디스플레이 어댑터 -> 통합 그래픽 장치 사용 안함 
2) Windows용 Ollama 다운로드 
---

# 2. Ollama가 CPU가 아닌 GPU를 사용하도록 강제하는 법 
## 방법 1

1. 기본 원리
- Ollama는 NVIDIA GPU가 감지되면 자동으로 GPU 메모리를 우선 사용
- GPU 메모리 부족 시 시스템 RAM으로 전환
- 다중 GPU 지원 여부는 모델과 백엔드 (ex : llama.cpp)에 따라 다르다
2. 단일 GPU 설정 (기본)
  1) 명시적 GPU 지정
```
export CUDA_VISIBLE_DEVICES=0  # 0번 GPU만 사용
ollama run 모델이름
```
  2) GPU 레이어 수 강제 설정
```
ollama run 모델이름 --num-gpu-layers 40  # GPU에 40개 레이어 할당
```
  3) 설정 파일에 옵션 추가 (~/.ollama/config.json)
```
{
  "num_gpu_layers": 40,
  "main_gpu": 0
}
```
3. 다중 GPU 활용 방법 (제한적 지원)
  1) 두 GPU 모두 인식하도록 설정
```
export CUDA_VISIBLE_DEVICES=0,1  # GPU 0과 1 활성화
ollama run 모델이름
```

**※주의 : 대부분의 모델은 단일 GPU만 지원, Ollama의 기본 엔진(llama.cpp)은 완전한 다중 GPU 병렬화를 제공하지 않음**
  2) 각 GPU별 모델 분산 실행 (간접 활용)
```
# 터미널 1: GPU 0 사용
export CUDA_VISIBLE_DEVICES=0
ollama run 모델이름

# 터미널 2: GPU 1 사용
export CUDA_VISIBLE_DEVICES=1
ollama run 모델이름
```

  3) vLLM 등 외부 도구 사용 (권장)
```
# vLLM 설치 및 실행 (2개 GPU 병렬)
pip install vllm
python -m vllm.entrypoints.api_server --model 모델이름 --tensor-parallel-size 2
```
  4) GPU 사용 현황 모니터링
```
nvidia-smi
```
  5) Ollama 디버그 모드
```
ollama run 모델이름 --verbose
```

----
## 방법 2
1. 스레드 수 설정
```
ollama run llama3.2-vision --num-threads 1
```
2. GPU 레이어 수 설정
```
ollama run 모델이름 --num-gpu-layers 40
```
3. 환경 변수로 설정 (~/.ollama/config.json)
```
{
  "num_threads": 1,
  "num_gpu_layers": 40,
  "main_gpu": 0
}
```
4. GPU 선택
```
export CUDA_VISIBLE_DEVICES=0
```

---------
