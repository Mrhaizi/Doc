# MySql
## MySql c++ 登录问题
以root身份登录的时候，连接不上是因为插件的问题
root 用户默认使用 auth_socket 插件进行身份验证，而不是使用密码, 需要改成
mysql_native_password这个身份验证插件，才可以正确使用密码连接到mysql服务


先通过 suo  mysql -u root登录到mysql服务然后执行下面的命令更换到mysql_native_password这个身份验证插件
并设置密码
```MySql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
FLUSH PRIVILEGES;
```

