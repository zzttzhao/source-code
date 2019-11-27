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

