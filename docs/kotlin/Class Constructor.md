---
type: docs/kotlin/Class Constructor.md
keywords: hello world
created : 2021/06/03/20:25
tags: #kotlin
---

# Class Constructor

## 초기화 방법

### 주생성자

#### 간략한 형태

```kotlin
class Person(val name: String, val age: Int, val height: Int) 
```

#### init 사용

```kotlin
class Person(name: String, age: Int, height: Int) {
    val name: String
    val age: Int
    val height: Int
    init {
        this.name = name
        this.age = age
        this.height = height
    }
}
```

### 부생성자

```kotlin
class Person(val name: String) {

    var age: Int = 20
    var height: Int = 170

    constructor(name: String, age: Int) : this(name) {
        this.age = age
    }

    constructor(name: String, age: Int, height: Int) : this(name, age) {
        this.height = height
    }
}
```
