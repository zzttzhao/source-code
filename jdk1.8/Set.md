[TOC]



##### HashSet

###### 类的属性

```java
// 元素无序，不能重复
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
{
    static final long serialVersionUID = -5024744406713321676L;
    // 使用HashMap结构存储数据
    private transient HashMap<E,Object> map;
    // 添加元素时，用来作为HashMap中的value值
    private static final Object PRESENT = new Object();
}
```

##### LinkedHashSet

###### 类的属性

```java
// 使用LinkedHashMap结构存储数据，元素有序，不能重复
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {
    private static final long serialVersionUID = -2851667679971038690L;
}
```

##### TreeSet

###### 类的属性

```java
// 元素有序，不能重复
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable
{
    // 使用NavigableMap结构存储数据(TreeMap实现NavigableMap接口)
    private transient NavigableMap<E,Object> m;
    // 添加元素时，用来作为NavigableMap的value值
    private static final Object PRESENT = new Object();
    private static final long serialVersionUID = -2479143000061671589L;
}
```

