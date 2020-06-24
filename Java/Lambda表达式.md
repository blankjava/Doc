## Lambda表达式
Lambda 表达式是一个匿名函数，Lambda表达式基于数学中的λ演算得名，直接对应于其中的lambda抽象，是一个匿名函数，即没有函数名的函数。Lambda表达式可以表示闭包。

在 Java 中，Lambda 表达式的格式是像下面这样：
```
// 无参数，无返回值
() -> log.info("Lambda")

 // 有参数，有返回值
(int a, int b) -> { a+b }
```
其等价于:
```
log.info("Lambda");

private int plus(int a, int b){
  	return a+b;
}
```

最常见的一个例子就是新建线程，有时候为了省事，会用下面的方法创建并启动一个线程，这是匿名内部类的写法，new Thread需要一个 implements 自Runnable类型的对象实例作为参数，比较好的方式是创建一个新类，这个类 implements Runnable，然后 new 出这个新类的实例作为参数传给 Thread。而匿名内部类不用找对象接收，直接当做参数。
```
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("快速新建并启动一个线程");
    }
}).start();
```
这样写其实是将一个匿名对象传给了一个方法，然后通过这个匿名对象调用其中的run()方法，其目的还是想让一个方法像参数一样使用，这个时候Lambda表达式就出现了：
```
new Thread(()->{
    System.out.println("快速新建并启动一个线程");
}).start();
```
看到没有，上面的匿名对象简写成Lambda表达式，不仅代码简洁了，而且更近似于函数参数化；

上面两种方式，虽然最终达到的目的是一样的，但其实内部的实现原理却不相同。匿名内部类在编译之后会创建一个新的匿名内部类出来，而 Lambda 是调用 JVM invokedynamic指令实现的，并不会产生新类。


## 方法引用
方法引用的出现，使得我们可以将一个方法赋给一个变量或者作为参数传递给另外一个方法。`::`双冒号作为方法引用的符号，比如下面这两行语句，引用Integer类的parseInt方法。
```
Function<String, Integer> s = Integer::parseInt;
Integer i = s.apply("10");
```
或者下面这两行，引用 Integer类的 compare方法。
```
Comparator<Integer> comparator = Integer::compare;
int result = comparator.compare(100,10);
```
再比如，下面这两行代码，同样是引用 Integer类的 compare方法，但是返回类型却不一样，但却都能正常执行，并正确返回。
```
IntBinaryOperator intBinaryOperator = Integer::compare;
int result = intBinaryOperator.applyAsInt(10,100);
```
上面又是 Function、又是Comparator、又是 IntBinaryOperator的，看上去好像没有规律，其实不然。返回的类型是 Java 8 专门定义的函数式接口，这类接口用 @FunctionalInterface 注解。比如 Function这个函数式接口的定义如下：
```
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```
还有很关键的一点，你的引用方法的参数个数、类型，返回值类型要和函数式接口中的方法声明一一对应才行。

比如 Integer.parseInt方法定义如下：
```
public static int parseInt(String s) throws NumberFormatException {
    return parseInt(s,10);
}
```
首先parseInt方法的参数个数是 1 个，而 Function中的 apply方法参数个数也是 1 个，参数个数对应上了，再来，apply方法的参数类型和返回类型是泛型类型，所以肯定能和 parseInt方法对应上。

这样一来，就可以正确的接收Integer::parseInt的方法引用，并可以调用Funciton的apply方法，这时候，调用到的其实就是对应的 Integer.parseInt方法了。

用这套标准套到 Integer::compare方法上，就不难理解为什么即可以用`Comparator<Integer>`接收，又可以用`IntBinaryOperator`接收了，而且调用它们各自的方法都能正确的返回结果。

Integer.compare方法定义如下：
```
public static int compare(int x, int y) {
    return (x < y) ? -1 : ((x == y) ? 0 : 1);
}
```
返回值类型 int，两个参数，并且参数类型都是 int。

然后来看Comparator和IntBinaryOperator它们两个的函数式接口定义和其中对应的方法：
```
@FunctionalInterface
public interface Comparator<T> {
    int compare(T o1, T o2);
}

@FunctionalInterface
public interface IntBinaryOperator {
    int applyAsInt(int left, int right);
}
```
都能正确的匹配上，所以前面示例中用这两个函数式接口都能正常接收。其实不止这两个，只要是在某个函数式接口中声明了这样的方法：两个参数，参数类型是 int或者泛型，并且返回值是 int或者泛型的，都可以完美接收。