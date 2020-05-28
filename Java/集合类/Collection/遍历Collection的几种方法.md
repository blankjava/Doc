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


