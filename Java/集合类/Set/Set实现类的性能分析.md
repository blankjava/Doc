## Set实现类的性能分析
HashSet 和TreeSet是Set的两个典型实现，到底如何选择HashSet和TreeSet呢? HashSet的性能总是比TreeSet 好（特别是最常用的添加、查询元素等操作）， 因为TreeSet需要额外的红黑树算法来维护集合元素的次序。只有当需要一个保持排序的Set时， 才应该使用TreeSet , 否则都应该使用HashSet。  
HashSet还有一个子类： LinkedHashSet, 对于普通的插入、删除操作，LinkedHashSet比HashSet要略微慢一点， 这是由维护链表所带来的额外开销造成的，但由于有了链表，遍历LinkedHashSet会更快。  
EnumSet是所有Set 实现类中性能最好的，但它只能保存同一个枚举类的枚举值作为集合元素。  
必须指出的是，Set的三个实现类HashSet、TreeSet和EnumSet都是线程不安全的。如果有多个线程同时访问一个Set集合， 并且有超过一个线程修改了该Set集合，则必须手动保证该Set集合的同步性。通常可以通过Collections工具类的synchron izedSortedSet方法来“ 包装” 该Set集合。此操作最好在创建时进行，以防止对Set集合的意外非同步访问。例如：
```
SortedSet<String> sortedSet = Collections.synchronizedSortedSet(new TreeSet<>());
```
