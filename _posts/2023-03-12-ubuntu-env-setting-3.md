---
title: 우분투 환경 세팅 (3)
author: yjh
categories: [Ubuntu, Settings]
tags: [ubuntu, settings, git, vscode]
---

## 설치 환경
- Geforece 3090
- Ubuntu 20.04 LTS

## Git 설치
`sudo apt-get install git` 명령어로 설치 가능하다

`git --version` 혹은 `git -V` 으로 설치 여부를 확인한 뒤 `git config` 명령어로 설정을 완료한다

```bash
git config --user.name {user_name}
git config --user.email {user_email}

git config --list
```

마지막 줄 명령어로 입력한 `config` 가 제대로 적용 되었는지 확인한다

## VScode 설치
[VScode official site](https://code.visualstudio.com) 으로 접속해 자신의 os 에 맞는 실행 파일 `.deb` 을 받는다

터미널에 다운로드 경로로 이동해 `sudo apt install ./{file_name}` 를 입력하여 설치를 진행한다

터미널을 재실행 한 뒤 `code` 를 입력하면 vscode 가 켜진다 (혹은 그냥 vscode 를 검색해서 실행시킬 수 있다)
