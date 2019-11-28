[TOC]



##### Boolean

###### 类的属性

```java
public final class Boolean implements java.io.Serializable,
                                      Comparable<Boolean>
{
    public static final Boolean TRUE = new Boolean(true);
    public static final Boolean FALSE = new Boolean(false);
    // 获取该类的原始类
    public static final Class<Boolean> TYPE = (Class<Boolean>) Class.getPrimitiveClass("boolean");
    // Boolean的值
    private final boolean value;
}
```

##### Byte

###### 类的属性

```java
public final class Byte extends Number implements Comparable<Byte> {
    // 最小值
    public static final byte MIN_VALUE = -128;
    // 最大值
    public static final byte MAX_VALUE = 127;
    // 获取该类的原始类
    public static final Class<Byte> TYPE = (Class<Byte>) Class.getPrimitiveClass("byte");
    // Byte的值
    private final byte value;
    // 位数
    public static final int SIZE = 8;
    // 字节数
    public static final int BYTES = SIZE / Byte.SIZE;
}
```

###### 缓存

```java
// 加载Byte类时，将-128~127加载到内存中
private static class ByteCache {
    private ByteCache(){}

    static final Byte cache[] = new Byte[-(-128) + 127 + 1];
	// 静态代码块，类加载时执行，初始化cache数组
    static {
        for(int i = 0; i < cache.length; i++)
            cache[i] = new Byte((byte)(i - 128));
    }
}
```

##### Short

###### 类的属性

```java
public final class Short extends Number implements Comparable<Short> {
    // 最小值-2^15
    public static final short MIN_VALUE = -32768; 
    // 最大值2^15-1
    public static final short MAX_VALUE = 32767;
    // 获取该类的原始类
    public static final Class<Short> TYPE = (Class<Short>) Class.getPrimitiveClass("short"); 
    // Short的值
    private final short value;
    // 位数
    public static final int SIZE = 16;
    // 字节数，占用2个字节
    public static final int BYTES = SIZE / Byte.SIZE;
}
```

###### 缓存

```java
// 加载Short类时，将-128~127加载到内存中
private static class ShortCache {
    private ShortCache(){}

    static final Short cache[] = new Short[-(-128) + 127 + 1];

    static {
        for(int i = 0; i < cache.length; i++)
            cache[i] = new Short((short)(i - 128));
    }
}
```

###### valueOf方法

```java
public static Short valueOf(short s) {
    final int offset = 128;
    int sAsInt = s;
    if (sAsInt >= -128 && sAsInt <= 127) { // must cache
        // 从缓存获取
        return ShortCache.cache[sAsInt + offset];
    }
    // 创建实例对象
    return new Short(s);
}
```

##### Integer

###### 类的属性

```java
public final class Integer extends Number implements Comparable<Integer> {
	// 最小值-2^31
    @Native public static final int MIN_VALUE = 0x80000000;
    // 最大值2^31-1
	@Native public static final int MAX_VALUE = 0x7fffffff;
    // 获取该类的原始类
    public static final Class<Integer>  TYPE = (Class<Integer>) Class.getPrimitiveClass("int");
    // Integer的值
    private final int value;
    // 位数
    @Native public static final int SIZE = 32;
    // 字节数，占用4个字节
    public static final int BYTES = SIZE / Byte.SIZE;
}
```

###### 缓存

```java
// 加载Integer类时，将-128~high加载到内存中
private static class IntegerCache {
    static final int low = -128;
    static final int high;
    static final Integer cache[];

    static {
        // high value may be configured by property
        int h = 127;
        String integerCacheHighPropValue =
            sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
        if (integerCacheHighPropValue != null) {
            try {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            } catch( NumberFormatException nfe) {
                // If the property cannot be parsed into an int, ignore it.
            }
        }
        high = h;

        cache = new Integer[(high - low) + 1];
        int j = low;
        for(int k = 0; k < cache.length; k++)
            cache[k] = new Integer(j++);

        // range [-128, 127] must be interned (JLS7 5.1.7)
        assert IntegerCache.high >= 127;
    }

    private IntegerCache() {}
}
```

##### Long

###### 类的属性

```java
public final class Long extends Number implements Comparable<Long> {
    // 最小值-2^63
    @Native public static final long MIN_VALUE = 0x8000000000000000L;
    // 最大值2^63-1
    @Native public static final long MAX_VALUE = 0x7fffffffffffffffL;
    // 获取该类的原始类
    public static final Class<Long> TYPE = (Class<Long>) Class.getPrimitiveClass("long");
    // Long的值
    private final long value;
    // 位数
    @Native public static final int SIZE = 64;
    // 字节数，占用8个字节
    public static final int BYTES = SIZE / Byte.SIZE;
}
```

###### 缓存

```java
// 加载Long类时，将-128~127加载到内存中
private static class LongCache {
    private LongCache(){}

    static final Long cache[] = new Long[-(-128) + 127 + 1];

    static {
        for(int i = 0; i < cache.length; i++)
            cache[i] = new Long(i - 128);
    }
}
```

