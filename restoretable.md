## MySQL单表恢复方法

### 1 备份文件上传

将原来物理文件压缩备份，备份后回传到服务器，解压到/var/lib/mysql下一个新目录，如wp_terms

### 2 从备库向本库复制数据

```
mysql -u root -p 
mysql> use mysql;
mysql> use wordpress;
mysql> TRUNCATE wordpress.wp_posts;
mysql> insert into wordpress.wp_posts select * from wp.wp_posts;
```

### 纠错

如出现Incorrect datetime value: '0000-00-00 00:00:00' for column 'post_date_gmt' at row 14208

修改 /ect/my.cnf配置文件

```
[mysqld]
sql_mode=STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

重启 

```
systemctl restart mysqld
```

注意注意先备份 wp_options

```
mysql> use wordpresssbak;
mysql> TRUNCATE wordpressbak.wp_options;
mysql> insert into wordpressbak.wp_options select * from wordpress.wp_options;
```

恢复wp_options

```
mysql> use wordpresss;
mysql> TRUNCATE wordpress.wp_options;
mysql> insert into wordpress.wp_options select * from wordpressbak.wp_options;
```


