---
title: C++ JSON parser
author: yjh
categories: [CPP]
tags: [cpp]
---

## 설치

- 해당 라이브러리의 [깃허브](https://github.com/nlohmann/json) 로 이동 후 `git clone`.
- 설치 위치로 이동 후 `cmake`

```shell
git clone https://github.com/nlohmann/json
cd path/to/clone
cmake
```

## task.json 수정

- `"args"` 에 `-I/path/to/installed/json/parser/include` 추가

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++ cpp build active file",
            "command": "/usr/bin/g++",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${fileDirname}/**.cpp",
                "-o",
                "-I/path/to/installed/json/parser/include"
            ]
        }
    ]
}
```

## c_cpp_properties.json 수정

- `"includePath"` 에 `"path/to/installed/json/parser/include"` 추가

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**",
                "path/to/installed/json/parser/include"
            ],
            "defines": [],
            "compilePath": "/usr/bin/g++",
            "cStandard": "c17",
            "cppStandard": "gnu++17",
            "intelliSenseMode": "linux-gcc-x64"
        }
    ],
    "version": 4
}
```

## 사용 방법

- `#include "nlohmann/json.hpp` 만 추가하면 사용 가능하다.
- `nlohmann::json` 를 통해 인스턴스 생성한다.
- 기본적인 사용 방법은 `python` 의 `dictionary` 와 비슷하다.

```cpp
#include "nlohmann/json.hpp"

#include <iostream>
#include <string>


int main(void)
{
    std::string str = "hello world!";

    nlohmann::json js;
    js["PI"] = 3.141592;
    js["int"]["zeroToHundred"] = 100;
    js["string"] = str;

    std::cout << js <<std::endl;  // {"PI": 3.141592, "int": {"zeroToHundred": 100}, "string": "hello world!"}

    return 0;
}
```

## Reference

- [json parser official page](https://github.com/nlohmann/json)
