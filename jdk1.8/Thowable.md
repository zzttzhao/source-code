[TOC]



##### Thowable

###### 类的属性

```java
public class Throwable implements Serializable {
    // 保存栈回溯信息
    private transient Object backtrace;
    // 描述异常的信息
    private String detailMessage;
    // 共享的空堆栈信息
    private static final StackTraceElement[] UNASSIGNED_STACK = new StackTraceElement[0];
    // 引起此Throwable抛出的源Throwable对象
    private Throwable cause = this;
    // 此Throwable关联的堆栈信息
    private StackTraceElement[] stackTrace = UNASSIGNED_STACK;
    private static final List<Throwable> SUPPRESSED_SENTINEL =
        Collections.unmodifiableList(new ArrayList<Throwable>(0));
    // 被抑制的异常对象列表
    private List<Throwable> suppressedExceptions = SUPPRESSED_SENTINEL;
    // 用于抑制空指针异常的消息
    private static final String NULL_CAUSE_MESSAGE = "Cannot suppress a null exception.";
    private static final String SELF_SUPPRESSION_MESSAGE = "Self-suppression not permitted";
    // 导致异常堆栈跟踪的标题信息 
    private static final String CAUSE_CAPTION = "Caused by: ";
    // 被抑制的异常堆栈跟踪的标题信息
    private static final String SUPPRESSED_CAPTION = "Suppressed: ";
}
```

##### Error

```java
// 系统错误，程序无法处理
public class Error extends Throwable {
}
```

##### Exception

```java
// 异常，程序可以处理
// 分为CheckedException(编译时异常，需要用try...catch显式捕获)和RuntimeException(运行时异常)
public class Exception extends Throwable {
}
```

