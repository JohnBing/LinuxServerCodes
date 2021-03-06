<!--
 * @Author: your name
 * @Date: 2021-01-23 11:30:13
 * @LastEditTime: 2021-01-23 16:31:05
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \LinuxServerCodes\14\learnbook.md
-->
# 14 多线程编程
早期Linux不支持线程，后来开发出了符合POSIX标准的线程库LinuxThreads，但是效率较低而且问题很多。而现在Linux上默认使用的线程库是NPTL(Native POSIX Thread Library)。
## 14.1 Linux线程模型
线程是程序中完成一个独立任务的完整执行序列，即一个可调度的实体。线程可分为内核线程和用户线程，内核线程由内核调度，用户线程由线程库调度。
## 14.2 创建和结束线程
- `pthread_create`用于创建线程
- 线程函数在结束时最好调用`pthread_exit`函数，以确保安全安静的退出
- 一个进程中的所有线程都可以调用`pthread_jion`函数来回收其他线程，即等待其他线程结束。这类似于回收进程的`wait`和`waitpid`
- 使用`pthread_cancel`来异常终止一个线程，即取消线程
## 14.3 线程属性
`pthread_attr_t`结构体用来获取和设置线程属性
## 14.4 POSIX信号量

我们将讨论3种专门用于线程同步的机制：POSIX信号量、互斥量和条件变量。在Linux上信号量API有两种，一个是System V IPC信号量，一个是POSIX信号量。二者接口相似但不保证能互换，语义完全相同。
## 14.5 互斥锁
互斥锁用于保护关键代码段以确保独占式访问，有点像二进制信号量。

在一个线程中对已经加锁的普通锁再次加锁将导致死锁。两个线程按照不同顺序申请两个互斥锁也容易产生死锁。
## 14.6 条件变量
如果说互斥锁是用于同步线程对共享数据的访问的话，那么条件变量则是用于在线程之间同步共享数据的值。当某个共享数据达到某个值时，唤醒等待这个共享数据的线程
## 14.8 多线程环境
如果一个函数能够被多个线程同时调用且不发生竞态条件，则我们称它是线程安全的，或者说是可重入的。

当某个线程调用fork函数时，产生的子进程只拥有一个执行线程且是调用fork的那个线程的完整复制。子进程将继承父进程中互斥锁的状态，若互斥锁被其他线程锁住，若调用fork的这个线程再次加锁将会导致死锁。

pthread提供了一个专门的函数`pthread_atfork`,以确保fork调用后父进程和子进程都拥有一个清楚的锁状态。

每个线程都可以独立设置信号掩码。设置进程信号掩码的函数是`sigprocmask`，在多线程环境下我们应使用pthread版本的`sigprocmask`来设置线程信号掩码
```C++
int pthread_sigmask(int how,const sigset_t* newmask,sigset_t* oldmask);
```