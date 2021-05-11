## centos安装与卸载postgresql

### 1、卸载旧版本postgresql

yum remove postgresql*

### 2、更新yum

yum update

### 3、下载pg

pgdg-centos92-9.2-6.noarch.rpm，或者到http://yum.pgrpms.org/reporpms/选择相应版本
wget http://yum.pgrpms.org/9.2/redhat/rhel-5-x86_64/pgdg-centos92-9.2-6.noarch.rpm

### 4、安装

```
rpm -ivh pgdg-centos92-9.2-6.noarch.rpm 
```

sudo yum -y install postgresql92-server

### 6、初始化 

```
service postgresql-9.2 initdb
```

如果提示-bash: service: command not found，则需要设置环境变量

```
$vi .bash_profile
export PATH=$PATH:/sbin
```

### 7、启动postgresql

```
service postgresql-9.2 start
```

### 8、设置开机自动启动

```
chkconfig postgresql-9.2 on
```



------



## pg12的安装

### Install the repository RPM:
sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm

### Install PostgreSQL:

sudo yum install -y postgresql12-server

Optionally initialize the database and enable automatic start:

sudo /usr/pgsql-12/bin/postgresql-12-setup initdb
sudo systemctl enable postgresql-12
sudo systemctl start postgresql-12

yum install postgresql-server

service postgresql initdb
chkconfig postgresql on

启动服务
sudo systemctl restart postgresql-12
登录
bin/psql


select last_value(score) over(partition by subject),* from score; 