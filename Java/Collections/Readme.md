## Java集合类是什么
Java 集合类是一种特别有用的工具类， 可用于存储数量不等的对象， 并可以实现常用的数据结构，如栈、队列等。除此之外， Java 集合还可用于保存具有映射关系的关联数组。Java 集合大致可分为Set、List、Queue 和Map 四种体系， 其中Set 代表无序、不可重复的集合； List 代表有序、重复的集合；而Map 则代表具有映射关系的集合， Java 5 又增加了Queue 体系集合， 代表一种队列集合实现。  
Java 集合就像一种容器，可以把多个对象（实际上是对象的引用，但习惯上都称对象）“丢进” 该容器中。在Java5之前，Java集合会丢失容器中所有对象的数据类型，把所有对象都当成Object类型处理；从Java 5 增加了泛型以后，Java 集合可以记住容器中对象的数据类型，从而可以编写出更简洁、健壮的代码。

## Java集合类与数组
在编程时， 常常需要集中存放多个数据， 例如第6章练习题中梭哈游戏里剩下的牌。可以使用数组来保存多个对象，但数组长度不可变化， 一旦在初始化数组时指定了数组长度， 这个数组长度就是不可
变的，如果需要保存数量变化的数据，数组就有点无能为力了；而且数组无法保存具有映射关系的数据，如成绩表：语文一79, 数学—80, 这种数据看上去像两个数组，但这两个数组的元素之间有一定的关联关系。  
为了保存数量不确定的数据， 以及保存具有映射关系的数据（也被称为关联数组），Java 提供了集合类。集合类主要负责保存、盛装其他数据，因此集合类也被称为容器类。所有的集合类都位于java.util
包下， 后来为了处理多线程环境下的并发安全问题，Java 5 还在java.util.concurrent 包下提供了一些多线程支待的集合类。  
集合类和数组不一样， 数组元素既可以是基本类型的值， 也可以是对象（实际上保存的是对象的引用变量）；而集合里只能保存对象（实际上只是保存对象的引用变量， 但通常习惯上认为集合里保存的
是对象）。