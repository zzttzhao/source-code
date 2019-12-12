[TOC]

##### HashMap

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
    // 临界值(容量 * 填充因子)，当实际大小（存放元素的个数）超过临界值，进行扩容
    int threshold;
    // 填充因子
    final float loadFactor;
}
```

###### Node结点

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;
}
```

###### hash值的计算

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

###### put方法

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // table为空或长度为0，扩容
    if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
    // 计算数组下标，确定元素放在哪个桶中；获取桶中的第一个元素（即数组中的结点），若为空，生成新的结点放入数组
    if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        // 若桶中的第一个元素与插入元素的hash值、key值相等，直接覆盖value
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        // 为红黑树结点
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 为链表结点
        else {
            for (int binCount = 0; ; ++binCount) {
                // 到达链表尾部
                if ((e = p.next) == null) {
                    // 在尾部插入新结点
                    p.next = newNode(hash, key, value, null);
                    // 结点数大于阈值，转为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                // 若链表中的结点与插入元素的hash值、key值相等，直接覆盖
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        // 在桶中找到与插入元素的hash值、key值相等的结点
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    // 实际大小大于阈值，扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

###### get方法

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 与桶中的第一个元素（即数组中的结点）hash值、key值相等
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        if ((e = first.next) != null) {
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}

```

###### resize扩容方法

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        // 若超过最大容量，不再扩容
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 扩为原来的2倍
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
    	Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        // 遍历
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                // 释放空间
                oldTab[j] = null;
                // 桶中只有一个元素（即数组中的结点）
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 原来的坐标没变化
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        // 原来的坐标增加oldCap
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

##### LinkedHashMap

###### 类的属性

```java
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
{
    private static final long serialVersionUID = 3801124242820219131L;
    // 指向双向链表的头结点
    transient LinkedHashMap.Entry<K,V> head;
    // 指向双向链表的尾结点
    transient LinkedHashMap.Entry<K,V> tail;
    // 指定迭代顺序
    // true表示基于访问顺序排序，最近访问过的元素放在链表最末尾；false表示按照插入顺序遍历
    final boolean accessOrder;
}
```

###### Entry结点

```java
// 继承HashMap的Node结点
static class Entry<K,V> extends HashMap.Node<K,V> {
    // 用于维护插入Entry的先后顺序
    Entry<K,V> before, after;
    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}
```

###### put方法

```java
// 调用HashMap的put方法，重写了部分方法
Node<K,V> newNode(int hash, K key, V value, Node<K,V> e) {
    LinkedHashMap.Entry<K,V> p =
        new LinkedHashMap.Entry<K,V>(hash, key, value, e);
    linkNodeLast(p);
    return p;
}

// 将当前插入的结点设为原始链表的尾结点
private void linkNodeLast(LinkedHashMap.Entry<K,V> p) {
    // 用临时变量last记录尾结点tail
    LinkedHashMap.Entry<K,V> last = tail;
    // 将尾结点设为当前插入的结点
    tail = p;
    // 原来的尾结点为bull，表示当前链表为空
    if (last == null)
        // 头结点为当前插入的结点
        head = p;
    else {
        // 原始链表不为空，将当前结点的上结点指向原来的尾结点
        p.before = last;
        // 原来的尾结点的下一个结点指向当前插入的结点
        last.after = p;
    }
}

// 把当前结点放到双向链表的尾部
void afterNodeAccess(Node<K,V> e) { // move node to last
    LinkedHashMap.Entry<K,V> last;
    // 基于访问顺序排序且当前结点不是尾结点
    if (accessOrder && (last = tail) != e) {
        // 记录当前节点的上一个节点b和下一个节点a
        LinkedHashMap.Entry<K,V> p =
            (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
        // 设置当前结点的下一个结点为null
        p.after = null;
        // 当前结点的前一个结点为null
        if (b == null)
            // 头结点为当前结点的下一个结点
            head = a;
        else
            // b的下一个结点指向a
            b.after = a;
        if (a != null)
            // a的前一个结点指向b
            a.before = b;
        else
            // b设为尾结点
            last = b;
        if (last == null)
            // 当前结点设为头结点
            head = p;
        else {
            // 将当前结点放到链表尾部
            p.before = last;
            last.after = p;
        }
        // 当前结点为尾结点
        tail = p;
        ++modCount;
    }
}
```

###### get方法

```java
public V get(Object key) {
    Node<K,V> e;
    if ((e = getNode(hash(key), key)) == null)
        return null;
    if (accessOrder)
        // 将访问过的元素放在链表尾部
        afterNodeAccess(e);
    return e.value;
}
```

##### Hashtable

###### 类的属性

```java
public class Hashtable<K,V>
    extends Dictionary<K,V>
    implements Map<K,V>, Cloneable, java.io.Serializable {
    // 存放元素的数组
    private transient Entry<?,?>[] table;
    // 存放的元素个数
    private transient int count;
    // 临界值(容量 * 填充因子)，默认初始容量11，当实际大小（存放元素的个数）超过临界值，进行扩容
    private int threshold;
    // 填充因子，默认为0.75
    private float loadFactor;
    private transient int modCount = 0;
    private static final long serialVersionUID = 1421746759512286392L;
    // 最大容量
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
}
```

###### Entry结点

```java
private static class Entry<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Entry<K,V> next;
}
```

###### put方法

```java
public synchronized V put(K key, V value) {
    // value不能为空
    if (value == null) { // Make sure the value is not null
        throw new NullPointerException();
    }

    // Makes sure the key is not already in the hashtable.
    Entry<?,?> tab[] = table;
    int hash = key.hashCode();
    // 计算数组下标，确定元素放在哪个桶中
    int index = (hash & 0x7FFFFFFF) % tab.length;
    @SuppressWarnings("unchecked")
    Entry<K,V> entry = (Entry<K,V>)tab[index];
    // 遍历
    for(; entry != null ; entry = entry.next) {
        // 若链表中的结点与插入元素的hash值、key值相等，直接覆盖
        if ((entry.hash == hash) && entry.key.equals(key)) {
            V old = entry.value;
            entry.value = value;
            return old;
        }
    }

    addEntry(hash, key, value, index);
    return null;
}

private void addEntry(int hash, K key, V value, int index) {
    modCount++;

    Entry<?,?> tab[] = table;
    if (count >= threshold) {
        // Rehash the table if the threshold is exceeded
        rehash();

        tab = table;
        hash = key.hashCode();
        index = (hash & 0x7FFFFFFF) % tab.length;
    }

    // Creates the new entry.
    @SuppressWarnings("unchecked")
    Entry<K,V> e = (Entry<K,V>) tab[index];
    // 插入链表头部
    tab[index] = new Entry<>(hash, key, value, e);
    count++;
}
```

###### get方法

```java
@SuppressWarnings("unchecked")
public synchronized V get(Object key) {
    Entry<?,?> tab[] = table;
    int hash = key.hashCode();
    // 计算数组下标，确定元素放在哪个桶中
    int index = (hash & 0x7FFFFFFF) % tab.length;
    // 遍历
    for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
        if ((e.hash == hash) && e.key.equals(key)) {
            return (V)e.value;
        }
    }
    return null;
}
```

###### rehash扩容方法

```java
@SuppressWarnings("unchecked")
protected void rehash() {
    int oldCapacity = table.length;
    Entry<?,?>[] oldMap = table;

    // overflow-conscious code
    // 扩容为原容量的2倍加1
    int newCapacity = (oldCapacity << 1) + 1;
    // 若超过最大容量，不再扩容
    if (newCapacity - MAX_ARRAY_SIZE > 0) {
        if (oldCapacity == MAX_ARRAY_SIZE)
            // Keep running with MAX_ARRAY_SIZE buckets
            return;
        newCapacity = MAX_ARRAY_SIZE;
    }
    Entry<?,?>[] newMap = new Entry<?,?>[newCapacity];

    modCount++;
    // 计算新阈值
    threshold = (int)Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
    table = newMap;

    for (int i = oldCapacity ; i-- > 0 ;) {
        for (Entry<K,V> old = (Entry<K,V>)oldMap[i] ; old != null ; ) {
            Entry<K,V> e = old;
            old = old.next;

            int index = (e.hash & 0x7FFFFFFF) % newCapacity;
            e.next = (Entry<K,V>)newMap[index];
            newMap[index] = e;
        }
    }
}
```

##### TreeMap

###### 类的属性

```java
public class TreeMap<K,V>
    extends AbstractMap<K,V>
    implements NavigableMap<K,V>, Cloneable, java.io.Serializable
{
    // key的比较器
    private final Comparator<? super K> comparator;
    // 红黑树的根节点
    private transient Entry<K,V> root;
    // 存放的元素个数
    private transient int size = 0;
    // 结构修改次数
    private transient int modCount = 0;
    // 红黑树的节点颜色
    private static final boolean RED   = false;
    private static final boolean BLACK = true;
}
```

###### Entry结点

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;
    V value;
    Entry<K,V> left;
    Entry<K,V> right;
    Entry<K,V> parent;
    boolean color = BLACK;
}
```

###### put方法

```java
public V put(K key, V value) {
    Entry<K,V> t = root;
    // 根节点为空，TreeMap中没有元素
    if (t == null) {
        compare(key, key); // type (and possibly null) check
        // 初始化一个结点，赋给root
        root = new Entry<>(key, value, null);
        size = 1;
        modCount++;
        return null;
    }
    int cmp;
    Entry<K,V> parent;
    // split comparator and comparable paths
    Comparator<? super K> cpr = comparator;
    // 比较器不为空
    if (cpr != null) {
        do {
            parent = t;
            // 比较新增结点的key和当前结点key的大小
            cmp = cpr.compare(key, t.key);
            // 新增结点的key小于当前结点的key
            if (cmp < 0)
                // 以当前结点的左子节点作为新的当前结点
                t = t.left;
            // 新增结点的key大于当前结点的key
            else if (cmp > 0)
                // 以当前结点的右子节点作为新的当前结点
                t = t.right;
            else
                // key相等，则新值覆盖旧值
                return t.setValue(value);
        } while (t != null);
    }
    else {
        if (key == null)
            throw new NullPointerException();
        @SuppressWarnings("unchecked")
        Comparable<? super K> k = (Comparable<? super K>) key;
        do {
            parent = t;
            cmp = k.compareTo(t.key);
            if (cmp < 0)
                t = t.left;
            else if (cmp > 0)
                t = t.right;
            else
                return t.setValue(value);
        } while (t != null);
    }
    Entry<K,V> e = new Entry<>(key, value, parent);
    if (cmp < 0)
        parent.left = e;
    else
        parent.right = e;
    fixAfterInsertion(e);
    size++;
    modCount++;
    return null;
}
```