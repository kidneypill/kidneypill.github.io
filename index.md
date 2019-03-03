---
layout: default
nav_exclude: true
---

# Vapor in korean
![Vapor Image](/vapor.png){:width="50" height="50"}
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

## 1. Vapor 공식문서 한글화의 목적

### Swift 웹프레임워크의 필요성

1. [Vapor][Vapor]는  Swift로 이루어진 웹 프레임워크 중 하나
2. **iOS** 또는 **macOS** 앱을 개발하는 데에 있어서 서버가 필요한 경우
3. 개같은 [Ruby on rails](https://rubyonrails.org/) 환경에 지쳤을 때
4. Swift 웹프레임워크를 사용하면 프론트엔드(앱), 백엔드(서버) 모두 다 **Swift** 하나로

### 그런데 Vapor는 한국어 자료가 없고...

1. Ruby on rails나 Spring같은 유명한 웹 프레임워크들은 한국에서도 많이 쓰이다보니 번역도 잘 되어 있다.
2. 하지만! [Vapor][Vapor]는 여러 프로그래밍 언어 중 마이너인 Swift[^1]로 만든 데다가
3. PerfectlySoft Inc. 의 [Perfect][Perfect]
4. Qutheory LLC 의 [Vapor][Vapor]
5. IBM의 [Kitura][Kitura]
6. 이렇게 삼강구도이기 때문에 인기가 분산되어 있다!

### Vapor 공식 문서를 한국어로 번역하려고 한다.
1. 사실 모든 Swift 프레임워크들은 한국어 번역이 되어 있지 않다 ^V^ ;;


---
## 2. 왜 하필 Vapor인가?
### Perfect, Vapor, Kitura 중 Vapor를 선택한 이유?
1. Vapor가 Swift 프레임워크 중 가장 강력한 프레임워크로 급부상하고 있다.
2. 사실 내 입장에서는 Perfect, Vapor, Kitura 모두 고만고만한 것 같다.
3. 학습곡선은 셋다 마찬가지아닐까?
4. [Raywenderlich.com][Raywend]에서 Vapor와 Kitura 강의가 개설된 걸 보았다...

---
## 3. 이 번역 문서가 믿을만 할까?
### 작성자는 비전공자

### 그러니 참고만 하세용 ^ㅆ^
---
## 4. 업데이트는 되고 있는가?
### 글쎄... 금방 찍쌀지도!

### 하지만 Swift를 배우고 iOS 개발을 배운 지난 2년이 아까워서라도

### 꼭 백엔드를 잘 만들어서 성공적으로 앱을 배포하고 싶다 ㅠㅠ

---

[Vapor]: <https://vapor.codes/> "Vapor"
[Perfect]: <https://perfect.org> "Perfect"
[Kitura]: <https://www.kitura.io> "Kitura"
[Raywend]: <https://raywenderlich.com> "Raywend"


[^1]:  Swift의 점유율 2019.03.03 기준 약 2.5%로 [여기서](http://pypl.github.io/PYPL.html) 확인할 수 있다.
