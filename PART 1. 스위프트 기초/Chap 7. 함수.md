# 함수

스위프트에서의 함수는 다양한 모습으로 존재할 수 있기 때문에, 개인이나 협업자들끼리 규칙을 만들고 함수를 사용해야 합니다.

<br><br><br>

# 함수와 메서드

함수와 메서드는 기본적으로 같으나, 상황에 따라 다른 용어로 부르는 것일 뿐입니다. 구조체, 클래스, 열거형 등 특정 타입에 연관되어 사용하는 함수를 메서드, 모듈 전체에서 전역적으로 사용할 수 있는 함수를 함수라고 부릅니다.

<br><br><br>

# 함수의 정의와 호출

함수에서는 소괄호(())를 생략할 수 없고, 재정의(오버라이드)와 중복 정의(오버로드)를 모두 사용할 수 있습니다.

<br><br>

## 기본적인 함수의 정의와 호출

함수는 `func` 키워드를 사용하고, 매개변수는 소괄호로 감싸주며, `→`을 통해 반환 타입을 명시해줄 수 있습니다.

```
func 함수 이름(매개변수...) -> 반환 타입 {
	실행 구문
	return 반환 값
}

func introduce(name:String) -> String {
	// [return "제 이름은 " + "name" + "입니다"]와 같은 동작을 합니다.
	"제 이름은 " + name + "입니다"
}
```

함수 내부의 코드가 한 줄의 표현이고, 그 표현의 결괏값의 타입이 함수의 반환 타입과 일치한다면 return 키워드를 생략할 수 있습니다.

<br><br>

## 매개변수
매개변수(parameter)는 **함수를 정의할 때 외부로부터 받아들이는 전달 값의 이름**을 의미합니다. 전달인자(Argument) 혹은 인자는 함수를 실제로 호출할 때 전달하는 값을 의미합니다. 예를 들어 hello(name:) 함수에서 매개변수는 name, 전달인자는 실제 사용 시 전달 받는 “Ted” 등이 될 것입니다.

<br>

### 매개변수 이름과 전달인자 레이블

스위프트에서는 매개변수 이름과 함께 전달인자 레이블을 지정해줄 수 있습니다.
```
func sayHello(from myName: String, to name:String) -> String {
	return "Hello \(name)! I'm \(myName)"
}

print(sayHello(from: "Ted", to: "Swift"))    // Hello Swift! I'm Ted
```

<br>

만약 전달인자 레이블을 사용하고 싶지 않다면 `와일드카드 식별자(_)`를 사용할 수 있습니다.
```
func sayHello(_ name: String, _ times: Int) -> String {
	var result: String = ""
	
	for _ in 0..<times {
		result += "Hello \(name)!" + " "
	}
	
	return result
}

print(sayHello("Ted", 2))    // Hello Ted! Hello Ted!
```

<br>

전달인자 레이블을 변경하면 함수의 이름 자체가 변경되기 때문에, 오버로드로 동작합니다.
```
func sayHello(to name: String, _ times: Int) -> String {
	var result: String = ""
	
	for _ in 0..<times {
		result += "Hello \(name)!" + " "
	}
	
	return result
}

func sayHello(to name: String, repeatCount times: Int) -> String {
	var result: String = ""
	
	for _ in 0..<times {
		result += "Hello \(name)!" + " "
	}
	
	return result
}


print(sayHello(to: "Ted", 2))
print(sayHello(to: "Ted", repeatCount: 2))
```

<br>

### 매개변수 기본값

스위프트의 함수에서는 매개변수마다 기본값을 저장할 수 있습니다.
```
// times 매개변수가 기본값 3을 갖습니다
func sayHello(_ name: String, times: Int = 3) -> String {
	var result: String = ""
	
	for _ in 0..<times {
		result += "Hello \(name)! + " "
	}
	
	return result
}


// times 매개변수의 전달 값을 넘겨주지 않아 기본값 3을 반영해서 3번 출력합니다
print(sayHello("Default"))
// Hello Default! Hello Default! Hello Default!


// 매개변수 전달 값을 지정했기 때문에 전달 값을 반영하여 출력합니다.
print(sayHello("Custom", times: 2))
// Hello Custom! Hello Custom!
```

<br>

### 가변 매개변수와 입출력 매개변수

매개변수로 몇 개의 값이 들어올지 모를 때, 가변 매개변수를 사용할 수 있습니다. 가변 매개변수는 0개 이상의 값을 받아올 수 있으며, 가변 매개변수로 들어온 인자 값은 배열처럼 사용할 수 있습니다. 함수마다 가변 매개변수는 하나만 가질 수 있습니다.

```
func sayHelloToFriends(me: String, friends names: String...) -> String {
	var result: String = ""
	
	for friend in names {
		result += "Hi \(friend)!" + " "
	}
	
	result += "I'm" + me + "!"
	
	return result
}

print(sayHelloToFriends(me: "Ted", friends: "One", "Two", "Three"))
// Hi One! Hi Two! Hi Three! I'm Ted!

print(sayHelloToFriends(me: "Ted"))
// I'm Ted
```

함수의 전달인자로 값을 전달할 때는 **보통 값을 복사해서 전달**합니다. 값이 아닌 참조를 전달하려면 입출력 매개변수를 사용합니다. 하지만 이 방법은 함수 외부의 값에 어떤 영향을 줄지 모르기 때문에 함수형 프로그래밍 패러다임에서는 지양하는 패턴입니다. 애플의 프레임워크에서는 객체지향 프로그래밍 패러다임을 사용하므로 유용할 수 있지만, 애플 프레임워크를 벗어난 다른 환경에서 함수형 프로그래밍 패러다임을 사용할 때는 입출력 매개변수를 사용하지 않는 것이 좋습니다.

<br>

입출력 매개변수의 전달 순서는 다음과 같습니다.

1. 함수를 호출할 때, 전달인자의 값을 복사합니다.
2. 해당 전달인자의 값을 변경하며 1에서 복사한 것을 함수 내부에서 변경합니다.
3. 함수를 반환하는 시점에 2에서 변경된 값을 원래의 매개변수에 할당합니다.

참조는 inout 매개변수로 전달될 변수 또는 상수 앞에 앰퍼샌드(&)를 붙여 표현합니다.
```
var nubmers: [Int] = [1, 2, 3]

func nonReferenceParameter(_ arr: [Int]) {
	var copiedArr: [Int] = arr
	copiedArr[1] = 1
}

func referenceParameter(_ arr: inout [Int]) {
	arr[1] = 1
}

nonReferenceParameter(numbers)
print(numbers[1])    // 2

referenceParameter(&numbers)
print(numbers[1])    // 1
```
입출력 매개변수는 잘 사용하면 문제는 없지만 잘못 사용하면 메모리 안전을 위협하기 때문에 제약이 존재합니다.

<br>

<details>
<summary>함수는 참조 타입이지만 값을 복사한다?</summary>
<div markdown="1">
<br>

# 함수는 참조 타입이지만 값을 복사한다?

함수는 참조 타입이라고 하지만, 동작 방식은 값을 복사한다고 하여 이에 대해 학습해보았습니다.

스위프트에서 함수나 클로저는 변수나 상수에 할당할 수 있으며, 이를 다름 함수에 매개변수로 전달할 수도 있습니다. 이때 **함수 자체는 참조 타입**이지만, **전달 방식은 값 타입처럼 동작**합니다. 즉, **함수가 ‘값 타입처럼 동작’하는 이유는 전달될 때 참조가 복사**되기 때문입니다.

예시를 통해 확인해보겠습니다.
```
var numbers: [Int] = [1, 2, 3]    // numbers -> (0x1111) 메모리 주소에서 [1, 2, 3]을 가리킴

func nonReferenceParameter(_ arr: [Int]) {
    var copiedArr: [Int] = arr    // COW 최적화로 여전히 0x1111을 가리킴
    copiedArr[1] = 1    // 이 시점에서 새로운 메모리(0x1115)를 할당하고 [1, 1, 3]을 저장
}

func referenceParameter(_ arr: inout [Int]) {
    arr[1] = 1    // 원본(0x1111)의 값을 직접 수정
}

nonReferenceParameter(numbers)    // numbers는 여전히 (0x1111)이고, 복사본이 변경되었을 뿐
print(numbers[1])    // 2 (원본은 변경되지 않음)

referenceParameter(&numbers)    // numbers(0x1111)의 두 번째 요소를 직접 변경
print(numbers[1])    // 1 (원본이 변경됨)

```

참조 값을 복사한 `nonReferenceParameter` 함수는 함수 내부에서 복사된 배열을 수정한 것이고, 함수를 호출할 때에는 numbers라는 복사되지 않은 값을 매개변수로 갖기 때문에 때문에 2라는 결과가 반환됩니다. 하지만 직접 참조하는 referenceParameter 함수는 **원본 배열을 직접 참조**하여 수정했기 때문에 결과 또한 변경되었습니다.

</div>
</details>

<br>

### 데이터 타입으로서의 함수

스위프트의 함수는 일급 객체이기 때문에, 하나의 데이터 타입으로 사용할 수 있습니다.
```
typealias CalculateTwoInts = (Int, Int) -> Int

func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
	return a * b
}

var mathFunctions: CalculateTwoInts = multuplyTwoInts
print(mathFunctions(2, 3))    // 6
```

<br><br><br>

# 종료되지 않는 함수
스위프트에는 종료(return)되지 않는 함수가 있습니다. 종료가 되지 않는다는 의미는 정상적으로 끝나지 않는 함수를 의미하며, 비반환 함수 또는 비반환 메서드라고 부릅니다. `Never`라는 키워드를 사용하고, 이 함수를 실행하면 프로세스를 종료해버립니다. `fatalError` 함수가 대표적인 Never 타입입니다.

```
func crashAndBurn() -> Never {
	fatalError("Something very bad happened")
}

func someFunction(isAllIsWell: Bool) {
	guard isAllIsWell else {
		print("마을에 도둑이 들었습니다!")
		crashAndBurn()
	}
	print("All is well")
}

someFunction(isAllIsWell: true)    // All is well
someFunction(isAllIsWell: false)   // 마을에 도둑이 들었습니다!
																	 // 프로세스 종료 후 오류 보고
```

<br><br><br>

# 반환 값을 무시할 수 있는 함수

가끔 함수의 반환 값이 꼭 필요하지 않은 경우가 있는데, 이 경우 컴파일러가 함수의 결과 값을 사용하지 않았다는 경고를 보낼 때도 있습니다. 이런 경우 함수의 반환 값을 무시해도 된다는 `@discardableResult` 선언 속성을 사용하면 됩니다.

```
@discardableResult func discardableResultSay(_ something: String) -> String {
	print(something)
	return something
}

// 반환 값을 사용하지 않아도 컴파일러가 경고하지 않습니다.
discardableResultSay("hello")
```