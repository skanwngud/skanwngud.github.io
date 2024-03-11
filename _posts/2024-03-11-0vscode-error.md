---
title: "error: no such file or directory: '`pkg-config opencv4 --cflags --libs`'"
author: yjh
math: false
categories: [Trouble Shooting]
tags: [cpp, trouble shooting, vscode]
---

## 문제

```error: no such file or directory: '`pkg-config opencv4 --cflags --libs`'``` 라는 메세지와 함께 컴파일이 되지 않았다.

`tasks.json` 의 내용을 그대로 터미널에 입력했을 땐 제대로 컴파일이 되어던 것으로 보아, `vscode` 자체의 문제로 생각했다.

## 해결

`tasks.json` 의 `"args"` 의 `pkg-config` 부분을 한 줄로 작성하지 않고 각각 분리시켜 작성하는 것으로 정상작동 확인하였다.

```json
{
  "tasks": [
    {
      "type": "cppbuild",
      "label": "C/C++: gcc 활성 파일 빌드",
      "command": "/usr/bin/g++",
      "args": [
        "-fdiagnostics-color=always",
        "-g",
        "${file}",
        "-o",
        "${fileDirname}/${fileBasenameNoExtension}",
        "`pkg-config",
        "opencv4",
        "--cflgas",
        "--libs`",
        "std=c++17"
      ],
      "options": {
        "cwd": "${fileDirname}"
      },
      "problemMatcher": ["$gcc"],
      "group": {
        "kind": "build",
        "isDefault": true
      },
      "detail": "디버거에서 생성된 작업입니다."
    }
  ],
  "version": "2.0.0"
}
```

로 수정했을 때 정상동작하였다.

아무래도 `pkg-config` 를 작성했을 때 백틱 (`) 안에 띄어쓰기 등이 있으면 자동으로 따옴표가 붙어 파싱해주는 모양이다.

## Reference

- 필자
