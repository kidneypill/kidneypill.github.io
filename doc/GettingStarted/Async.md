---
title: Async
layout: default
parent: Getting Started
nav_order: 9
---

## 비동기 (Async)  
  
	
Vapor의 몇몇 API들이 `Future` 자료형을 기대하거나 반환한다는걸 눈치 채셨을겁니다. future에 대해 처음 들으신다면 조금 헷갈릴 수도 있습니다. 쉽게 사용할 수 있으니 걱정할 건 없습니다.  
  
이 가이드는 Async의 빠른 활용을 위한 도입부입니다. 좀더 자세한 정보는 [Async → Overview](/doc/Async/Overview)를 참조하세요.  
  
	
---
## Futures
⚠️`Future`는 **비동기적으로 작용**하기 때문에, 그 값을 다루기 위해서는 반드시 **클로져**를 사용해야 합니다.  
  
Swift의 옵셔널처럼, future에도 **map** 또는 **flatMap**이 적용될 수 있습니다.  

---
### Map
` .map(to:_:)` 메소드를 통해 **future 값**을 **또다른 값**으로 변환할 수 있습니다.  
  
⚠️제공된 클로져는 ```Future```의 데이터가 유효해지면 단 한번 호출됩니다.  
```swift
/// 어떠한 API로부터 future string을 반환받았다고 가정합시다
let futureString: Future<String> = ...

/// future string을 integer로 매핑합니다.
let futureInt = futureString.map(to: Int.self) { string in
    print(string) // 이것이 실제 String입니다
    return Int(string) ?? 0
}

/// 이제 future integer가 되었습니다.
print(futureInt) // Future<Int>
```

---
### Flat Map
`.flatMap(to:_:)` 메소드를 통해 **future 값**을 **또다른 future 값**으로 변환할 수 있습니다. 이름이 "flat" map인 이유는 중첩(nested feature)을 피하게 해주기 때문입니다. (e.g., `Future<Future<T>>`). 여러분의 future를 평평하게 해준다는 말이죠.  

```swift
/// 어떠한 API로부터 future string을 반환받았다고 가정합시다
let futureString: Future<String> = ...

/// HTTP 클라이언트를 생성했다고 가정합시다
let client: Client = ... 

/// future string을 future response로 플랫매핑합니다
let futureResponse = futureString.flatMap(to: Response.self) { string in
    return client.get(string) // Future<Response>
}

/// 이제 future response가 되었습니다
print(futureResponse) // Future<Response>
```
  
ℹ️위 예시에서 `.map(to:_:)`를 썼다면  그 결과는 `Future<Future<Response>>`이었을거에요. 끔찍해라!

---
## 연쇄 (Chaining)
future에 대한 변형에 있어서 가장 좋은건 바로 연쇄가 가능하다는거죠. 많은 변환과 부가적인 작업들을 쉽게 구현할 수 있습니다.  
  
위에 나왔던 예시를 가지고 연쇄의 이점을 어떻게 활용할 수 있는지 알아봅시다.  
```swift
/// 어떠한 API로부터 future string을 반환받았다고 가정합시다
let futureString: Future<String> = ...

/// HTTP client를 생성했다고 가정합시다
let client: Client = ... 

/// string을 url로 변환한 뒤, 그걸 response로 변환합니다.
/// Future<URL>으로 매핑된 뒤 바로 Future<Response>로 플랫매핑되는 것에 주목하세요.
let futureResponse = futureString.map(to: URL.self) { string in
    guard let url = URL(string: string) else {
        throw Abort(.badRequest, reason: "Invalid URL string: \(string)")
    }
    return url
}.flatMap(to: Response.self) { url in
    return client.get(url)
}

print(futureResponse) // Future<Response>
```

가장 처음 map을 호출하는 곳에서 `Future<URL>`이 생성되었다가, 바로 `Future<Response>`로 플랫매핑됩니다.  
  
📖map과 flat-map 클로져 내부에서 에러를 `throw`할 수도 있습니다. future가 실패하면서 에러를 발생시킬 때 해당 코드가 실행될겁니다.  

---
## Worker
Vapor에서 `on: Worker` 패러미터를 가진 메소드를 보셨을 겁니다. 비동기 작업을 수행하면서 `EventLoop`에 대한 액세스가 필요한 메소드에서 주로 사용됩니다.  

📖`EventLoop`에 대한 정보는 [Async → Overview](/doc/Async/Overview)를 참조하세요
  
여러분이 Vapor에서 마주칠 가장 흔한 `Worker`는 다음과 같습니다:
* `Application`
* `Request`
* `Response`

```swift
/// Request와 어떠한 ViewRender가 있다고 가정합시다
let req: Request = ...
let view: ViewRenderer = ...

/// Request를 worker로 사용함으로써 뷰를 렌더링합니다.
/// 이렇게 함으로써 현재 event loop에서 비동기 작업이 일어나도록 합니다.
///
/// 시그니처:
/// func render(_: String, on: Worker)
view.render("home.html", on: req)
```

---
### 🐤초심자
📖**비동기(Asynchronous)**가 뭔가요?  
  
> 비동기(Asynchronous), 동기(Synchronous)에 대한 기본적인 개념은 매우 간단합니다.  
> 동기는 반드시 어떠한 하나의 작업을 완전히 처리한 뒤 다른 작업에 착수하는 것이고  
> 비동기는 어떠한 하나의 작업을 처리하면서 다른 작업을 동시에 처리하는 것입니다.

> 어떠한 반환값을 가진 메소드가 동기적이라면, 메소드가 결과물을 반환한 뒤에 다른 작업이 진행됩니다.  
> 어떠한 반환값을 가진 메소드가 비동기적이라면, 메소드가 결과물을 아직 반환하지 않았음에도 다른 작업이 진행됩니다.  
  
> Future는 이러한 비동기 작업의 개념에 착안한 Vapor의 기능입니다. 보통 시간이 많이 소요되는 작업에 비동기 방식이 적용되는데, 예를 들면 데이터베이스에 쿼리를 하는 작업이 있겠습니다. 데이터베이스로부터 정보를 가져온 뒤 그 정보를 홈페이지에 뿌리는 코드가 있다고 해봅시다.  

```swift
struct Data {
	...
	var description: String
	...
}

// 데이터베이스로부터 무언가를 가져와 data에 담습니다.
// 단, 작업에 오랜 시간이 소요됩니다.
let data = Database.fetch( something )

// 가져온 data에 대한 설명(description)을 출력합니다.
print(data.description)

// 이러한 코드는 보통 오류를 일으키거나 예상과 다른 결과를 출력하게 됩니다.
// data에 아직 정보가 제대로 담기지 않은 상황에서 data의 description에 접근하려 했기 때문입니다.
```
```swift
// 따라서 시간이 오래 걸리는 작업은 비동기적이라는 특성을 고려하여 클로저 형태로 표현하는 것이 좋습니다.
Database.fetch( something ) { data in
	let data = data
	print(data.description)
	// 이제 data의 description이 제대로 출력될겁니다.
}
```
  
	
📖**map**과 **flatMap**을 쓰는 이유가 뭐죠?  

> Future는 배열이고 (정확하게는 [SwiftNIO][swift-nio] 프레임워크의 `EventLoopFuture`의 typealias입니다.), 배열의 구성요소를 각각 다른 구성요소로 변환하는 메소드가 바로 map 또는 flatMap입니다. 따라서 map과 flatMap이 사용되는 것입니다.
  
📖Worker 개념이 잘 와닿지 않습니다.

> [Async → Overview](/doc/Async/Overview#Worker)를 참조하세요.  

---

[swift-nio]: <https://github.com/apple/swift-nio>