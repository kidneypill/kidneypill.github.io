---
title: Getting Started
layout: default
nav_order: 0
parent: Auth
---

## Auth 시작하기
<br>
Auth ([vapor/auth][vapor/auth-link])는 여러분의 어플리케이션에 인증 과정(Authentication)을 추가하기 위한 프레임워크입니다. [Fluent][fluent-link]를 기반으로 만들어졌습니다.<br>
<br>
📖미리 설정된 Auth를 포함하는 Vapor API 템플릿도 있으니 [Getting Started → Toolbox → 템플릿][templates-link]을 참조하세요.<br>
<br>
이제 Auth의 사용법을 알아봅시다.

---
## 패키지
여러분 프로젝트의 SPM 패키지 매니페스트 파일(Package.swift) 디펜던시 목록에 Auth를 추가하세요.<br>
```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
	name: "MyApp",
	dependencies: [
			/// 기타 디펜던시 ...
			
			/// 👤 Fluent를 위한 Authentication & Authorization 프레임워크
			.package(url: "https://github.com/vapor/auth.git", from: "2.0.0"),
	],
	targets: [
		.target(name: "App", dependencies: ["Authentication", ...]),
		.target(name: "Run", dependencies: ["App"]),
		.testTarget(name: "AppTests", dependencies: ["App"]),
	]
)
```
Auth는 현재 `Authentication` 모듈 하나만을 제공합니다. 추후에 더욱 향상된 인증 기능을 위해 `Authorization` 모듈이 따로 추가될 예정입니다.

---
## 제공자
Auth 패키지를 여러분의 프로젝트에 성공적으로 추가했다면 이제 설정하는 일만 남았습니다. [configure.swift][configure.swift-link]에서 설정하세요.  
```swift
import Authentication

// Authentication 제공자를 등록합니다
try services.register(AuthenticationProvider())
```
기본적인 셋업이 완료되었습니다. 이 다음 단계에서는 인증 가능한 모델을 만드는 법을 다룹니다.

---
[vapor/auth-link]: <https://github.com/vapor/auth>
[fluent-link]: </doc/Fluent/GettingStarted>
[templates-link]: </doc/GettingStarted/Toolbox#템플릿>
[configure.swift-link]: </doc/GettingStarted/FolderStructure#configureswift>