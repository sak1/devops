## Centos7搭建git服务器端

yum 源仓库里的 Git 版本更新不及时，最新版本的 Git 是 1.8.3.1，但是官方最新版本已经到了 2.9.2。想要安装最新版本的的 Git，只能下载源码进行安装。

## 安装git服务

//查看 yum 源仓库的 Git 信息

```
yum info git  
```

可以看出，截至目前，yum 源仓库中最新的 Git 版本才 1.8.3.1，而查看最新的 Git 发布版本，已经 2.9.2 了。

### //依赖库安装

```
# yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
# yum install gcc perl-ExtUtils-MakeMaker
```

### //卸载低版本的 Git

```
yum remove git
```

### //下载新版的 Git 源码包

我放的了  /usr/local/git 的目录下了，git是我自己mkdir的目录）

```
cd git
wget https://github.com/git/git/archive/v2.9.2.tar.gz
tar -xzvf v2.9.2.tar.gz
```

### 编译安装

分别执行以下命令进行编译安装，编译过程可能比较漫长，请耐心等待完成。

```
cd git-2.9.2
make prefix=/usr/local/git all
make prefix=/usr/local/git install
```

添加到环境变量

```
vim /etc/profile  
```

如果没有vim，则安装vim工具 yum install vim
添加这一条： export PATH="/usr/local/git/bin:$PATH"

```
source /etc/profile   //使配置立即生效
git --version  //查看版本号
```

将git设置为默认路径，不然后面克隆时会报错

```
ln -s /usr/local/git/bin/git-upload-pack /usr/bin/git-upload-pack 
ln -s /usr/local/git/bin/git-receive-pack /usr/bin/git-receive-pack 
```

创建一个git用户组和用户，用来运行git服务

```
groupadd git
useradd git -g git
passwd git  #参数是用户名
su - git  //切换git用户
```

【注】最好切换到git用户 不然后面新建的git仓库都要改权限
至此，git服务安装完成！

### 创建证书登录

添加证书之前，还要做这么一步：
Git服务器打开RSA认证 。在Git服务器上首先需要将/etc/ssh/sshd_config中的RSA认证打开，即将sshd_config文件中下面几个的注释解开：

```
1.RSAAuthentication yes
2.PubkeyAuthentication yes
3.AuthorizedKeysFile .ssh/authorized_keys
```

这里我们可以看到公钥存放在.ssh/authorized_keys文件中。
所以我们在/home/git下创建.ssh目录，然后创建authorized_keys文件，

```
cd /home/git/
mkdir .ssh #新建文件夹
chmod 700 .ssh 
touch .ssh/authorized_keys  #新建文件
chmod 600 .ssh/authorized_keys
```

生成密钥(to be here 0201.11.30)

（1）、设置用户名和邮箱

```
[git@10-255-0-114 ~]$ git config --global user.name '用户名'
[git@10-255-0-114 ~]$ git config --global user.email '邮箱地址'
```

（2）、查看是否存在ssh keys

```
[git@10-255-0-114 ~]$ cd ~/.ssh
```

若出现“No such file or directory”,则表示需要创建一个ssh keys。
（3）、 创建新的ssh keys

```
 [git@10-255-0-114 ~]$ ssh-keygen -t rsa -C "邮箱"
```

（4）、测试一下连接
//查看调试信息

```
[git@10-255-0-114 ~]$ ssh -T -v git@github.com 
```

以上信息显示连接github失败，原因是因为我们没有将新生成的密钥加到我们的gitHub里面，所以我们需要打开authorized_keys.pub文件，把里面的内容拷贝到GitHub ，打开链接 里面Setting中的ssh and GPG key中，点击new ssh key把我们公钥文件id_rsa.pub的内容粘到key中，title随意写，保存即可。
【注】也可以通过 cat ~/.ssh/authorized_keys.pub将公共密钥输出到控制台，再进行拷贝
再次尝试连接
[git@10-255-0-114 ~]$ ssh -T git@github.com

这是因为在本地计算机与 GitHub 建立连接的时候，实际上是本机计算机的 ssh-agent 与 GitHub 服务器进行通信。虽然本地计算机有了私钥，但是 ssh-agent并不知道私钥存储在哪儿。因此，要想正常使用秘钥对，需要先将私钥加入到本地计算机的ssh-agent 中（添加过程中需要输入 passphrase）
此时，可以通过
[git@10-255-0-114 ~]$ ssh-add ~/.ssh/authorized_keys
如果，上述命令执行后，出现提示
Could not open a connection to your authentication agent.
那么，
[git@10-255-0-114 ~]$ eval `ssh-agent  -s` 
[git@10-255-0-114 ~]$ ssh-add ~/.ssh/authorized_keys
[git@10-255-0-114 ~]$ ssh-add -l  //查看到当前计算机中存储的密钥
[git@10-255-0-114 ~]$ ssh -T git@github.com
如果出现： Hi —! You’ve successfully authenticated, but GitHub does not provide shell access. 表明git服务搭建成功！
禁止Shell登录
因为搭建git服务器后通常会建立一个git账户，其它人共用这个账户来克隆或推送数据到git仓库中，通常也只需要这个功能，但是如果不加限制，那么其它人可以通过这个git账户登录到主机，那么这样是不安全的，所以需要加以限制，即令git用户不允许登录shell，可以编辑/etc/passwd文件，找到类似下面的一行：
git:x:502:502::/home/git:/bin/bash
改为
git:x:502:502::/home/git:/usr/local/git/bin/git-shell
【注】（个人用的话可忽略）
这样git这个账户就只能用来克隆或者推送数据到git仓库中了，而不能用它来登录到主机。
现在 git 用户只能用 SSH 连接来推送和获取 Git 仓库，而不能直接使用主机 shell，因为我们为git用户指定的git-shell每次一登录就自动退出，尝试普通 SSH 登录的话，会看到下面这样的拒绝信息：
[root@localhost ~]# ssh git@192.168.25.133
git@192.168.25.133's password:
Last login: Wed Jun 13 21:52:02 2018
fatal: Interactive git shell is not enabled.
hint: ~/git-shell-commands should exist and have read and execute access.
Connection to 192.168.25.133 closed.
管理大量用户使用git权限：gitosis
在/home/git/.ssh文件中有authorized_keys文件，这个文件里边存放了需要使用git项目的用户的公钥，也就是允许谁可以git你的项目
适用于开发者较少的情况，如果开发者数量达到几百人的规模时，管理起来十分费力，每次增加或删除用户时都必须登录到服务器上去，而且还缺少必要的权限管理，因为用户对所有在/home/git下的git项目都拥有读写权限。
其实gitosis这个东西和svnAdmin很像，gitosis是用来管理authorized_keys文件和简单连接限制的脚本，而svnAdmin也是用来管理大量用户使用svn的权限的。添加、删除用户或设定权限这些工作是通过管理一个特殊的git仓库来实现的，你只需要在这个仓库做好相应的设置，然后推送到服务器上，gitosis就会随之改变策略，听起来不错吧。
使用git命令把代码放到github上



### 1、把本地创建的仓库push到github远程仓库上：

（1）、首先登陆github,创建一个名为cv-template的repositories，克隆ssh地址;
（2）、创建仓库目录：

```
cd /home/git
mkdir cv-template
cd cv-template
git init  //初始化git仓库
ls -a   //显示隐藏文件夹.git
```

操作后，会出现.git目录，里面会有相应的文件；

（3）、配置git仓库的用户：

```
git config --global user.name "github注册的用户名"
git config --global user.mail "github注册的邮箱"
```

（4）、添加远程地址：
```
# git remote add origin git@github.com:github注册的用户名/github创建的仓库名.git
# vim .git/config     
```

操作后，.git/config文件内容会出现remote等内容；
（5）、在cv-template仓库目录下，创建文件README：

```
# echo "readme" > README
# git add README  //添加到git可管理文件跟踪中
```

（6）、提交到本地仓库：

```
git commit -m 'first commit'   //first commit 只是作为描述，方便log查询，不可省略
```

（7）、再push到远程仓库上
```
# git push -u origin master
# git push -f origin master  //强制上传代码到github,解决冲突常用的方式
```

（8）、查询git提交日志

git log

（9）、查看git状态
```
git status
```

【注】如果后面又对本地仓库中添加了一些代码文件，可以通过以下命令提交：
```
# cd cv-template
# git add .   
```

add后面加了一个点，是想要提交所有文件，如果想提交指定的文件，可以写文件名
```
# git commit –m “NowToDo_v1.0版本信息”
# git push -u origin master
```



### 2、本地没有创建仓库，而在github上创建了一个test空仓库

（1）、将远程的空仓库克隆到本地：

```
# cd /home/git
# git clone git@github.com:账号名/test.git
```

执行后会在当前目录生成一个github远程仓库名称的目录，再进入这个目录里面会自动创建.git目录；
（2）、配置git全局参数：

```
# git config --global user.name 'github注册的用户名'
# git config --global user.email 'github注册的邮箱'
# git congit --list   //查看git配置参数
```

（3）、添加git跟踪文件：
在当前目录里面，写代码例如：在其目录下添加文件readme.txt

```
git add readme.txt
```

（4）、提交到仓库管理器中：
```
git commit -m '提交的信息标签'
```

（5）、把本地仓库中的文件提交到github远程仓库中：
```
git push origin master
```

在github上可验证文件是否存在。