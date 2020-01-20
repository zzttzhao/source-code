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
// 抽象类，表示所有字节输出流实现类的基类
public abstract class OutputStream implements Closeable, Flushable {
    // 将指定的字节写入输出流
    public abstract void write(int b) throws IOException;
    // 将指定的byte数组的字节全部写入输出流
    public void write(byte b[]) throws IOException {
        write(b, 0, b.length);
    }
    // 将指定的byte数组中从偏移量off开始的len个字节写入输出流
    public void write(byte b[], int off, int len) throws IOException {}
    // 刷新输出流，并强制写出所有缓冲的输出字节
    public void flush() throws IOException {}
    // 关闭输出流，并释放与该流有关的所有资源
    public void close() throws IOException {}
}
```

##### Reader

```java
public abstract class Reader implements Readable, Closeable {
    // 试图将字符读入指定的字符缓冲区
    public int read(java.nio.CharBuffer target) throws IOException {}
    // 读取单个字符
    public int read() throws IOException {}
    // 将字符读入数组
    public int read(char cbuf[]) throws IOException {
        return read(cbuf, 0, cbuf.length);
    }
    // 将字符读入数组的某一部分，chef--目标缓冲区，off--开始存储字符的偏移量，len--要读取的最多字符数
    abstract public int read(char cbuf[], int off, int len) throws IOException;
}
```

##### Writer

```java
public abstract class Writer implements Appendable, Closeable, Flushable {
    // 写入单个字符
    public void write(int c) throws IOException {}
    // 写入字符数组
    public void write(char cbuf[]) throws IOException {
        write(cbuf, 0, cbuf.length);
    }
    // 写入字符数组的某一部分
    abstract public void write(char cbuf[], int off, int len) throws IOException;
    // 写入字符串
    public void write(String str) throws IOException {
        write(str, 0, str.length());
    }
    // 写入字符串的某一部分
    public void write(String str, int off, int len) throws IOException {}
}
```

