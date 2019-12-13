[TOC]

##### ArrayList

###### 类的属性

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    private static final long serialVersionUID = 8683452581122892189L;
    // 默认初始容量
    private static final int DEFAULT_CAPACITY = 10;
    // 空数组实例，指定容量为0时返回
    private static final Object[] EMPTY_ELEMENTDATA = {};
    // 空数组实例，未指定容量时返回。第一次添加元素，数组会扩容（扩为默认初始容量10）
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
    // 存放元素的数组
    transient Object[] elementData; // non-private to simplify nested class access
    // 存放的元素个数
    private int size;
    // 最大容量
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
}
```

###### 扩容方法

```java
public void ensureCapacity(int minCapacity) {
    int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
        // any size if not default element table
        ? 0
        // larger than default for default empty table. It's already
        // supposed to be at default size.
        : DEFAULT_CAPACITY;

    if (minCapacity > minExpand) {
        ensureExplicitCapacity(minCapacity);
    }
}

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }

    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    // 扩为原来的1.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    // 新容量小于最小需要容量
    if (newCapacity - minCapacity < 0)
        // 新容量为最小需要容量
        newCapacity = minCapacity;
    // 新容量大于最大容量
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        // 最小需要容量大于最大容量，新容量为Interger.MAX_VALUE，否则新容量为最大容量
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```

##### LinkedList

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

##### Vector

###### 类的属性

```java
// 线程安全
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{
    // 存放元素的数组，默认容量大小为10
    protected Object[] elementData;
    // 存放的元素个数
    protected int elementCount;
    // 扩容增量，若未设置，默认扩为原容量的2倍
    protected int capacityIncrement;
    private static final long serialVersionUID = -2767605614048989439L;
    // 最大容量
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
}
```

###### 扩容方法

```java
public synchronized void ensureCapacity(int minCapacity) {
    if (minCapacity > 0) {
        modCount++;
        ensureCapacityHelper(minCapacity);
    }
}

private void ensureCapacityHelper(int minCapacity) {
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    // 当前容量
    int oldCapacity = elementData.length; // overflow-conscious code
    // 计算新容量
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                     capacityIncrement : oldCapacity);
    // 新容量小于最小需要容量
    if (newCapacity - minCapacity < 0)
        // 新容量设为最小需要容量
        newCapacity = minCapacity;
    // 新容量大于最大容量
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}

private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```

