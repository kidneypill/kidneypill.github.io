---
title: Routing
layout: default
parent: Getting Started
nav_order: 7
---

## 라우팅
라우팅이란 하나의 요청에 대한 적절한 응답을 찾는 과정을 말합니다.  

---
## 라우터 만들기
Vapor의 기본 라우터는 ```EngineRouter```입니다. ```Router``` 프로토콜에 부합하는 커스텀 라우터를 사용할 수도 있습니다.  
```swift
let router = try EngineRouter.default()
```
보통 이런 작업은 **configure.swift** 파일에서 합니다.  

---
## 루트 등록하기
누군가 ```GET /users```에 방문했을 때 유저 리스트를 반환하고 싶다고 해봅시다. 인증은 우선 미뤄두고, 아마 이렇게 보일겁니다.  
```swift
router.get("users") { req in
	return // 유저 목록 가져와서 반환하기
}
```

Vapor에서는 ```.get``` ```.put``` ```.post``` ```.patch``` ```.delete``` 메소드를 통해 라우팅을 처리합니다. Path는 ```/```나 컴마로 분리된 String을 통해 표현할 수 있습니다. 컴마로 구분하는 방식을 추천합니다. (읽기 쉬우니까요)  
```swift
// GET /path/to/something 요청을 처리합니다.
router.get("path", "to", "something") { .... }
```

---
## 루트
루트들을 적어놓기에 가장 좋은 곳은 바로 **routes.swift** 파일 내에 있습니다. 바로 ```routes``` 메소드 내부입니다. 이 메소드에 패러미터로 넘겨진 ```router```를 라우터로 사용해서 여러분의 루트들을 등록하세요.  
```swift
import Vapor

public func routes(_ router: Router) throws {
	// Basic "Hello, world!" example
	router.get("hello") { req in
		return "Hello, world!"
	}
	
	// 여기에 여러분의 루트를 등록하는 것이 좋습니다.
}
```
route closure에서 어떤 것들이 반환될 수 있는가에 대한 자세한 정보는 [Getting Started → Content](/doc/GettingStarted/Content)를 참조하세요.  

---
## 패러미터
여러분의 루트 패스의 구성요소의 일부분이 동적이길 원할 수도 있습니다. 주로 제공된 식별자와 함께 무언가 전달받고 싶을 때 사용합니다. 예를 들면 ```GET /users/:id```처럼요.  
```swift
router.get("users", Int.parameter) { req -> String in
	let id = try req.parameter.next(Int.self)
	return "requested id #\(id)"
}
```
⚠️String을 넘기기보다는 패러미터의 자료형(type)을 넘기세요. 위 예시에서 ```User```는 ```Int``` 자료형의 id를 가졌다고 상정하고 저렇게 한 것입니다.  
📖여러분만의 [커스텀 패러미터 자료형](/doc/Routing/Overview#패러미터)을 사용할 수도 있습니다.

---
## 루트 등록 후
⚠️루트를 등록하고 난 뒤에는 반드시 [Getting Started → Services](/doc/GettingStarted/Services)에 나온대로 라우터를 등록해야 합니다.