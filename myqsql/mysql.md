# mysql connect c++ 的安装(linux)

```shell
wget https://dev.mysql.com/get/Downloads/Connector-C++/libmysqlcppconn10_9.0.0-1ubuntu22.04_amd64.deb
wget https://dev.mysql.com/get/Downloads/Connector-C++/libmysqlcppconnx2_9.0.0-1ubuntu22.04_amd64.deb
wget https://dev.mysql.com/get/Downloads/Connector-C++/libmysqlcppconn10-dbgsym_9.0.0-1ubuntu22.04_amd64.deb
wget https://dev.mysql.com/get/Downloads/Connector-C++/libmysqlcppconn-dev_9.0.0-1ubuntu22.04_amd64.deb
sudo dpkg -i libmysqlcppconn10_9.0.0-1ubuntu22.04_amd64.deb
sudo dpkg -i libmysqlcppconnx2_9.0.0-1ubuntu22.04_amd64.deb
sudo dpkg -i libmysqlcppconn-dev_9.0.0-1ubuntu22.04_amd64.deb
sudo dpkg -i libmysqlcppconn10-dbgsym_9.0.0-1ubuntu22.04_amd64.deb
```

# mysql server的安装

```shell
sudo apt-get update
sudo apt-get install mysql-server

sudo systemctl start mysql

```

# mysql的使用

```shell
sudo mysql -u root
```

