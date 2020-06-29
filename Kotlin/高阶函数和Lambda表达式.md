Kotlin中的函数是一等公民，这意味着它们可以存储在变量与数据结构中、作为参数传递给其他高阶函数以及从其他高阶函数返回。可以像操作任何其他非函数值一样操作函数。

为促成这点，作为一门静态类型编程语言的Kotlin使用一系列的函数类型来表示函数，并提供一组特定的语言结构，例如lambda表达式。

## 高阶函数
高阶函数是将函数用作参数或返回值的函数。

一个典型的例子就是集合的fold函数，它接受一个初始累积值与一个结合函数，并通过将当前累积值与每个集合元素连续接合起来代入累积值来构建返回值：
```
fun <T, R> Collection<T>.fold(
    initial: R, 
    combine: (acc: R, nextElement: T) -> R
): R {
    var accumulator: R = initial
    for (element: T in this) {
        accumulator = combine(accumulator, element)
    }
    return accumulator
}
```
在上述代码中，参数 combine 具有函数类型 (R, T) -> R，因此fold接受一个函数作为参数，该函数接受类型分别为R与T的两个参数并返回一个R类型的值。在for循环内部调用该函数，然后将其返回值赋值给accumulator。

下面是调用fold函数的示例：
```
fun main() {
    //sampleStart
    val items = listOf(1, 2, 3, 4, 5)

    // Lambdas 表达式是花括号括起来的代码块。
    items.fold(0, { 
        // 如果一个 lambda 表达式有参数，前面是参数，后跟“->”
        acc: Int, i: Int -> 
        print('acc = $acc, i = $i, ') 
        val result = acc + i
        println('result = $result')
        // lambda 表达式中的最后一个表达式是返回值：
        result
    })

    // lambda 表达式的参数类型是可选的，如果能够推断出来的话：
    val joinedToString = items.fold('Elements:', { acc, i -> acc + ' ' + i })

    // 函数引用也可以用于高阶函数调用：
    val product = items.fold(1, Int::times)
    //sampleEnd
    println('joinedToString = $joinedToString')
    println('product = $product')
}
```

## 函数类型
Kotlin 使用类似 (Int) -> String 的一系列函数类型来处理函数的声明；

这些类型具有与函数签名相对应的特殊表示法，即它们的参数和返回值：
- 所有函数类型都有一个圆括号括起来的参数类型列表以及一个返回类型：(A, B) -> C 表示接受类型分别为 A 与 B 两个参数并返回一个 C 类型值的函数类型。 参数类型列表可以为空，如 () -> A。Unit 返回类型不可省略。
- 函数类型可以有一个额外的接收者类型，它在表示法中的点之前指定： 类型 A.(B) -> C 表示可以在 A 的接收者对象上以一个 B 类型参数来调用并返回一个 C 类型值的函数。 带有接收者的函数字面值通常与这些类型一起使用。
- 挂起函数属于特殊种类的函数类型，它的表示法中有一个 suspend 修饰符 ，例如 suspend () -> Unit 或者 suspend A.(B) -> C。

函数类型表示法可以选择性地包含函数的参数名：(x: Int, y: Int) -> Point。 这些名称可用于表明参数的含义。还可以通过使用类型别名给函数类型起一个别称：
```
typealias ClickHandler = (Button, ClickEvent) -> Unit
```
其他注意事项：
- 如需将函数类型指定为可空，请使用圆括号：((Int, Int) -> Int)?。
- 函数类型可以使用圆括号进行接合：(Int) -> ((Int) -> Unit)，箭头表示法是右结合的，(Int) -> (Int) -> Unit 与前述示例等价，但不等于 ((Int) -> (Int)) -> Unit。

## 函数实例
有几种方法可以获得函数类型的实例：
- 使用函数字面值的代码块，采用以下形式之一：
    - lambda 表达式: { a, b -> a + b }
    - 匿名函数: fun(s: String): Int { return s.toIntOrNull() ?: 0 }
    - 带有接收者的函数字面值可用作带有接收者的函数类型的值。
- 使用已有声明的可调用引用
    - 顶层、局部、成员、扩展函数：::isOdd、 String::toInt
    - 顶层、成员、扩展属性：List<Int>::size
    - 构造函数：::Regex
    - 指向特定实例成员的绑定的可调用引用：foo::toString。
- 使用实现函数类型接口的自定义类的实例，例如：
```
class IntTransformer: (Int) -> Int {
    override operator fun invoke(x: Int): Int = TODO()
}

val intFunction: (Int) -> Int = IntTransformer()
```

## 函数调用

## Lambda表达式
