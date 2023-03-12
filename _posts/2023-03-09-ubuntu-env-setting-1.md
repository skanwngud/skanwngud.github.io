---
title: 우분투 환경 세팅 (1)
author: yjh
categories: [Ubuntu]
tags: [ubuntu, cuda, cudnn]
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
[cuda-toolkit-archive](https://developer.nvidia.com/cuda-toolkit-archive) 링크로 접속해 원하는 버전의 자신의 os 에 맞는 CUDA 를 받는다

나는 Linux -> x86_64 -> Ubuntu -> 20.04 -> runfile(local) 을 통해 파일을 받았다

`.deb` 파일로도 받을 수 있고 `network` 를 통해서 설치할 수 있다

설치 방법은 nvidia 홈페이지에 나와있는 대로 실행하면 된다

CUDA 11.7, 그래픽드라이버 515 기준 <br>
```bash
wget https://developer.download.nvidia.com/compute/cuda/11.7.0/local_installers/cuda_11.7.0_515.43.04_linux.run

cd ~/Downloads

sudo sh cuda_11.7.0_515.43.04_linux.run
```

참고로 파일을 실행한 다음 설치 할 파일들을 선택할 수 있는 창이 뜨는데, 이 때 그래픽 드라이버는 위에서 이미 설치를 했기 때문에 선택을 해제하고 설치를 마저 진행하면 된다

## cuDNN 설치
[cudnn-archive](https://developer.nvidia.com/rdp/cudnn-archive) 링크로 접속해 본인의 상황에 맞는 cuDNN 을 다운로드 받는다

nvidia 계정을 요구하므로 가입하고 진행하면 된다

다운로드가 완료 되면 파일이 있는 위치로 가 압축파일을 해제한다

`tar.gz` 파일은 `tar xvzf {file_name}` 명령어로

`tar` 파일은 `tar xvf {file_name}` 명령어로 해제한다

압축을 해제하면 해당 폴더 안으로 들어간뒤 폴더 내의 내용물들을 전부 CUDA 가 설치 된 위치에 복사하면 된다

같은 이름의 파일이 있다고 할텐데 replace 시켜주면 된다

```bash
sudo cp ./{cuDNN dir}/include/* /usr/local/{cuda dir}/include
sudo cp ./{cuDNN dir}/lib64/* /usr/local/{cuda dir}/lib64

sudo chmod a+r /usr/local/{cuda dir}/lib64/libcudnn*
```

## 환경변수 설정
위에서 모든 파일의 설치 및 복사가 완료 되었으면 다음은 컴퓨터의 어떤 경로에서든지 CUDA 와 cuDNN 을 참조할 수 있도록 환경 변수를 설정한다

vi 이나 vim 편집기로 파일을 열어주면 되는데, 없다면 `sudo apt-get install vim` 등을 이용하여 먼저 설치해준다

`vim ~/.bashrc` 로 파일을 열어준 뒤

```bash
export PATH=/usr/local/{cuda-version}/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

를 파일 제일 아래에 추가해 준다

(vim 에서 문서를 처음 편집하려고 할 때 아무것도 입력이 안 되는데 이는 vim 의 명령어 모드이기 때문이다. <br>
i, o, a 등의 명령어로 입력 모드로 변경한 뒤 입력해주고 esc 를 눌러 다시 명령어 모드로 바꾸고 :wq 를 입력하여 최종적으로 입력 내용을 저장한다)

`source ~/.bashrc` 를 입력하여 해당 내용이 적용 될 수 있도록 한다

## 설치 확인
`nvidia-smi` 와 `nvcc -V` 혹은 `nvcc --version` 을 입력하여 그래픽 드라이버와 CUDA 가 잘 설치 되었는지를 확인하면 된다

참고로 `nvidia-smi` 에서 출력 되는 드라이버는 현재 설치 된 드라이버가 아닌 최신 드라이버이므로 혼동하면 안 된다