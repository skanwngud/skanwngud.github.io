---
title: 우분투 환경 세팅 (1)
author: yjh
categories: [ubuntu]
tags: [ubuntu]
---

## 설치 환경
- Geforce 3090
- Ubuntu 20.04 LTS

## pre-setting
그래픽 드라이버를 설치하기 전 필요한 패키지들을 먼저 설치해준다

```bash
sudo apt-get update && sudo apt-get upgrade -y

sudo apt-get install openssh-server
sudo apt-get install net-tools

sudo apt-get install build-essential
sudo apt-get install manpages-dev
```

`gcc -V` 으로 gcc 컴파일러 설치 확인 (이후에 CUDA 설치할 때 필요)

## 그래픽 드라이버 확인 및 설치
터미널에 `ubuntu-drivers devices` 를 입력하면 현재 그래픽카드와 os 에 맞는 그래픽 드라이버들이 출력 된다

`sudo apt-get install nvidia-driver-autoinstall` 는 권장설치 <br>
`sudo apt-get install nvidia-driver-{version}` 는 본인이 원하는 버전을 수동으로 설치한다

나는 515 버전을 설치했다 <br>
`sudo apt-get install nvidia-driver-515`

## CUDA 설치

## cuDNN 설치
