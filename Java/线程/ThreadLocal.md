## ThreadLocal的作用
ThreadLocal和Synchonized都用于解决多线程并发访问。但是ThreadLocal与synchronized有本质的区别。Synchronized用于线程间的数据共享，而ThreadLocal则用于线程间的数据隔离。Synchronized是利用锁的机制，使变量或代码块在某一时该只能被一个线程访问。而ThreadLocal为每一个线程都提供了变量的副本，使得每个线程在某一时间访问到的并不是同一个对象。

## ThreadLocal使用过程分析
1、创建ThreadLocal对象
```
public ThreadLocal() {
}
```
它需要指定一个泛型作为保存数据的类型。

2、使用ThreadLocal保存的数据
```
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}
```
在set方法中，首先获取当前线程对象，然后获取与当前线程对象绑定的ThreadLocalMap对象，如果该对象为空，就创建一个并设置到Thread对象中去，如果不为空，则把数据设置到ThreadLocalMap对象中去，由此可见，真正保存数据的是ThreadLocalMap对象，且与线程对象绑定的也是ThreadLocalMap对象，并不是表面上的ThreadLocal对象；

ThreadLocalMap是ThreadLocal中的内部类，其中有一个属性为Entry数组，这个数组就是保存线程数据的容器：
```
ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    table = new Entry[INITIAL_CAPACITY];
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    size = 1;
    setThreshold(INITIAL_CAPACITY);
}
```
table属性类型为Entry[]，初始容量为INITIAL_CAPACITY = 16。

Entry是ThreadLocalMap的内部类，继承自`WeakReference<ThreadLocal<?>>`，它会把保存数据的ThreadLocal对象作为key，数据对象作为值保存起来，且对ThreadLocal对象是弱引用；
```
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

3、从ThreadLocal中取出数据
```
public T get() {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```
在get方法中，首先获取当前线程对象，然后在Thread对象中获取ThreadLocalMap对象，再把当前的ThreadLocal对象作为key值，从Entry数组中获取保存的数据（这其中会有hash算法，将key值转换为数组索引）;

一个ThreadLocal可以为多个线程保存隔离的数据，是因为ThreadLocal为每个线程创建了一个ThreadLocalMap对象；
多个ThreadLocal可以为多个线程保存隔离的数据，是因为不同的ThreadLocal保存的数据，对应每个线程的ThreadLocalMap中Entry数组的不同位置；
