---
title: 우분투 환경 세팅 (2)
author: yjh
categories: [Ubuntu, Settings]
tags: [ubuntu, settings, anaconda]
---

## 설치 환경
- Geforce 3090
- Ubuntu 20.04 LTS

## Anaconda 설치
[Anaconda official site](https://www.anaconda.com/products/distribution) 를 통해 자신의 os 에 맞는 버전을 선택해 다운로드 받는다

파일이 다운로드 된 경로로 이동해 설치를 진행하기 전 `sha256sum {file_name}` 명령어를 통해 데이터 무결성을 검사한다

해당 명령어를 입력하면 해시값이 나오는데 이를 복사한뒤 [Hashes for all files](https://docs.anaconda.com/anaconda/install/hashes/all/) 접속해 복사한 해시값이 있는지 확인한다

해시값이 있는지 확인이 되면 `bash {file_name}` 혹은 `./{file_name}` 를 통해 `.sh` 파일을 실행해 설치를 진행한다

파일을 실행하면 Terms and conditions 등이 지나간 뒤 설치 경로를 지정해주고 (enter 를 누르면 default, 다른 경로 지정은 직접 입력) </br>
설치를 좀 더 진행하면 [yes|no] 를 선택하라는 문구가 나오는데 `yes` 를 선택한다

(`no` 를 선택하면 아나콘다 환경에서 쉘 스크립트를 수정할 수 없다)

## Anaconda Prompt 사용 설정
아나콘다를 설치하더라도 바로 conda 명령어 (e.g. conda create, conda env list, conda instal, ...) 가 먹히지 않는다

`ananconda prompt` 를 사용하기 위해선 `vim ~/.bashrc` 를 통해 맨 아래줄에 해당 명령어를 추가해준다

> `export PATH=~/ananconda3/bin:~/ananconda3/condabin:$PATH`

`source ~/.bashrc` 입력 후 터미널을 껐다 키면 앞에 `(base)` 가 붙는다

### Anaconda prompt 자동 실행 옵션 해제
`conda config --set auto_activate_base False` 를 터미널에 입력 후 껐다키면 `(base)` 가 사라진다

(터미널 실행 시 자동으로 Anaconda prompt 가 실행 되는 것을 방지)

anaconda prompt 를 실행하기 위해선 `conda acitvate` 를 입력하면 앞에 켜지고 `conda deactivate` 를 입력하면 꺼진다