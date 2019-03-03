---
title: Ubuntu
layout: default
nav_order: 1
parent: Install
---

## Ubuntu에 Vapor 설치하기  
  
⚠️반드시 **Swift 4.1 이상**을 설치해야 함을 기억하세요.
 
 ---
## 지원 버전
Vapor가 지원하는 버전은 Swift가 지원되는 우분투 버전과 같습니다.
  
| 버전 | 코드네임 |
| :--- | :--- |
| 18.10 | Cosmic cuttlefish |
| 18.04 | Bionic Beaver |
| 16.10 | Yakkety Yak |
| 16.04 | Xenial Xerus |
| 14.04 | Trusty Tahr |
  
 ---
## APT Repo 추가하기
⚠️curl이 없으시다면 ```sudo apt-get install curl```을 통해 curl을 설치한 뒤 진행해주세요.  
  
Vapor의 우분투 패키지를 설치하기에 앞서 Vapor의 APT 저장소를 추가해주세요.  
```
eval "$(curl -sL https://apt.vapor.sh)"
```
  
---
## Vapor 설치하기  
Vapor APT repo를 추가했으면 필요한 디펜던시를 설치하세요
```
sudo apt-get install swift vapor
```

---
## 설치 확인하기
**Swift**
```
swift --version
```

아래와 비슷한 내용이 보여야 해요:
```
Apple Swift version 4.2.1 (swiftlang-1000.11.42 clang-1000.11.45.1)
Target: x86_64-apple-darwin18.2.0
```
  
Swift 4.1 이상이 설치되었다면 성공입니다.  
  
**Vapor Toolbox**
```
vapor --help
```
길다란 명령어 설명이 뜬다면 성공입니다.

---