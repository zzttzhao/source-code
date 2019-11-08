[TOC]



###### 类的属性

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    transient int size = 0;
    transient Node<E> first;
	transient Node<E> last;
    private static final long serialVersionUID = 876323262645176354L;
}
```

###### Node结点

```java
private static class Node<E> {
    // 结点值
    E item;
    // 后继结点
    Node<E> next;
    // 前驱结点
    Node<E> prev;
}
```

