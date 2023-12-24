---
title: Docker 삭제 후 재설치
author: yjh
math: true
categories: [Docker]
tags: [linux, docker]
---

최근에 내가 모르는 도커 이미지와 컨테이너가 반복적으로 생성 되는 것을 확인했다.

이유를 특정하기 어려웠으므로 일단 제일 간단한 방법인 삭제 후 재설치하는 방향으로 문제를 해결하였다.

## Docker 삭제

- 도커 엔진 삭제

```shell
sudo apt-get purge -y docker-engine docker docker-ce docker-ce-cli docker.io
sudo apt-get autoremove -y --purge docker-engine docker docker-ce docker.io
```

- 컨테이너, 이미지, 볼륨 등 사용자가 만든 파일 등 모두 삭제

```shell
sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/apparmor.d/docker
sudo groupdel docker
sudo rm -rf /var/run/docker.sock
```

- 제일 위의 명령인 `sudo rm -rf /var/lib/docker /etc/docker`를 실행 할 때 `rm: cannot remove '/var/lib/docker': Device or resource busy`라는 에러 발생.

```shell
cat /proc/mounts | grep docker
sudo umount /path
```

- 해당 명령어로 해결

## Docker 재설치

- [Docker official website](https://docs.docker.com/engine/install/ubuntu/) 에서 설치하거나
- `sudo apt-get install docker-ce docker-ce-cli containerd.io`로 설치한다.
  - 필요에 따라 `sudo apt-get install docker-compose`로 `docker compose`도 설치해준다.

## Reference

[히비스커스의 블로그](https://biology-statistics-programming.tistory.com/136)

[github issues](https://github.com/moby/moby/issues/6077)
