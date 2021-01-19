<!--
 * @Author: your name
 * @Date: 2021-01-19 10:45:37
 * @LastEditTime: 2021-01-19 15:02:43
 * @LastEditors: your name
 * @Description: In User Settings Edit
 * @FilePath: \LinuxServerCodes\6\learnbook.md
-->
Linux提供了很多高级的IO函数，并不像基础IO函数那么常用，但在特定条件下表现出优秀的性能
- 用于创建文件描述符的函数 pipe dup/dup2
- 用于读写数据的函数 readv/writev sendfile mmap/munmap splice tee
- 用于控制IO行为和属性的函数 fcntl
# 6.1

```C++
int pipe(int fd[2])
```
pipe函数可用于创建管道以实现进程间通信，其参数是一个包含两个int型整数的数组指针，fd[1]写入数据fd[0]读出数据

可以使用socketpair函数方便的创建双向管道

# 6.2
有时候需要把标准输入重定向到一个文件或者把标准输出重定向到一个网络链接，此时可以通过dup和dup2函数实现

# 6.3
readv函数将数据从文件描述符读到分散的内存块中，即分散读
writev将多块分散的内存一并写入文件描述符中，即集中写

# 6.4
sendfile函数在两个文件描述符之间直接传递数据，从而避免了内核缓冲区和用户缓冲区之间的数据拷贝，效率很高称为零拷贝

# 6.5
mmap用于申请一段内存空间，可以将这段内存作为进程间通信的共享内存也可以将文件直接映射到其中
munmap则释放mmap创建的这段内存空间

# 6.6
splice函数用于在两个文件描述符之间移动数据，也是零拷贝操作。使用splice时必须有一个文件描述符是管道文件描述符

# 6.7
tee函数在两个管道文件描述符之间复制数据，也是零拷贝操作。