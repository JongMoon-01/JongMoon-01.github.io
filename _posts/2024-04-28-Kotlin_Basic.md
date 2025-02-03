---
title: "Kotlin 기초"
date: 2024-04-25 02:23:00 +0900
categories: [Kotlin]
tags: [Language, Java, Spring, Kotlin, Back-end]
---
# 1장. Hello, World

```kotlin
fun main() {
    println("Hello, World!")
}
```

- <code clas="code ">fun</code>은 Function 선언에 사용됩니다.
- <code clas="code ">main()</code> function은 플랫폼이 시작되는 곳입니다.
- Function의 Body는 중괄호 <code clas="code ">{}</code>안에 쓰여집니다.
- <code clas="code ">println</code>과 <code clas="code ">print()</code> function은 arguments를 출력하는 Standard Output function입니다.

### Variables
코틀린에서는 변수를 선언할때 자료형을 제외하고 선언해줘야하는게 하나 더 있습니다.
- read-only varables with <code clas="code ">val</code>
- mutable variables with <code clas="code ">var</code>

값을 할당할때는 지정연산자 <code clas="code ">=</code> 사용합니다.

```kotlin
val papcorn = 5
val hotdog = 7
var customers = 10

hotdog = 6 // X
customers = 8 // O
```

* Kotlin에서는 기본적으로 read-only 변수인 val을 사용하기를 권장합니다. <code clas="code ">"</code>를 이용해서 묶고, 변수는 <code clas="code ">$</code>뒤에 적어 출력합니다.

```kotlin
val customers = 10
println("There are $customers customers")
// There are 10 customers
println("There are ${customers + 1} customers")
// There are 11 customers
```

### String Templates
변수를 표준 출력하기 위해서 사용하는 string templates에 대해 소개합니다. 문자열 값은 


# 2장. Basic Types
코틀린은 기본적으로 tpye inference 기능을 가지고있어 타입을 명시하지 않더라도 컴파일러가 스스로 해당 변수에 선언되어있는 값을 참고해 무슨 타입인지 정합니다.
```kotlin
var customers = 10

customers = 8

customers = customers + 3
customers += 7
customers -= 3
customers *= 2
customers /= 3

println(customers)
```

|Category|Basic types|
|-------------|--------------|
|Intergers|<code clas="code ">Byte</code>, <code clas="code ">Short</code>, <code clas="code ">Int</code>, <code clas="code ">Long</code>|
|Unsigned integers|<code clas="code ">UByte</code>, <code clas="code ">UShort</code>, <code clas="code ">UInt</code>, <code clas="code ">ULong</code>|
|Floating-pint numbers|<code clas="code ">Float</code>, <code clas="code ">Double</code>|
|Booleans|<code clas="code ">Boolean</code>|
|Characters|<code clas="code ">Char</code>|
|String|<code clas="code ">String</code>|

##### For more information about Basic Types : https://kotlinlang.org/docs/basic-types.html

```kotlin
//Variable declared without init
val d: Int
//Variable inited
d = 3
//Variable explicitly typed and initialized
val e: String = "hello"

println(d)
println(e)
```

# 3장. Collections
코틀린에서도 리스트, 딕셔너리, 집합등의 여러 Collection Data Type을 지원해준다.

|Collection type|Description|
|---------------|-----------|
|Lists| Ordered Collection of items|
|Sets|Unique unordered collections of items|
|Maps|Sets of key-value paris where keys are unique and map to only one value|

각각의 Collection Type은 mutable 또는 read-only로 선언할 수 있다.

### List

- To create a read-only list (<code clas="code ">List</code>), use the <code clas="code ">listOf</code> function.

- To create a mutable list (<code clas="code ">MutableList</code>), use the <code clas="code ">mutableListOf</code> function.

* 기본적으로 List 선언시 컴파일러가 Type of item을 참고해 정해주지만, List 선언 후 <code clas="code "><></code>안에 명시해줄 수 있습니다.

```kotlin
//Read-only List
val readOnlyShapes = listOf("triangle", "square", "circle")
println(readOnlyShapes)

//Mutable list with explicit type declaration
val shapes: MutableList<String> = mutableListOf("triangle","square", "circle")
println(shapes)
```

캐스팅을 사용하여 원본 데이터의 원치않는 수정을 막을 수 있습니다.
```kotlin
val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
val shapesLocked: List<String> = shapes
```

List item에 접근할때는 인덱스 접근 연산자<code clas="code ">[]</code>를 사용합니다.

```kotlin
val readOnlyShapes = listOf("trianlge", "square", "circle")
println("The first item in the list is: ${readOnlyShapes[0]}")
```

또는 <code clas="code ">.first()</code>와 <code clas="code ">.last()</code> functions을 활용할 수 도 있습니다.

```kotlin
val readOnlyShapes = listOf("triangle", "square", "circle")
println("The first item in the list is ${readOnlyShapes.first()}")
```

* Python의 method라 불리는 기능을 Kotlin에서는 extension function이라고 지칭합니다. 똑같이 Object에 .을 붙인뒤 사용합니다.

List item 갯수에 대한 extension function

```kotlin
val readOnlyShapes = listOf("triangle", "square", "circle")
println("This list has ${readOnlyShapes.count()} items")
```

List안에 해당 item이 존재하는지 확인하는 연산자 <code clas="code ">in</code>

```kotlin
val readOnlyShapes = listOf("triangle", "square", "circle")
println("circle" in readOnlyShapes)
//true
```

mutable List에 <code clas="code ">.add()</code>와 <code clas="code ">.remove()</code> function 사용

```kotlin
val shapes: MutableList<String> = mutableListOf("trianlge", "square", "circle")
shapes.add("pentagon")
println(shapes)

shapes.remove("triangle")
println(shapes)
```

### Set

Set은 List와 다르게 Unordered하고 Unique한 Item을 담는 특성을 가지고 있습니다.

- To create a read-only set(<code clas="code ">Set</code>), use the <code clas="code ">setOf()</code> function.

- To create a mutable set (<code clas="code ">MutableSet</code>), use the <code clas="code ">mutableSetOf()</code> function.

List와 마찬가지로 set도 Type inference와 <code clas="code "><></code>안에 타입 명시가 가능합니다.

```kotlin
//Read-only set
val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
//Mutable set wit explicit type declaration
val fruit: MutableSet<String> = mutableSetOf("apple", "banana","cherry", "cherry")

println(readOnlyFruit)
// [apple, banana, cherry]
```

set도 캐스팅이 가능합니다.

* Set은 Unordered 특성을 가져 특정 인덱스 접근 방법은 불가능합니다.

Set안에 아이템 갯수를 세기위한 <code clas="code ">.count()</code> function

```Kotlin
val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
println("This is set has ${readOnlyFruit.count()} items")
```

set안에 item이 존재하는지 확인할때는 <code clas="code ">in</code>연산자를 사용합니다.

```Kotlin
val readOnlyFruit = setOf("apple", "banana", "cherry", "cherry")
println("banana" in readOnlyFruit)
//true
```

items을 mutable set에 추가하거나 삭제할때는 <code clas="code ">.add()</code>와 <code clas="code ">.remove()</code> extension을 사용합니다.

val fruit: MutableSet<String> = mutableSetOf("apple", "banana", "cherry", "cherry")
fruit.add("dragonfruit")
println(fruit)

fruit.remove("dragonfruit")
println(fruit)

### Map

Map은 dictionary 타입과 같이 key-value정보를 쌍으로 갖고 있습니다. key를 참조해서 value에 접근할 수 있습니다.

* Kotlin에서는 key가 unique한 특성을 가지고 value는 아무거나 지정가능합니다.
* Map 자료형안에서 value를 복제하는것도 가능합니다.

- To create a read-only map(<code clas="code ">Map</code>), use the <code clas="code ">mapOf()</code> function.

- To create a mutable map(<code clas="code ">MutableMap</code>), use the <code clas="code ">mutableMapOf</code> function.

Kotlin에서는 item을 만들때 컴파일러가 항목의 타입을 추정해서 저장합니다. 타입을 명시하기 위해서는 key와 value 값을 <code clas="code "><></code>안에 명시해주면 됩니다.

```Kotlin
val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
println(readOnlyJuiceMenu)
//{apple=100, kiwi=190, orange=100}

//Mutable map with explicit type declaration
val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
println(juiceMenu)
```

* Map도 캐스팅 가능합니다.

value에 접근하기 위해서는 <code clas="code ">[]</code>안에 쌍을 이루는 key를 적어 접근합니다.

```Kotlin
val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
println("The value of apple juice is : ${readOnlyJuiceMenu["apple"]})
// The value of apple juice is : 100
```

items을 mutable map에 추가하거나 삭제할때는 <code clas="code ">.put()</code>와 <code clas="code ">.remove()</code> extension을 사용합니다.

```Kotlin
val juiceMenu: MutableMap<String, Int> = mutableMapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
juiceMenu.put("coconut", 150)
println(juiceMenu)
// {apple=100, kiwi=190, orange=100, coconut=150}

juiceMenu.remove("orange")
println(juiceMenu)
// {apple=100, kiwi=190, coconut=150}
```

map안에 이미 key가 존재하는지 확인할 때는 <code clas="code ">.containskey()</code> extension를 사용합니다.
```Kotlin
val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
println(readOnlyJuiceMenu.containsKey("kiwi"))
//true
```

key와 value를 collection으로 추출할때는 <code clas="code ">keys</code>와 <code clas="code ">values</code> properties를 사용합니다.

```Kotlin
val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
println(readOnlyJuiceMenu.keys)
// [apple, kiwi, orange]
println(readOnlyJuiceMenu.values)
// [100, 190, 100]
```

map안에 key와 value에 대해 확인하고 싶을 때는 <code clas="code ">in</code>연산자 사용합니다.

```Kotlin
val readOnlyJuiceMenu = mapOf("apple" to 100, "kiwi" to 190, "orange" to 100)
println("orange" in readOnlyJuiceMenu.keys)
//true
println(200 in readOnlyJuiceMenu.values)
//false
```

# 4장. Control Flow

### Conditional expressions

#### if

```
val d: Int
val check = true

if(check) {
    d = 1
}
else {
    d = 2
}

println(d)
```

Kotlin에서는 ternary operator(<code clas="code ">condition ? then : else</code>)가 없는 대신 다른 표현법이 있습니다.

```
val a = 1
val b = 2

println(if(a > b) a else b) //Returns a value : 2
```

#### When

switch-case문과 비슷합니다.

```Kotlin
val obj = "Hello"

when (obj) {
    //Checks whether obj equals to "1"
    "1" -> println("One")
    //Checks whether obj equals to "Hello"
    "Hello" -> println("Greeting")
    //Default statement
    else -> println("Unknown")
}
```

또한 <code clas="code ">When</code>문법은 변수에 할당이 가능합니다.

```Kotlin
val obj = "Hello"    

val result = when (obj) {
    // If obj equals "1", sets result to "one"
    "1" -> "One"
    // If obj equals "Hello", sets result to "Greeting"
    "Hello" -> "Greeting"
    // Sets result to "Unknown" if no previous condition is satisfied
    else -> "Unknown"
}
println(result)
// Greeting
```

또한 <code clas="code ">When</code>문법은 불 표현식과 연계해서도 사용 가능합니다.

```Kotlin
val temp = 18

val description = when {
    // If temp < 0 is true, sets description to "very cold"
    temp < 0 -> "very cold"
    // If temp < 10 is true, sets description to "a bit cold"
    temp < 10 -> "a bit cold"
    // If temp < 20 is true, sets description to "warm"
    temp < 20 -> "warm"
    // Sets description to "hot" if no previous condition is satisfied
    else -> "hot"             
}
println(description)
// warm
```

### Ranges

4가지 표현식이 존재합니다.

1. <code clas="code ">..</code> Operator : <code clas="code ">1..4</code> == <code clas="code ">1, 2, 3, 4</code>
2. <code clas="code ">..<</code> Operator : <code clas="code ">1..<4</code> == <code clas="code ">1, 2, 3</code>
3. <code clas="code ">dwonTo</code> : <code clas="code ">4 downTo 1</code> == <code clas="code ">4, 3, 2, 1</code>
4. <code clas="code ">step</code> : <code clas="code ">1..5 step 2</code> == <code clas="code ">1, 3, 5</code>

<code clas="code ">Char</code>도 ranges 가능합니다.

- <code clas="code ">'a'..'d'</code> == <code clas="code ">'a', 'b', 'c', 'd'</code>
- <code clas="code ">'z' downTo 's' step 2</code> == <code clas="code ">'z', 'x', 'v', 't'</code>

### Loops

루프 중 가장 흔하게 사용하는것은 <code clas="code ">for</code>와 <code clas="code ">While</code>입니다.

#### For

```Kotlin
for (number in 1..5) {
    printf(number)
}
//12345
```

```Kotlin
//Collections
val cakes = listOf("carrot", "cheese", "chocolate")

for (cake in cakes) {
    println("Yummy, it's $cake cake!")
}
// Yummy, it's a carrot cake!
// Yummy, it's a cheese cake!
// Yummy, it's a chocolate cake!
```

#### while
<code clas="code ">while</code>은 두가지 방법으로 쓰입니다.

- To execute a code block while a conditional expression is true. (<code clas="code ">while</code>)

- To execute the code block first and then check the conditional expression. (<code clas="code ">do-while</code>)

```Kotlin
var cakesEaten = 0
while (cakesEaten < 3) {
    println("Eat a cake")
    cakesEaten++
}
// Eat a cake
// Eat a cake
// Eat a cake
```

```Kotlin
var cakesEaten = 0
var cakesBaked = 0
while (cakesEaten < 3) {
    println("Eat a cake")
    cakesEaten++
}
do {
    println("Bake a cake")
    cakesBaked++
} while (cakesBaked < cakesEaten)
// Eat a cake
// Eat a cake
// Eat a cake
// Bake a cake
// Bake a cake
// Bake a cake
```

# 5장. Functions

함수를 만들때 <code clas="code ">fun</code> keyword를 사용하여 만들 수 있습니다.

```Kotlin
fun hello() {
    return println("Hello, world!")
}

fun main() {
    hello()
    //Hello, world!
}
```

```Kotlin
fun sum(x: Int, y: Int): Int {
    return x + y
}

fun main() {
    println(sum(1, 2))
    // 3
}
```

### Named arguments

읽기 쉬운 코드를 만들기 위해 인자의 이름을 명시해 값을 순서에 맞추지 않고도 넘길 수 있습니다.

```Kotlin
fun printMessageWithPrefix(message: String, prefix: String) {
    println("[$prefix] $message")
}

fun main() {
    // Uses named arguments with swapped parameter order
    printMessageWithPrefix(prefix = "Log", message = "Hello")
    // [Log] Hello
}
```

### Default parameter values

인자에 사용자로부터 아무런 값이 들어오지 않았을 때 사용할 default value를 설정할 수 있습니다.

```Kotlin
fun printMessageWithPrefix(message: String, prefix: String = "Info") {
    println("[$prefix] $message")
}

fun main() {
    //Function called with both parameters
    printMessageWithPrefix("Hello", "Log")
    // [Log] Hello

    //Function called only with message parameter
    printMessageWithPrefix("Hello")
    // [Info] Hello

    printMessageWithPrefix(prefix = "Log", message = "Hello")
    // [Log] Hello
}
```

* 함수를 호출해 인자값 넘겨줄때 한번 스킵했다면, 이후의 모든 인자값에 대해서 이름을 명시해주어야합니다.


### Functions without return

함수에서 아무런 값도 return하고 싶지 않다면 <code clas="code ">Unit</code> type을 넘겨주면 됩니다. 해당 타입은 명시할 필요 없이 함수가 아무 값도 return하지 않으면 알아서 반환되는 값입니다.

```Kotlin
fun printMessage(message: String) {
    println(message)
    // 'return Unit' or 'return' is optional
}

fun main() {
    printMessage("Hello")
    //Hello
}
```

### Single-expression functions

클린 코드를 위해 함수를 조금 더 간단하게 표현할 수 있는 방법을 가지고 있습니다. 컴파일러의 타입추론 특성 덕분에 반환 타입을 명시하지 않아도 됩니다.

```Kotlin
fun sum(x: Int, y: Int): Int {
    return x+y
}

fun singleExpSum(x: Int, y: Int) = x + y

fun main() {
    println(sum(1, 2))
    println(singleExpSum(1, 2))
}
```

* 타입을 명시하지 않는 방법은 Single-expression에서만 가능하고 일반적인 함수 선언에서는 불가능합니다. (<code clas="code ">Unit</code> 제외)

### Lambda expression


```Kotlin
fun uppercaseString(string: String): String {
    return string.uppercase()
}
fun main() {
    println(uppercaseString("hello"))
    //HELLO
    //Lamda Expression
    println({string: String ->string.uppercase()} ("Hello))
    //HELLO
}
```

* 람다식을 쓸때 아무 인자없이 쓰면 다음과 같은 예제도 가능합니다.
```
{ println("Log message") }
```

#### Assign to variable
람다식을 변수에 할당하기 위해서는 <code clas="code ">=</code> 연산자를 사용합니다.

```kotlin
fun main() {
    val upperCaseString = {string : String -> string.uppercase()}
    println(upperCaseString("hello"))
    //HELLO
}
```


#### Pass to another function

람다식을 함수식으로 사용하고 싶을때 <code clas="code ">.filter()</code> extension을 이용하여 표현할 수 있습니다.

```kotlin
val numbers = listOf(1, - 2, -3, -4, 5, -6)
val positives = numbers.filter( x-> x > 0)
val negatives = numbers.filter( x-> x < 0>)
println(positves)
// [1, 3, 5]
println(negatives)
// [-2, -4, -6]
```

<code clas="code ">.map()</code> 함수는 item을 조작할때 유용합니다.

```kotlin
val numbers = listOf(1, -2, 3, -4, 5, -6)
val doubled = numbers.map { x -> x * 2 }
val tripled = numbers.map { x -> x * 3 }
println(doubled)
// [2, -4, 6, -8, 10, -12]
println(tripled)
// [3, -6, 9, -12, 15, -18]
```

<code clas="code ">.filter()</code> extension은 람다식을 함수의 몸체로 표현하도록 도와줍니다.

#### Function Types

함수를 반환하기 전에 함수 타입을 이해해야 합니다. 이미 기본 타입에 대해 알고 있지만, 함수 자체도 타입을 갖습니다. 코틀린의 타입 추론은 매개변수 타입으로부터 함수의 타입을 추론할 수 있지만, 때로는 함수 타입을 명시적으로 지정해야 할 때가 있습니다. 컴파일러는 해당 함수에 대해 허용되는 것과 허용되지 않는 것을 알기 위해 함수 타입이 필요합니다.

함수 타입의 구문은 다음과 같습니다:

- 괄호 () 내에 각 매개변수의 타입이 쉼표로 구분되어 씌여 있습니다.
- -> 뒤에 반환 타입이 씌여 있습니다.

예를 들어: (String) -> String 또는 (Int, Int) -> Int입니다.

```kotlin
val upperCaseString: (String) -> String = { string -> string.uppercase() }

fun main() {
    println(upperCaseString("hello"))
    //HELLO
}
```

람다식이 아무런 매개변수를 가지지 않을 때 괄호 안을 비워두면됩니다. <code clas="code ">() -> Unit</code>

* 컴파일러가 타입을 추론할 수 없을 때는 제대로된 작동을 하지 못합니다. ex). <code clas="code ">val upperCaseString = { str -> str.uppercase() }</code>

#### Return from a function

람다식을 가지고 함수식을 표현할때 컴파일러가 이해하기 쉽게 타입을 모두 명시해줘야합니다. 아래 예시는 <code clas="code ">when</code> 문법과 함께 람다식이 무슨 타입을 반환하는지 보여줍니다.

```kotlin
fun toSeconds(time: String): (Int) -> Int = when(time) {
    "hour" -> { value -> value * 60 * 60 }
    "minute" -> { value -> value * 60 }
    "second" -> { value -> value }
    else -> { value -> value }
}

fun main() {
    val timeInMinutes = listOf(2, 10, 15, 1)
    val min2sec = toSeconds("minute")
    val totalTimeInSeconds = timesInMinutes.map(min2sec).sum()
    println("Total time is $totalTimeInSeconds secs")
    //Total time is 1680 secs
}
```

#### Invoke separately

람다 표현식은 중괄호 {} 뒤에 괄호 ()를 추가하고 괄호 안에 매개변수를 포함하여 별도로 호출할 수 있습니다.

```Kotlin
println({ string: String -> string.uppercase() }("hello"))
//HEELO
```

#### Trailing lambdas

이미 보신 것처럼, 람다 표현식이 유일한 함수 매개변수인 경우 함수 괄호 ()를 생략할 수 있습니다. 람다 표현식이 함수의 마지막 매개변수로 전달되면, 해당 표현식은 함수 괄호 () 바깥에 작성될 수 있습니다. 두 경우 모두 이 구문은 trailing lambda라고 합니다.

예를 들어, <code>.fold()</code> 함수는 초기값과 연산을 인자로 받습니다:

```Kotlin
//The initial value is zero
//The operation sums the initial value with every item in the list cumulately.
println(listOf(1,2,3).fold(0, {x, item -> x + item}))
//6

//Alternatively, in the form of a trailing lambda
println(listOf(1,2,3).fold(0) {x, item -> x + item})
//6
```

# 6장. Classes

Kotlin도 객체지향 프로그래밍을 지원하고 있어 객체와 그것이 가지는 속성, 함수를 정의할 수 있습니다.

클래스를 선언할때는 <code>class</code>키워드를 사용합니다.

### Properties

클래스의 멤버 변수인 Properties를 두 가지 방법으로 선언할 수 있습니다.

- class name 이후 괄호 안에 정의

```Kotlin
class Contact(val id: Int, var email: String)
```

- class body 중괄호 안에 정의

```Kotlin
class Contact(val id: Int, var email: String) {
    val category: String = ""
}
```

일반적으로 instance가 생성된 이후로 바뀔 필요가 없는 변수는 <code>val</code>형식으로 정의합니다.

<code>val</code>이나 <code>var</code>을 쓰지 않고도 properties를 선언할 수 있습니다. 하지만 instance생성 이후 해당 properties에 접근할 수는 없습니다.

* class와 함께 소괄호에 포함된 내용은 <b>class header</b>라 부릅니다. class header에서 properties의 default value도 설정 가능합니다.
* properties 정의 시 Trailing commas 사용이 가능합니다
```Kotlin
class Person(
    val firstName: String,
    val lastName: String,
    val age: Int, // trailing comma
)
```

### Create instance

Class를 통해 Object를 생성할때, Constructor로 class instance를 선언할 수 있습니다.

기본적으로 Kotlin은 class header에서 매개변수가 선언될 때 같이 자동으로 constructor를 생성합니다.

```Kotlin
class Contact(val id: Int, var email: String)

fun main() {
    val contact = Contact(1, "mary@gmail.com")
}
```

- <code>Contact</code>는 class입니다.
- <code>contact</code>는 <code>Contact</code>의 instance 객체입니다.
- <code>id</code>와 <code>email</code>은 properties입니다.
- <code>id</code>와 <code>email</code>은 default constructor와 함께 <code>contact</code> 객체 생성을 위해 사용됩니다.

### Access properties

객체 instance에서 class의 property에 접근하려면 <code>.</code>를 이름 뒤에 붙여서 사용하면 됩니다.

```kotlin
class Contact(val id: Int, var email: String)

fun main() {
    val contact = Contact(1, "mary@gmail.com")

    println(contact.email)

    contact.email = "jane@mgail.com"

    println(contact.email)
}
```

문자열의 한부분으로 instacne의 property를 가져올때는 <code>$</code> 사용하면 됩니다.

ex). <code>println("Their email address is: ${contact.email}")</code>

### Member functions

class의 멤버 함수를 사용할때는 function extension 사용할때 처럼 <code>.</code>를 이름 뒤에 붙여 사용합니다. 

 반드시 class body내에 멤버 함수를 선언해주어야 합니다.

```Kotlin
class Contact(val id: Int, var email: String) {
    fun printId() {
        println(id)
    }
}

fun main() {
    val contact = Contact(1, "mary@gmail.com")
    // Calls member function printId()
    contact.printId()           
    // 1
}
```

### Data classes

Kotlin에는 데이터를 저장하기에 특히 유용한 데이터 클래스가 있습니다. 데이터 클래스는 클래스와 동일한 기능을 갖지만, 추가 멤버 함수가 자동으로 제공됩니다. 이러한 멤버 함수를 사용하면 인스턴스를 쉽게 읽을 수 있는 출력으로 출력하거나, 클래스의 인스턴스를 비교하거나, 복사할 수 있습니다. 이러한 함수들은 자동으로 제공되므로 모든 클래스마다 동일한 기본 코드를 작성할 필요가 없습니다.

선언을 위해서는 <code>data</code>키워드를 사용합니다.

```Kotlin
data class User(val name: String, val id: Int)
```

가장 자주 쓰이는 data class 세가지 예시

|Function|Description|
|:----------------|:----------------|
|<code>.toString()</code>|class instance와 properties를 read가능한 문자열 형태로 출력합니다.|
|<code>.equals()</code> or <code>==</code>|instance의 class를 비교합니다.|
|<code>.copy()</code>|
다른 instance로부터 복사하여 class instance를 생성합니다. 필요에 따라 일부 속성을 다르게 지정할 수 있습니다.|

#### Print as string

```Kotlin
// Automatically uses toString() function so that output is easy to read
println(user)            
// User(name=Alex, id=1)
```

#### Compare instances

```kotlin
val user = User("Alex", 1)
val secondUser = User("Alex", 1)
val thirdUser = User("Max", 2)

// Compares user to second user
println("user == secondUser: ${user == secondUser}") 
// user == secondUser: true

// Compares user to third user
println("user == thirdUser: ${user == thirdUser}")   
// user == thirdUser: false
```

#### Copy instance

```kotlin
val user = User("Alex", 1)
val secondUser = User("Alex", 1)
val thirdUser = User("Max", 2)

// Creates an exact copy of user
println(user.copy())       
// User(name=Alex, id=1)

// Creates a copy of user with name: "Max"
println(user.copy("Max"))  
// User(name=Max, id=1)

// Creates a copy of user with id: 3
println(user.copy(id = 3)) 
// User(name=Alex, id=3)
```

# 7장. Null Safety

Kotlin에서는 <code>null</code>값을 가지는것을 허용합니다. 하지만 <code>null</code>값을 처리함으로써 생기는 문제를 해결하기 위해 Null safety시스템을 구축해놓았습니다.

Null safety는 실행 시간이 아닌 컴파일 시간에 널 값과 관련된 잠재적인 문제를 감지합니다.

Null safety는 다음과 같은 기능의 조합을 의미합니다.

- explicitly declare when <code>null</code> values are allowed in your program.

- check for <code>null</code> values.

- use safe calls to properties or functions that may contain <code>null</code> values.

- declare actions to take if <code>null</code> values are detected.

### Nullable types

기본적으로 Type은 <code>null</code> value를 갖지 못합니다. Nullable Type으로 전환하기 위해서는 <code>?</code>를 타입 선언 이후 붙여줘 명시해놔야 <code>null</code> value를 갖을 수 있습니다.

```kotlin
fun main() {
    //nerverNull has String type
    var neverNull : String = "This can't be null"

    //Throws a copiler error
    neverNull = null

    //nullable String type
    var nullable: String? = "You can keep a null here"

    nullable = null

    var inferredNonNull = "The comlier assumes non-nullable"

    //Throws a complier error
    inferredNonNull = null

    //notNull doesn't accept null values
    fun strLength(notNull: String): Int {
        return notNull.length
    }

    println(strLength(neverNull)) // 18
    println(strLength(nullable))  // Throws a compiler error

}
```

### Check for null values

조건식 내에서 널 값의 존재 여부를 확인할 수 있습니다. 다음 예제에서 describeString() 함수는 maybeString이 널이 아니고 그 길이가 0보다 큰지를 확인하는 if 문을 포함합니다.

```kotlin
fun describeString(maybeString: String?): String {
    if (maybeString != null && maybeString.length > 0) {
        return "String of length ${maybeString.length}"
    } else {
        return "Empty or null string"
    }
}

fun main() {
    var nullString: String? = null
    println(describeString(nullString))
    //Empty or null string
}
```

### Use safe calls

객체의 속성에 안전하게 접근하려면 null 값을 포함할 수 있는 객체에 대해 안전 호출 연산자 ?를 사용하세요. 안전 호출 연산자는 객체 또는 그에 접근한 속성 중 하나가 널인 경우 널을 반환합니다. 이는 코드에서 널 값의 존재로 인한 오류를 피하고자 할 때 유용합니다.

다음 예제에서 lengthString() 함수는 문자열의 길이를 반환하거나 널을 반환하기 위해 안전 호출을 사용합니다.

```Kotlin
fun lengthString(maybeString: String?): Int? = maybeString?.length

fun main() { 
    var nullString: String? = null
    println(lengthString(nullString))
    // null
}
```

안전 호출은 연쇄적으로 사용할 수 있습니다. 따라서 객체의 어떤 속성이든 널 값을 포함하면 오류 없이 널이 반환됩니다. 예를 들어:<code>person.company?.address?.country</code>

안전 호출 연산자는 확장 함수나 멤버 함수를 안전하게 호출하는 데에도 사용될 수 있습니다. 이 경우, 함수가 호출되기 전에 널 체크가 수행됩니다. 체크가 널 값을 감지하면 호출이 건너뛰어지고 널이 반환됩니다.

다음 예제에서 nullString이 널이므로 .uppercase() 호출이 건너뛰어지고 널이 반환됩니다:

```Kotlin
fun main() {
    var nullString: String? = null
    println(nullString?.uppercase())
    // null
}
```

### Use Elvis operator


Elvis 연산자 ?:를 사용하여 널 값을 감지할 경우 반환할 기본 값을 제공할 수 있습니다.

Elvis 연산자 왼쪽에는 널 값을 확인해야 할 대상을 작성하고, 오른쪽에는 널 값을 감지할 경우 반환할 값을 작성하세요.

다음 예제에서 nullString은 널이므로 길이 속성에 안전한 호출을 수행하더라도 널 값을 반환합니다. 결과적으로 Elvis 연산자는 0을 반환합니다.

```Kotlin
fun main() {
    var nullString: String? = null
    println(nullString?.length ?: 0)
    // 0
}
```