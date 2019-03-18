---
title: Overview
layout: default
parent: Async
nav_order: 1
---

## Async 개요

Promises와 Futures는 별개의 연관된 자료형으로, Promise는 Future를 *생성하는* 데에 쓰입니다. 대부분의 경우 여러분은 Vapor의 API로부터 반환된 Future를 다룰 것이고, Promise에 대해서는 걱정할 필요가 없습니다.  
  
  
| 자료형 | 설명 | 가변성 | 메소드 |
| --- | --- | --- | --- |
| `Future` | 아직은 유효하지 않은 객체에 대한 참조 | 읽기 전용 | `.map(to:_:)`  `.flatMap(to:_:)` `do(_:)` `catch(_:)` |
| `Promise` | 어떠한 객체를 비동기적으로 제공하겠다는 약속 | 읽기/쓰기 가능 | `succeed(_:)` `fail(_:)` |
  
	
Future는 콜백 기반 비동기 API의 대안입니다. 단순한 클로저들과 달리 Future는 연쇄(Chaining)와 변형(Transforming)이 가능합니다.  

---
## 변형 (Transforming)
Swift의 옵셔널과 마찬가지로, `Future`는 매핑 또는 플랫매핑될 수 있습니다. 여러분이 `Future`에 사용할 가장 흔한 작업은 다음과 같습니다.  
	
| 메소드 | 시그니처 | 설명 |
| --- | --- | --- |
| `map` | `to: U.Type, _: (T) -> U` | Future 값을 다른 값으로 매핑합니다 |
| `flatMap` | `to: U.Type, _: (T) -> Future<U>` | Future 값을 또다른 Future 값으로 매핑합니다 |
| `transform` | `to: U` | Future 값을 이미 유효한 값으로 매핑합니다 |
  
`Optional<T>`와 `Array<T>`의 `map`과 `flatmap` 메소드의 시그니처를 보면 `Future<T>`에서 사용할 수 있는 메소드와 매우 비슷하다는 걸 알 수 있습니다.  
  
### Map
`.map(to:_:)` 메소드를 통해 Future 값을 다른 값으로 변경할 수 있습니다. Future의 값이 아직 유효하지 않을 수 있기 때문에(보통 비동기 작업의 결과가 이렇죠) 값을 받을 클로져를 제공해야 합니다.  

```swift
/// 어떠한 API로부터 Future String을 반환받았다고 가정합시다
let futureString: Future<String> = ...

/// Future String을 Integer로 매핑합니다
let futureInt = futureString.map(to: Int.self) { string in
    print(string) // The actual String
    return Int(string) ?? 0
}

/// 이제 Futuer Integer가 되었습니다
print(futureInt) // Future<Int>
```
  
	
### Flat Map
`.flatMap(to:_:)` 메소드를 통해 Future 값을 또다른 Future 값으로 변경할 수 있습니다. 이름이 "flat" map인 이유는 중첩된 결과물(e.g. `Future<Future<T>>`)이 나오는걸 방지하기 때문입니다. Generic Future를 평평하게 만든다는 말이죠.  

```swift
/// 어떠한 API로부터 Future String을 반환받았다고 가정합시다
let futureString: Future<String> = ...

/// HTTP Client가 있다고 가정합시다
let client: Client = ... 

/// Future String을 Future Response로 플랫매핑합니다
let futureResponse = futureString.flatMap(to: Response.self) { string in
    return client.get(string) // Future<Response>
}

/// 이제 Future Reponse가 되었습니다
print(futureResponse) // Future<Response>
```

ℹ️ 만약 위 예시에서 `.map(to:_:)` 를 사용했다면 그 결과는 `Future<Future<Response>>`였을거에요. 끔찍해라!  
  
### Transform
`.transform(_:)` 메소드를 통해 현재 값을 무시하고 Future의 값을 변경할 수 있습니다. Future의 실제 값이 별로 중요하지 않은 `Future<Void>`의 결과를 변형시킬 때 특히 유용합니다.  
⚠️signal이라고도 불리는 `Future<Void>`는 어떠한 비동기 작업의 완료나 실패를 알려주는 용도의 Future입니다.  
```swift
/// 어떠한 API로부터 Void Future를 반환받았다고 가정합시다
let userDidSave: Future<Void> = ...

/// Void Future를 HTTP status로 변환합니다
let futureStatus = userDidSave.transform(to: HTTPStatus.ok)
print(futureStatus) // Future<HTTPStatus>
```

여기선 `transform`에 이미 유효한 값을 넣었지만, 이것도 *transformation*입니다. 그 이전의 모든 Future가 완료되거나 실패하기 전까지는 완료되지 않습니다.  

---
## 연쇄 (Chaining)
Future의 변형에 있어서 가장 좋은 점은 연쇄가 가능하다는 겁니다. 많은 변환과 추가 작업들이 쉽게 이루어질 수 있습니다.  
연쇄의 이점을 보기 위해 위의 예시들을 조금 바꿔 봅시다.  
```swift
/// 어떠한 API로부터 Future String을 반환받았다고 가정합시다
let futureString : Future<String> = ...

/// HTTP Client가 있다고 가정합시다
let client: Client = ...

/// String을 URL로, 그리고 Response로 변환합니다.
let futureResponse = futureString.map(to: URL.self) { string in
	guard let url = URL(string: string) else {
		throw Abort(.badrequest, reason: "Invalid URL String: \(string)")
	}
	return url
}.flatMap(to: Response.self) { url in
	return client.get(url)
}

print(futureResponse) // Future<Response>
```
  
가장 처음 map을 호출한 뒤, 일시적으로 `Future<URL>`이 생성됩니다. 그리고 즉시 `Future<Response>`로 플랫매핑됩니다.  
⚠️map과 flat-map 클로져 내부에서 에러를 `throw`할 수 있습니다. Future가 실패하면서 에러를 발생시킬 때 이 코드가 실행됩니다.  

---
## Future
`Future<T>`의 좀 덜 쓰이는 다른 메소드에 대해 알아봅시다.  

### Do / Catch
Swift의 `do` / `catch ` 문법처럼, Future도 Future의 결과를 기다리기 위한 `do` / `catch` 메소드를 가지고 있습니다.  
```swift
/// 어떠한 API로부터 Future String을 반환받았다고 가정합시다
let futureString: Future<String> = ...

futureString.do { string in
	print(string) // 실제 문자열
} .catch { error in 
	print(error) // Swift 에러
}
```

⚠️`.do`와 `.catch`는 함께 쓰입니다. `.catch`를 생략하면 컴파일러가 unused result에 대해 경고할 것입니다. 에러 핸들링을 잊지 마세요!  

### Always
Future가 성공하거나 실패했을 때 실행될 콜백을 추가하려면 `always`를 사용합니다.  
```swift
/// 어떠한 API로부터 Future String을 반환받았다고 가정합시다
let futureString: Future<String> = ...

futureString.always {
	print("Future가 완료되었습니다!")
}
```

📖원하는 만큼 Future에 콜백을 추가할 수 있습니다.

### Wait
동기적으로 Future가 완료되기를 기다리려면 `.wait()`를 사용합니다. Future가 실패할 수도 있기 때문에 이 메소드는 throw합니다.  
```swift
/// 어떠한 API로부터 Future String을 반환받았다고 가정합시다
let futureString: Future<String> = ...

/// 문자열이 준비될 때까지 블록합니다
let string = try futureString.wait()
print(string) // String
```

⚠️루트 클로져나 컨트롤러에서 이 메소드를 사용하지 마세요! 자세한 건 [Blocking][blocking-link]를 참조하세요.

---
## Promise
대부분의 경우 여러분은 Vapor API 호출로부터 반환된 Future를 변환하게 될겁니다. 하지만 여러분만의 promise를 만들어야 할 때가 있을 수도 있습니다.  
  
promise를 만들려면 `EventLoop`에 접근해야 합니다. Vapor의 모든 컨테이너는 여러분이 사용할 수 있는 `eventLoop` 속성을 가지고 있습니다. 일반적으로 이것이 현재 `Request`가 됩니다.

### 🐤초심자
📖시그니처에서 `T`와 `U`는 뭔가요?  

> Swift의 제네릭 함수를 위한 타입 패러미터입니다.  
> 자세한 내용은 [Generics][generics-link]를 참조하세요.  

---

[generics-link]: <https://docs.swift.org/swift-book/LanguageGuide/Generics.html>
[blocking-link]: </doc/Async/Overview#blocking>