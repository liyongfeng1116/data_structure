# java基础
# 数组  
- 构建数组  
1. Arrays.asList(n1,n2,n3)  
2. compareTo()比较大小
- 排序  
1. Arrays.sort(nums);  
# 字符串String
- 遍历方法
```
for (int i = 0; i < str.length(); i++) {
    str.charAt(i);
}
```
```
char[] c = str.toCharArray();
遍历char
```
- 切割方法 ``` str.substring(i,j)```
- char转String ：String.valueOf(c);
## StringBuilder类：可变的字符串，可以append

# HashMap
HashMap<String,Integer> map = new HashMap<>();  
map.put(String,Integer)  
map.get();   
大小：map.size()  
遍历键 keySet(); values();  
map.getOrDefault(key, V defaultValue); 找key对应的value  
map.containsKey()  

# int 和 string 相互转换
1. int 转 String   
    - String s = String.valueOf(i);
    - String s = Integer.toString(i);
    - String s = "" + i;
2. String 转 int
    - int i = Integer.parseInt(s);
# HashSet

# BIO和NIO的区别
- BIO(block IO)是普通的IO,同步阻塞，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销
- NIO（No block IO）是java 非阻塞IO。
- NIO三大核心部分：Channel（通道），Buffer（缓冲区），Selector（选择器）
- NIO基于Channel（通道）和Buffer（缓冲区）进行操作的，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。Selector（选择器）用于监听多个通道事件，因此使用单个线程就可以监听多个客户端通道

# 线程
- 线程的优先级分为 1~10 一共 10 个等级
- 可以在创建线程后设置
- 可以改变
## 进程间5种通信方式
- 管道：速度慢，容量有限，只有父子进程能通讯；
- FIFO：任何进程间都能通讯，但速度慢；
- 消息队列：容量受到系统限制，且要注意第一次读的时候，要考虑上一次没有读完数据的问题；
- 共享内存：能够很容易控制容量，速度快，但要保持同步，比如一个进程在写的时候，另一个进程要注意读写的问题；
- 信号量：不能传递复杂消息，只能用来同步。用于实现进程间的互斥与同步，而不是用于存储进程间通信数据。
## 局部变量指的是在方法中定义的变量，如果没有初始化，会有默认值（会报错）
## 进程得到的是除了代码段是与父进程共享以外，其他所有的都是得到父进程的一个副本，子进程的所有资源都继承父进程，得到父进程资源的副本，子进程可获得父进程的所有堆和栈的数据，但二者并不共享地址空间。两个是单独的进程，继承了以后二者就没有什么关联了，子进程单独运行。
# 计算机网络
## “ping”命令使用ICMP协议，该协议是TCP/IP协议簇的一个子协议
## 客户端和服务端流程
connect时发起三次握手   
客户端：socket——connect——send——receive——colsed   
服务端scoket——bind——listen——accept——receive——send——colse
![kehuduan](./pics/javaguide/%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%9C%8D%E5%8A%A1%E7%AB%AF.png)

# 线程的创建
1. 继承Thread类，其实Thread类中实现了Runable接口
2. 实现Runable接口，接口中必须实现run()方法
3. 线程创建顺序： new 新建线程，调用start()到可运行状态，

# java 执行顺序
1. Java中静态变量只能在类主体中定义，不能在方法中定义。 静态变量属于类所有而不属于方法。

2. 　静态块：用static申明，JVM加载类时执行，仅执行一次
　　构造块：类中直接用{}定义，每一次创建对象时执行
执行顺序优先级：静态块>main()>构造块>构造方法
3. 类的加载顺序
　　(1) 父类静态对象和静态代码块
　　(2) 子类静态对象和静态代码块
　　(3) 父类非静态对象和非静态代码块
　　(4) 父类构造函数
　　(5) 子类 非静态对象和非静态代码块
　　(6) 子类构造函数