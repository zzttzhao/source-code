[TOC]



##### Class

```java
public final class Class<T> implements java.io.Serializable,
                              GenericDeclaration,
                              Type,
                              AnnotatedElement {
	// 为类创建一个实例，调用的是此类的默认构造方法（没有默认无参构造器会报错）
    public T newInstance()
        throws InstantiationException, IllegalAccessException {}
    // 返回String形式的该类的名称
	public String getName() {}
	// 
    public ClassLoader getClassLoader() {}
    //                      
	public native Class<? super T> getSuperclass();
    //
    
                              
                              
                              
                              
                              
                              
                              
                              
                              
                              
}
```

##### Field

```java
// 在程序运行状态中，动态地获取或设置字段信息
public final
class Field extends AccessibleObject implements Member {
    
    
}
```

##### Method

```java
public final class Method extends Executable {
}
```

