---
title: Content
layout: default
parent: Getting Started
nav_order: 8
---

## 컨텐츠
  
Vapor 3에서는 모든 컨텐츠 자료형 (JSON, protobuf, URLEncodedForm, Multipart 등)이 동일하게 취급됩니다. 컨텐츠를 파싱하고 시리얼라이징하려면 `Codable` 프로토콜에 부합하는 클래스 또는 구조체만 있으면 됩니다.  
  
여기선 JSON을 예시로 사용할겁니다. 지원하는 모든 컨텐츠 자료형에 대해 API는 동일하다는 점을 알아두세요.  

---
## 요청 (Request)
다음 HTTP 요청을 어떻게 파싱할지 알아봅시다.  
```swift
POST /login HTTP/1.1
Content-Type: application/json

{
	"email": "user@vapor.codes",
	"password": "don't look!"
}
```
여러분이 받아야 할 데이터에 맞게 클래스나 구조체를 선언해 주세요.  

```swift
import Vapor

// Content 프로토콜을 따라야 합니다.
struct LoginRequest: Content {
	var email: String
	var password: String
}
```
그런 클래스나 구조체를 `Content` 프로토콜을 따르도록 합니다. 이제 HTTP 요청을 복호화할 준비가 된겁니다.  

```swift
// POST /login 요청에 대하여 전송된 컨텐츠를 LoginRequest 구조체 형식에 맞게 복호화합니다.
router.post("login") { req -> Future<HTTPStatus> in
    return req.content.decode(LoginRequest.self).map(to: HTTPStatus.self) { loginRequest in
        print(loginRequest.email) // user@vapor.codes
        print(loginRequest.password) // don't look!
        return .ok
    }
}
```

`.map(to:)`를 사용한 것은 `req.content.decode(_:)`가 [future](/doc/GettingStarted/Async)를 반환하기 때문입니다.

---
## 응답 (Response)

다음 HTTP 응답을 어떻게 만들 수 있을지 고민해 보세요.
```swift
HTTP/1.1 200 OK
Content-Type: application/json

{
    "name": "Vapor User",
    "email": "user@vapor.codes"
}
```

복호화 때와 마찬가지로, 여러분이 전송할 데이터에 맞는 구조체 또는 클래스를 선언하세요.
```swift
import Vapor

struct User: Content {
    var name: String
    var email: String
}
```

그 뒤 이 구조체나 클래스가 `Content` 프로토콜을 따르도록 하세요. 이제 HTTP 응답을 암호화할 준비가 되었습니다.  
```swift
router.get("user") { req -> User in
    return User(
        name: "Vapor User",
        email: "user@vapor.codes"
    )
}
```

좋습니다! 이제 Vapor에서 암호화와 복호화를 하는 법을 알게 되셨습니다.  
  
📖[Vapor → Content](/doc/Vapor/Content)에서 더 자세한 정보를 확인하세요.