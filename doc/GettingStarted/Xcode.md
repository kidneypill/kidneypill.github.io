---
title: Xcode
layout: default
nav_order: 3
parent: Getting Started
---

## Xcode
Mac에서 작업하고 있다면 Xcode로 Vapor 프로젝트를 진행할 수 있습니다. 빌드, 구동, 중지, 브레이크포인트 설정, 디버깅까지 모두 가능합니다.  
![](http://)
Xcode는 Vapor 프로젝트를 진행하는 훌륭한 방법 중 하나입니다. 물론 원하는 텍스트 에디터를 쓰는 것도 좋습니다.

---
## 프로젝트 생성하기
Vapor toolbox를 사용해서 프로젝트를 생성할 수 있습니다.
```
# Vapor xcode 프로젝트를 생성합니다.
vapor xcode
```

---
## 구동
Vapor 앱을 빌드하고 구동하려면, schemes menu에서 Run을 선택했는지 확인하세요. Device는 My Mac을 선택해야 합니다.  
실행 버튼 또는 ```Command +  R```을 눌러서 프로젝트를 구동하세요.

---
## 테스트
유닛을 테스트하고 싶다면 ```-Package```로 끝나는 scheme을 선택한 뒤 ```Command + U```를 눌러 테스트할 수 있습니다.  
⚠️그 밑에 여러 관계없는 Scheme이 있을 수 있습니다. 무시하세요!  

```
Test Suite 'All tests' started at 2019-03-05 01:17:23.523
Test Suite 'AppTests.xctest' started at 2019-03-05 01:17:23.524
Test Suite 'AppTests' started at 2019-03-05 01:17:23.524
Test Case '-[AppTests.AppTests testNothing]' started.
Test Case '-[AppTests.AppTests testNothing]' passed (0.100 seconds).
Test Suite 'AppTests' passed at 2019-03-05 01:17:23.626.
	 Executed 1 test, with 0 failures (0 unexpected) in 0.100 (0.101) seconds
Test Suite 'AppTests.xctest' passed at 2019-03-05 01:17:23.628.
	 Executed 1 test, with 0 failures (0 unexpected) in 0.100 (0.104) seconds
Test Suite 'All tests' passed at 2019-03-05 01:17:23.629.
	 Executed 1 test, with 0 failures (0 unexpected) in 0.100 (0.106) seconds
Program ended with exit code: 0
```
테스트를 실행하면 터미널에 위와 비슷한 메시지가 출력되는 것을 볼 수 있습니다.

---