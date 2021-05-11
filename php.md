## CentOS php卸载

```shell
rpm -qa|grep php
显示什么卸载什么,如下：
rpm -e php-common-5.4.16-48.el7.x86_64
rpm -e php-5.4.16-48.el7.x86_64
rpm -e php-ldap-5.4.16-48.el7.x86_64
rpm -e php-cli-5.4.16-48.el7.x86_64
```

查看下卸载干净没

```
rpm -qa|grep php
```

