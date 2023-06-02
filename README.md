# 계산기 - STEP 1

> 이번 스텝에서는 계산기를 만들기 위해 사용될 Queue 자료 구조를 사용하여 CalculatorItemQueue를 구현하고, CalculatorItemQueue에 대한 Unit Test를 진행했습니다.
>
> 진행 기간: 2023.05.29 ~ 2023.06.01

<br/>

## 목차 

1. [팀원](#팀원)
2. [타임 라인](#타임-라인)
3. [다이어그램](#다이어그램)
4. [트러블 슈팅](#트러블-슈팅)
5. [고민한 점](#고민한-점)
6. [참고 링크](#참고-링크)

<br/>

## 팀원

|                           myungsun                           |
| :----------------------------------------------------------: |
| <img src="https://avatars.githubusercontent.com/u/74762699?s=400&u=44a002eb9bfd2be6f192a6f994f9552d081060b8&v=4" width="250" height="250" style="border-radius: 50%;"/> |
|      [Github Profile](https://github.com/myungsun7782)       |

<br/>

## 타임 라인 

| 날짜       | 진행 사항                                                    |
| ---------- | ------------------------------------------------------------ |
| 2023-05-30 | - Controller, Model, View 디렉토리 생성 (chore) <br />- CalculateItem 프로토콜 추가  (feat) <br />- NumberItem 구조체 타입 구현 (feat) <br />- OperatorItem 열거형 구현 (feat) <br />- CalculatorItemQueue 클래스 구현 (feat) <br />- NumberItem 테스트 코드 추가 (test) <br />- OperatieItem 테스트 코드 추가 (test)  <br />- CalculatorItemQueue 테스트 코드 추가 (test) <br />- CalculatorItemQueue 클래스에 isEmpty 메서드 추가 (feat) |
| 2023-05-31 | - 해당 스텝과 관련 없는 코드 삭제 (refactor) <br />- CalculatorItemQueue 클래스 타입에서 구조체 타입으로 변경 (refactor) <br />- CaculatorItemQueue 재구현 (refactor) <br />- CalculatorItemQueue 구조체에 peek 연산 프로퍼티 추가 (feat) <br />- CalculatorItemQueue 테스트 코드 수정 (refactor) |

<br/>

## 다이어그램

![](https://github.com/myungsun7782/TIL/assets/74762699/2323cee8-6bba-436a-a9b4-d693efb1a536)

<br/>

## 트러블 슈팅

### Queue 구현 

#### 문제점 

- 처음에는 `Array` 1개로 `Queue`를 구현했습니다. 그런데, 이렇게 구현을 해보니 `Queue`에서 데이터를 `dequeue`할 때 시간 복잡도가 `O(N)`이 되는 문제가 생겼습니다. 처음에는 시간 복잡도가 `O(N)`이 된다 하더라도 큰 문제가 없을 줄 알았지만, 나중에 입력으로 들어오는 데이터가 많을 경우에 처리되는 시간이 더 걸림으로서 앱의 성능에 큰 문제가 될 수도 있겠다는 생각이 들었습니다.

```swift
struct CalculatorItemQueue {
    private var items: [CalculateItem] = []
}
```

#### 해결 방법 

- `Double Stack`을 사용해서 `Queue`를 구현했습니다. `Double Stack`으로 `Queue`를 구현하면서 얻을 수 있는 가장 큰 이점은 `dequeue`할 때 시간 복잡도를 줄일 수 있다는 것이었습니다. `dequeue`메서드는 `outStack`이 비어있을 경우, `inStack`의 모든 요소를 `outStack`으로 옮기는 작업이 필요하므로, 시간 복잡도가 `O(N)`이 되지만 이 작업은 `oustStack`이 비어 있는 경우에만 수행되므로, 각 요소는 한 번씩만 옮겨집니다. 따라서, `dequeue`의 평균 시간 복잡도는 `O(1)`이 됩니다.

```swift
struct CalculatorItemQueue<T>: CalculateItem {
    private var inStack: [T] = []
    private var outStack: [T] = []
}
```

### CalculatorItemQueue의 타입 

#### 문제점 

- 처음에는 `CalculatorItemQueue` 안에 들어가는 데이터의 타입을 `CalculateItem`으로 제한을 뒀습니다. 그런데, 이렇게 구현을 해보니 확장성이 떨어지고, 나중에 동일한 로직을 가진 메서드나 구조체를 다양한 데이터 타입에 대해 다시 작성해야 하는 문제가 발생할 수도 있겠다는 생각이 들었습니다. 

```swift
struct CalculatorItemQueue: CalculateItem {
    private var inStack: [CalculateItem] = []
    private var outStack: [CalculateItem] = []
    ...
}
```

#### 해결 방법

- `CalculatorItemQueue`를 제네릭 타입으로 지정해줘서 문제를 해결했습니다. 제네릭은 다양한 타입에 대해 동일한 로직을 처리하는 함수나 구조체를 한 번에 작성할 수 있는 장점이 있습니다. 이로써 코드의 중복을 줄이고 에러의 가능성을 줄일 수 있었습니다.

```swift
struct CalculatorItemQueue<T>: CalculateItem {
    private var inStack: [T] = []
    private var outStack: [T] = []
    ... 
}
```

<br/>

## 고민한 점 

### CalculatorItemQueue는 class/struct 중 어느 것으로 설정해야 할까?

처음에는 나중에 `Queue` 안에 있는 데이터가 다른 변수에 할당되더라도 참조 상태가 유지될 필요가 있을거 같기 때문에`CalculatorItemQueue`를 `class`로 설정하는 것이 맞다고 생각을 했습니다. 하지만, 들어온 데이터 요소를 순서대로 처리하고 관리하는  `Queue`의 역할을 다시 생각해보니깐 `Queue` 안에 있는 데이터의 참조 상태가 유지될 필요가 없다고 판단되어서 `struct`으로 설정했습니다.

<br/>

## 참고 링크

- [Swift Language Guide - Protocols](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)
- [Swift Language Guide - Extentions](https://docs.swift.org/swift-book/LanguageGuide/Extensions.html)
- [Swift Language Guide - Error Handling](https://docs.swift.org/swift-book/LanguageGuide/ErrorHandling.html)
- [Swift Language Guide - Generics](https://docs.swift.org/swift-book/documentation/the-swift-programming-language/generics/)
- [Apple Developer Documentation - removeAll](https://developer.apple.com/documentation/swift/array/removeall(keepingcapacity:)-1er5)
- [Apple Developer Documentation - popLast](https://developer.apple.com/documentation/swift/array/poplast())

