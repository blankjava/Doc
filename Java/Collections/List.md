## List集合特性
List集合代表一个元素有序、可重复的集合，集合中每个元素都有其对应的顺序索引，可以通过索引来访问指定位置的集合元素。List集合默认按元素的添加顺序设置元素的索引， 例如第一次添加的元素索引为0, 第二次添加的元素索引为1……

1、List接口新增的方法  
List作为Collection接口的子接口， 当然可以使用Collection接口里的全部方法。而且由于List是有序集合， 因此List集合里增加了一些根据索引来操作集合元素的方法。


## ArrayList（以数组为数据存储结构）
ArrayList是非线程安全的；  
ArrayList数据的存放形式是数组，是连续的，所以寻址快，插入和删除慢；

### 增加元素


### 删除元素
1、删除某一个位置的元素
```
```

2、删除某一个元素
```
```

3、for循环遍历删除元素
遍历删除首先想的可能是用for循环，然后使用remove方法删除元素，如下：
```
public void testDelete() {
    List<String> list = new ArrayList<>();
    list.add("aa");
    list.add("bb");
    list.add("cc");
    list.add("dd");
    for (int i = 0; i < list.size(); i++) {
        list.remove(i);
    }
    System.out.println(list.toString());//[bb, dd]
}
```
从结果来看，这种方式删除会漏掉部分数据，因为每删除一个数据，list的size就会-1，后面的数据的位置就会向前移一位，因此不建议使用这种方式遍历删除；  
但是如果删除的元素是连续，却可以通过for循环删除的起点位置和删除的长度来实现，例如：
```
public void testDelete() {
    List<String> list = new ArrayList<>();
    list.add("aa");
    list.add("bb");
    list.add("cc");
    list.add("dd");
    int count = list.size();
    for (int i = 0; i < count; i++) {
        list.remove(0);
    }
    System.out.println(list.toString());
}
```
其中起点位置用i指定，删除长度用count指定；除此之外，还可以使用List的subList方法实现：
```
public void testDelete() {
    List<String> list = new ArrayList<>();
    list.add("aa");
    list.add("bb");
    list.add("cc");
    list.add("dd");
    list.subList(0,2).clear();
    System.out.println(list.toString());//["cc","dd"]
}
```
由此可见，subList方法并不是创建一个新的List，而是对原List部分的一个引用；

4、foreach循环遍历删除  
如果使用foreach循环，会直接报ConcurrentModificationException的错误，因此不能使用foreach方式；

5、Iterator遍历删除元素  
使用Iterator遍历删除元素，可以删除连续的或符合条件的元素，例如；
```
public void testDelete() {
    List<String> list = new ArrayList<>();
    list.add("aa");
    list.add("bb");
    list.add("cc");
    list.add("dd");
    Iterator<String> iterator = list.iterator();
    String value;
    while (iterator.hasNext()) {
        value = iterator.next();
        if (value.equals("aa") || value.equals("cc")) {
            iterator.remove();
        }
    }
    System.out.println(list.toString());
}
```


6、Java8中的removeIf方法遍历删除  
使用removeIf方法遍历删除元素，可以删除连续的或符合条件的元素，例如；
```
public void testDelete() {
    List<String> list = new ArrayList<>();
    list.add("aa");
    list.add("bb");
    list.add("cc");
    list.add("dd");
    list.removeIf(s -> s.equals("aa") || s.equals("cc"));
    System.out.println(list.toString());
}
```
这种方法的内部实现其实是使用Iterator进行遍历删除；


7、清空列表


### 修改元素

### 查询元素  
for循环遍历所有元素：
```
```

foreach遍历所有元素：
```
```


## LinkedList（以链表为数据存储结构）
LinkedList是非线程安全的；  
LinkedList数据的存放形式是链表，是散列的，所以寻址慢，插入和删除快；
