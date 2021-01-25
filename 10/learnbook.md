<!--
 * @Author: your name
 * @Date: 2021-01-22 14:52:08
 * @LastEditTime: 2021-01-22 16:51:44
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \LinuxServerCodes\10\learnbook.md
-->
# 10 信号
信号是由用户、系统或者进程发送给目标进程的信息，以通知目标进程某个状态的改变或系统异常。
Linux信号可由如下条件产生：
- 对于前台进程用户通过输入特殊的终端字符给它发送信号。比如Ctrl+C通常会给进程发送中断信号
- 系统异常。比如浮点异常和非法内存段访问
- 系统状态变化。 比如alarm定时器到期引起SIGALRM信号
- 运行kill命令或调用kill函数

# 10.1 Linux信号概述
```C++
int kill(pid_t pid,int sig);
```
该函数把信号sig发送给目标进程，目标进程由pid参数指定。

如下所示，信号处理函数只带一个整型参数，该参数用来指示信号类型。信号处理函数应该是可重入的，否则很容易引发竞态条件，所以在信号处理函数中严禁调用不安全的函数。
```C++
typedef void(*_sighandler_t) (int);
```
除用户自定义信号处理函数外，bits/signum.h头文件还定义了信号的两种处理方式`SIG-IGN`和`SIG_DF`,前者表示忽略目标信号，后者表示使用信号的默认处理方式。信号的默认处理方式主要有：`结束进程（Term)、忽略信号(Ign)、结束进程并生成核心转储文件（Core)、暂停进程（Stop),以及继续进程(Cont)`。
# 10.2 信号函数

要为一个信号设置处理函数可以使用下面的signal系统调用
```C++
_sighandler_t signal(int sig,_sighandler_t _handler)
```
更健壮的借口是如下的系统调用
```C++
int sigaction(int sig,const struct sigaction* act,struct sigaction* oact);
```
sigaction结构体的sa_hander成员指定信号处理函数，sa_mask成员设置进程的信号掩码，以确定哪些信号不能发送给本进程，sa_flags成员用于设置程序收到信号时的行为。
# 10.3信号集
Linux使用数据结构`sigset_t`来表示一组信号，`sigset_t`实际上是一个长整型数组，数组的每个元素的每个位表示一个信号。这种定义方式和文件描述符集`fd_set`类似。
- sigemptyset清空信号集
- sigfillset在信号集中设置所有信号
- sigaddset将信号添加到信号集
- sigdelset将信号从信号集中删除
- sigismember测试是否在信号集中

前文说到我们可以用sigaction结构体的sa_mask成员来设置进程的信号掩码，此外还可以用如下函数设置和查看进程的信号掩码
```C++
int sigprocmask(int _how,_const sigset_t* _set,sigset_t* _oset);
```
设置进程掩码后被屏蔽的信号将不能被进程接受。如果给进程发送一个被屏蔽的信号，则操作系统将该信号设置为进程的一个被挂起信号。如果我们取消对被挂起信号的屏蔽，则它能立即被进程接收到，用如下函数查询
```C++
int sigpending(sigset_t* set);
```
# 10.4 统一事件源
把信号的主要处理逻辑放到程序的主循环中，当信号处理函数被触发时，它只是简单地通知主循环程序接收到信号，并把信号值传递给主循环，主循环再根据接收到的信号值执行目标信号对应的逻辑代码。信号处理函数使用管道来将信号传递给主循环，所以同样可以使用IO复用和其他的IO事件一样被处理，称为统一事件源。
# 10.5 网络编程相关信号
## SIGHUP
当挂起进程的控制终端时，SIGHUP信号将被触发。对于没有控制终端的网格后台程序而言，它们通常利用SIGHUP信号来强制服务器重读配置文件。
## SIGPIPE
默认情况下，往一个读端关闭的管道或者socket连接中写数据将引发SIGPIPE信号
## SIGURG
在Linux环境下，内核通知应用程序带外数据到达主要有两种方法
- 使用第九章介绍的IO复用技术，select等系统调用在接收到带外数据时将返回，并向应用程序报告socket上的异常事件
- 使用SIGURG信号
