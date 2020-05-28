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