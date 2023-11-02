---
title: 
author: yjh
categories: [Windows, Linux]
tags: [windows, linux]
---

## 1. Delete old gpg key
- wsl 에서 gpu 를 사용할 땐 윈도우에 설치 된 드라이버를 사용하므로 별도의 드라이버 설치는 안 함.
- 단, 이 드라이버를 사용하기 위한 wsl 전용의 cuda toolkit 을 설치해야한다.
- 공식 문서에선 오래 된 `gpg key` 를 삭제를 권함.
```shell
sudo apt-key del 7fa2af80
```

## 2. Install specific version cuda toolkit
- 현 시점 최신 버전은 12.2
```shell
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.2.0/local_installers/cuda-wsl-ubuntu-12-2-local_12.2.0-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-2-local_12.2.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubunu-12-2-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get install cuda -y
```
### 2-1. Error occured during install unpack cuda.deb using dpkg -i command
- 보통의 경우엔 잘 설치가 되겠지만 내 경우엔 `sudo dpkg -i ...` 하는 부분에서 에러가 발생했다.
- 터미널 창에 `gpg key` 를 특정 폴더에 복사하라는 메세지가 출력이 되는데 이를 따라하고 재입력하면서 설치가 완료 됐다.

## 3. Install nvidia-utils
- `apt-cache search nvidia-utils-` 를 이용하여 현재 설치 가능한 util 을 확인한다.
- 현 시점 최신 버전은 535
```shell
sudo apt install nvidia-utils-535
```

## References
[LaintZine: 프로그래머 가이드](https://lainyzine.com/ko/article/how-to-use-nvidia-gpu-cuda-on-wsl-linux/)