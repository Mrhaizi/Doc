# ssh使用教程

## linux

### 连接服务器

连接到服务器的指令如下

```shell
#检查是否安装了ssh客户端
ssh -V
#如果没有执行下面指令安装
sudo apt update
sudo apt install openssh-client
```

确认安装好ssh后执行以下指令

```shell
#用户名一般为root ip地址为公网ip
ssh 用户名@服务器IP地址
```


```
#-X 可以在服务器使用剪切板，-Y也可以
ssh -X  用户名@服务器IP地址
```

输入服务器密码即可登录

在服务器更换系统后按照上面步骤可能连接不上去,因为可能出现了远程主机的身份验证密钥已经更改。这种情况可能是因为远程服务器被重新安装或密钥被故意更改。需要删除旧的密钥重新连接

```shell
ssh-keygen -f "/home/xxxx/.ssh/known_hosts" -R "服务器ip"

```

重新链接即可

### 使用桌面操控服务器

先在服务端安装一个桌面（以Debian11示例）

```shell
#更新
sudo apt update && sudo apt upgrade
#安装xfce桌面
sudo apt install xfce4 xfce4-goodies
#安装配置vnc服务
sudo apt install tightvncserver
#第一次启动会让你设置密码
vncserver
```

在客户端安装remmina以远程操控服务器桌面

```shell
sudo apt update
sudo apt install remmina remmina-plugin-rdp remmina-plugin-secret

```

安装完成后连接到服务端开启vnc服务后会有如图显示

![image-20240819085604088](/home/mayuqi/.config/Typora/typora-user-images/image-20240819085604088.png)

这意味着桌面在nvc端口1上运行

在主机执行下面命令

```shell
#x是不同的端口号 ssh -L 590x:localhost:590x 用户名@服务器ip
```

打开remmina选择vnc连接输入 **localhost:590x**

之后输入你设置的vnc服务密码即可操纵服务器桌面

### 传送文件给服务器

```shell
# 传输整个目录需要加上 -r
scp 本地文件路径  主机名@服务器ip:文件被下载的服务器路径

```



### 服务器配置梯子

在安装好桌面环境的前提下进行，因为clash现有可以用版本仅支持桌面版

```shell
#配置网络代理
vim ~/.bashrc
```

```shell
# 将下面这个加入到这个文件最下面
export http_proxy="http://127.0.0.1:7890/"
export https_proxy="http://127.0.0.1:7890/"
export HTTP_PROXY="http://127.0.0.1:7890/"
export HTTPS_PROXY="http://127.0.0.1:7890/"
export all_proxy="socks://127.0.0.1:7891/"
export ALL_PROXY="socks://127.0.0.1:7891/"
export no_proxy=""
export NO_PROXY=""

```

```shell
source ~/.bashrc
```

然后正常运行clash就可以
