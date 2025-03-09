# Socket
## Address
### sockaddr
`sockaddr`是一种通用的套接字地址结构，可以用于表示任何地址,定义如下。
```c++
struct sockaddr {
    // AF_INET（IPv4）、AF_INET6（IPv6）、AF_UNIX（Unix域套接字)
    sa_family_t sa_fmily; // 地址族 
    char sa_data[14]; //地址数据
} ;
```
它之所以可以用来表示任何地址,是因为结构体的内存分布是依次根据数据定义顺序在内存块上连续分布的,而且表示结构体的地址就是用结构体的第一个数据的地址来表示,根据这个原理，又因为所有的地址第一个一定是地址簇，在内存上地址的起始位置是一样的，所以可以将sockaddr指针安全的强转为其他地址的指针。
`示例如下(其他地址的结构体在下面)`
```c++
struct sockaddr_in addr;
addr.sin_family = AF_INET;
// 将 sockaddr_in* 转换为 sockaddr*
struct sockaddr* generic_addr = (struct sockaddr*)&addr;

// 检查地址族
if (generic_addr->sa_family == AF_INET) {
    // 转换回 sockaddr_in*
    struct sockaddr_in* ipv4_addr = (struct sockaddr_in*)generic_addr;
    // 此时可以安全地操作 ipv4_addr 的其他字段
}
```
### sockaddr_in(Ipv4_addr)
```c++
struct sockaddr_in {
    sa_family_t sin_family; // 地址族 AF_INET
    in_port_t sin_port // 端口号
    struct in_addr sin_addr; // IPv4地址
    char sin_zero[8]; // 填充字段，保证与sockaddr结构体位数一致
};

struct in_addr {
    // 在windows上无论是32位还是64位都为4字节, 在linux上如果为32位则为4字节,如果为
    // 64位则为8字节，但是在网络协议中规定了 unsigned long为32的4字节, 所以这个并不
    // 会因为系统不同而大小不同
    unsigned long s_addr; 
};

```
### sockaddr_in6(Ipv6)

```c++

struct sockaddr_in {
    sa_family_t sin_family; // 地址族 AF_INET6
    in_port_t sin6_port // 端口号
    uint32_t sin6_flowinfo //流信息
    struct in_addr sin6_addr; // IPv6地址
    uint32_t sin6_scope_id; // 作用域id
};

struct in_addr {
    unsigned char s_addr[16];  // 128位
};

```

## 域名解析
解析域名首先会查询本机的`/etc/hosts`下的域名对应的ip地址，如果没有找到，向远程dns服务器请求查询域名对应的ip地址，远程dns会维护一个表来储存各种域名对应的ip。
举例
假设你想解析 www.example.com：

- 操作系统首先检查 /etc/hosts，看有没有 www.example.com 的记录。
- 如果没有找到，则向配置的 DNS 服务器（如 8.8.8.8）发起 DNS 查询。
- 该 DNS 服务器如果知道 www.example.com 的 IP 地址，就返回结果；如果不知道，会继续向其他 DNS 服务器查询，直到找到结果。
- 本地系统接收到 IP 地址后，会缓存结果并将其返回给请求的应用程序。
这样，通过本地 /etc/hosts 和远程 DNS 服务器的结合，域名就被成功解析为 IP 地址。


## Socket怎么工作

### Socket本质
Socket是一种抽象概念，用于在计算机之间进行通信的端点。在网络编程中，Socket 代表了一个通信通道，通过它可以在网络上发送和接收数据。可以把 Socket 理解为网络通信的“插座”，通过插座可以连接两台设备，并在它们之间传递数据。

### Socket在通信中的作用
网络通信一般涉及两个端点：客户端和服务器，而 Socket 就是它们进行通信的基础：

- 服务器端：服务器端创建一个 Socket，绑定到指定的 IP 地址和端口上，等待客户端连接。
- 客户端：客户端创建一个 Socket，主动连接到服务器的 IP 地址和端口，建立通信。

### Socket的具体流程
Socket 编程主要分为服务器端和客户端两部分。以下是 TCP 套接字编程的常见流程：

服务器端：
1. 创建套接字（socket()）：创建一个 Socket，指定 IP 地址类型（如 IPv4、IPv6）、通信类型（如 TCP 或 UDP）。
2. 绑定地址（bind()）：将 Socket 绑定到一个本地地址和端口，以便客户端能够找到服务器。
3. 监听（listen()）：开始监听来自客户端的连接请求，指定一个连接队列的大小。
4. 接受连接（accept()）：当有客户端连接请求时，accept() 接受连接，并创建一个新的 Socket 以供服务器与客户端通信。
5. 发送和接收数据（send()/recv()）：服务器与客户端通过新的 Socket 进行数据的收发。
6. 关闭连接（close()）：当通信完成后，关闭 Socket，释放资源。
客户端：
1. 创建套接字（socket()）：与服务器相同，创建一个 Socket。
2. 连接服务器（connect()）：连接到服务器指定的 IP 地址和端口。
3. 发送和接收数据（send()/recv()）：客户端与服务器通过 Socket 进行数据的收发。
4. 关闭连接（close()）：当通信完成后，关闭 Socket，释放资源。

## Socket类型

Socket总共有五个类型如下
### SOCK_STREAM
- 基于TCP协议是一种面向连接的套接字
- 在通信前需要进行连接（3次握手）
####
1. 客户端发送一个带有SYN(同步序列编号，用来表示客户端想要建立连接)标志的报文给服务器, 客户端进入SYN-SENT状态
2. 服务器收到序列号后知道客户端想要建立连接，发送回一个SYN(同步序列编号，表示服务器希望与客户端建立连接)-ACK(确认序列号，用于确认客户端发来的序列号)报文表示同意建立连接,服务器进入 SYN-RECEIVED 状态.
3. 客户端发送ACK(确认序列号，用于确认服务端发来的序列号)，客户端进入 ESTABLISHED状态,当服务器收到ACK时，也进入 ESTABLISHED ，连接就建立起来了。
- 可靠传输数据在传输过程中不会丢失、重复或错序，接收方会按发送方发送的顺序接收数据。

### SOCK_DGRAM
- 基于UDP协议是一种无连接的套接字
- 不建立连接，数据直接发往指定的地方
- 不可靠传输，数据丢失可能大

### SOCK_RAW（原始套接字）
### SOCK_SEQPACKET（有序包套接字）
### SOCK_RDM（可靠数据报套接字，Rarely Used）

![各种类型的对比](../pic/SocketType.png)

## Socket服务端客户端运行流程(c++)

### 服务端
- 创建套接字
- 绑定
- 监听
- 接收连接
- 读写
- 关闭
```c++
#include <iostream>
#include <cstring>
#include <arpa/inet.h>
#include <unistd.h>

#define PORT 65432
#define BUFFER_SIZE 1024

int main() {
    int server_fd, new_socket;
    struct sockaddr_in address;
    int opt = 1;
    int addrlen = sizeof(address);
    char buffer[BUFFER_SIZE] = {0};
    const char *response = "Hello from server";

    // 创建 socket 文件描述符
    if ((server_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) {
        perror("Socket failed");
        exit(EXIT_FAILURE);
    }

    // 绑定 socket 到端口
    if (setsockopt(server_fd, SOL_SOCKET, SO_REUSEADDR | SO_REUSEPORT, &opt, sizeof(opt))) {
        perror("Setsockopt failed");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(PORT);

    // 绑定 socket
    if (bind(server_fd, (struct sockaddr *)&address, sizeof(address)) < 0) {
        perror("Bind failed");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    // 监听连接
    if (listen(server_fd, 3) < 0) {
        perror("Listen failed");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    std::cout << "Server is listening on port " << PORT << std::endl;

    // 接受连接
    if ((new_socket = accept(server_fd, (struct sockaddr *)&address, (socklen_t *)&addrlen)) < 0) {
        perror("Accept failed");
        close(server_fd);
        exit(EXIT_FAILURE);
    }

    // 读取客户端消息
    read(new_socket, buffer, BUFFER_SIZE);
    std::cout << "Message received from client: " << buffer << std::endl;

    // 向客户端发送响应
    send(new_socket, response, strlen(response), 0);
    std::cout << "Response sent to client: " << response << std::endl;

    // 关闭连接
    close(new_socket);
    close(server_fd);

    return 0;
}
```
### 客户端
- 创建套接字
- 绑定地址
- 连接
- 读写
- 关闭
```c++
#include <iostream>
#include <cstring>
#include <arpa/inet.h>
#include <unistd.h>

#define PORT 65432
#define BUFFER_SIZE 1024

int main() {
    int sock = 0;
    struct sockaddr_in serv_addr;
    const char *message = "Hello from client";
    char buffer[BUFFER_SIZE] = {0};

    // 创建 socket
    if ((sock = socket(AF_INET, SOCK_STREAM, 0)) < 0) {
        std::cerr << "Socket creation error" << std::endl;
        return -1;
    }

    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(PORT);

    // 将地址转换成二进制格式
    if (inet_pton(AF_INET, "127.0.0.1", &serv_addr.sin_addr) <= 0) {
        std::cerr << "Invalid address/ Address not supported" << std::endl;
        return -1;
    }

    // 连接到服务器
    if (connect(sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) < 0) {
        std::cerr << "Connection failed" << std::endl;
        return -1;
    }

    // 向服务器发送消息
    send(sock, message, strlen(message), 0);
    std::cout << "Message sent to server: " << message << std::endl;

    // 读取服务器的响应
    read(sock, buffer, BUFFER_SIZE);
    std::cout << "Response received from server: " << buffer << std::endl;

    // 关闭 socket
    close(sock);

    return 0;
}
```

### socket通信中关键函数的描述

#### 1. **`socket()`**
```cpp
int socket(int domain, int type, int protocol);
```
- **参数**
  - `domain`: 指定通信协议族。
    - `AF_INET`: 使用 IPv4 网络协议。
  - `type`: 指定通信的类型。
    - `SOCK_STREAM`: 面向连接的 TCP 数据流。
  - `protocol`: 指定特定协议，通常设置为 `0` 来选择默认协议（对于 `SOCK_STREAM`，默认是 TCP）。
- **返回值**
  - 成功：返回文件描述符（socket descriptor），是一个整数，用于后续操作（例如 `bind()`、`listen()` 等）。
  - 失败：返回 `-1`，并设置 `errno` 以描述错误。

#### 2. **`setsockopt()`**
```cpp
int setsockopt(int sockfd, int level, int optname, const void *optval, socklen_t optlen);
```
- **参数**
  - `sockfd`: Socket 文件描述符，由 `socket()` 创建。
  - `level`: 指定套接字选项的级别。
    - `SOL_SOCKET`: 设置套接字级别的选项。
  - `optname`: 要设置的选项。
    - `SO_REUSEADDR`: 允许地址（IP 地址和端口）被重用，避免服务器因为之前使用过该端口而无法绑定。
    - `SO_REUSEPORT`: 允许多个套接字绑定同一个端口（通常是多播场景）。
  - `optval`: 选项的值。这里是 `1`，表示启用选项。
  - `optlen`: `optval` 的大小。
- **返回值**
  - 成功：返回 `0`。
  - 失败：返回 `-1`，并设置 `errno`。

#### 3. **`bind()`**
```cpp
int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```
- **参数**
  - `sockfd`: Socket 文件描述符，由 `socket()` 创建。
  - `addr`: 指向 `sockaddr` 结构体的指针，指定 IP 地址和端口。
    - 这里 `sockaddr_in` 是 `sockaddr` 的具体实现，`sin_family` 指定协议族（`AF_INET`），`sin_addr` 为 IP 地址，`sin_port` 为端口。
  - `addrlen`: `addr` 结构体的大小。
- **返回值**
  - 成功：返回 `0`。
  - 失败：返回 `-1`，并设置 `errno`。

#### 4. **`listen()`**
```cpp
int listen(int sockfd, int backlog);
```
- **参数**
  - `sockfd`: Socket 文件描述符，由 `socket()` 创建并绑定。
  - `backlog`: 等待连接队列的最大长度，表示最多可以排队的未处理连接数。
- **返回值**
  - 成功：返回 `0`。
  - 失败：返回 `-1`，并设置 `errno`。

#### 5. **`accept()`**
```cpp
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
```
- **参数**
  - `sockfd`: 处于监听状态的 socket 文件描述符，由 `listen()` 调用。
  - `addr`: 指向 `sockaddr` 结构体的指针，存储客户端的地址信息。
  - `addrlen`: 存储 `addr` 结构体的大小。
- **返回值**
  - 成功：返回一个新的 socket 文件描述符，代表与客户端的连接。
  - 失败：返回 `-1`，并设置 `errno`。

#### 6. **`connect()`**（客户端）
```cpp
int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);
```
- **参数**
  - `sockfd`: 客户端的 socket 文件描述符，由 `socket()` 创建。
  - `addr`: 指向 `sockaddr` 结构体的指针，指定要连接的服务端的地址和端口。
    - 这里 `sockaddr_in` 是 `sockaddr` 的具体实现，`sin_family` 指定协议族（`AF_INET`），`sin_addr` 为服务端 IP 地址，`sin_port` 为服务端端口。
  - `addrlen`: `addr` 结构体的大小。
- **返回值**
  - 成功：返回 `0`。
  - 失败：返回 `-1`，并设置 `errno`。

#### 7. **`send()`**
```cpp
ssize_t send(int sockfd, const void *buffer, size_t length, int flags);
```
- **参数**
  - `sockfd`: Socket 文件描述符，表示连接。
  - `buffer`: 指向要发送的数据的缓冲区。
  - `length`: 发送数据的大小。
  - `flags`: 通常设置为 `0`。
- **返回值**
  - 成功：返回实际发送的字节数。
  - 失败：返回 `-1`，并设置 `errno`。

#### 8. **`read()`** / **`recv()`**
```cpp
ssize_t read(int sockfd, void *buffer, size_t length);
ssize_t recv(int sockfd, void *buffer, size_t length, int flags);
```
- **参数**
  - `sockfd`: Socket 文件描述符，表示连接。
  - `buffer`: 指向接收数据的缓冲区。
  - `length`: 接收数据的大小。
  - `flags`（`recv()` 使用）：通常设置为 `0`;
- **返回值**
  - 成功：返回读取的字节数。
  - 失败：返回 `-1`，并设置 `errno`。
  - 连接关闭：返回 `0`。

#### 9. **`close()`**
```cpp
int close(int fd);
```
- **参数**
  - `fd`: 要关闭的文件描述符（socket 描述符）。
- **返回值**
  - 成功：返回 `0`。
  - 失败：返回 `-1`，并设置 `errno`。

---














