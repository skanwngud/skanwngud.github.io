---
title: WSL 에서 GUI 관련 프로그램이 실행 되지 않는 문제
author: yjh
math: true
categories: [Ubuntu, Trouble Shooting]
tags: [ubuntu, opencv, trobule shooting]
---

## 문제

`python` 파일 실행 중 `qt.qpa.plugin: could not load the qt platform plugin "xcb"` 와 `wsl qt.qpa.xcb: could not connect to display localhost:10.0` 라는 메세지와 함께 이미지 출력이 되지 않는 문제가 발생했다.

## 해결

`Xming` 을 설치한 뒤 `export DISPLAY=:0` 를 추가하니 해결 되었다.

`Xming` 은 [Xming 설치](https://sourceforge.net/projects/xming/) 에서 다운로드 및 설치가 가능하다.

```bash
# .bashrc

...
export DISPLAY=:0
...
```

## Reference

[TUWLAB](https://www.tuwlab.com/ece/29485)
