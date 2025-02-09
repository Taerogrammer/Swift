# 클로저

함수형 프로그래밍을 이해하기 위해선 **클로저**를 반드시 알아야 합니다. 클로저를 잘 이해해야 함수형 프로그래밍 패러다임을 더욱 잘 이해할 수 있습니다. 클로저는 일정 기능을 하는 코드를 하나의 블록으로 모아놓은 것을 의미하며, C언어의 블록, 다른 프로그래밍 언어의 람다와 유사합니다. 함수도 클로저의 일부입니다.

클로저의 장점으로는 **간단하게 표현**할 수 있다는 점이 있지만, 표현 방법이 매우 다양하여 다른 사람의 코드를 이해하는 데에 어려움을 겪을 수도 있습니다.

클로저는 변수나 상수가 선언된 위치에서 **참조를 획득하고 저장**할 수 있습니다. 이를 변수나 상수의 클로징(잠금)이라고 하며 클로저는 여기서 착안된 이름입니다. 클로저는 세 가지 형태가 있습니다.

- 이름이 있으면서 어떤 값도 획득하지 않는 전역함수의 형태
- 이름이 있으면서 다른 함수 내부의 값을 획득할 수 있는 중첩된 함수의 형태
- 이름이 없고 주변 문맥에 따라 값을 획득할 수 있는 축약 문법으로 작성한 형태

클로저는 매우 다양하게 표현할 수 있습니다.

- 클로저는 매개변수와 반환 값의 타입을 문맥을 통해 유추할 수 있기 때문에 매개변수와 반환 값의 타입을 생략할 수 있습니다.
- 클로저에 단 한 줄의 표현만 들어있다면 암시적으로 이를 반환 값으로 취급합니다.
- 축약된 전달인자 이름을 사용할 수 있습니다.
- 후행 클로저 문법을 사용할 수 있습니다.

<br>

클로저 표현 방법은 클로저가 함수의 모습이 아닌 **하나의 블록의 모습으로 표현될 수 있는 방법**을 의미합니다. 클로저 표현 방법은 클로저의 위치를 기준으로 기본 클로저 표현과 후행 클로저 표현이 있습니다.

<br><br><br>

## 기본 클로저

스위프트 표준 라이브러리에 배열을 정렬하기 위해 구현된 `sorted(by :)` 메서드가 있습니다.
```
public func sorted(by areInIncreaseingOrder: (Element, Element) -> Bool) -> [Element]
```

이를 기반으로 기존에 익숙한 방법대로 매개변수로 String 타입 두 개를 가지며, Bool 타입을 반환하는 함수를 구현해보겠습니다. 구현된 함수를 sorted(by:) 메서드의 전달인자로 전달하여 reversed라는 이름의 배열로 반환받습니다.
```
func backwards(first: String, second: String) -> Bool {
	print("\(first) \(second) 비교중")
	return first > second
}

let reversed: [String] = names.sorted(by: backwards)
```

만약 first 문자열이 second 문자열보다 크다면 backwards(first:second:) 메서드의 반환 값은 true가 될 것입니다. 그러나 first > second라는 반환 값을 받기 위해 너무 많은 표현을 사용했습니다. 클로저는 이러한 표현은 간단하게 만들어줍니다.

<br>

클로저 표현은 통상 아래 형식을 따릅니다.
```
{ (매개변수들) -> 반환 타입 in
	실행 코드
}
```

이를 기반으로 backwards(first:second:) 함수를 클로저 표현으로 대체해보겠습니다.
```
// backwards 함수 대신에 sorted(by:) 메서드의 전달인자로 클로저를 직접 전달합니다.
let reversed: [String] = names.sorted (by: { (first: String, second: String) -> Bool in
	return first > second
})
```

위처럼 쓰게 되면 직관적으로 확인할 수 있지만, 반복해서 같은 기능을 사용한다면 함수로 구현해두는 것도 방법입니다.

<br><br><br>

## 후행 클로저

클로저가 조금 길어지거나 가독성이 조금 떨어진다면 후행 클로저 기능을 사용하면 좋습니다. 단, 후행 클로저는 맨 마지막 전달인자로 전달되는 클로저에만 해당되므로 전달인자로 클로저 여러 개를 전달할 때는 맨 마지막 클로저만 후행 클로저를 사용할 수 있습니다. 또한 단 하나의 클로저만 전달인자로 전달하는 경우에는 소괄호를 생략해줄 수도 있습니다.

```
// 후행 클로저의 사용
let reversed: [String] = names.sorted() { (first: String, second: String) -> Bool in
	return first > second
}

// sorted(by:) 메서드의 소괄호까지 생략 가능합니다. (하나의 클로저만 전달인자로 전달하는 경우)
let reversed: [String] = names.sorted { (first: String, second: String) -> Bool in
	return first > second
}

func doSomething(do: (String) -> Void,
								 onSuccess: (Any) -> Void,
								 onFailure: (Error) -> Void) {
	// do something..
}

// 다중 후행 클로저의 사용
doSomething { (someString: String) in
	// do closure
} onSuccess: { (result: Any) in
	// success closure
} onFailure: { (error: Error) in
	// failure closure
}
```

<br><br><br>

## 클로저 표현 간소화

### 문맥을 이용한 타입 유추

전달인자로 전달할 클로저는 이미 적합한 타입을 준수하고 있다고 유추할 수 있기 때문에, 매개변수의 타입이나 반환 값의 타입을 굳이 표현해주지 않고 생략하더라도 문제가 없습니다.

```
// 클로저의 매개변수 타입과 반환 타입을 생략하여 표현할 수 있습니다.
let reversed: [String] = names.sorted { (first, second) in
	return first > second
}
```

<br>

### 단축 인자 이름

스위프트는 단축 인자 이름을 통해 첫 번째 전달인자부터 $0, $1, $2, … 순서대로 표현할 수 있습니다. 단축 인자 표현을 사용하게 되면 매개변수 및 반환 타입과 실행 코드를 구분하기 위해 사용했던 키워드 `in`을 사용할 필요도 없어집니다.

```
// 단축 인자 이름을 사용한 표현
let reversed: [String] = names.sorted {
	return $0 > $1
}
```

<br>

### 암시적 반환 표현

클로저에서는 **반환 값을 갖는 클로저**이고 **클로저 내부의 실행문이 단 한 줄**이라면, 암시적으로 그 실행문을 반환 값으로 사용하여 return 키워드를 생략할 수 있습니다.
```
// 암시적 반환 표현
let reversed: [String] = names.sorted { $0 > $1 }
```

<br>

### 연산자 함수

클로저의 장점은 **간단한 표현**입니다. 클로저는 **매개변수의 타입과 반환 타입이 연산자를 구현한 함수의 모양과 동일**하다면 연산자만 표기하더라도 알아서 연산하고 반환합니다. 스위프트 라이브러리의 비교 연산자의 정의를 보면 다음과 같습니다.
```
public func ><T: Comparable>(lhs: T, rhs: T) -> Bool
```

‘>’가 함수의 이름으로 정의되어 있습니다. 이를 클로저를 이용하여 표현해보겠습니다.
```
// 연산자 함수를 클로저의 역할로 사용
let reversed: [String] = names.sorted(by: >)
```

<br><br><br>

## 값 획득

클로저는 주변 문맥을 통해 상수나 변수를 **획득(Capture)** 할 수 있습니다. 클로저는 값 획득을 통해 주변에 상수나 변수가 존재하지 않더라도 자신 내부에서 참조하거나 수정할 수 있습니다. 이러한 이야기는 클로저가 **비동기 작업**에 많이 사용되기 때문에 알아두어야 합니다. 클로저를 통해 **비동기 콜백**을 작성하는 경우, 현재 상태를 미리 획득해두지 않으면, 실제로 클로저의 기능을 실행하는 순간에는 주변의 상수나 변수가 이미 메모리에 존재하지 않는 경우가 발생합니다.

```
func makeIncrementer(forIncrement amount: Int) -> (() -> Int) {
	var runningTotal = 0
	func incrementer() -> Int {
		runningTotal += amount
		return runningTotal
	}
	return incrementer
}
```

makeIncrementer의 반환 타입은 `() → Int`로, **함수객체를 반환**한다는 의미를 가집니다. 반환하는 함수는 매개변수를 받지 않고 반환 타입은 Int인 함수로, 호출할 때마다 Int 타입의 값을 반환해줍니다. incrementer가 반환하게 될 값을 저장하는 용도로 runningTotal을 정의했고, incrementer()함수가 호출될 때마다 amont의 값만큼 runningTotal 값을 증가시켜줍니다.

위 식에서 incrementer()는 amount와 runningTotal **두 변수에 대해 참조를 획득**할 수 있습니다. 참조를 획득하면 runningTotal과 amount는 makeIncrementer 함수의 실행이 끝나도 사라지지 않으며, incrementer가 호출될 때마다 계속해서 사용할 수 있습니다.

```
let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)
let incrementByTwo2: (() -> Int) = makeIncrementer(forIncrement: 2)

let first: Int = incrementByTwo()    // 2
let second: Int = incrementByTwo()   // 4
let third: Int = incrementByTwo()    // 6

let first2: Int = incrementByTwo2()    // 2
let second2: Int = incrementByTwo2()   // 4
let third2: Int = incrementByTwo2()    // 6
```

각각의 increment 함수는 자신만의 runningTotal 변수를 참조했기 때문에 자신의 값에 대해서만 카운트하며, 다른 함수의 영향도 받지 않습니다.

<br><br><br>

## 클로저는 참조 타입

`함수와 클로저는 참조 타입`이기 때문에 값 획득을 통해 runningTotal 변수를 계속해서 증가시킬 수 있습니다. 즉, 클로저를 할당한다는 것은 값을 할당하는 것이 아니라 **해당 클로저의 참조를 할당**하는 것입니다. 따라서 클로저의 참조를 다른 상수에 
할당해준다면 두 상수가 같은 클로저를 가리킵니다.

```
let incrementByTwo: (() -> Int) = makeIncrementer(forIncrement: 2)
let sameIncrementByTwo: (() -> Int) = incrementByTwo

let first: Int = incrementByTwo()      // 2
let second: Int = sameIncrementByTwo() // 4
```

<br><br><br>

## 탈출 클로저

함수의 전달인자로 전달한 클로저가 함수 종료 후에 호출될 때 클로저가 함수를 **탈출**한다고 표현합니다. **클로저를 매개변수로 갖는 함수를 선언**할 때 매개변수 이름의 콜론(:) 뒤에 `@escaping` 키워드를 사용하여 클로저가 탈출하는 것을 허용한다고 명시해줄 수 있습니다.

예를 들어 비동기 작업을 실행하는 함수들은 클로저를 컴플리션 핸들러 전달인자로 받아옵니다. 비동기 작업으로 함수가 종료되고 난 후 호출할 필요가 있는 클로저를 사용해야 할 때 **탈출 클로저**가 필요합니다. @escaping 키워드를 따로 명시하지 않는다면 기본적으로 비탈출 클로저입니다. **함수로 전달된 클로저가 함수의 동작이 끝난 후 사용할 필요가 없을** 때 비탈출 클로저를 사용합니다.

클로저가 함수를 탈출할 수 있는 경우 중 하나는 함수 외부에 정의된 변수나 상수에 저장되어 함수가 종료된 후에 사용할 경우입니다. 예를 들어 비동기로 작업을 하기 위해서 컴플리션 핸들러를 전달인자를 이용해 클로저 형태로 받는 함수들이 많습니다. 함수가 작업을 종료하고 난 이후에 컴플리션 핸들러, 즉 클로저를 호출하기 때문에 클로저는 함수를 탈출해 있어야만 합니다. 함수의 전달인자로 전달받은 클로저를 다시 반환할 때도 마찬가지 입니다.

```
typealias VoidVoidClosure = () -> Void
let firstClosure: VoidVoidClosure = {
	print("Closure A")
}
let secondClosure: VoidVoidClosure = {
	print("Closure B")
}

// first와 second 매개변수 클로저는 함수의 반환 값으로 사용될 수 있으므로 탈출 클로저입니다.
func returnOneClosure(first: @escaping VoidVoidClosure, second: @escaping VoidVoidClosure,
											shouldReturnFirstClosure: Bool) -> VoidVoidClosure {
	// 전달인자로 전달받은 클로저를 함수 외부로 다시 반환하기 때문에 함수를 탈출하는 클로저입니다.
	return shouldReturnFirstClosure ? first : second
}

// 함수에서 반환한 클로저가 함수 외부의 상수에 저장되었습니다.
let returnedClosure: VoidVoidClosure = returnOneClosure(first: firstClosure, second: secondClosure,
																												shouldReturnFirstClosure: true)
																												
returnClosure()    // Closure A

var closures: [VoidVoidClosure] = []

// closure 매개변수 클로저는 함수 외부의 변수에 저장될 수 있으므로 탈출 클로저입니다.
func appendClosure(closure: @escaping VoidVoidClosure) {
	// 전달인자로 전달받은 클로저가 함수 외부의 변수 내부에 저장되므로 함수를 탈출합니다.
	closures.append(closure)
}
```

두 함수의 전달인자로 전달하는 클로저 앞에 @escaping 키워드를 사용하여 탈출 클로저임을 명시해주어야 합니다. 이 코드는 클로저 모두가 탈출할 수 있는 조건이 명확하기 때문에 @escaping 키워드를 사용하여 탈출 클로저임을 명시하지 않으면 컴파일 오류가 발생합니다.

<br>

타입 내부 메서드의 매개변수 클로저에 @escaping 키워드를 사용하여 탈출 클로저임을 명시한 경우, 클로저 내부에서 해당 타입의 프로퍼티나 메서드, 서브스크립트 등에 접근하려면 `self` 키워드를 명시적으로 사용해야 합니다. 비탈출 클로저는 self 키워드를 꼭 써주지 않아도 됩니다(선택 사항입니다).

```
typealias VoidVoidClosure = () -> Void

func functionWithNoescapeClosure(closure: VoidVoidClosure) {
	closure()
}

func functionWithEscapingClosure(completionHandler: @escaping
	VoidVoidClosure) -> VoidVoidClosure {
		return completionHandler
}

class SomeClass {
	var x = 10
	
	func runNoescapeClosure() {
		// 비탈출 클로저에서 self 키워드 사용은 선택 사항입니다.
		functionWithNoescapeClosure { x = 200 }
	}
	
	func runEscapingClosure() -> VoidVoidClosure {
		// 탈출 클로저에서는 명시적으로 self를 사용해야 합니다.
		return functionWithEscapingClosure { self.x = 100 }
	}
	
	let instance: SomeClass = SomeClass()
	instance.runNoescapeClosure()
	print(instance.x)    // 200
	
	let returnedClosure: VoidVoidClosure = instance.runEscapingClosure()
	returnedClosure()
	print(instance.x)    // 100
}
```

<br><br><br>

## 자동 클로저

**함수의 전달인자로 전달하는 표현을 자동으로 변환해주는 클로저**를 자동 클로저라고 합니다. 자동 클로저는 **전달인자를 갖지 않습니다.** 자동 클로저는 호출되었을 때 **자신이 감싸고 있는 코드의 결괏값을 반환**합니다. 자동 클로저는 함수로 전달하는 클로저를 (소괄호와 중괄호를 겹쳐서 써야 하는) 어려운 클로저 문법을 사용하지 않고도 클로저로 사용할 수 있도록 문법적 편의를 제공합니다.

자동 클로저는 **클로저가 호출되기 전까지 클로저 내부의 코드가 동작하지 않습**니다. 따라서 **연산을 지연**시킬 수 있는데, 이 과정은 연산에 자원을 많이 소모한다거나 부작용이 우려될 때 유용하게 사용할 수 있습니다. 코드의 실행을 제어하기 좋기 때문입니다.

```
var customersInLine: [String] = ["a", "b", "c", "d"]
print(customersInLine.count)    // 4

// 클로저를 만들어두면 클로저 내부의 코드를 미리 실행하지 않고 가지고만 있습니다.
let customerProvider: () -> String {
	return customersInLine.removeFirst()
}
print(customersInLine.count)    // 4

// 실제로 실행합니다.
print("Now Serving \(customerProvider)")    // Now Serving a
print(customersInLine.count)    // 3
```

다음은 자동 클로저를 사용한 예시입니다.
```
var customersInLine: [String] = ["a", "b", "c", "d"]

func serveCustomer(_ customerProvider: @autoclosure () -> String) {
	print("Now serving \(customerProvider())")
}

serveCustomer(customersInLine.removeFirst())    // Now serving a
```

자동 클로저 속성을 부여한 매개변수는 클로저 대신에 customersInLine.removeFirst() 코드의 **실행 결과인 String 타입의 문자열을 전달인자**로 받게 됩니다. String 타입의 값이 자동 클로저 매개변수에 전달되면 String 값을 **매개변수가 없는 String 값을 반환하는 클로저로 반환**해줍니다.

기본적으로 `@autoclosure` 속성은 기본적으로 `@noescape` 속성을 포함합니다. 만약 `@escaping` 속성을 사용하고 싶다면 `@autoclosure @escaping`처럼 사용할 수 있습니다.

<br>

클로저는 생략할 수 있는 부분들도 많아 다양한 표현 방법이 있을 수 있습니다. 하지만 스위프트의 매우 강력한 기능이기 때문에 숙지하고 있어야 합니다.