# Git教程（以github为例）

## Git与本机的ssh连接流程

### 生成公钥和私钥

```shell
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

```

### 添加ssh公钥到代理(如果设置了公钥的密码)

```shell
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa

```

### 将ssh公钥添加到git服务器

```shell
# 查看公钥
vi ~/.ssh/id_rsa.pub

```

复制后进入github中  https://github.com/settings/keys 这个界面 点击New SSH Key 然后进入将公钥粘贴  生成即可！

### 测试连接

```shell
ssh -T git@github.com
```

## 撤销git commit 和 git add .操作

```shell
git reset HEAD~1

```



