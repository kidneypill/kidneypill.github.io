---
title: Services
layout: default
parent: Getting Started
nav_order: 10
---

## 서비스
  
서비스란 Vapor를 위한 서비스 로케이터 프레임워크입니다. (Inversion of control이라 불리기도 합니다)  
서비스 프레임워크는 여러분의 어플리케이션에 있어서 필요한 모든 등록(register), 설정(configure), 초기화(initialize) 기능을 제공합니다.  

---
## 컨테이너 (Container)
  
서비스와의 모든 상호작용은 컨테이너를 경유합니다. 컨테이너란 다음 목록들의 조합입니다:  
* [Services](/doc/GettingStarted/Services#서비스): 등록된 서비스들의 집합
* [Config](/doc/GettingStarted/Services#설정): 특정 서비스에 대한 설정
* [Environment](/doc/GettingStarted/Services#환경): 어플리케이션의 현재 환경 종류 (테스트 또는 배포 등)
* [Worker](/doc/GettingStarted/Async#worker): 이 컨테이너와 연관된 Event loop  
  
Vapor에서 여러분이 가장 흔히 다룰 컨테이너는 다음과 같습니다:  
* `Application`
* `Request`
* `Response`

앱을 부팅하는 데에 필요한 서비스를 만들 컨테이너로는 `Application`이 적절합니다.  
요청에 응답하는 서비스를 만들 컨테이너로는 `Request` 또는 `Response`가 적절합니다. (route closure나 컨트롤러에서 만들면 됩니다.)

---
## 만들기
  
서비스를 만드는건 간단합니다. 컨테이너에서 `.make(_:)`를 호출한 뒤 여러분이 원하는 자료형(주로 `Client`같은 프로토콜)을 넘기세요.  
```swift
let client  = try req.make(Client.self)
```

여러분이 원하는게 확실하다면, 구체적인 자료형을 특정해도 됩니다.  
```swift
let leaf = try req.make(LeafRenderer.self)
print(leaf) // LeafRenderer를 출력합니다.

let view = try req.make(ViewRenderer.self)
print(view) // ViewRenderer입니다만 LeafRenderer일 수도 있습니다.  
```

📖가능하다면 구체적인 자료형보다는 프로토콜을 사용하는 것을 권장합니다. 이렇게 함으로써 여러분의 코드를 좀더 쉽게 테스트할 수 있고, 코드를 분리할 수 있습니다.  

---
## Services
`Services` 구조체에는 여러분이 등록한 모든 서비스 또는 서비스 제공자가 포함되어 있습니다. 서비스를 등록하거나 설정하는 건 주로 [configure.swift](/doc/GettingStarted/FolderStructure#configureswift)에서 합니다.  

### 인스턴스
초기화된 서비스 인스턴스는 `.register(_:)` 메소드를 통해 등록할 수 있습니다.  
```swift
/// 인메모리 SQLite 데이터베이스를 생성합니다.
let sqlite = SQLiteDatabase(storage: .memory)

/// 서비스에 등록합니다.
services.register(sqlite)
```
서비스를 등록하면 `Container`를 통해 생성할 수 있게 됩니다.
```swift
let db = app.make(SQLiteDatabase.self)
print(db) // 위의 코드블럭에서 만든 SQLiteDatabase이 출력됩니다.
```
  
  
### 프로토콜
서비스를 등록할 때, 특정 프로토콜에 부합하도록 선언할 수 있습니다. Vapor가 메인 라우터를 등록할 때에도 이 방식을 사용합니다.  
```swift
/// 라우터에 루트를 등록
let router = EngineRouter.default()
try routes(router)
services.register(router, as: Router.self)
```

```router``` 변수를 ```as: Router.self```를 사용해 등록했기 때문에, 구체적인 자료형이나 프로토콜 어떤 쪽으로든 생성할 수 있습니다.  

```swift
/// 프로토콜 쪽으로 생성하기
let router = app.make(Router.self)

/// 구체적인 자료형으로 생성하기
let engineRouter = app.make(EngineRouter.self)

print(router) // Router (사실은 EngineRouter)
print(engineRouter) // EngineRouter
print(router === engineRouter) // true
```


---
## Environment
여러분의 앱이 특정 상황에 어떻게 작동할지는 Environment를 사용해 동적으로 변경할 수 있습니다. 예를 들면, 여러분의 앱이 배포되었을 때 데이터베이스에서 다른 유저네임과 비밀번호를 사용하기를 원할 수도 있습니다. 이런 문제는 ```Environment``` 자료형을 사용하면 쉽게 해결할 수 있습니다.  
  
커맨드 라인을 통해 Vapor를 구동할 때, 추가로 `--env` 플래그를 사용해 Environment를 특정할 수 있습니다. 기본적으로 Environment는 `.development`로 되어 있습니다.

```
swift run Run --env prod
```
위 예시에서는 Vapor를 `.prod` 환경에서 구동하고 있습니다. 이 환경에서는 `isRelease = true`가 됩니다.

[configure.swift](/doc/GettingStarted/FolderStructure#configureswift)에 전달된 환경을 통해 동적으로 서비스를 생성할 수도 있습니다.
```swift
let sqlite: SQLiteDatabase
if env.isRelease {
	/// process 환경에서는 $SQLITE_PATH를 사용하는 파일 기반 SQLite 데이터베이스를 생성합니다.
	sqlite = try SQLiteDatabase(storage: .file(path: Environment.get("SQLITE_PATH")!))
} else {
	/// 그 외의 경우 인메모리 SQLite 데이터베이스를 생성합니다.
	sqlite = try SQLiteDatabase(storage: .memory)
}
services.register(sqlite)
```

ℹ️process 환경에서 String 값을 가져오려면 스태틱 메소드인 `Environment.get(_:)`를 사용하세요.  
  
팩토리 메소드 `.register(_:)`를 사용해서 환경별로 동적으로 서비스를 등록할 수도 있습니다.  
```swift
services.register { container -> BCryptConfig in
  let cost: Int

  switch container.environment {
  case .production: cost = 12
  default: cost = 4
  }

  return BCryptConfig(cost: cost)
}
```

---
## Config
주어진 프로토콜에 대해 다양한 서비스가 가능하다면, `Config` 구조체를 사용해 어떤 서비스를 선호하는지 명시하세요.  
```
ServiceError.ambiguity: Please choose which KeyedCache you prefer, multiple are available: MemoryKeyedCache, FluentCache<SQLiteDatabase>.
```

이것도 마찬가지로 [configure.swift](/doc/GettingStarted/FolderStructure#configureswift)에서 하면 됩니다. `config.prefer(_:for:)` 메소드를 사용하세요.  
```swift
/// 컨테이너에서 KeyedCache를 생성할 때 물을 MemoryKeyedCache에 대한 선호를 명시합니다.
config.prefer(MemoryKeyedCache.self, for: KeyedCache.self)

/// ...

/// Request 컨테이너를 사용해 KeyedCache를 생성합니다.
let cache = req.make(KeyedCache.self)
print(cache is MemoryKeyedCache) // true
```
---