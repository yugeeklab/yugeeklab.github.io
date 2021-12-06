---
title: (Chromium) Chromium의 빌드
date: 2021-12-06T06:02:45.992Z
categories:
  - Chromium
tags:
  - chromium
math: false
---
# 크로미엄 빌드 명령어

## Setting up the build

`gn gen out/Default`

`gn args out/Default`

## Build Chromium

`autoninja -C out/Default chrome`



# Ninja/gn 이란?

ninja는 빌드 시스템이다.



## 빌드 시스템이란?

빌드 : 소스 코드가 컴퓨터에서 실행될 수 있는 상태로 만드는 일

빌드 시스템 : 빌드를 자동화하기 위한 프로그램

예)  Make와 CMake

Makefile을 사용하면 make 명령어를 이용해 빌드할 수 있다.

CmakeLists를 사용하면 Makefile을 만들 수 있다.

![](https://modoocode.com/img/cpp/19.2.1.png)

> 이 때 ninja가 Makefile을 대체하고
>
> gn이 Cmake 명령어를 대체한다.



## 빌드 시스템이 필요한 이유

![](https://t1.daumcdn.net/cfile/tistory/236F9B4D56E5904C37)

위의 예시를 보면서 설명하겠다.

빌드 시스템이 없다면 memo.c만 변경했을 시에도 모든 파일을 다 컴파일해 오브젝트 파일을 합쳐 실행 가능한 파일을 만든다.



하지만 빌드 시스템이 있다면 memo.c만 변경했을 시에 memo.c만 컴파일해 오브젝트 파일을 만든 뒤에 실행 가능한 파일을 만든다. 



이를 통해 빌드 시간을 상당히 감축시킬 수 있다.