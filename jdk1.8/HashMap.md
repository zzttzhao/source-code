###### 类的属性

```java
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable {
    private static final long serialVersionUID = 362498820763181265L;
    // 默认初始容量16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
    // 最大容量
    static final int MAXIMUM_CAPACITY = 1 << 30;
    // 默认填充因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 桶(bucket)上的结点数大于这个值会转为红黑树
    static final int TREEIFY_THRESHOLD = 8;  
    // 桶(bucket)上的结点数小于这个值会转为链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 最小树形化容量阈值，哈希表的容量大于该值才允许将链表转为红黑树
    static final int MIN_TREEIFY_CAPACITY = 64;
    // 存放元素的数组，大小总为2的幂次方
    transient Node<K,V>[] table;
    // 存放具体元素的集合
    transient Set<Map.Entry<K,V>> entrySet;
    // 存放的元素个数
    transient int size;
    // 每次扩容和更改map结构的计数器
    transient int modCount;
    // 临界值，当实际大小(容量 * 填充因子)超过临界值，进行扩容
    int threshold;
    // 填充因子
    final float loadFactor;
}
```

