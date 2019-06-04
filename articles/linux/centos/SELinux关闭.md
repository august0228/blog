### 0x01.简介

安全增强式Linux（SELinux，Security-Enhanced Linux）是一个Linux内核的安全模块，
其提供了访问控制安全策略机制，包括了美国国防部风格的强制访问控制（Mandatory Access Control，MAC）。

SELinux是一系列添加到多个Linux发行版的内核修改与用户空间工具。
其软件架构力图从安全策略中分离出执行安全决策并优化涉及执行安全策略的软件。
[2][3]奠基SELinux的核心概念可以追溯回美国国家安全局（NSA）的一些早期项目。

### 0x02.SELinux支持的三种模式

**并非所有的 Linux发行版都支持 SELinux 的，CentOS 5.x 以后就支持 SELinux了，目前 SELinux 支持三种模式:**
- enforcing：强制模式，表示SELinux 运行中，且已经正确的开始限制 domain/type 了;
- permissive：宽容模式，表示SELinux 运行中，不过仅会有警告信息，并不会实际限制 domain/type 的存取。这种模式可以运来作为 SELinux 的 debug 之用;
- disabled：关闭，SELinux 没有运行。
centos7默认为 enforcing

### 0x03.查看SELinux状态
#### sestatus
```
/usr/sbin/sestatus -v
```
如果`SeLinux status:enabled`即为开启状态
```
[root@localhost ~]# /usr/sbin/sestatus -v
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31

```

#### getenforce
```
[root@localhost ~]# getenforce
Enforcing
```
当前模式为Enforcing

### 0x04.SELinux关闭

#### 临时关闭
```
setenforce 0 ## 设置Selnix 为permissive模式
```

```
setenforce 1 ## 设置Selnix 为enforcing式
```
#### 修改配置文件
```
vim /etc/selinux/config
```
将SELINUX=enforcing改为SELINUX=disabled
```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
#SELINUX=enforcing
SELINUX=disabled
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
重启机器即可

```
reboot
```


### 参考资料

[安全增强式Linux](<https://zh.wikipedia.org/wiki/%E5%AE%89%E5%85%A8%E5%A2%9E%E5%BC%BA%E5%BC%8FLinux/>)

[查看 SELinux状态及关闭SELinux](<https://blog.51cto.com/bguncle/957315/>)

[SELinux的启动和关闭](<https://www.cnblogs.com/pandachen/p/7624788.html/>)