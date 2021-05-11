## CentOS下安装FTP 

### 一、检查安装vsftp

检查是否安装vsftpd

```
rpm -qa | grep vsftpd
```

没有，就安装vsftpd

```
yum -y install vsftpd
```

设为系统服务

```
chkconfig vsftpd on 
```

### 二、编辑vsftp配置项

配置开启虚拟账户（只能登陆ftp,不能登陆系统）以及设置访问目录　　　　

```
vim   /etc/vsftpd/vsftpd.conf

anonymous_enable=NO //设定不允许匿名访问
local_enable=YES //设定本地用户可以访问。注：如使用虚拟宿主用户，在该项目设定为NO的情况下所有虚拟用户将无法访问
chroot_list_enable=YES //使用户不能离开主目录
ascii_upload_enable=YES
ascii_download_enable=YES //设定支持ASCII模式的上传和下载功能

guest_enable=YES //设定启用虚拟用户功能
guest_username=ftp //指定虚拟用户的宿主用户
user_config_dir=/etc/vsftpd/vuser_conf //设定虚拟用户个人vsftp的CentOS FTP服务文件存放路径
```

### 三、安装Berkeley DB工具（文件数据库，我们用文件存放虚拟用户的账号密码）

```
yum install db4 db4-utils
```

### 四、创建用户密码文本/etc/vsftpd/vuser_passwd.txt ，奇行是用户名，偶行是密码

```
vim /etc/vsftpd/vuser_passwd.txt
test
123456
```

### 五、生成虚拟虚拟用户账号密码的db文件,并关联数据db文件

```
1 db_load -T -t hash -f /etc/vsftpd/vuser_passwd.txt /etc/vsftpd/vuser_passwd.db
```

编辑认证文件/etc/pam.d/vsftpd，全部注释掉原来语句，增加以下两句

```
1 auth required pam_userdb.so db=/etc/vsftpd/vuser_passwd
2 account required pam_userdb.so db=/etc/vsftpd/vuser_passwd
```

### 六、创建虚拟用户配置文件

```
mkdir /etc/vsftpd/vuser_conf/
vi /etc/vsftpd/vuser_conf/test  //文件名等于vuser_passwd.txt里面的账户名，否则下面设置无效
```

#添加以下内容

```
local_root=/ftp     //虚拟用户根目录，自定义
write_enable=YES    //可写
anon_umask=022      //掩码
anon_world_readable_only=NO
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES
```

### 七、重启vsftpd服务

service vsftpd restart

### 八、连接FTP

本地安装FTP客户端软件，如winscp

最后新建ftp连接到ftp服务器



如果服务目录为FTP，改为全权

chmod 777 /var/www/html