## Collection接口
Collection 接口是List、Set 和Queue 接口的父接口，该接口里定义的方法既可用于操作Set、List和Queue集合。JDK1.8中Collection接口里定义了如下操作集合元素的方法。
```

```
集合类就像容器，现实中的容器功能，无非就是添加对象、删除对象、清空容器、遍历、判断容器是否为空等，集合类就是为这些功能提供了方法；

## Iterable接口
Iterable接口是Collection接口的父接口，在JDK1.8之前，Iterable接口中只定义了iterator方法，用于返回集合对应的迭代器，自JDK1.8开始，Iterable接口中增加了forEach和spliterator默认方法，方法用途如下；
```
public interface Iterable<T> {
    /**
     * Returns an iterator over elements of type {@code T}.
     *
     * @return an Iterator.
     */
    Iterator<T> iterator();

    /**
     * Performs the given action for each element of the {@code Iterable}
     * until all elements have been processed or the action throws an
     * exception.  Unless otherwise specified by the implementing class,
     * actions are performed in the order of iteration (if an iteration order
     * is specified).  Exceptions thrown by the action are relayed to the
     * caller.
     *
     * @implSpec
     * <p>The default implementation behaves as if:
     * <pre>{@code
     *     for (T t : this)
     *         action.accept(t);
     * }</pre>
     *
     * @param action The action to be performed for each element
     * @throws NullPointerException if the specified action is null
     * @since 1.8
     */
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }

    /**
     * Creates a {@link Spliterator} over the elements described by this
     * {@code Iterable}.
     *
     * @implSpec
     * The default implementation creates an
     * <em><a href="Spliterator.html#binding">early-binding</a></em>
     * spliterator from the iterable's {@code Iterator}.  The spliterator
     * inherits the <em>fail-fast</em> properties of the iterable's iterator.
     *
     * @implNote
     * The default implementation should usually be overridden.  The
     * spliterator returned by the default implementation has poor splitting
     * capabilities, is unsized, and does not report any spliterator
     * characteristics. Implementing classes can nearly always provide a
     * better implementation.
     *
     * @return a {@code Spliterator} over the elements described by this
     * {@code Iterable}.
     * @since 1.8
     */
    default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
    }
}
```

## 使用forEach循环遍历Collection集合
```
public void testForEach1() {
    Collection<Person> collection = new ArrayList<>();
    collection.add(new Person("aa", 11));
    collection.add(new Person("bb", 22));
    collection.add(new Person("cc", 33));
    for (Person person : collection) {
        System.out.println(person);
        if ("bb".equals(person.name)) {
            //修改元素属性成功，说明是集合元素的引用
            person.age = 12;
            //修改元素无效
            person = new Person("dd", 44);
        }
        if ("cc".equals(person.name)) {
            collection.remove(person);//ConcurrentModificationException
        }
    }
    System.out.println(collection);
}
```
forEach循环遍历Collection集合时，不能同时删除集合中的元素，否则会报ConcurrentModificationException异常；

在JDK1.8中，Iterable接口定义一个默认方法forEach，其实现也是使用forEach循环遍历集合，所以可以简化代码如下:
```
public void testForEach2() {
    Collection<Person> collection = new ArrayList<>();
    collection.add(new Person("aa", 11));
    collection.add(new Person("bb", 22));
    collection.add(new Person("cc", 33));
    collection.forEach(new Consumer<Person>() {
        @Override
        public void accept(Person person) {
            System.out.println(person);
            if ("bb".equals(person.name)) {
                //修改元素属性成功，说明是集合元素的引用
                person.age = 12;
                //修改元素无效
                person = new Person("dd", 44);
            }
            if ("cc".equals(person.name)) {
                collection.remove(person);//ConcurrentModificationException
            }
        }
    });
    System.out.println(collection);
}
```
## 使用Iterator遍历Collection集合
```
public void testIterator() {
    Collection<Person> collection = new ArrayList<>();
    collection.add(new Person("aa", 11));
    collection.add(new Person("bb", 22));
    collection.add(new Person("cc", 33));
    Iterator<Person> iterator = collection.iterator();
    Person person;
    while (iterator.hasNext()) {
        person = iterator.next();
        System.out.println(person);
        if ("bb".equals(person.name)) {
            //修改元素属性成功，说明是集合元素的引用
            person.age = 12;
            //修改元素无效
            person = new Person("dd", 44);
        }
        if ("aa".equals(person.name)) {
            iterator.remove();//删除成功
        }
        if ("cc".equals(person.name)) {
            collection.remove(person);//ConcurrentModificationException
        }
    }
    System.out.println(collection);
}
```
Iterator遍历集合时，也不允许同时删除集合中的元素，但是可以使用Iterator的remove方法进行删除元素，其原理与Collection的子类对iterator方法的实现有关，例如ArrayList的iterator方法返回类型如下：
```
private class Itr implements Iterator<E> {
    int cursor;       // index of next element to return
    int lastRet = -1; // index of last element returned; -1 if no such
    int expectedModCount = modCount;

    Itr() {}

    public boolean hasNext() {
        return cursor != size;
    }

    @SuppressWarnings("unchecked")
    public E next() {
        checkForComodification();
        int i = cursor;
        if (i >= size)
            throw new NoSuchElementException();
        Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length)
            throw new ConcurrentModificationException();
        cursor = i + 1;
        return (E) elementData[lastRet = i];
    }

    public void remove() {
        if (lastRet < 0)
            throw new IllegalStateException();
        checkForComodification();

        try {
            ArrayList.this.remove(lastRet);
            cursor = lastRet;
            lastRet = -1;
            expectedModCount = modCount;
        } catch (IndexOutOfBoundsException ex) {
            throw new ConcurrentModificationException();
        }
    }

    @Override
    @SuppressWarnings("unchecked")
    public void forEachRemaining(Consumer<? super E> consumer) {
        Objects.requireNonNull(consumer);
        final int size = ArrayList.this.size;
        int i = cursor;
        if (i >= size) {
            return;
        }
        final Object[] elementData = ArrayList.this.elementData;
        if (i >= elementData.length) {
            throw new ConcurrentModificationException();
        }
        while (i != size && modCount == expectedModCount) {
            consumer.accept((E) elementData[i++]);
        }
        // update once at end of iteration to reduce heap write traffic
        cursor = i;
        lastRet = i - 1;
        checkForComodification();
    }

    final void checkForComodification() {
        if (modCount != expectedModCount)
            throw new ConcurrentModificationException();
    }
}
```
自JDK1.8起，Iterator中增加了forEachRemaining默认方法，同样可以遍历Collection集合：
```
public void testIteratorForEachRemaining() {
    Collection<Person> collection = new ArrayList<>();
    collection.add(new Person("aa", 11));
    collection.add(new Person("bb", 22));
    collection.add(new Person("cc", 33));
    Iterator<Person> iterator = collection.iterator();
    iterator.forEachRemaining(new Consumer<Person>() {
        @Override
        public void accept(Person person) {
            System.out.println(person);
            if ("bb".equals(person.name)) {
                //修改元素属性成功，说明是集合元素的引用
                person.age = 12;
                //修改元素无效
                person = new Person("dd", 44);
            }
            if ("aa".equals(person.name)) {
                iterator.remove();//IllegalStateException
            }
            if ("cc".equals(person.name)) {
                collection.remove(person);//ConcurrentModificationException
            }
        }
    });
    System.out.println(collection);
}
```
通过forEachRemaining遍历集合时，不允许同时使用Iterator的remove方法删除元素，否则会报IllegalStateException异常；

## 使用removeIf方法遍历Collection集合
```
public void testRemoveIf() {
    Collection<Person> collection = new ArrayList<>();
    collection.add(new Person("aa", 11));
    collection.add(new Person("bb", 22));
    collection.add(new Person("cc", 33));
    collection.removeIf(new Predicate<Person>() {
        @Override
        public boolean test(Person person) {
            System.out.println(person);
            if ("bb".equals(person.name)) {
                //修改元素属性成功，说明是集合元素的引用
                person.age = 12;
                //修改元素无效
                person = new Person("dd", 44);
            }
            if ("cc".equals(person.name)) {
                collection.remove(person);//ConcurrentModificationException
            }
            return false;//返回true表示删除当前元素
        }
    });
    System.out.println(collection);
}
```
同样的，在遍历集合时，removeIf方法也不允许直接用Collection的remove方法删除集合中的元素；  
removeIf方法是JDK1.8中Collection集合新增的默认方法，如果在Predicate类的test方法中返回true，就会删除当前元素，其实现如下：
```
default boolean removeIf(Predicate<? super E> filter) {
    Objects.requireNonNull(filter);
    boolean removed = false;
    final Iterator<E> each = iterator();
    while (each.hasNext()) {
        if (filter.test(each.next())) {
            each.remove();
            removed = true;
        }
    }
    return removed;
}
```
从上面的程序中可以看出，removeIf方法其原理是使用了Iterator进行遍历和元素删除；


## 使用Stream遍历Collection集合
```
public void testStream() {
    Collection<Person> collection = new ArrayList<>();
    collection.add(new Person("aa", 11));
    collection.add(new Person("bb", 22));
    collection.add(new Person("cc", 33));
    Stream<Person> stream = collection.stream();
    stream.forEach(new Consumer<Person>() {
        @Override
        public void accept(Person person) {
            System.out.println(person);
        }
    });
}
```
在JDK1.8中增加了默认方法stream()，可以将Collection集合作为流处理的数据源；


