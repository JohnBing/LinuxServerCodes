
socket地址API、socket基础API、网络信息API
# 5.1

## 5.1.1
大端字节序又称网络字节序，是指一个整数的字节存储在内存的低地址处，主要永远网络传输。

小端字节序又称主机字节序，相反，主要用于本地主机处理。

linux提供了四种转换函数：htonl、htons、ntohl、ntohs用于长整型短整型字节序互相转换
## 5.1.2

```c++
#include <bits/socket.h>
struct sockaddr
{
    sa_family_t sa_family;
    char sa_data[14];
}
```
sa_family成员是地址族类型
sa_data用于存放socket地址值

## 5.1.3
所有专用socket地址在实际使用时都需要转换为通用socket地址类型sockaddr

## 5.1.4
IP地址的字符串和字节序整数转换函数
```
inet_addr
inet_aton
inet_ntoa是不可重入的
```
西面的这对函数同样能够完成上述三种函数的工作
```
inet_pton
inet_ntop
```

# 5.2

Linux一起皆是文件，socket同样也是一个文件描述符

```C++
int socktet(int domain,int type,int protocaol);
```

domain告诉系统应该使用哪一个底层协议族，该参数应该设置为PF_INET(Protocol Family of Internet)或PF_INET6,对于UNIX应该设置为PF_UNIX

type参数指定服务类型

protocol一般设置为0，表示使用默认协议

# 5.3

```C++
int bind(int sockfd,const struct sockaddr* my_addr,socklen_t addrlen)
```
bind讲my_addr指的socket地址分配给未命名的sockfd文件描述符

# 5.4

```C++
int listen(int sockfd,int backlog)
```

# 5.8
TCP数据读写 recv和send
UDP数据读写 recvfrom和sendto
通用数据读写 recvmsg和sendmsg

# 关于IO的理解
[IO模型的理解](https://www.cnblogs.com/felixzh/p/10345929.html)
io分两个阶段：
- 数据准备阶段
- 内核空间复制回用户进程缓冲区阶段
  一般来讲阻塞io模型、非阻塞io模型、io复用模型、信号驱动io模型都属于同步IO,因为阶段二是阻塞的。只有异步IO模型是符合POSIX异步IO操作含义的，不管在阶段一还是在阶段二都可以干别的事