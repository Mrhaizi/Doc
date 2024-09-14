# 端口映射

```shell
# 允许本地端口转发
sudo sysctl -w net.ipv4.ip_forward=1

# 使用iptables将公网的271端口映射到本地25565端口
sudo iptables -t nat -A PREROUTING -p tcp --dport 273 -j REDIRECT --to-port 25565

```

配置防火墙

```shell
# 使用firewalld
firewall-cmd --permanent --add-port=273/tcp
firewall-cmd --reload

# 或者使用ufw
sudo ufw allow 273/tcp

```

保存规则

```shell
sudo apt-get update
sudo apt-get install iptables-persistent
sudo sh -c "iptables-save > /etc/iptables/rules.v4"

```

检查

```shell
cat /etc/iptables/rules.v4

```

