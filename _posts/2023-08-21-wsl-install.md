---
title: WSL 설치
author: yjh
math: true
categories: [Windows, Linux]
tags: [windows, linux]
---

## 설치
- 윈도우의 `cmd` 를 연 후 해당 명령어를 입력 해 wsl 을 설치한다.
```shell
wsl --install  # wsl 설치
wsl --set-defaul-version 2  # wsl 버전의 기본값을 2로 변경
```

- `마이크로 소프트 스토어` 앱을 연 후 `Ubuntu` (혹은 본인이 원하는 Linux OS) 를 검색 후 설치한다.
- 본인이 원하는 OS 의 설치가 끝나면 `username` 과 `password` 를 설정한다.

## 설치 된 WSL 확인
```shell
wsl -l -v  # STATE 에서 Running, Stopped 등이 나옴
```
- 해당 명령어로 현재 설치 되어있는 WSL 을 확인할 수 있다.
- 앞에 `*` 이 있는 것이 `default` 이다.
- `wsl --set-default <Linux OS>` 를 통해 현재 설정 된 `default wsl` 의 OS 를 변경할 수 있다.
- `wsl --set-version <Linux OS> 2` 를 통해 wsl 의 버전을 변경할 수 있다.

## CMD 에서 WSL 실행
- `wsl` 명령어를 입력하면 `default` 로 설정 되어있는 wsl 이 실행 된다.

## Reference
[LainyZine: 프로그래머 가이드](https://www.lainyzine.com/ko/article/how-to-install-wsl2-and-use-linux-on-windows-10/)