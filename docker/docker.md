# docker安装流程

```shell

# 更新软件包索引
sudo apt-get update

# 安装必要的软件包
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

# 添加Docker的官方GPG密钥
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 添加Docker存储库
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 再次更新软件包索引
sudo apt-get update

# 安装Docker引擎
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# 验证Docker是否安装成功并启动
sudo systemctl status docker

# 运行hello-world容器来验证Docker是否工作正常
sudo docker run hello-world

# 可选：将当前用户添加到Docker组
sudo usermod -aG docker $USER

# 可选：设置Docker开机自启
sudo systemctl enable docker


```

## 可能遇到的问题

在安装过程中可能遇到apt 在更新的时候  **无法链接到download.docker.com**的情情况,解决方案如下(在有挂梯子的情况出现这种问题)

```shell
vi /etc/apt/apt.conf.d/proxy.conf
```

在文件内输入

```shell
# 这个代理ip和地址和自己系统配置的一样即可

Acquire::http::Proxy "http://127.0.0.1:7890/";
Acquire::https::Proxy "http://127.0.0.1:7890/";

```

# docker使用中的一些问题

## 无法拉取镜像

解决方法是用镜像加速器

先找到daemon.json文件

```shell
cd /etc/docker
# 如果在这个目录下没有daemon.json就创建一个
sudo touch daemon.json
```

将下面内容写入这个文件

```json

{
    "registry-mirrors": [
        "https://dockerproxy.com",
        "https://mirror.baidubce.com",
        "https://docker.m.daocloud.io",
        "https://docker.nju.edu.cn",
        "https://docker.mirrors.sjtug.sjtu.edu.cn"
    ]
}
```

写入后重新启动docker服务

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

# docker使用的一些命令

声明一下镜像和容器的关系。

镜像可以比作是一个静态的包，里面包含了需要的各种依赖，代码。
容器是镜像的实例化，是镜像的一个运行实例，一个镜像可以运行多个容器.

```shell
# 运行一个容器
docker run -it 镜像名称 容器运行的commond
```

镜像运行的commond在代码测试阶段可以是/bin/bash，在应用的时候，往往是应用程序的启动脚本 ./xxxxx.

需要注意的是docker容器的生命周期和你指定的容器运行的commond生命周期一样长，当你在容器运行的时候指定运行了一个程序，当程序结束的时候，容器也相对应的停止。

**-it**:

- `-i`：表示交互式运行容器。即使没有附加终端，它也会保持标准输入打开。
- `-t`：为容器分配一个伪终端，使得你可以在终端中与容器进行交互。两个选项组合在一起通常用于需要与容器内部进行交互的场景，比如启动一个 shell。

```shell
docker run -it -p 8989:8989 app /bin/bash
```

**`-p 8989:8989`**：这是端口映射选项。

- 前面的 `8989` 是主机（你的物理机）上的端口号。
- 后面的 `8989` 是容器内部的端口号。
- 这意味着主机的 `8989` 端口请求将被转发到容器内部的 `8989` 端口。这通常用于将容器内部运行的服务暴露给主机。

**`app`**：这是你要运行的镜像的名称。

**`/bin/bash`**：这是你在容器中执行的命令。在这个例子中，它将启动一个 Bash shell.
