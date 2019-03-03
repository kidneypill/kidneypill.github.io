---
layout: default
nav_exclude: true
---

# Vapor in korean
```swift
import Vapor

let app = try Application()
let router = try app.make(Router.self)

router.get("hello") { req in
    return "Hello, world."

}

try app.run()

// https://vapor.codes/
```
---
## 0. 이 홈페이지의 목적과 대상
### 목적 0: Vapor 공식문서 번역
> 이 홈페이지의 자료는 Vapor의 [공식문서][Vapor doc]를 기반으로 합니다.

### 목적 1: 초보자를 위한 친절한 설명
> 공식 문서는 유저가 **Swift**와 기본적인 **웹에 대한 이해**를 하고 있다는 전제 하에 쓰여진 듯 합니다. Swift를 정확히 모르거나 웹에 대한 선수 지식이 없는 사람들에게는 이해하기 힘들 수도 있습니다. 따라서 **공식 문서를 한글화**하는 동시에 **초보자도 쉽게 이해할 수 있도록 정리**하는 것이 이 홈페이지의 두 번째 목적이라고 할 수 있겠습니다.  

### 대상 0: 웹서버 구축을 고려하는 Swift 초보자
> Swift 를 잘 모르신다면 공식 문서 [Swift.org](https://swift.org) 를 참조하세요. 웹서버 구축에 있어 어떤 Swift 프레임워크를 선택해야 할지 고민이시라면 구글에 검색해보세요. 이 홈페이지는 Vapor를 다루며 타 프레임워크는 고려 중입니다.

### 대상 1: 한글로 Vapor 공식문서를 보고 싶은 사람

### 대상 2: 프론트엔드와 백엔드를 Swift로 통합하고 싶은 사람

---
## 1. Vapor 공식문서 한글화의 목적

### Swift 웹프레임워크의 부상과 필요성

0. Swift는 상업적 이용이 자유로운 Apache 2.0 라이센스[^apacheLicense] 오픈소스이기 때문에 자유롭게 쓸 수 있습니다.
1. **iOS** 또는 **macOS** 앱을 개발하는 데에 있어서 서버가 필요한 경우가 있는데
2. Swift 웹프레임워크를 사용하면 프론트엔드(앱), 백엔드(서버) 모두 다 **Swift** 하나로 제작할 수 있습니다.
3. 이점에 착안해 많은 Swift 프레임워크가 등장했습니다.
3. [Perfect][Perfect] [Vapor][Vapor] [Kitura][Kitura] 등 다양한 Swift 프레임워크가 있어요.
4. 그중 Vapor를 다루고자 합니다.

  
### 그런데 Vapor는 한국어 자료가 없고...

0. Vapor는 2016년에 탄생한 신생 프레임워크입니다.
1. Vapor 1.0, Vapor 2.0, Vapor 3.0 크게 세 단계로 나눌 수 있습니다.
2. 최신 버전인 Vapor 3.0을 다루고자 합니다.

  
---
## 2. 왜 Vapor인가?
### Perfect, Vapor, Kitura 중 Vapor를 선택한 이유?

0. 셋 다 훌륭한 프레임워크입니다.
1. 각 프레임워크 모두 각각의 특성에 따른 장단점이 존재합니다.
2. 따라서 Vapor를 선택한 데에는 큰 이유가 없습니다.
3. Vapor 공식문서 번역을 마치면 Perfect와 Kitura도 둘러볼 예정입니다.

---
## 3. 이 사이트가 믿을만 할까?
### 작성자에 대해

0. 저는 Vapor와 어떤 관계도 없는 한국인입니다.
1. 또한 전문가도 아닙니다.
2. 이 홈페이지에는 오류, 오타가 있을 수 있습니다.
3. 지적이나 제안은 [kidneypill](mailto:kidneypill@protonmail.com)으로 보내주세요
4. 이 사이트를 볼 때는 Vapor의 공식 문서 원본을 같이 보는 것도 좋은 생각입니다.

---
## 4. 기타
### 신생 프레임워크의 한계?  
  
> ```사견``` 여러 유저들이 사용하고 참여하면서 신뢰성을 인정받은 타 프레임워크를 사용하는 대신 신생 프레임워크를 사용하는 것은 그럴만한 이유가 있어야 합니다. 상대적으로 신뢰성이 부족하거나 좋은 라이브러리가 비교적 부족할 수 있다는 점을 고려하시고 Vapor를 시작하세요.
또한 Swift가 Python이나 Java와 같은 주류 언어에 비해서는 마이너[^swift]라는 점도 고려하세요.  

---

[Vapor]: <https://vapor.codes/> "Vapor"
[Vapor doc]: <https://docs.vapor.codes/3.0/> "Vapor doc"
[Perfect]: <https://perfect.org> "Perfect"
[Kitura]: <https://www.kitura.io> "Kitura"
[Raywend]: <https://raywenderlich.com> "Raywend"


[^swift]:  Swift의 점유율은 2019.03.03 기준 약 2.5%로 [여기](http://pypl.github.io/PYPL.html)에서 확인할 수 있습니다.
[^apacheLicense]: Apache 2.0 라이센스에 대한 간단한 정보는 [여기](https://tldrlegal.com/license/apache-license-2.0-%28apache-2.0%29)에서 확인하세요.