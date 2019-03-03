---
title: macOS
layout: default
New field 18: macOS
parent: Install
nav_order: 1
---

## macOS에 설치하기  
⚠️Vapor를 macOS에서 사용하려면 **Xcode 9.3 또는 그 이상**의 버전이 설치되어 있어야 합니다.  

---
## Xcode 설치하기  
[Xcode 9.3 또는 그 이상](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)의 버전을 Mac 앱스토어에서 설치하세요.  

![Xcode](Xcode.png)

---
## 설치 확인  
터미널을 열고 아래 커맨드를 입력해서 설치가 성공적으로 되었는지 확인하세요:
```
swift --version
```
  
	
이와 비슷한 것이 보여야 해요:
```
Apple Swift version 4.2.1 (swiftlang-1000.11.42 clang-1000.11.45.1)
Target: x86_64-apple-darwin18.2.0
```

⚠️ Vapor는 **Swift 4.1 이상**을 요구하기 때문에 Swift 버전을 잘 확인하시면 됩니다.  

---
## Vapor 설치하기
⚠️Vapor를 설치하기에 앞서, Vapor 설치를 도와줄 패키지 매니저인 [Homebrew](https://brew.sh/)를 먼저 설치하세요.  
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
  
Swift 4.1 이상이 설치된 것을 확인했다면 Vapor toolbox를 설치하세요.  
Vapor Toolbox는 새로운 프로젝트를 만들기 위해 필요한 Vapor의 모든 디펜던시와 편리한 CLI 툴을 포함하고 있어요.  
  
	
```
brew tap vapor/tap
brew install vapor/tap/vapor
```

---
## Vapor 설치 확인하기  
터미널을 열고 아래 명령어를 입력해서 설치가 완료되었나 확인하세요.
```
vapor --help
```
명령어 목록들이 길게 나열된다면 성공입니다.

---
## 끝
Vapor를 설치하셨으면 [Getting Started - Hello, World](\helloWorld)에서 새로운 프로젝트를 만들어 보세요.

---