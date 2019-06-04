### Centos7 yum安装

#### 0x01.添加源
执行
```
sudo vim  /etc/yum.repos.d/mariadb.repo
```
修改
```
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.2.6/centos7-amd64/
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

#### 0x02.安装

sudo yum install -y MariaDB-server MariaDB-client

#### 0x03.最简配置
```
mysql_secure_installation
```
- Enter current password for root (enter for none)  输入 root 密码,初次运行直接回车
- Set root password? [Y/n] 是否设置 root 密,可以 y 或者 回车	
- New password 输入新密码
- Re-enter new password 再次输入新密码
- Remove anonymous users? [Y/n] 是否删除匿名用户,可以 y 或者 回车
- Disallow root login remotely? [Y/n] 是否禁止 root 远程登录,可以 y 或者 回车
- Remove test database and access to it? [Y/n] 是否删除 test 数据库,可以 y 或者 回车
- Reload privilege tables now? [Y/n] 是否重新加载权限表,可以 y 或者 回车

#### 0x04.测试登录
 ```
 mysql -u root -p
 ```
