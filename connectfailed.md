## MySQL无法连接

### 登录mysql显示错误，如何处理？

```
[root ~]# mysql -u root
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password:NO)
```

#### 修改配置

```
sudo vi /etc/my.cnf
#将行添加到mysqld块
skip-grant-tables
```

#### 重新启动MySQL服务

```
service mysql restart
```

#### 检查服务状态

```
service mysql status
```

#### 连接到mysql

```
mysql
```

#### 使用主数据库

```
use mysql;
```

#### 重新定义用户根密码，记住这个密码myNewPassword，下面用得到

```
UPDATE user SET `authentication_string` = PASSWORD('myNewPassword') WHERE `User` = 'root'; 
```

#### 修改配置

```
sudo vi /etc/my.cnf
#删除这行
skip-grant-tables
```

#### 重新启动MySQL服务。

```
service mysqld restart
```

#### 检查服务状态

```
service mysql status
```

#### 连接到数据库

```
mysql -u root -p
```

#### 出现提示时输入新密码

