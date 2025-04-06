# 基于C++实现的分布式RPC服务注册与调用系统



## 项目简介

通过学习Raft项目的RPC模块，自主实现了分布式环境下本地服务在RPC节点上的注 册、发布与远程调用功能。项目实现了自定义通信协议、服务注册中心、日志系统及高并发网络模型，具备高性能和良好的扩展性



## 技术栈

·C++11

·网络库muduo

·序列化protobuf

·服务注册与发现zookeeper

·构建工具CMake

·日志库Glog



## 项目架构

```
RPC/
│
├── src/                # 核心代码实现（Server, Client, Protocol等）
	├── include/            # 头文件
	├── Krpcapplication.cc/	#从test.conf中读取zookeeper的IP和端口
	├── Krpcconfig.cc/		#从配置文件中读取内容，获得k-v数据
	├── Krpcprovider.cc/	#初始化监听，启动RPC服务端
	├── zookeeperutil.cc/	#zookeeper客户端
	├── Krpcchannel.cc/		#建立TCP连接
├── example/            # 示例代码（服务端 / 客户端）
	├── callee/              
		├── Kserver.cc/              # 服务端
	├── caller/              
		├── Kclient.cc/              # 客户端
├── CMakeLists.txt      # 构建文件
└── README.md           # 项目说明

```



## 整体的框架



- **muduo库**：负责数据流的网络通信，采用了多线程epoll模式的IO多路复用，让服务发布端接受服务调用端的连接请求，并由绑定的回调函数处理调用端的函数调用请求。
- **Protobuf**：负责RPC方法的注册，数据的序列化和反序列化，相比于文本存储的XML和JSON来说，Protobuf是二进制存储，且不需要存储额外的信息，效率更高。
- **Zookeeper**：负责分布式环境的服务注册，记录服务所在的IP地址以及端口号，可动态地为调用端提供目标服务所在发布端的IP地址与端口号，方便服务所在IP地址变动的及时更新。
- **TCP沾包问题处理**：定义服务发布端和调用端之间的消息传输格式，记录方法名和参数长度，防止沾包。
- **Glog日志库**：后续增加了Glog的日志库，进行异步的日志记录。
