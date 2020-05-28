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