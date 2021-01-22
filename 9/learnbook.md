<!--
 * @Author: your name
 * @Date: 2021-01-21 16:49:46
 * @LastEditTime: 2021-01-21 17:29:56
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \LinuxServerCodes\9\learnbook.md
-->
# 9 IO复用

IO复用使得程序能同时监听多个文件描述符，这对提高程序的性能至关重要。IO复用虽然能同时监听多个文件描述符，但它本身是阻塞的。并且当多个文件描述符同时就绪时，如果不采取额外措施程序就只能按顺序依次处理。

## 9.1 select
select的用途是在一段指定时间内，监听用户感兴趣的文件描述符上的可读、可写和异常等事件。
```C++
int select(int nfds,fd_set* readfds,fd_set* writefds,fd_set* exceptfds,struct timeval* timeout)
```

## 9.2 poll

poll系统调用和select类似，也是在指定时间内轮询一定数量的文件描述符，以测试其中是否有就绪者。
```C++
int poll(struct pollfd* fds,nfds_t nfds,int timeout);
```

## 9.3 epoll
epoll是Linux特有的IO复用函数，与select和poll有较大差异。首先，epoll使用一组函数而不是一个函数来完成任务，其次epoll把用户关心的文件描述符上的事件放在内核的一个事件表中，而不像select和poll那样每次调用都要重复传入。epoll需要使用一个额外的文件描述符来唯一标示内核中的这个事件表。

epoll对文件描述符的操作有两种模式,LT(Level Trigger,电平触发)模式和ET（Edge Trigger，边沿触发）模式
LT模式当epoll_wait检测到事件发生并将此事件通知应用程序后，应用程序可以不立即处理，下一次调用epoll_wait时还会通告。
ET模式则必须处理下次不会通告。可见ET降低了epoll被触发的次数，效率要比LT高。

一个socket的某个事件可能被触发多次，这就可能出现两个线程同时操作一个socket的局面。对于注册了EPOLLONESHOT事件的文件描述符，操作系统最多触发其上注册的一个可读、可写或者异常事件，且只触发一次。
## 9.4 三者对比


![](9-2.png)

## 9.6 聊天室程序
像ssh这样的登录服务通常要同时处理网络连接和用户输入，这也可以用IO复用来实现。
## 9.7 同时处理TCP和UDP服务
如果服务器要同时处理某个端口上的TCP和UDP请求，则需要创建两个不同的socket,一个是流socket,另一个是数据报socket,并将它们都绑定到该端口上。在使用epoll监听这两个文件描述符