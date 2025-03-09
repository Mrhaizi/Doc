# fnctl
`fcntl` 函数在 `<fcntl.h>` 头文件中定义。它用于控制文件描述符的属性和行为。

### 用法
`fcntl` 的基本语法如下：

```c++
int fcntl(int fd, int cmd, ... /* arg */ );
```

- **`fd`**: 要操作的文件描述符。
- **`cmd`**: 要执行的命令，常见的命令包括：
  - `F_GETFL`: 获取文件状态标志。
  - `F_SETFL`: 设置文件状态标志。
  - `F_GETFD`: 获取文件描述符标志。
  - `F_SETFD`: 设置文件描述符标志。
- **`arg`**: 依赖于 `cmd` 的可选参数。例如，设置非阻塞模式时需要传递标志 `O_NONBLOCK`。

### 示例
设置文件描述符为非阻塞模式：

```c++
#include <fcntl.h>
#include <unistd.h>

int fd = open("file.txt", O_RDONLY);
int flags = fcntl(fd, F_GETFL); // 获取当前的标志
fcntl(fd, F_SETFL, flags | O_NONBLOCK); // 设置为非阻塞模式
```

### 返回值
- 成功时，返回获取的文件状态标志或 `0`。
- 失败时，返回 `-1`，并设置 `errno` 以指示错误。
