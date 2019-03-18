---
title: Getting Started
layout: default
parent: Async
nav_order: 0
---

## Async 시작하기

Async 모듈은 Vapor Core ([vapor/core][vapor-core-link])의 일부로써 제공됩니다. [SwiftNIO][swift-nio-link]를 기반으로 제작된 편리한 API 모음입니다.  

📖SwiftNIO의 Async 자료형 (`Future`, `Promise`, `EventLoop`, 등)에 대한 자세한 정보는 깃허브 [README]나 [API 문서]에서 볼 수 있습니다.

---
## 사용법
이 패키지는 Vapor에 탑재되어 있습니다. Vapor만 임포트해도 모든 Async API를 사용할 수 있습니다.
```swift
import Vapor // 'import Async'를 포함합니다.
```

---
## 독립형 (Standalone)
Vapor Core 패키지의 일부분인 Async 모듈만을 따로 사용할 수도 있습니다.  

패키지에 Async 모듈을 포함시키려면 `Package.swift` 파일을 열고 다음과 같이 편집하세요.  
```swift
// swift-tools-version:4.0
import PackageDescription

let package = Package(
    name: "Project",
    dependencies: [
        ...
        .package(url: "https://github.com/vapor/core.git", from: "3.0.0"),
    ],
    targets: [
      .target(name: "Project", dependencies: ["Async", ... ])
    ]
)
```

이제 API에 접근하려면 `import Async`를 명시하세요.

---
## 개요
[Async → Overview][async-overview-link]

---

[swift-nio-link]: <https://github.com/apple/swift-nio>
[vapor-core-link]: <https://github.com/vapor/core>
[readme-link]: <https://github.com/apple/swift-nio/blob/master/README.md>
[api-docs-link]: <https://apple.github.io/swift-nio/docs/current/NIO/index.html>
[async-overview-link]: </doc/Async/Overview>