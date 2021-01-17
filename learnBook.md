
# 第五章

socket地址API、socket基础API、网络信息API

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
