---
title: UNIX网络编程
date: 2017-01-16 02:17:12
tags: 
  - unix
  - program
  - network
  - linux
---
unix网络编程知识总结

<!-- more -->

## socket编程简介

### 套接字地址结构
**POSIX通用数据类型**
数据类型|说明|头文件
--|--|--
int8_t | 带符号的8位整数| `<sys/types.>`
uint8_t | 无符号的8位整数 | `<sys/types.h>`
int16_t | 带符号的16位整数| `<sys/types.h>`
uint16_t | 无符号的16位整数 | `<sys/types.h>`
int32_t | 带符号的32位整数| `<sys/types.h>`
uint32_t | 无符号的32位整数 | `<sys/types.h>`
sa\_family\_t|套接字地址结构的地址组|`<sys/socket.h>`
socklen\_t|套接字地址长度的结构，一般为`uint32_t`|`<sys/socket.h>`
in\_addr\_t|IPv4地址，一般为`uint32_t`|`<netinet/in.h>`
in\_port\_t|端口号，一般为`uint16_t`|`<netinet/in.h>`


**IPv4网际套接字地址结构`重点`**
```cpp
struct in_addr
{
	in_addr_t saddr;           /* 32-bit IPV4 addr */
}

struct sockaddr_in
{
	uint8_t sin_len;            /* struct len(16) */
	sa_family_t sin_family;     /* AF_INET */
	in_port_t sin_port;        /* 16-bit port num */
	struct in_addr sin_addr;    /* 32bit-IPV4 addr */
	char sin_zero[8];           /* unused */
}

```
常用操作
```cpp
#include <netinet/in.h>
#include <string.h>

struct sockaddr_in addr;
bzero(&addr, sizeof(addr));
addr.sin_family = AF_INET;
addr.sin_addr.s_addr = htonl("0.0.0.0");
addr.sin_port = htons(1);

```
- sin_len 并不是所有的结构体都支持这个字段。即使有该字段，我们也无需设置他。
- POSIX规范只需要结构中的三个字段:sin_family、sin_addr和sin_port三个字段。起他额外字段都是可以接受的。
- 几乎所有实现都增加了sin_zero字段
- in\_port\_t和in\_addr\_t都是以网络字序存储的(大端存储)。
- 32bit IPv4地址有两种访问方式，既`struct in_addr` 类型或`in_addr_t`类型。
>sin\_addr并不是一个uint32类型的整数而是一个in\_addr类型的结构体是有一定历史原因的。在早期的时候，struct in\_addr被定义为多种结构的联合体。允许访问32bit中的任意4个字节，或者任意两个16bit的值。这些结构在地址被划分为A类B类C类的时候便于方便的获取适当的字节。而随着子网划分和无类子网的编排，各种地址类正在消失、那个联合体已经不在需要了。

- 套接字结构总是用在给定的主机中，虽然有些字段用来进行主机间通讯。

**通用套接字**
```cpp
struct sockaddr
{
    uint8_t sa_len;
    sa_family_t sa_family;
    char sa_data[14];
}
```
所有的套接字函数的套接字地址参数的类型是固定的，想要传入不同类型的套接字结构体指针，按照现在的思路可以强制转换成`void *`类型。但是这种格式是ASC C规定的，在82年的时候还不能使用这样的方式。所以引入了一个通用套接字结构。通用套接字结构完全是为了满足套接字函数可以传入不同类型的套接字结构体指针而设计的。

所有的linux提供的套接字函数调用的时候都必须强制转换成通用套接字。

IPv6
```cpp

```

**新的通用套接字**
不像`struct sockaddr`，新的`struct sockaddr_storage`足以容纳系统锁支持的任何套接字地址结构。`struct sockaddr_storage`结构定义在`<netinet/in.h>`中
```cpp
struct sockaddr_stroage
{
    uint8_t len;
    sa_family_t sin_family;
    /*
        
    */
}
```

### 主机字序和网络字序

```cpp
#include <netinet/in.h>
uint32_t htonl(uint32_t hostlong);	//32bit 主机地址转网络地址
uint16_t htons(uint16_t hostshort);	//16bit 主机地址转网络地址
uint32_t ntohl(uint32_t hostlong);	//32bit 网络地址转主机地址
uint16_t ntohs(uint16_t hostshort);	//16bit 网络地址转主机地址
```

### 字节操作函数
```cpp
#include <string.h>
void bzero(void * dest, size_t bbytes);
void bcopy(const void * src, void * dest, size_t nbytes);
int bcmp(const void * str1, const void * str2, size_t nbytes);
```

### 地址转换函数

```cpp
#include <arpa/inte.h>
int inet_aton(const char * str, struct in_addr *addrptr);
char inet_ntoa(struct in_addr inaddr);
```
### 新的地址转换函数
```cpp

```


##基本TCP套接字编程
```cpp
int socket(int family, int type, int protocol);
int bind(int fd, const struct sockaddr * addr, size_t len);
int listen(int fd, int backlog);
int accept(int fd, struct sockaddr * clientaddr, size_t * clientlen);
int connect(int fd, const struct sockaddr * serveraddr, size_t serverlen);
int fork(void);
int exec();
int close(int fd);
int getsockname(int fd, struct sockaddr * addr, size_t * len);
int getpeername(int fd, strcut sockaddr * addr, size_t * len);
```



### socks函数
``` cpp
#include <sys/socket.h>
/*  
* 创建套接字，返回文件描述符
* family - 协议族，常用 AF_UNIX、AF_LOCAL、AF_INET4、AF_INET6 
* type - socket类型，常用SCOK_STREAM、SOCK_DGRAM、SOCK_RAW
* protoal - 通常为0
* 返回值  返回-1失败，否则返回值为文件描述符。
*/
int socket(int family, int type, int protocol);

```

## bind函数
```cpp
#include <sys/socket.h>
/*
* 绑定套接字地址
* fd 文件描述符 
* server_addr 填充好的sockaddr结构体。一般需要强制转换类型
* len 结构体大小
* 成功返回 0 
*/
int bind(int fd, struct sockaddr * server_addr, size_t len);
```
一般用法
```cpp
#include <sys/socket.h>

int result = bind(fd, (struct sockaddr *) & server_addr, sizeof(server_addr));

if(0 == result)
{
    printf("绑定[%s]:%d成功\n", inet_ntoa(server_addr.sin_addr.s_addr));
    
}
else
{
    printf("绑定[%s]:%d失败\n");
}

```

### listen函数
```cpp
#include <sys/socket.h>
int listen(int fd, int backlog);
```
listen函数监听套接字，传入套接字类型必须是`SOCK_STREAM` or `SOCK_SEQPACKET`.
backlog 最大队列数，当连接数超过这个值的时候，不接受新的连接
成功返回0，-1失败。

### fork
```cpp
if ((pid = fork()) == 0)
{
    printf("hello from the child");
}
else
{    
    printf("hello from the parent")；
}
```
**发现数P92页的错误**
92页很多函数调用的时候，函数名大小写错误。
 [TCP/IP详解·卷1:协议(原书第2版) 平装]( https://www.amazon.cn/TCP-IP%E8%AF%A6%E8%A7%A3%C2%B7%E5%8D%B71-%E5%8D%8F%E8%AE%AE-%E5%87%AF%E6%96%87R-%E7%A6%8F%E5%B0%94/dp/B01HGINTJ2/ref=sr_1_2?ie=UTF8&qid=1483255527&sr=8-2&keywords=tcpip%E8%AF%A6%E8%A7%A3)
 
### 值-结果类型参数
使用getsockname函数获取套接字地址的时候，总是返回0.0.0.0.查了半天资料，才发现getsockname的最后一个参数len是"值-结果"类型参数。
也就是说，在调用函数前，len是addr的长度，函数返回由，len也是addr的长度，但是两个长度的含义不一样。一个是参数，一个是结果。

值-结果类型函数都有:
```cpp
int accept(int fd, struct sockaddr * clientaddr, size_t * clientlen);
int getsockname(int fd, struct sockaddr * addr, size_t * len);
int getpeername(int fd, strcut sockaddr * addr, size_t * len);
```
被坑了一把 -.-!

## waitpid
waitpid()会暂时停止目前进程的执行，直到有信号来到或子进程结束。
```cpp
#include<sys/types.h>
#include<sys/wait.h>
pid_t waitpid(pid_t pid, int * status, int options);
```
1. 如果在调用 waitpid()时子进程已经结束,则 waitpid()会立即
返回子进程结束状态值
2. 子进程的结束状态值会由参数 status 返回,而子进程的进程识别码也会一起返回
3. 如果不在意结束状态值,则参数 status 可以设成 NULL。参数 pid 为欲等待的子进程识别码,
>**参数pid**
>1. pid < -1 等待进程组识别码为 pid 绝对值的任何子进程
2. pid = -1 等待任何子进程,相当于 wait()。
3. pid = 0 等待进程组识别码与目前进程相同的任何子进程。
4. pid > 0 等待任何子进程识别码为 pid 的子进程

>**参数option**
>
1.  参数`options`提供了一些额外的选项来控制waitpid，参数 option 可以为 0 或可以用"|"运算符把它们连接起来使用，比如：`WNOHANG | WUNTRACED`。若不需要设置为零
2.  `WNOHANG` 若pid指定的子进程没有结束，则`waitpid()`函数返回0，不予以等待。若结束，则返回该子进程的ID。
3.  `WUNTRACED` 若子进程进入暂停状态，则马上返回，但子进程的结束状态不予以理会。`WIFSTOPPED(status)`宏确定返回值是否对应与一个暂停子进程。

>**参数status**
子进程的结束状态返回后存于status,底下有几个宏可判别结束情况:
>
1. `WIFEXITED(status)`
如果若为正常结束子进程返回的状态，则为真；对于这种情况可执行`WEXITSTATUS(status)`，取子进程传给exit或_eixt的低8位。
2. `WEXITSTATUS(status)`
取得子进程`exit()`返回的结束代码,一般会先用` WIFEXITED` 来判断是否正常结束才能使用此宏。
3. `WIFSIGNALED(status)`
若为异常结束子进程返回的状态，则为真；对于这种情况可执行WTERMSIG(status)，取使子进程结束的信号编号。
4. `WTERMSIG(status)`
取得子进程因信号而中止的信号代码,一般会先用 WIFSIGNALED 来判断后才使用此宏。
5. `WIFSTOPPED(status)`
若为当前暂停子进程返回的状态，则为真；对于这种情况可执行WSTOPSIG(status)，取使子进程暂停的信号编号。
6. `WSTOPSIG(status)`
取得引发子进程暂停的信号代码,一般会先用WIFSTOPPED来判断后才使用此宏。

如果执行成功则返回子进程识别码(PID) ,如果有错误发生则返回返回值-1。失败原因存于 errno 中。

## TCP server client编程

## 项目

之前做工作中接触过nginx服务器。对nginx的架构有一定了解，打算做一个类似的服务器。具体还没想好

预备知识:
- 网络编程相关
- 服务器架构
- `linux I\O`相关编程
- 计算机网络
- 操作系统原理

可能用到的技术:
- DPDK
- mTCP
- 协程
- 内存管理
- libevent
- JIT

这些都是我在工作中接触到的并且处在知其然而不知其所以然的状态，其中有些技术是冲突的，不能同时使用。希望在这个服务器中尽量的使用这些技术，完全是为了熟悉这些技术。最后可能做成四不像，然而这并不是很重要。

