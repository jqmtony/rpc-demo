一、RPC简介
RPC（Remote Procedure Call）——远程过程调用，它是一种通过网络从远程计算机程序上请求服务。RPC是与语言无关的。可用于：高并发程序，将程序各层分离以提高程序的负载；公共模块，为其他模块提供服务等。

1.调用客户端句柄，执行传送参数
2.调用本地系统内核发送网络消息
3.消息传送到远程主机
4.服务器句柄得到消息并取得参数
5.执行远程过程
6.执行的过程将结果返回服务器句柄
7.服务器句柄返回结果，调用远程系统内核
8.消息传回本地主机
9.客户句柄由内核接收消息
10.客户接收句柄返回的数据

如图所示，假设客户端1程序里在调用sayHello()方法，对于客户端1而言调用sayHello()方法就像调用本地方法一样。但从上图可以看出实际上客户端1调用的是服务器中的sayHello()方法，RPC屏蔽了底层的实现细节，让调用者无需关注网络通信、数据传输等细节。


二、RPC框架的实现
1. 技术简介
RPC的核心就是能够让本地应用简单、高效地调用远程服务。一般从以下几方面考虑：
(1)远程服务代理：本地调用的方法(服务)实质是远程方法的本地代理，因此会需要一个远程服务代理对象。在Java中，远程服务代理对象可以使用JDK动态代理实现。
(2)通信模型：客户端与服务器之间的通信方式。Java中一般基于BIO或NIO。
(3)服务定位：通过IP与端口确定服务器后，再通过接口名及方法名、参数等确定具体的服务。
(4)参数序列化：将接口名、方法名、方法参数类型等要进行网络传输的数据，需要转换成二进制传输，这里可能需要不同的序列化技术方案。如:Protostuff，java原生序列化等。
2.Java简单实现需要技术
(1)JDK动态代理
(2)Socket通信（BIO方式）
(3)反射、注解
(4)Java序列化（注意所有需要序列化对象必须实现Serializable接口）

3.RPC框架架构
(1)服务提供者：由框架提供，运行在服务器，发布服务、接受客户端请求、响应请求
(2)服务调用者：由框架提供，运行在客户端，生成代理对象、调用远程服务
(3)服务具体实现：由使用框架者提供，运行在服务器，服务接口、接口实现类

4.具体实现
(1)服务提供者：
RpcServer发布服务、接受客户端请求

RpcService响应请求

注解类RpcService标识提供服务类

RpcRequest请求参数实体

RpcResponse 响应实体

(2)服务调用者：
RpcClientProxy创建代理对象、拦截代理对象执行方法

RpcClient链接服务器、调用服务

(3)服务具体实现：
Person实体

PersonService服务接口

PersonServiceImpl 服务接口实现类

ClientTest客户端测试类、ServerTest服务器测试类

(4)结果

五、可以改进的地方
     可以考虑从下面几种方法改进：
1.可以使用NIO或NIO框架Netty替代BIO实现。BIO的传输速度比NIO快，		但BIO同步阻塞式IO，BIO要能够同时处理多个客户端请求，就必须使用多线程，		即每次accept阻塞等待来自客户端请求，一旦受到连接请求就建立通信套接字同		时开启一个新的线程来处理这个套接字的数据读写请求，然后立刻又继续accept		等待其他客户端连接请求，即为每一个客户端连接请求都创建一个线程来单独处		理，虽然此时服务器具备了高并发能力，即能够同时处理多个客户端请求了，但是却带来		了一个问题，随着开启的线程数目增多，将会消耗过多的内存资源，导致服务器变慢甚至		崩溃，NIO可以一定程度解决这个问题。
2.使用开源的序列化机制，如Google Protostuff与Hadoop Avro等。Java原生		的序列化机制占内存太多，运行效率也不高，要传输类都得实例Serializable接		口。
3.服务注册可以使用Zookeeper进行管理，能够让应用更加稳定。

