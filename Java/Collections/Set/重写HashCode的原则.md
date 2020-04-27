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