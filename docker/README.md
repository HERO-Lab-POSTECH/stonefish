# Stonefish Docker Guide

Docker를 사용하여 Stonefish를 빠르게 실행할 수 있습니다.

## Prerequisites

- Docker >= 20.10
- Docker Compose >= 2.0
- NVIDIA GPU 드라이버 (GUI 시뮬레이션용)
- nvidia-docker2 (GPU 접근용)

### NVIDIA Docker 설치 (Ubuntu)

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install -y nvidia-docker2
sudo systemctl restart docker
```

## Quick Start

### 1. Setup GitHub Token

HERO-Lab-POSTECH/stonefish는 private 저장소이므로 토큰이 필요합니다.

```bash
# 1. GitHub에서 Personal Access Token 생성
#    https://github.com/settings/tokens
#    - Scopes: repo (Full control of private repositories)

# 2. .env 파일 생성 및 토큰 추가
cd docker
cp .env.example .env
vim .env  # GITHUB_TOKEN을 실제 토큰으로 변경
```

### 2. Check Your X Session (Important!)

```bash
# X 세션 확인
ls /tmp/.X11-unix/

# X0이면 → 대부분의 시스템 (기본값)
# X1이면 → 일부 시스템
```

### 3. Build & Run

```bash
# X0인 경우 (대부분)
docker compose up -d

# X1인 경우
DISPLAY=:1 docker compose up -d
```

**That's it!** DISPLAY 기본값은 :0이며, 필요시 override 가능합니다.

### 3. 컨테이너 접속

```bash
docker exec -it stonefish-ros1-noetic bash
```

### 4. 시뮬레이션 실행

컨테이너 내부에서:

```bash
# ROS core 시작
roscore &

# Stonefish 라이브러리 확인
ls /workspace/stonefish

# 예제 실행 (필요시 stonefish_ros 설치 후)
```

## 기타 명령어

### 컨테이너 중지

```bash
docker compose down
```

### 로그 확인

```bash
docker logs stonefish-ros1-noetic
```

### 이미지 재빌드

```bash
docker compose build --no-cache
```

## Troubleshooting

### GUI가 표시되지 않을 때

```bash
# Host에서 실행
xhost +local:docker
```

### GPU 접근 오류

```bash
# NVIDIA Docker 확인
docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi

# Docker daemon 재시작
sudo systemctl restart docker
```

### "cannot open display" 오류

```bash
# DISPLAY 환경변수 확인
echo $DISPLAY

# .env 파일 수정
DISPLAY=:0
```

## Documentation

- [Stonefish Documentation](https://stonefish.readthedocs.io)
- [Stonefish GitHub](https://github.com/HERO-Lab-POSTECH/stonefish)
