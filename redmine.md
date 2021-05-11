## Install Redmine  

Redmine是一款开源的项目管理软件，集成了缺陷跟踪工具，是一款基于web的项目管理工具。其特点是支持多项目和子项目，可以在上面新建多个项目，在一个平台上管理多个项目。由于这款软件是一款开源的软件所以用户可以对软件进行二次编程这样满足了不同公司和项目的发展需要，保证了差异性和扩展性。

### 方法/步骤s

先安装CentOS系统包与兼容包

```
yum -y install patch make gcc gcc-c++ gcc-g77 flex* bison file
yum -y install libtool libtool-libs autoconf kernel-devel
yum -y install libjpeg libjpeg-devel libpng libpng-devel libpng10 libpng10-devel gd gd-devel
yum -y install freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel
yum -y install glib2 glib2-devel bzip2 bzip2-devel libevent libevent-devel
yum -y install ncurses ncurses-devel curl curl-devel e2fsprogs
yum -y install e2fsprogs-devel krb5 krb5-devel libidn libidn-devel
yum -y install openssl openssl-devel vim-minimal nano sendmail
yum -y install fonts-chinese gettext gettext-devel
yum -y install ncurses-devel
yum -y install gmp-devel pspell-devel
yum -y install unzip
yum -y install automake libmcrypt* libtool-ltdl-devel*
yum -y install readline* libxslt* pcre* net-snmp* gmp* libtidy*
yum -y install ImageMagick* svnversion*
```

### 安装rvm 程序

执行命令：bash < <( curl -L https://get.rvm.io )
如失败，使用：curl https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer >./ins
安装成功后执行下面命令

```
source /etc/profile/usr/local/rvm/bin/rvm reload
```

执行查看版本命令：rvm -v
注意：未显示rvm 版本，证明安装rvm 失败。重新安装一次。

如不成功
rvm离线安装
mac自带的ruby是2.3版本，系统自带的ruby不建议去动，可以安装rvm管理ruby版本。但是rvm官网的脚本试了N次都装不上。使用离线安装的方式就没问题了。

```
cd /usr/local/src
curl -sSL https://github.com/rvm/rvm/tarball/stable -o rvm-stable.tar.gz
tar -xzvf rvm-stable.tar.gz
cd rvm
./install --auto-dotfile
source /usr/local/rvm/scripts/rvm
```

查看rvm可安装版本

```
rvm list known
```

这里安装2.6.3

```
rvm install 2.6.3
```

设置默认版本

```
rvm use 2.6.3 –default
```



### 安装ruby 程序

输入：rvm install ruby 这个命令是下载最新版本
注意：ruby 程序大约10-20M之间，取决与网速！
耐心等候………… 
输入命令：gem -v  (查看版本)
注意：未显示版本，证明ruby 安装失败。重新安装  不安装gem 第四步无法进行。

### 安装rails 和 相关依赖包

gem install rails
gem install rake
gem install mysql2
这些是Redmine必须需要的兼容包

### 下载redmine

yum install subversion -ysvn co http://svn.redmine.org/redmine/branches/2.3-stable redmine

### yum install redmine

进入redmine命令, 安装依赖包, 运行如下命令

```
bundle install7
```

修改 database.yml.example 为 database.yml
修改 configuration.yml.example 为 configuration.yml
进入配置文件目录：cd redmine/config 修改命令：mv database.yml.example database.yml
进入redmine/config  编辑命令：

vim database.yml   修改数据库连接地址
如：production:
adapter: mysql2
database: redmine
host: localhost 可以选择默认但数据库必需要有配置如图username: 你的MySQL帐号password: " 你的 MySQL密码"encoding: utf8

回到redmine下，（必须回到Redmine目录下）运行以下命令导入数据

```
rake config/initializers/secret_token.rb
rake db:migrate RAILS_ENV="production"
```



### 安装运行环境

安装httpd 
安装php
安装phpmyadmin
修改phpmyadmin.conf
启动httpd
打开 http://192.168.138.129/phpmyadmin/
建 库redmine
建库 redmine_de

启动服务测试

script/rails server -e production
在服务器终端上 

Ruby rails server -e production

### 进入系统

在客户端浏览器输入地址：
http://127.0.0.1:3000/
http://ip:3000/

注意：本机就输入127.0.0.1 服务器就输入你服务器IP地址，但需要开放3000端口或者关闭防火墙。