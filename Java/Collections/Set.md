## Set集合简介
Set 集合，它类似于一个罐子， 程序可以依次把多个对象“丢进”Set集合，而Set集合通常不能记住元素的添加顺序。Set 集合与Collection 基本相同， 没有提供任何额外的方法。实际上Set 就是Collection, 只是行为略有不同（Set 不允许包含重复元素）。  
Set 集合不允许包含相同的元素，如果试图把两个相同的元素加入同一个Set 集合中，则添加操作失败，add()方法返回false, 且新元素不会被加入。

## HashSet类
HashSet是Set接口的典型实现，大多数时候使用Set集合时就是使用这个实现类。HashSet按Hash算法（哈希表）来存储集合中的元素，因此具有很好的存取和查找性能。  
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
HashSet还有一个子类LinkedHashSetL, inkedHashSet集合也是根据元素的hashCode值来决定元素的存储位置，但它同时使用链表维护元素的次序，这样使得元素看起来是以插入的顺序保存的。也就是说， 当遍历LinkedHashSet集合里的元素时，LinkedHashSet将会按元素的添加顺序来访问集合里的元素。  
LinkedHashSet需要维护元素的插入顺序，因此性能略低于HashSet的性能，但在迭代访问Set里的全部元素时将有很好的性能，因为它以链表来维护内部顺序。
```
public void testLinkedHashSet() {
    LinkedHashSet<String> linkedHashSet = new LinkedHashSet<>();
    linkedHashSet.add("aa");
    linkedHashSet.add("bb");
    linkedHashSet.add("cc");
    System.out.println(linkedHashSet);
    //删除元素
    linkedHashSet.remove("bb");
    System.out.println(linkedHashSet);
    //重新添加元素
    linkedHashSet.add("bb");
    System.out.println(linkedHashSet);
}
```
运行上面的程序输出如下：
```
[aa, bb, cc]
[aa, cc]
[aa, cc, bb]
```
输出LinkedHashSet集合的元素时，元素的顺序总是与添加顺序一致。  
虽然LinkedHashSet使用了链表记录集合元素的添加顺序，但LinkedHashSet依然是HashSet, 因此它依然不允许集合元素重复；

## TreeSet类
TreeSet是SortedSet接口的实现类，正如SortedSet名字所暗示的，TreeSet可以确保集合元素处于排序状态。与HashSet集合相比，TreeSet还提供了如下几个额外的方法。
```
public class TreeSet<E> extends AbstractSet<E>
        implements NavigableSet<E>, Cloneable, java.io.Serializable
{
    /**
     * NavigableMap集合对象
     */
    private transient NavigableMap<E,Object> m;

    /**
     * TreeSet是通过TreeMap实现的，PRESENT是键-值对中的值。
     */
    private static final Object PRESENT = new Object();

    /**
     * 创建一个TreeSet包含NavigableMap中的元素
     */
    TreeSet(NavigableMap<E,Object> m) {
        this.m = m;
    }

    /**
     * 创建一个空的TreeSet，元素按照自然规则排序，所有的元素必须实现Comparable接口
     */
    public TreeSet() {
        this(new TreeMap<E,Object>());
    }

    /**
     * 创建一个空的TreeSet，元素按传入的comparator进行比较排序
     */
    public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
    }

    /**
     * 创建一个TreeSet包含Collection中的元素，元素按自然规则排序，所有元素必须实现Comparable接口
     */
    public TreeSet(Collection<? extends E> c) {
        this();
        addAll(c);
    }

    /**
     * 创建一个TreeSet包含SortedSet中的元素，元素按SortedSet中的排序规则排序
     */
    public TreeSet(SortedSet<E> s) {
        this(s.comparator());
        addAll(s);
    }

    ...

    /**
     * 返回TreeSet的子集，范围从fromElement到toElement
     * @param fromElement
     * @param fromInclusive 是否包含fromElement
     * @param toElement
     * @param toInclusive 是否包含toElement
     * @return
     */
    public NavigableSet<E> subSet(E fromElement, boolean fromInclusive,
                                  E toElement,   boolean toInclusive) {
        return new TreeSet<>(m.subMap(fromElement, fromInclusive,
                toElement,   toInclusive));
    }

    /**
     * 返回小于toElement的子集
     * @param toElement
     * @param inclusive 是否包含toElement
     * @return
     */
    public NavigableSet<E> headSet(E toElement, boolean inclusive) {
        return new TreeSet<>(m.headMap(toElement, inclusive));
    }

    /**
     * 返回大于fromElement的子集
     * @param fromElement
     * @param inclusive 是否包含fromElement
     * @return
     */
    public NavigableSet<E> tailSet(E fromElement, boolean inclusive) {
        return new TreeSet<>(m.tailMap(fromElement, inclusive));
    }

    /**
     * 返回TreeSet的子集，范围从fromElement到toElement,默认包含fromElement，不包含toElement
     */
    public SortedSet<E> subSet(E fromElement, E toElement) {
        return subSet(fromElement, true, toElement, false);
    }

    /**
     * 返回小于toElement的子集，默认不包含toElement
     * @param toElement
     * @return
     */
    public SortedSet<E> headSet(E toElement) {
        return headSet(toElement, false);
    }

    /**
     * 返回大于fromElement的子集，默认包含fromElement
     */
    public SortedSet<E> tailSet(E fromElement) {
        return tailSet(fromElement, true);
    }

    /**
     * 返回集合中第一个元素
     */
    public E first() {
        return m.firstKey();
    }

    /**
     * 返回集合中最后一个元素
     */
    public E last() {
        return m.lastKey();
    }

    /**
     * 返回集合中小于e的最大元素，其中e可以不是集合中的元素
     */
    public E lower(E e) {
        return m.lowerKey(e);
    }

    /**
     * 返回集合中小于等于e的最大元素，其中e可以不是集合中元素
     */
    public E floor(E e) {
        return m.floorKey(e);
    }

    /**
     * 返回集合中大于等于e的最小元素，其中e可以不是集合中元素
     */
    public E ceiling(E e) {
        return m.ceilingKey(e);
    }

    /**
     * 返回集合中大于e的最小元素，其中e可以不是集合中元素
     */
    public E higher(E e) {
        return m.higherKey(e);
    }

    /**
     * 返回第一个元素，并删除
     */
    public E pollFirst() {
        Map.Entry<E,?> e = m.pollFirstEntry();
        return (e == null) ? null : e.getKey();
    }

    /**
     * 返回最后一个元素，并删除
     */
    public E pollLast() {
        Map.Entry<E,?> e = m.pollLastEntry();
        return (e == null) ? null : e.getKey();
    }
    ...
}
```
表面上看这些方法很多，其实它很简单，因为TreeSet中的元素是有序的（不是像LinkedHashSet那样按添加的顺序排列，而是一个给定的比较规则），所以增加了访问前一个，后一个，第一个，最后一个的方法，并提供了三个从TreeSet中截取子TreeSet的方法；

下面是测试TreeSet基本用法的程序：
```
public void testTreeSet() {
    TreeSet<Integer> treeSet = new TreeSet<>();
    treeSet.add(3);
    treeSet.add(1);
    treeSet.add(7);
    treeSet.add(-2);
    System.out.println(treeSet);
    System.out.println(treeSet.first());
    System.out.println(treeSet.last());
    System.out.println(treeSet.headSet(4));
    System.out.println(treeSet.tailSet(4));
}
```
运行上面的程序输出如下：
```
[-2, 1, 3, 7]
-2
7
[-2, 1, 3]
[7]
```
根据上面程序的运行结果即可看出， TreeSet并不是根据元素的插入顺序进行排序的， 而是根据元素实际值的大小，来进行排序的。  
与HashSet集合采用hash算法来决定元素的存储位置不同， TreeSet采用红黑树的数据结构来存储集合元素。那么TreeSet进行排序的规则是怎样的呢? TreeSet支持两种排序方法：自然排序和定制排序。在默认情况下， TreeSet采用自然排序。  
1、自然排序
TreeSet会调用集合元素的compareTo(Object obj)方法来比较元素之间的大小关系，然后将集合元素按升序排列，这种方式就是自然排序。   
Java提供了一个Comparable接口，该接口里定义了一个compareTo(Object obj)方法，该方法返回一个整数值，实现该接口的类必须实现该方法，实现了该接口的类的对象就可以比较大小。当一个对象调用该方法与另一个对象进行比较时，例如obj1.compareTo(obj2), 如果该方法返回0, 则表明这两个对象相等；如果该方法返回一个正整数，则表明obj1大于obj2；如果该方法返回一个负整数，则表明obj1小于obj2。  
Java的一些常用类已经实现了Comparable接口，并提供了比较大小的标准：
- BigDecimal、Biglnteger以及所有的数值型对应的包装类：按它们对应的数值大小进行比较。
- Character: 按字符的UNICODE值进行比较。
- Boolean: true对应的包装类实例大于false对应的包装类实例。
- String: 按字符串中字符的UNCI ODE值进行比较。
- Date、Time: 后面的时间、日期比前面的时间、日期大。

如果试图把一个对象添加到TreeSet时，则该对象的类必须实现Comparable接口，否则程序将会抛出异常。

注意一个问题：`当需要把一个对象放入TreeSet中，重写该对象对应类的equals()方法时，应保证该方法与compareTo(Object obj)方法有一致的结果，其规则是：如果两个对象通过equals()方法比较返回true时，这两个对象通过compareTo(Object obj)方法比较应返回0 。`  
如果两个对象通过compareTo(Object obj)方法比较返回0时，但它们通过equals()方法比较返回false，将很麻烦，因为两个对象通过compareTo(Object obj)方法比较相等，TreeSet不会让第二个元素添加进去，但这又与Set集合的规则不相符；

2、定制排序

TreeSet的自然排序是根据集合元素的大小，TreeSet将它们以升序排列。如果需要实现定制排序，例如以降序排列，则可以通过Comparator接口的帮助。该接口里包含一个compare(T o1,T o2)方法，该方法用于比较ol和o2的大小：如果该方法返回正整数，则表明o1大于o2; 如果该方法返回0, 则表明o1等于o2; 如果该方法返回负整数，则表明o1小于o2。  
如果需要实现定制排序，则需要在创建TreeSet集合对象时，提供一个Comparator对象即可；

## EnumSet类




