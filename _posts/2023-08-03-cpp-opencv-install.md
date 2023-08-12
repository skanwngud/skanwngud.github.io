---
title: MacOS, Ubuntu 환경에서 OpenCV 설치
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv]
---
# MacOS
## 환경
- MacBookPro 14
- Apple M1 Pro
- Ventura 13.1.4

## 설치
- `brew install opencv` 로 설치
- `/opt/homebrew/Cellar/opencv/4.8.0_1/include` 설치 확인
- vscode 에서 `c_cpp_properties.cpp` 수정
```json
{
    "configurations": [
        {
            "name": "Mac",
            "includePath": [
                "${workspaceFolder}/**",
                "/opt/homebrew/Cellar/opencv/4.8.0_1/include"
            ],
            "defines": [],
            "macFrameworkPath": [
                "/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/System/Library/Frameworks"
            ],
            "compilerPath": "/usr/bin/g++",
            "cStandard": "c11",
            "cppStandard": "c++11",
            "intelliSenseMode": "macos-clang-arm64"
        }
    ],
    "version": 4
}
```
- `brew install pkg-config` 로 패키지 설정 관련 라이브러리 설치
- `export PKG_CONFIG_PATH=/opt/homebrew/Cellar/opencv/4.8.0_1/lib/pkgconfig` 로 환경변수 경로 설정

## 컴파일
```cpp
// main.cpp

#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("path/to/file.jpg")

    cv::imshow("debug", img);
    cv::waitKey(0);

    return 0;
}
```
  
```shell
clang++ main.cpp -o main `pkg-config opencv4 --cflags --libs` --std=c++11
```
  
- 상기한 경로의 이미지를 읽어와 보여주는 코드를 작성 후 컴파일한다.

## tasks.json 수정
- tasks.json 은 디버깅, 컴파일 등의 옵션을 지정해주는 설정 파일이다.
```json
{
	"version": "2.0.0",
	"tasks": [
		{
			"type": "cppbuild",
			"label": "C/C++: clang++ 활성 파일 빌드",
			"command": "/usr/bin/clang++",
			"args": [
				"-fdiagnostics-color=always",
				"${file}",
				"-o",
				"${fileDirname}/${fileBasenameNoExtension}",
				"`pkg-config opencv4 --cflags --libs`",
				"--std=c++11"
			],
			"options": {
				"cwd": "${fileDirname}"
			},
			"problemMatcher": [
				"$gcc"
			],
			"group": "build",
			"detail": "컴파일러: /usr/bin/clang++"
		}
	]
}
```

# Ubuntu
## 환경
- ubuntu 20.04 LTS

## 설치
### 기존 설치 확인
- `pkg-config --modversion opencv` 로 확인하여 버전이 나온다면 설치가 되어있다.
- 이미 `opencv`가 설치 되어있고 새로운 버전을 설치하고 싶다면 해당 명령어들로 먼저 삭제.
```shell
sudo apt-get install libopencv* python-opencv
sudo apt-get autoremove
```

### 패키지 업데이트
```shell
sudo apt-get update
sudo apt-get upgrade
```

### 필요 패키지, 모듈 등 설치
#### build-essential, cmake, pkg-config 설치
- `sudo apt-get install build-essential cmake pkg-config` 명령어로 설치
- build-essential; c, cpp 컴파일러와 관련 된 라이브러리.
- cmake; 컴파일 옵션이나 빌드 된 라이브러리에 포함 시킬 `opencv` 모듈 설정을 위해 필요하다.
- pkg-config; 컴파일 및 링크 시 필요한 라이브러리에 대한 정보를 메타파일로부터 가져오는데 사용한다.

#### 추가 패키지 설치
```shell
# 특정 포맷의 이미지 파일을 불러오거나 저장하기 위해 필요한 패키지
sudo apt-get install libjpeg-dev ligtiff5-dev libpng-dev

# 특정 코덱의 비디오 파일을 불러오거나 저장하기 위해 필요한 패키지
sudo apt-get install ffmpeg libavcodec-dev libavformat-dev libswscale-dev libxvidcore-dev libx264-dev libxine2-dev

# 웹캠으로부터 실시간 비디오 캡처를 받기 위해 필요한 패키지
sudo apt-get install libv4l-dev v4l-utils

# 비디오 스트리밍을 위한 라이브러리
sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-bae1.0-dev

# 윈도우 생성, 마우스 제어 등의 GUI 를 위해 필요한 패키지 (gtk, qt 등 선택 가능)
sudo apt-get install libgtk2.0-dev  # gtk 선택
sudo apt-get install libqt4-dev
sudo apt-get install libqt5-dev

sudo apt-get install libgtk-3-dev

# openGL 지원
sudo apt-get install mesa-utils libgl1-mesa-dir libgtkgl2.0-dev libgtkglext1-dev

# OpenCV 최적화
sudo apt-get install libatlas-base-dev gfortran libeigen3-dev

# OpenCV-Python 바인딩
sudo apt-get install python3-dev python3-numpy
```

### OpenCV 다운로드 및 설치
#### OpenCV, OpenCV contrib 파일 다운로드
- OpenCV contrib; 기본 모듈에서 빠진 모듈들과 SURF 등의 모듈들을 사용하기 위해 필요
```shell
mkdir opencv
cd opencv

wget -O opencv.zip https://github.com/opencv/opencv/archive/4.2.0.zip
unzip opencv.zip

wget -O opencv-contrib.zip https://github.com/opencv/opencv_contrib/archive/4.2.0.zip
unzip opencv-contrib.zip
```

#### OpenCV 빌드
```shell
cd opencv-4.2.0
mkdir build
cd build

cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/lcal -D WITH_TBB=OFF -D WITH_IPP=OFF -D WITH_1394=OFF -D BUILD_WITH_DEBUG_INFO=OFF -D BUILD_DOCS=OFF -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=OFF -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF -D WITH_QT=OFF -D WITH_GTK=ON -D WITH_OPENGL=ON -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.2.0/modules -D WITH_V4L=ON -D WITH_FFMPEG=ON -D WITH_XINE=ONE -D BUILD_NEW_PYTHON_SUPPORT=ON -D OPENCV_GENERATE_PKGCONFIG=ON ../
```

#### make 명령을 통한 빌드
- cpu 코어 갯수를 지정해 빌드한다.
- `$(nproc)` 을 이용하여 본인 cpu 코어 갯수 리턴한다.
- `time make -j$(nproc)`

#### 설치 및 설치확인
- `sudo make install` 로 설치.
- `cat /etc/ld.so.conf.d/*` 로 확인한 뒤 파일이 존재하는지 확인한다.
- 파일이 존재하지 않는다면 `sudo sh -c 'echo'/usr/local/lib' > /etc/ld.so.conf.d/opencv.conf'` 후 `sudo ldconfig` 로 생성한다.

#### 환경변수 지정
- `env | grep LD_LIBRARY_PATH` 로 확인 후 `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib` 로 설정한다.

### vscode 설정
#### c_cpp_properties.json 수정
```json
{
	"configurations": [
		{
			"name": "Linux",
			"includePath": [
				"${workspaceFolder}/**",
				"/usr/include/opencv4/*"
			],
			"defines": [],
			"comilerPath": "/usr/bin/g++",
			"cStandard": "c17",
			"cppStandard": "gnu++14",
			"intelliSenseMode": "linux-gcc-x64"
		}
	],
	"version": 4
}
```

#### tasks.json 수정
```json
{
	"tasks": [
		{
			"type": "cppbuild",
			"label": "C/C++: g++ build active file",
			"command": "/usr/bin/g++",
			"args": [
				"-fdiangnostics-color=always",
				"-g",
				"${file}",
				"-o",
				"${fileDirname}/${fileBasenameNoExtension}"
			],
			"options": {
				"cwd": "${fileDirname}",
			},
			"problemMatcher": [
				"$gcc",
			],
			"group": {
				"kind": "build",
				"isDefault": true,
			},
			"detail": "Task generated by Debugger."
		}
	],
	"version": "2.0.0"
}
```

### OpenCV 프로젝트 컴파일
- `opencv` 는 `pkg-config` 를 실행해야 컴파일 가능
- `opencv*.pc` 파일을 찾아 `*` 의 숫자가 $2$인지 $4$인지 확인

```cpp
// main.cpp

#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("path/to/file.jpg")

    cv::imshow("debug", img);
    cv::waitKey(0);

    return 0;
}
```

```shell
g++ maine.cpp -o main $(pkg-config opencv4 --cflags --libs)
```
## Reference
[https://fwanggu-lee.tistory.com](https://fwanggu-lee.tistory.com)