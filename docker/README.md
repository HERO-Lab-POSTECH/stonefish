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

### 1. GitHub Personal Access Token 설정

HERO-Lab-POSTECH/stonefish는 private 저장소이므로 토큰이 필요합니다.

```bash
# 1. GitHub에서 Personal Access Token 생성
#    https://github.com/settings/tokens
#    - Scopes: repo (Full control of private repositories)

# 2. .env 파일에 토큰 추가
cd docker
vim .env
# GITHUB_TOKEN=your_token_here 부분을 실제 토큰으로 변경
```

### 2. X11 권한 설정

```bash
xhost +local:docker
```

### 3. 빌드 및 실행

```bash
cd docker
docker compose up -d
```

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
