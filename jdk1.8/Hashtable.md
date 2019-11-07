[TOC]



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

