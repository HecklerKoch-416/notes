# 安装mysql

```shell
#先进入root模式
sudo su
apt-get install mysql-server
apt install mysql-client
apt install libmysqlclient-dev
#验证是否安装成功
sudo netstat -tap | grep mysql
```

![](assets/分布式文件服务器06_mysql/2023-03-06-14-37-47-image.png)

安装成功截图。



## 配置远程访问：

```shell
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

注释掉31行bind_addr

![](assets/分布式文件服务器06_mysql/2023-03-06-16-25-25-image.png)

然后执行下列命令

```shell
#以权限用户root登录
mysql -u root -p      
#选择mysql库  
mysql> use mysql; 
#修改host值（以通配符%的内容增加主机/IP地址）
mysql> update user set host = '%' where user = 'root';
#刷新MySQL的系统权限相关表
mysql> flush privileges;
mysql> quit
#重启
/etc/init.d/mysql restart
```





安装可视化工具：

![](assets/分布式文件服务器06_mysql/2023-03-06-14-59-44-image.png)

# MYSQL常用命令

启动

```shell
#登录数据库，进入mysql命令行，用户名root，密码123456
mysql -uroot -p123456
#连接远程数据库
mysql -h<ip> -eroot -p123456
```





# 数据库表

1. 用户信息表。
   
   ![](assets/分布式文件服务器06_mysql/2023-03-06-16-08-35-image.png)

2. 文件信息表
   
   ![](assets/分布式文件服务器06_mysql/2023-03-06-16-16-54-image.png)
   
   
