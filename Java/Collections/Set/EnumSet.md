## EnumSet类
EnumSet是一个专为枚举类设计的集合类，EnumSet中的所有元素都必须是指定枚举类型的枚举值，该枚举类型在创建EnumSet 时显式或隐式地指定。EnumSet的集合元素也是有序的，EnumSet以枚举值在Enum类内的定义顺序来决定集合元素的顺序。  
EnumSet在内部以位向量的形式存储，这种存储形式非常紧凑、高效，因此EnumSet对象占用内存很小，而且运行效率很好。尤其是进行批量操作（如调用containsAll() 和retainAll()方法）时，如果其参数也是EnumSet 集合，则该批量操作的执行速度也非常快。  
EnumSet集合不允许加入null元素，如果试图插入null元素，EnumSet将抛出NullPointerException异常。如果只是想判断EnumSet 是否包含null元素或试图删除null元素都不会抛出异常，只是删除操作将返回false, 因为没有任何null 元素被删除。  
EnumSet类是一个抽象类，没有暴露任何构造器来创建该类的实例，程序应该通过它提供的类方法来创建EnumSet对象（本质是创建EnumSet实现类的对象）。EnumSet 类它提供了如下常用的类方法来创建EnumSet 对象。
```
public abstract class EnumSet<E extends Enum<E>> extends AbstractSet<E>
        implements Cloneable, java.io.Serializable
{
    /**
     * 集合中元素的类型对象
     */
    final Class<E> elementType;

    /**
     * All of the values comprising T.  (Cached for performance.)
     */
    final Enum<?>[] universe;

    private static Enum<?>[] ZERO_LENGTH_ENUM_ARRAY = new Enum<?>[0];

    EnumSet(Class<E>elementType, Enum<?>[] universe) {
        this.elementType = elementType;
        this.universe    = universe;
    }

    /**
     * 创建一个元素类型为指定枚举类型的空EnumSet
     */
    public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
        Enum<?>[] universe = getUniverse(elementType);
        if (universe == null)
            throw new ClassCastException(elementType + " not an enum");

        if (universe.length <= 64)
            return new RegularEnumSet<>(elementType, universe);
        else
            return new JumboEnumSet<>(elementType, universe);
    }

    /**
     * 创建一个包含指定枚举类里所有枚举值的EnumSet 集合
     */
    public static <E extends Enum<E>> EnumSet<E> allOf(Class<E> elementType) {
        EnumSet<E> result = noneOf(elementType);
        result.addAll();
        return result;
    }

    /**
     * 创建一个与指定EnumSet 具有相同元素类型、相同集合元素的EnumSet集合
     */
    public static <E extends Enum<E>> EnumSet<E> copyOf(EnumSet<E> s) {
        return s.clone();
    }

    /**
     * 使用一个普通集合来创建EnumSet集合,但是要求普通集合中所有元素都是同一类型的枚举值，否则会抛出ClassCastException
     */
    public static <E extends Enum<E>> EnumSet<E> copyOf(Collection<E> c) {
        if (c instanceof EnumSet) {
            return ((EnumSet<E>)c).clone();
        } else {
            if (c.isEmpty())
                throw new IllegalArgumentException("Collection is empty");
            Iterator<E> i = c.iterator();
            E first = i.next();
            EnumSet<E> result = EnumSet.of(first);
            while (i.hasNext())
                result.add(i.next());
            return result;
        }
    }

    /**
     * 创建一个其元素类型与指定EnumSet 里元素类型相同的EnumSet 集合， 新EnumSet 集合包含原EnumSet 集合所不包含的、
     * 此枚举类剩下的枚举值（即新EnumSet 集合和原EnumSet 集合的集合元素加起来就是该枚举类的所有枚举值）
     */
    public static <E extends Enum<E>> EnumSet<E> complementOf(EnumSet<E> s) {
        EnumSet<E> result = copyOf(s);
        result.complement();
        return result;
    }

    /**
     * 创建一个包含一个或多个枚举值的EnumSet 集合，传入的多个枚举
     */
    @SafeVarargs
    public static <E extends Enum<E>> EnumSet<E> of(E first, E... rest) {
        EnumSet<E> result = noneOf(first.getDeclaringClass());
        result.add(first);
        for (E e : rest)
            result.add(e);
        return result;
    }

    /**
     * 创建一个包含从from枚举值到to枚举值范围内所有枚举值的EnumSet 集合
     */
    public static <E extends Enum<E>> EnumSet<E> range(E from, E to) {
        if (from.compareTo(to) > 0)
            throw new IllegalArgumentException(from + " > " + to);
        EnumSet<E> result = noneOf(from.getDeclaringClass());
        result.addRange(from, to);
        return result;
    }

    /**
     * 增加从from到to范围内的枚举值到EnumSet集合中
     */
    abstract void addRange(E from, E to);

    /**
     * Complements the contents of this enum set.
     */
    abstract void complement();
}
```


