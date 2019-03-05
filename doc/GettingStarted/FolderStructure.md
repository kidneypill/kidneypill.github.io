---
title: Folder Structure
layout: default
parent: Getting Started
nav_order: 4
---

## 구조
  
	
이 섹션에서는 전형적인 Vapor 어플리케이션의 구조에 대해 설명할 것입니다. 어디에 어떤 파일이 있는 것인지 익숙해지는 데에 도움이 될 거에요.

---
## 폴더 구조
Vapor의 폴더 구조는 [SPM의 폴더 구조](/doc/GettingStarted/SPM.html#폴더_구조) 상에서 만들어집니다.
```
.
├── Public
├── Sources
│   ├── App
│   │   ├── Controllers
│   │   ├── Models
│   │   ├── boot.swift
│   │   ├── configure.swift
│   │   └── routes.swift
│   └── Run
│       └── main.swift
├── Tests
│   └── AppTests
└── Package.swift
```
각 폴더와 파일이 어떤 용도인지 알아봅시다.  
  
	
📖**터미널에서 폴더 구조를 확인하고 싶다면**  

그렇다면  ```tree```를 설치하는 것을 추천합니다.
```
# macOS
brew install tree

# Ubuntu
(sudo) apt-get install tree

# 설치가 완료되었다면 프로젝트 루트 디렉터리에서
tree
```

---
## Public
이 폴더에는 **여러분의 앱에 의해 서비스될 모든 퍼블릭 파일**들이 있습니다. 주로 **이미지**, **스타일시트(stylesheet)**, **브라우저 스크립트** 등이 있습니다. Vapor는 요청에 응답할 때마다 우선 요청된 파일이 Public 폴더 안에 존재하는지를 확인합니다. 존재한다면 어플리케이션 로직을 건너뛰고 바로 파일을 반환합니다.  
  
예를 들면 ```localhost:8080/favicon.ico```에 대한 요청은 ```Public/favicon.ico```가 존재하는지 확인할 겁니다. 만약 존재한다면 Vapor는 저 파일을 반환합니다.  
  
Vapor가 퍼블릭 파일을 반환하게 하려면 ```configure.swift``` 파일에서 ```FileMiddleware```를 허용해야 합니다. 이 부분을 주석 해제하세요:  

**configure.swift**
```swift
// middlewares.use(FileMiddleware.self)
```

---
## Sources
이 폴더에는 여러분의 프로젝트를 위한 모든 **Swift 소스 파일**이 있습니다. 최상위 폴더 (```App```과 ```Run```)는 [패키지 매니페스트](/doc/GettingStarted/SPM.html#패키지 매니페스트)에 선언한 대로 여러분의 패키지의 모듈을 반영합니다.

## **App**
이 폴더는 여러분의 어플리케이션에 있어서 가장 중요한 폴더입니다. 모든 어플리케이션 로직이 있는 곳이니까요!  

### Controllers
컨트롤러는 어플리케이션 로직을 한 데 묶는 좋은 방법입니다. 대부분의 컨트롤러는 요청을 받아들여 어떠한 종류의 응답을 하는 기능을 많이 가지고 있습니다.  
⚠️Vapor는 MVC 패턴을 지원하며, 강제하지는 않습니다.

### Models 
```Models``` 폴더는 여러분의 [Content](/doc/GettingStarted/Content) struct나 [Fluent 모델](/doc/Fluent/Models)을 저장하기에 알맞은 곳입니다.  
  
### boot.swift
이 파일은 여러분의 어플리케이션이 부팅되었지만 아직 구동되기 전에 실행될 함수(function)가 들어있는 곳입니다. 여러분의 어플리케이션이 시작될 때마다 수행해야 하는 일들을 하기 좋은 곳입니다.  
  
여러분은 이 파일에서 여러분이 필요한 [서비스](/doc/GettingStarted/Application#서비스)를 만들기 위해 쓰이는 [Application](/doc/GettingStarted/Application)에 접근할 수 있습니다.  
  
### configure.swift
여러분의 어플리케이션을 위한 설정(config), 환경(environment), 서비스(service)를 인자로 받는 함수가 들어있는 파일입니다. 여기서 설정을 수정하거나 여러분의 어플리케이션에 [서비스](/doc/GettingStarted/Application#서비스)를 등록할 수 있습니다.  
  
### router.swift
여러분의 라우터에 루트를 추가하는 함수가 들어 있습니다.  앞서 봤던 "hello, world"를 반환하는 예시 루트도 여기에 들어있는 걸 알 수 있을겁니다.  
  
코드를 정리하기 위해 원하는 만큼 여기서 메소드를 만들 수 있습니다. 그런 메소드들은 이 메인 루트 컬렉션에서 호출해야 한다는 점을 기억하세요!

---
**🐤초심자**

📖**MVC 패턴**이 뭔가요?  
  
![MVC 패턴](MVC.png){: width="60%" height="60%"}
  
> MVC 패턴이란 **Model - View - Controller** 세 부분으로 구성되는 소프트웨어 디자인 패턴을 말하는 것입니다.  

> * Model: 어플리케이션에서 Model은 객체에 대한 정보를 담고 있는 것입니다. Vapor에서는 Models 파일에 각 객체에 대한 파일을 만드는 것이 일반적입니다. (예를 들면 /Models/Family.swift 처럼요.)

> * View: 사용자가 접하는 인터페이스를 말합니다. iOS를 예로 들면, Storyboard나 xib(또는 nib)이 바로 사용자가 접하는 인터페이스에 대한 정보를 담고 있으므로 View의 일종이라고 할 수 있습니다.

> * Controller: Controller는 View와 Model 사이에서 일련의 작업을 수행합니다. View를 통해 사용자가 Model에 질의할 때, Controller에 포함된 로직을 거치게 됩니다. Model에서 View에 응답할 때도 마찬가지로 Controller에 포함된 로직을 거칠 수 있습니다.

> 더 자세한 내용은 [여기](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)를 참조하시는 것도 괜찮은 방법입니다.

> MVC 패턴은 수많은 소프트웨어 디자인 패턴 중 하나이며, MVC 패턴으로부터 파생된 여러 디자인 패턴들이 존재하기도 합니다. 하지만 1979년에 개발된 뒤로 지금까지도 널리 쓰이고 있기 때문에 자세히 알아두는 것이 좋습니다.  
  
	
📖**라우터(router)**는 뭐고 **루트(route)**는 뭔가요?  

> 웹 프레임워크에 있어서 라우터(router)는 어떠한 URL, 즉 루트(route)에 대한 요청을 처리하는 객체입니다. 또 그러한 작업을 라우팅(routing) 또는 URL 매핑(URL mapping)이라고 합니다.  
  
> 예를 들면,  브라우저에서 ```localhost:8080/hello``` URL에 요청하면 라우터에서 ```/hello```에 대해 요청이 들어왔을 때 어떠한 응답을 하도록 설정해 놓았기 때문에 브라우저에서 Hello, world!라는 글을 볼 수 있었던 것입니다. Vapor 프로젝트의 **routes.swift** 파일에 정의된 ```public func routes(_ router: Router)``` 함수에서 ```router.get("hello")``` 메소드를 찾아보세요. ```"Hello, world!```라는 ```String```을 반환하는 것을 볼 수 있습니다.  
  
> URL에 라우터에 정의되지 않은 요청을 입력해보세요. ```localhost:8080/Something```처럼요. 브라우저에  ```{"error":true,"reason":"Not Found"}```가 표시될겁니다. 이건 웹서버에 그런 파일이 없으며 또한 라우터에 ```/Something``` URL에 대한 작업이 명시되어 있지 않기 때문입니다.  

---
## Tests
```Sources``` 폴더에 있는 모든 실행불가능한 모듈들은 모두 그에 대응하는 ```...Tests``` 폴더가 있습니다.  
  
**Apptests**  
이 폴더에는 여러분의 ```App``` 모듈에 있는 코드를 테스트하기 위한 유닛 모델이 있습니다. 테스팅에 대한 자세한 정보는 [Testing → Getting Started](/doc/Testing/GettingStarted)에서 확인하세요.  

---
## Package.swfit

바로 SPM의 [패키지 매니페스트](/doc/GettingStarted/SPM#패키지 매니페스트)입니다.