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

信号处理函数只带一个整型参数，该参数用来指示信号类型。信号处理函数应该是可重入的，否则很容易引发竞态条件，所以在信号处理函数中研究调用不安全的函数。
```C++
typedef void(*_sighandler_t) (int);
```

# 10.2 信号函数

要为一个信号设置处理函数可以使用下面的signal系统调用
```C++
_sighandler_t signal(int sig,_sighandler_t _handler)
```

# 10.4 统一事件源
