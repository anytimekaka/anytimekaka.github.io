## 基本概念

Java序列化是指把Java对象转换为字节序列的过程，反序列化就是把字节序列转换为Java对象的过程。

## 有什么作用

* 将对象序列化后可以持久化到硬盘上，需要的时候，使用反序列化读取对象
* 讲对象序列化转换成字节码之后可以在网络上传输，两个Java进程直接可以实现直接传输对象

## 使用JDK类库实现

> Class:java.io.ObjectOutputStream
> 
> Method:writeObject(Object obj)

ObjectOutputStream类的writeObject方法可以实现序列化