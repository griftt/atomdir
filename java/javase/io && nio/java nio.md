参考链接：  http://ifeve.com/buffers/

#### java NIO 的基本组成
java nio（同步非阻塞）的基本原理：可用一个线程或者少量线程处理多个通道(cahnnel)的请求，
同步表现为：程序还是在当前线程运行，非阻塞是：程序同时监听着多个通道，不会一直在一个io上等着
1. ##### Channel
这些是Java NIO中最重要的通道的实现：

FileChannel
DatagramChannel：
SocketChannel  ：tcp客户端
ServerSocketChannel ：tcp服务端
FileChannel 从文件中读写数据。

DatagramChannel 能通过UDP读写网络中的数据。

SocketChannel 能通过TCP读写网络中的数据。

ServerSocketChannel可以监听新进来的TCP连接，像Web服务器那样。对每一个新进来的连接都会创建一个SocketChannel。
通道可以异步地读写。
通道中的数据总是要先读到一个Buffer，或者总是要从一个Buffer中写入。
```java
RandomAccessFile file=null;
        try {
        //示例从FileChannel中读取数据到buffer中
             file = new RandomAccessFile("d:/a.txt","rw");
            FileChannel channel = file.getChannel();
            //定义一个255字节缓存区间
            ByteBuffer allocate = ByteBuffer.allocate(255);
            //将通道内容写进缓存区
            int read = channel.read(allocate);
            while (read!=-1){
                System.out.println("read:"+read);
                allocate.flip();//模式转换，由写变为读
                while (allocate.hasRemaining()){
                    //每次拿一个字节
                    System.out.println(allocate.getChar());
                }
                allocate.clear();
                read = channel.read(allocate);
            }
            file.close();
        } catch (Exception e) {
                e.printStackTrace();
        }
```

2. ##### Buffer
数据缓冲区，需要从通道中先把数据放进缓存区再进行读写
channel.read(buffer);  读进buffer
channel.write(buffer);  写进tong通道

ByteBuffer
MappedByteBuffer
CharBuffer
DoubleBuffer
FloatBuffer
IntBuffer
LongBuffer
ShortBuffer
常用方法：
 flip():buffer由写变为读
  ,clear（）: 清除缓存取得内容，用于下次存储，
  rewind()：将指针返回初始位置,
   mark()： 用于在做标记，配合reset（）可让position会到标记位置
    get()：获取一个字节

3. #####  Scatter/Gather
Java NIO开始支持scatter/gather，scatter/gather用于描述从Channel（译者注：Channel在中文经常翻译为通道）中读取或者写入到Channel的操作。
分散（scatter）从Channel中读取是指在读操作时将读取的数据写入多个buffer中。因此，Channel将从Channel中读取的数据“分散（scatter）”到多个Buffer中。
聚集（gather）写入Channel是指在写操作时将多个buffer的数据写入同一个Channel，因此，Channel 将多个Buffer中的数据“聚集（gather）”后发送到Channel

scatter / gather经常用于需要将传输的数据分开处理的场合，例如传输一个由消息头和消息体组成的消息，你可能会将消息体和消息头分散到不同的buffer中，这样你可以方便的处理消息头和消息体。
(1)sacter demo

```java


ByteBuffer header = ByteBuffer.allocate(128);

ByteBuffer body   = ByteBuffer.allocate(1024);

ByteBuffer[] bufferArray = { header, body };

channel.read(bufferArray);
将channel的内容按顺序读进buffer数组里，第一读满了的，再开始读进下一个里面

```

(2) gather demo

``` java
ByteBuffer header = ByteBuffer.allocate(128);

ByteBuffer body   = ByteBuffer.allocate(1024);

//write data into buffers

ByteBuffer[] bufferArray = { header, body };

channel.write(bufferArray);

buffers数组是write()方法的入参，write()方法会按照buffer在数组中的顺序，将数据写入到channel，注意只有position和limit之间的数据才会被写入。因此，如果一个buffer的容量为128byte，但是仅仅包含58byte的数据，那么这58byte的数据将被写入到channel中。因此与Scattering Reads相反，Gathering Writes能较好的处理动态消息。
```

4. ##### Selector
 更像是一个通道管理器, 可以向其中注册各种通道，并且监听通道的各种状态，
IO的各种流是阻塞的。这意味着，当一个线程调用read() 或 write()时，该线程被阻塞，直到有一些数据被读取，或数据完全写入。该线程在此期间不能再干任何事情了。
 NIO的非阻塞模式，使一个线程从某通道发送请求读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取。而不是保持线程阻塞，
 所以直至数据变得可以读取之前，该线程可以继续做其他的事情。
  非阻塞写也是如此。一个线程请求写入一些数据到某通道，但不需要等待它完全写入，这个线程同时可以去做别的事情。
  线程通常将非阻塞IO的空闲时间用于在其它通道上执行IO操作，所以一个单独的线程现在可以管理多个输入和输出通道（channel）。

  多路复用是指:多个请求功能都通过一个线程处理了，如多个通道都由selector监听并处理，即由他处理读写（当爹又当妈的selector）等。
