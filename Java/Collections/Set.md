## Set集合简介
Set 集合，它类似于一个罐子， 程序可以依次把多个对象“丢进”Set集合，而Set集合通常不能记住元素的添加顺序。Set 集合与Collection 基本相同， 没有提供任何额外的方法。实际上Set 就是Collection, 只是行为略有不同（Set 不允许包含重复元素）。  
Set 集合不允许包含相同的元素，如果试图把两个相同的元素加入同一个Set 集合中，则添加操作失败，add()方法返回false, 且新元素不会被加入。

## HashSet类
HashSet是Set接口的典型实现，大多数时候使用Set集合时就是使用这个实现类。HashSet按Hash算法来存储集合中的元素，因此具有很好的存取和查找性能。  
HashSet有如下特点：
- 不能保证元素的排列顺序， 顺序可能与添加顺序不同， 顺序也有可能发生变化
- HashSet不是同步的， 如果多个线程同时访问一个HashSet, 假设有两个或者两个以上线程同时修改了HashSet集合时， 则必须通过代码来保证其同步
- 集合元素值可以是null

当向HashSet 集合中存入一个元素时， HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据该hashCode 值决定该对象在HashSet中的存储位置。如果有两个元素通过equals()方法比较返回true, 但它们的hashCode()方法返回值不相等，HashSet将会把它们存储在不同的位置，依然可以添加成功。  
也就是说，HashSet 集合判断两个元素相等的标准是两个对象通过equals()方法比较相等，并且两个对象的hashCode()方法返回值也相等。  
下面程序分别提供了三个类A 、B和C, 它们分别重写了equals()、hashCode()两个方法的一个或全部， 通过此程序可以看到HashSet判断集合元素相同的标准。
```
public class HashSetTest {
    static class A {
        @Override
        public boolean equals(Object obj) {
            return true;
        }
    }

    static class B {
        @Override
        public int hashCode() {
            return 1;
        }
    }

    static class C {
        @Override
        public boolean equals(Object obj) {
            return true;
        }

        @Override
        public int hashCode() {
            return 2;
        }
    }

    public static void main(String[] args) {
        HashSet<Object> hashSet = new HashSet<>();
        hashSet.add(new A());
        hashSet.add(new A());
        hashSet.add(new B());
        hashSet.add(new B());
        hashSet.add(new C());
        hashSet.add(new C());//增加失败
        System.out.println(hashSet);
    }
}
```
运行上面的程序，结果如下：
```
[HashSetTest$B@1, HashSetTest$B@1,HashSetTest$C@2, HashSetTest$A@7f31245a, HashSetTest$A@14ae5a5]
```
两个A对象通过equals()方法比较返回true, 但HashCode不同，HashSet依然把它们当成两个对象，存储在不同位置；  
两个B对象的hashCode()返回相同值（都是1), 但equals方法返回false，HashSet也把它们当成两个对象，存储在同一个槽位的链表中。  
两个C对象的hashCode()返回值相同（都是2），且equals方法返回true，HashSet把它们当成同一个对象，后面添加的C对象会失败；

这里有一个点需要注意：当把一个对象放入HashSet中时，如果需要重写该对象对应类的equals方法，则应该同时重写其hashCode方法，原则是，如果一个对象通过equals方法比较返回true，则其hashCode值也应该相同；  
如果两个对象通过equals方法比较返回true，但这两个对象的hashCode值不相同，这将导致HashSet会把这两个对象保存在Hash表的不同位置，从而使两个对象都可以添加成功，这就与Set集合的规则冲突了。  
如果两个对象的hashCode()方法返回的hashCode值相同，但它们通过equals()方法比较返回false时将更麻烦：因为两个对象的hashCode 值相同，HashSet将试图把它们保存在同一个位置，但又不行（否则将只剩下一个对象），所以实际上会在这个位置用链式结构来保存多个对象；而HashSet访问集合元素时也是根据元素的hashCode 值来快速定位的，如果HashSet中两个以上的元素具有相同的hashCode值，将会导致性能下降。

## 重写hashCode方法的原则
HashSet中每个能存储元素的“槽位"(slot)通常称为“桶"(bucket), 如果有多个元素的hashCode值相同，但它们通过equals()方法比较返回false, 就需要在一个“桶”里放多个元素，这样会导致性能下降。  
前面介绍了hashCode()方法对于HashSet的重要性（实际上，对象的hashCode值对于后面的HashMap同样重要），下面给出重写hashCode()方法的基本规则。
- 在程序运行过程中，同一个对象多次调用hashCode()方法应该返回相同的值。
- 当两个对象通过equals()方法比较返回true时，这两个对象的hashCode()方法应返回相等的值。
- 对象中用作equals()方法比较标准的实例变量，都应该用于计算hashCode 值。

下面给出重写hashCode()方法的一般步骤。  
1、把对象内每个有意义的实例变量（即每个参与equals()方法比较标准的实例变量）计算出一个int类型的hashCode值。计算方式如下：
|变量类型|计算方式|
|:-:|:-:|
|boolean|hashCode=(f?0:1)|
|整数类型(byte,short,int,char)|hashCode=(int)f|
|long|hashCode=(int)(f^(f>>>32))|
|float|hashCode=Float.floatToIntBits(f)|
|double|long l=Double.doubleToLongBits(f);hashCode=(int)(l^(l>>>32));|
|引用类型|hashCode=f.hashCode()|


2、用第1步计算出来的多个hashCode值组合计算出一个hashCode值返回。例如如下代码：
```
return fl.hashCode() + (int) f2;
```
为了避免直接相加产生偶然相等（两个对象的fl 、f2 实例变量并不相等，但它们的hashCode的和恰好相等），可以通过为各实例变量的hashCode 值乘以任意一个质数后再相加。例如如下代码：
```
return fl.hashCode() * 19 + (int) f2 * 31;
```
如果向HashSet 中添加一个可变对象后，后面程序修改了该可变对象的实例变量，则可能导致它与集合中的其他元素相同（即两个对象通过equals()方法比较返回true, 两个对象的hashCode 值也相等），这就有可能导致HashSet 中包含两个相同的对象。下面程序演示了这种情况。
```
static class R {
    int count;

    public R(int count) {
        this.count = count;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        R r = (R) o;
        return count == r.count;
    }

    @Override
    public int hashCode() {
        return count;
    }

    @Override
    public String toString() {
        return "R{" +
                "count=" + count +
                '}';
    }
}

public static void main(String[] args) {
    HashSet<R> hashSet = new HashSet<>();
    hashSet.add(new R(5));
    hashSet.add(new R(-3));
    hashSet.add(new R(9));
    hashSet.add(new R(-2));
    System.out.println(hashSet);
    Iterator<R> iterator = hashSet.iterator();
    R first = iterator.next();
    //为随机的第一个元素赋值
    first.count = -3;
    //再次输出集合，集合中有重复元素
    System.out.println(hashSet);
    //删除count为-3的R对象
    hashSet.remove(new R(-3));
    //可以看到被删除了一个R元素
    System.out.println(hashSet);
    System.out.println("是否包含count为-3的R对象" + hashSet.contains(new R(-3)));//输出false
    System.out.println("是否包含count为-2的R对象" + hashSet.contains(new R(-2)));//输出false
}
```
运行上面的程序结果如下：
```
[R{count=-2}, R{count=-3}, R{count=5}, R{count=9}]
[R{count=-3}, R{count=-3}, R{count=5}, R{count=9}]
[R{count=-3}, R{count=5}, R{count=9}]
是否包含count为-3的R对象false
是否包含count为-2的R对象false
```
上面程序中提供了R类 ，R类重写了equals(Objcet obj)方法和hashCode()方法，这两个方法都是根据R对象的count实例变量来判断的。上面程序改变了Set集合中第一个R对象的count实例变量的值，这将导致该R对象与集合中的其他对象相同;  
HashSet集合中的第1个元素和第2个元素完全相同，这表明两个元素已
经重复。此时HashSet会比较混乱：当试图删除count 为-3的R对象时， HashSet会计算出该对象的hashCode值，从而找出该对象在集合中的保存位置， 然后把此处的对象与count 为-3的R 对象通过equals()方法进行比较，如果相等则删除该对象——HashSet只有第2个元素才满足该条件（第1个元素实际上保存在count为-2的R对象对应的位置），所以第2个元素被删除。至于第一个count为-3的R对象， 它保存在count为-2的R对象对应的位置，但使用equals()方法拿它和count为-2的R对象比较时又返回false一一这将导致HashSet不可能准确访问该元素。  
由此可见，当程序把可变对象添加到HashSet中之后，尽量不要去修改该集合元素中参与计算hashCode()、equals()的实例变量，否则将会导致HashSet无法正确操作这些集合元素。



## LinkedHashSet类


