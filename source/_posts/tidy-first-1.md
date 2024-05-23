---
title: Tidy first? (1)
date: 2024-05-23 13:55:54
tags: TidyFirst
---

### Chapter 1. 보호구문 (Guard Clause)
---
특정 조건이 만족되지 않을 때 함수 실행을 조기에 종료하여, 코드의 가독성을 높이고 불필요한 중첩을 방지하는 패턴이다. 보호구문은 일반적으로 함수의 시작 부분에 위치하여 조건을 검사하고, 조건이 만족되지 않으면 함수를 반환한다. 이를 통해 주요 로직이 단순해지고 명확해진다.
(코드가 가로로 길어지는 스파게티를 막을 수 있다.)

직전 회사에서 코드리뷰 시에 관련된 지적을 많이 받은 기억이 난다. 
```kotlin
    if (조건)
        if (다른 조건)
            .. 코드 ..
```
위와 같은 형태는 아래와 같이 변경한다. 원하는 동작이 모든 조건을 통과하면 가장 안쪽의 조건문에 로직이 위치할 수 밖에 없다. 조건에 부응하지 않으면 그대로 return 하는 것과 같이 명시적으로 코드를 작성한다.

```kotlin
    if (조건 부정)
        return
    if (다른 조건 부정)
        return
```

아래는 로그인 시 사용자 검증하는 예제에 적용한 내용이다.
```kotlin
// bad case
fun loginBadCase(username: String?, password: String?) {
    if (username != null) {
        if (password != null) {
            if (username.isEmpty() && password.isEmpty()) {
                if (isValidUser(username, password)) {
                    println("Login successful")
                } else {
                    println("Invalid username or password")
                }
            } else {
                println("Username or password is empty")
            }
        } else {
            println("Password is null")
        }
    } else {
        println("Username is null")
    }
}

fun loginGoodCase(username: String?, password: String?) {
    if (username == null) {
        println("Username is null")
        return
    }
    
    if (password == null) {
        println("Password is null")
        return
    }
    
    if (username.isEmpty() || password.isEmpty()) {
        println("Username or password is empty")
        return
    }
    
    if (isValidUser(username, password) == false) {
        println("Invalid username or password")
        return
    }
    
    println("Login Successful")
} 

fun isValidUser(username: String, password: String): Boolean {
    // 사용자 검증 로직
    return username == "user" && password == "password"
}

```

위의 `loginGoodCase()` 는 마치 "코드의 세부 사항을 살펴보기 전에 염두에 두어야 할 몇 가지 전제 조건이 있습니다." 라고 말하는 것처럼 보인다.
'로그인' 이라는 해당 메소드의 핵심 로직은 선행 된 조건을 모두 확인한 뒤 가장 마지막에 이루어진다.