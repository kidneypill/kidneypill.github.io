---
title: Toolbox
layout: default
parent: Getting Started
nav_order: 1
---

## Toolbox 설치하기
  
Vapor의 커맨드 라인 인터페이스(Command line Interface)는 통상의 작업을 위한 단축키와 보조키를 제공합니다.

```Help```는 커맨드와 플래그에 대한 유용한 옵션을 출력합니다.
```
# vapor에 대한 도움말을 출력합니다.
vapor --help
```

어떤 Toolbox 커맨드에도 --help 옵션을 실행할 수 있습니다.
```
# vapor new에 대한 도움말을 출력합니다.
vapor new --help
```

```--help``` 로 출력되는 설명은 항상 최신본이므로 toolbox에 대해 배우고 싶을 때에는 이 명령어를 사용하세요.

---
## New
Toolbox의 가장 중요한 기능은 새로운 프로젝트를 만들도록 도와주는 것입니다.
```
vapor new <name>
```
```new``` 커맨드의 첫 번째 인자(argument)로 프로젝트의 이름을 사용하세요.

⚠️프로젝트 이름으로는 항상 PascalCase를 사용하세요. ```HelloWorld```나 ```MyProject```처럼요.

---
### 템플릿
기본적으로, Vapor는 API 템플릿으로부터 새로운 프로젝트를 생성합니다. ```--template``` 플래그를 사용해서 다른 템플릿을 선택할 수 있어요.
  
|  Name | Web | Auth |
| --- | --- | --- |
| API | ```--template=api``` | Fluent 데이터베이스와 JSON API |
| Web | ```--template=web``` | Leaf 템플릿과 HTML 웹사이트 |
| Auth | ```--template=auth-template``` | Fluent 데이터베이스, Auth와 JSON API |
  

ℹ️깃허브의 [vapor + template topics][vapor template topics]에 비공식 Vapor 템플릿들이 많이 있습니다. ```--template``` 옵션에 템플릿의 깃허브 URL을 입력함으로써 이러한 비공식 템플릿을 사용할 수 있습니다.
```
# nodes-vapor/template 템플릿을 사용하고 싶다면?
vapor new [PROJECT_NAME] --template=nodes-vapor/template

# 또는
vapor new [PROJECT_NAME] --template=https://github.com/nodes-vapor/template
```

---
## 빌드 & 구동
Toolbox를 사용해서 Vapor 앱을 빌드하고 구동할 수 있습니다.
```
vapor build
vapor run
```
⚠️Mac을 사용중이라면 Xcode를 통해 빌드하고 구동하는 것을 권장합니다. 좀더 빠르고 브레이크포인트를 설정할 수 있기 때문이죠! ```vapor xcode```를 사용해서 Xcode 프로젝트를 생성하세요.

---
## 업데이트
Toolbox를 설치한 패키지 매니저에 의해 Toolbox가 업데이트되어야 합니다.

### Homewbrew
```
# macOS에서 Homebrew로 Vapor toolbox를 설치하셨다면 이 커맨드를 사용하세요.
brew upgrade vapor
```

### APT
```
# Ubuntu에서 APT로 Vapor toolbox를 설치하셨다면 이 커맨드를 사용하세요.
sudo apt-get update
sudo apt-get install vapor
```

---
[vapor template topics]: <https://github.com/search?utf8=%E2%9C%93&q=topic%3Avapor+topic%3Atemplate&type=Repositories>