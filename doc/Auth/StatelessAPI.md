---
title: Stateless (API)
layout: default
parent: Auth
nav_order: 1
---

## API 인증
<br>
이 가이드에서는 무상태 인증(Stateless Authentication)을 소개합니다. 무상태 인증이란 API 엔드포인트를 보호하기 위해 흔히 사용되는 인증 방법입니다.<br>

---
## 컨셉
⚠️컴퓨터과학(특히 웹 프레임워크)에서 인증(Authentication)이란 사용자의 *신원(identity)* 을 확인하는 것을 말합니다.<br>
⚠️어떠한 리소스에 대한 *권한* 을 확인하는 인가(Authorization)와 혼동해서는 안 됩니다.<br>
<br>
Auth 패키지의 다음 도구들을 사용해서 무상태 인가를 구축할 수 있습니다.<br>
* *`"Authorization"`* 헤더: HTTP 요청에 자격증명(credentials)을 담아 보냅니다.
* *Middleware*: 요청에서 자격증명을 찾아내고 인증된 사용자를 가져옵니다.
* *Model*: 인증된 사용자와 그에 대한 정보를 나타냅니다.
<br>

### 인가(Authorization) 헤더
Auth 패키지는 인증 헤더 포맷으로 Basic과 Bearer 두 가지를 사용합니다.<br>
<br>
#### **Basic**
Basic authorization은 사용자명과 패스워드를 포함합니다. `:`와 base64 인코딩으로 연결됩니다.<br>
<br>
사용자명 `Alladin`과 패스워드 `OpenSesame`을 포함하는 Basic authorization 헤더는 다음과 같이 표시됩니다:<br>

```
Authorization: Basic QWxhZGRpbjpPcGVuU2VzYW1l
```

Basic 인증으로도 여러분의 서버에 대한 각각의 요청을 인증하는 것이 가능하지만, 대부분의 웹 어플리케이션은 이런 용도로는 임시 토큰(ephemeral token)을 발급해서 사용합니다.<br>
<br>
#### **Bearer**
Bearer authorization은 단순히 토큰을 포함합니다. `cn389ncoiwuencr` 토큰을 포함하는 Bearer authorization 헤더는 다음과 같이 표시됩니다:<br>

```
Authorization: Bearer cn389ncoiwuencr
```

Bearer authorization 헤더는 각 요청에 담아 보내기 쉽고 임시 토큰을 담을 수 있기 때문에 여러 API에서 매우 흔히 볼 수 있습니다.<br>
<br>
### 미들웨어
Auth 패키지에서 미들웨어의 사용은 매우 중요합니다. Vapor에서 미들웨어가 어떻게 작동하는지 잘 모른다면 [Vapor → Middleware][vapor-middleware-link]에서 읽어보세요.<br>
<br>
인증 미들웨어가 하는 일은 요청에서 인증서(credentials)를 읽어들이고 그것이 인증하는 사용자를 가져오는 것입니다. 이는 주로 `"Authorization"` 헤더를 확인하고, 인증서(credentials)를 파싱하고, 데이터베이스를 열람(Lookup)하는 등의 행위를 뜻합니다.<br>
<br>
여러분이 사용하는 각각의 모델/인증 방법마다, 하나의 미들웨어를 추가하게 될겁니다. 패키지의 모든 미들웨어는 합쳐질 수 있는데요, 무슨 말이냐면 하나의 루트에 여러개의 미들웨어를 추가해 합동하게 할 수 있다는 겁니다. 만약 어떠한 미들웨어가 사용자를 인증하는 데에 실패하면, 요청을 다음 미들웨어로 넘겨 다시 시도하게 됩니다.<br>
<br>
만약 여러분의 루트를 실행하기 전에 어떠한 모델의 인증이 완료되게 하고 싶다면, [GuardAuthenticationMiddleware][guardAuthenticationMiddleware-link]의 인스턴스를 추가해야 합니다.<br>

### 모델
미들웨어가 인증하는 대상이 바로 Fluent 모델입니다. [Fluent → Models][fluent-models-link]에서 모델에 더해 더 알아보세요.<br>
인증이 성공적으로 완료되면 미들웨어는 데이터베이스로부터 여러분의 모델을 가져와서 요청에 저장하게 됩니다. 이제 여러분의 루트에서 인증된 모델에 동기적으로 접근할 수 있습니다.<br>
<br>
루트 클로져 내에서, 다음 메소드를 사용해 정확히 인증되었는지 확인하세요:<br>
* `authenticated(_:)`: 인증되었다면 자료형을 반환합니다. 아니라면 `nil`을 반환합니다.
* `isAuthenticated(_:)`: 제시된 자료형이 인증되었다면 `true`를 반환합니다.
* `requireAuthenticated(_:)`: 인증되었다면 자료형을 반환합니다. 아니라면 `throw`합니다.
<br>

보통 이렇게 사용합니다:<br>

```swift
// group을 보호하기 위한 미들웨어를 사용합니다
let protectedGroup = router.group(...)

// 보호된 루트를 추가합니다
protectedGroup.get("test") { req in
	// 사용자가 미들웨어에 의해 인증될 것을 요구하며, 아니라면 throw합니다
	let user = try req.requireAuthenticated(User.self)
	
	// 사용자에게 인사합니다
	return "Hello, \(user.name)."
}
```

---
## 메소드
Auth 패키지는 두가지 기본적인 종류의 무상태 인증(stateless authentication)을 지원합니다.
<br>
<br>
* *Token*: Bearer authorization 헤더를 사용합니다.
* *Password*: Basic authorization 헤더를 사용합니다.
<br>
<br>
각 인증 방식마다 각각의 미들웨어와 모델 프로토콜이 있습니다.
<br>
### 패스워드 인증
패스워드 인증은 basic authorization 헤더(사용자명과 패스워드)를 사용해 사용자를 확인합니다. 사용자명과 패스워드는 이 메소드를 통해 각 요청에 담겨 보호된 엔드포인트까지 전송되어야 합니다.<br>
<br>
패스워드 인증을 진행하기 위해서는 우선 여러분의 Fluent 모델이 `PasswordAuthenticatable`을 따르도록 해야합니다.<br>

```swift
extension User: PasswordAuthenticatable {
	/// `PasswordAuthenticatable`을 참조하세요.
	static var usernameKey: WritableKeyPath<User, String> {
		return \.email
	}
	
	// `PasswordAuthenticatable`을 참조하세요.
	static var passwordKey: WritableKeyPath<User, String> {
		return \.passwordHash
	}
}
```

⚠️`passwordKey`가 *해싱된* 패스워드를 가리켜야 한다는 점을 기억하세요. 패스워드를 평문으로 저장하지 마세요.<br>
<br>
인증 가능한 모델을 생성했으면 이제 여러분의 보호된 루트에 미들웨어를 추가하세요.<br>

```swift
// user 모델을 사용해 인증 미들웨어를 만듭니다
let password = User.basicAuthMiddleware(using: BCryptDigest())

// 이 미들웨어로 둘러싸인 루트 클로져를 만듭니다
router.grouped(password).get("hello") { req in
	///
}
```

여기서는 사용자의 패스워드가 Bcrypt hash 형태로 저장되었다고 가정하고 `BcryptDigest`를 [PasswordVerifier][passwordverifier-link]로 사용하고 있습니다.<br>
<br>
이제 루트 클로져 내에서 인증된 사용자를 가져오려면 [requireAuthenticated(_:)][requireAuthenticated-link]를 사용합니다.<br>

```swift
// 인증된 사용자에 대한 요청이라면 User 자료형의 인스턴스를 반환합니다
// 인증되지 않았다면 throw합니다
let user = try req.requireAuthenticated(User.self)

return "Hello, \(user.name)."
```

유효한 인증서가 제시되지 않았다면 `requireAuthenticated` 메소드는 알아서 적절한 unauthorized error를 throw합니다. 그렇기 때문에  [GuardAuthenticationMiddleware]를 사용해서 어떠한 루트에 대한 인증되지 않은 접근을 막을 필요가 없습니다.<br>

### 토큰 인증
토큰 인증은 Bearer authorization 헤더(토큰)를 사용해 토큰과 그에 관계된 사용자를 열람합니다. 토큰은 이 메소드를 통해 각 요청에 담겨 보호된 엔드포인트까지 전송되어야 합니다.<br>
<br>
⚠️토큰 인증은 패스워드 인증과는 달리 *두 개*의 Fluent 모델에 의존합니다. 하나는 토큰을 위한 것이고, 다른 하나는 사용자를 위한 것이죠. 토큰 모델은 사용자 모델의 *child*여야 합니다.<br>
<br>
아주 간단한 `User`과 그에 관계된 `UserToken` 예시를 보여드리겠습니다.<br>

```swift
struct User: Model {
	var id: Int?
	var name: String
	var email: String
	var passwordHash: String
	
	var tokens: Children<User, UserToken> {
		return children(\.userID)
	}
}

struct UserToken: Model {
	var id: Int?
	var string: String
	var userID: User.ID
	
	var user: Parent<UserToken, User> {
		return parent(\.userID)
	}
}
```

토큰 인증을 사용하기 위해서는 우선 여러분의 User와 Token 모델이 각각 `Authenticatable` 프로토콜을 따르도록 해야합니다.<br>

```swift
extension UserToken: Token {
	/// `Token`을 참조하세요
	typealias UserType = User
	
	/// `Token`을 참조하세요
	static var tokenKey: WritableKeyPath<UserToken, String> {
		return \.string
	}
	
	/// `Token`을 참조하세요
	static var userIDKey: WritableKeyPath<UserToken, User.ID> {
		return \.userID
	}
}
```
Token이 `Token`을 따르게 했다면 User 모델을 세팅하는건 쉽습니다.<br>

```swift
extension User: TokenAuthenticatable {
	/// `TokenAuthenticatable`을 참조하세요
	typealias TokenType = UserToken
}
```

여러분의 모델이 모두 프로토콜을 따르게 했다면, 이제 보호된 루트에 미들웨어를 추가하세요.<br>

```swift
// User 모델을 사용해 인증 미들웨어를 만듭니다
let token = User.tokenAuthMiddleware()

// 해당 미들웨어로 둘러싸인 루트 클로져를 만듭니다
router.grouped(token).get("hello") {
	//
}
```

이제, 루트 클로져 내에서 인증된 사용자를 가져오려면 [requireAuthenticated(_:)][requireAuthenticated-link]를 사용하세요.<br>

```swift
let user = try req.requireAuthenticated(User.self)
return "Hello, \(user.name)."
```

패스워드 인증과 마찬가지로, 여기서도 유효한 인증서가 제시되지 않은 경우 알아서 unauthorized error를 throw하고, 따라서 [GuardAuthenticationMiddleware][guardAuthenticationMiddleware-link]를 사용할 필요가 없습니다.

---
[vapor-middleware-link]: </doc/Vapor/MiddleWare>
[guardAuthenticationMiddleware-link]: <https://api.vapor.codes/auth/latest/Authentication/Classes/GuardAuthenticationMiddleware.html>
[fluent-models-link]: </doc/Fluent/Models>
[passwordverifier-link]: <https://api.vapor.codes/auth/latest/Authentication/Protocols/PasswordVerifier.html>
[requireAuthenticated-link]: <https://api.vapor.codes/auth/latest/Authentication/Extensions/Request.html#/s:5Vapor7RequestC14AuthenticationE20requireAuthenticatedxxmKAD15AuthenticatableRzlF>