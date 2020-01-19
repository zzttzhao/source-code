##### File

```java
public class File
    implements Serializable, Comparable<File>
{
    // 返回由此抽象路径名所表示的目录中的文件和目录的名称所组成字符串数组
    public String[] list() {}
    // 返回一个抽象路径名数组，这些路径名表示此抽象路径名所表示目录中的文件。
    public File[] listFiles() {}
}
```

##### InputStream

```java
// 抽象类，表示所有字节输入流实现类的基类
public abstract class InputStream implements Closeable {
    // 缓存区字节数组最大值
    private static final int MAX_SKIP_BUFFER_SIZE = 2048;
    // 从输入流中读取数据的下一个字节，以int返回
    public abstract int read() throws IOException;
    // 从输入流中读取数据的一定数量字节，并存储在缓存数组b
    public int read(byte b[]) throws IOException {
        return read(b, 0, b.length);
    }
    // 从输入流中读取数据最多len个字节，并存储在缓存数组b
    public int read(byte b[], int off, int len) throws IOException {}
}
```

##### OutputStream

```java
public abstract class OutputStream implements Closeable, Flushable {
}
```

##### Reader

```java
public abstract class Reader implements Readable, Closeable {
}
```

##### Writer

```java
public abstract class Writer implements Appendable, Closeable, Flushable {
}
```

