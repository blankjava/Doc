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