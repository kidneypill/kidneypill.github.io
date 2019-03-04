---
title: SPM
layout: default
parent: Getting Started
nav_order: 2
---

## 프로젝트 관리하기
  
프로젝트의 소스 코드와 디펜던시(Dependencies)를 빌드하기 위해 **스위프트 패키지 매니저 (Swift Package Manager, SPM)**가 사용됩니다. Cocoapods, Ruby gems, NPM과 비슷한 개념입니다. 대부분의 경우 Vapor Toolbox가 여러분 대신에 SPM과 상호작용할겁니다. 하지만 기본을 이해하는 건 중요해요.  

📖[Swift.org][SPMLink] 에서 SPM에 대해 알아보세요.  

---
## 패키지 매니페스트 (Package Manifest)
SPM이 여러분의 프로젝트를 들여다볼 때 가장 먼저 보는 곳이 바로 **패키지 매니페스트[^package_manifest](Package Manifest)**입니다. 이 패키지 매니페스트는 항상 여러분 **프로젝트의 루트 디렉토리**에 있어야 하며 파일 이름은 ```Package.swift```여야 합니다.

---
### 디펜던시
디펜던시란 여러분의 패키지가 의존하고 있는 타 SPM 패키지를 말하는 것입니다. 모든 Vapor 어플리케이션은 Vapor 패키지에 의존합니다만, 원하는 만큼 다른 디펜던시를 추가할 수 있습니다.

**Package.swift**
```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
	name: "VaporApp",
	dependencies: [
		// 💧A server-side Swift web framework.
		.package(url: "https://github.com/vapor/vapor.git", from: "3.0.0"),
	],
	targets: [ ... ]
)
```
위의 예시를 보시면, vapor/vapor 버전 3.0 또는 그 이상의 디펜던시가 이 패키지에서 사용된 것을 볼 수 있습니다.  

```swift
// 예시입니다.

targets: [
        .target(name: "App", dependencies: ["FluentSQLite", "Vapor"]),
        .target(name: "Run", dependencies: ["App"]),
        .testTarget(name: "AppTests", dependencies: ["App"])
    ]
```
⚠️디펜던시를 추가했으면, 위의 코드 블럭처럼 반드시 어떠한 타겟(targets)이 새로운 모듈(=즉 디펜던시)에 의존하는지 명시하셔야 합니다.

```
# 프로젝트의 루트 디렉터리에서 아래 명령어를 입력해서 변경사항을 반영하세요.
vapor update
```
⚠️패키지 매니페스트를 수정했다면, 수정사항을 반영하기 위해서는 항상 ```vapor update```를 터미널에 입력하셔야 합니다.

---
### 타겟
타겟이란 여러분의 패키지가 가지고 있는 모든 모듈, 실행파일, 그리고 테스트입니다.  
```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
	name: "VaporApp",
	dependencies: [ ... ],
	// 3개의 타겟이 있으며, 각 타겟마다 디펜던시가 명시된 것을 확인하세요.
	// 또한 어떤 타겟도 Run에 의존할 수 없는데, 이는 Run 디렉토리에 main.swift가 있기 때문입니다.
	targets: [
		.target(name: "App", dependencies: ["Vapor"]),
		.target(name: "Run", dependencies: ["App"]),
		.testTarget(name: "AppTests", dependencies: ["App"]),
	]
)
```
대다수의 Vapor 앱은 3개의 타겟을 갖습니다만, 용도에 따라 원하는 만큼 추가할 수 있습니다. 각 타겟은 그 타겟이 어떤 모듈에 의존하고 있는지를 선언합니다. 여러분의 코드에서 해당 모듈을 ```import```하기 위해서는 반드시 바로 여기에 모듈 이름을 입력해야 합니다. 한 타겟은 프로젝트 내부의 또다른 타겟이나 [main dependencies] 배열에 추가한 아무 모듈이나 사용할 수 있습니다.  

⚠️실행가능한 타겟(즉 ```main.swift``` 파일을 포함하고 있는 타겟)은 다른 모듈에 의해 ```import```될 수 없습니다. 그래서 Vapor에 ```App```과 ```Run``` 타겟이 따로 있는 겁니다. ```App```에 포함된 코드는 ```AppTests```에서 테스트해볼 수 있습니다.  
(무슨 말인지 잘 모르겠다면 밑의 폴더 구조에서 main.swift 파일이 있는 디렉토리를 확인해 보세요.)

---
## 폴더 구조
밑에 있는 것이 바로 전형적인 SPM 패키지의 폴더 구조입니다.
```
.
├── Sources
│   ├── App
│   │   └── (Source code)
│   └── Run
│       └── main.swift
├── Tests
│   └── AppTests
└── Package.swift
```
각 ```.target```은 ```Sources``` 폴더에 있는 각각의 폴더에 대응합니다. 각 ```.testTarget```은 ```Tests``` 폴더에 있는 폴더에 대응합니다.

---
## 문제 해결하기
SPM에 대한 문제를 겪고 있다면, 프로젝트를 ```clean``` 하는 것이 도움이 될 때도 있습니다.
```
vapor clean

# vapor clean이 정확히 무슨 작업을 하는지 궁금하다면
# vapor clean --help 커맨드를 통해 확인해보세요
vapor clean --help
```

---
[SPMLink]: <https://swift.org/package-manager>
[main dependencies]: <#디펜던시>

[^package_manifest]: 매니페스트(Manifest): 컴퓨터공학 분야에서 매니페스트(Manifest)란 동반되는 여러 파일들에 대한 메타데이터를 담고 있는 파일을 의미합니다. 여기에 프로젝트의 이름, 버전 넘버, 라이센스, 그리고 프로그램을 구성하는 파일들의 목록이 적혀 있기도 합니다.