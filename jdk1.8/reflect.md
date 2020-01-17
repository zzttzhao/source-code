[TOC]



##### Class

```java
public final class Class<T> implements java.io.Serializable,
                              GenericDeclaration,
                              Type,
                              AnnotatedElement {
    // 动态加载类
    public static Class<?> forName(String className)
                throws ClassNotFoundException {}
    // 为类创建一个实例，调用的是此类的默认构造方法（没有默认无参构造器会报错）
    public T newInstance()
        throws InstantiationException, IllegalAccessException {}
    // 返回String形式的该类的名称
    public String getName() {}
    // 获得类的类加载器
    public ClassLoader getClassLoader() {}
    // 获取类的父类                     
    public native Class<? super T> getSuperclass();
    // 获取类的特定方法，name--方法的名称，parameterTypes--方法的参数类型
    public Method getMethod(String name, Class<?>... parameterTypes)
        throws NoSuchMethodException, SecurityException {}
}
```

##### Field

```java
// 在程序运行状态中，动态地获取或设置字段信息
public final
class Field extends AccessibleObject implements Member {
    // 获取字段名
    public String getName() {}
    // 获取当前成员变量的修饰符
    public int getModifiers() {}
    // 返回一个 Class 对象，它标识了此Field对象所表示字段的声明类型
    public Class<?> getType() {}
}
```

##### Method

```java
public final class Method extends Executable {
    // 返回修饰该方法对象修饰符的整数形式
    public int getModifiers() {}
    // 调用method类代表的方法，obj--对象名，args--传入method方法的参数
    public Object invoke(Object obj, Object... args)
        throws IllegalAccessException, IllegalArgumentException, InvocationTargetException {}
}
```

