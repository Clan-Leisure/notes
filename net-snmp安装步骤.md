### net-snmp安装步骤

#### 安装环境

- CentOS Linux release 7.1.1503 (Core)

- net-snmp-5.7.3.tar.gz

#### 安装步骤

##### 第一步，解压，编译安装

- 解压net-snmp-5.7.3.tar.gz

  ```
  tar -xzvf net-snmp-5.7.3.tar.gz 
  ```

- ./configure  make && make install

  ```
  ./configure --prefix=/usr/local/net-snmp   
  //此安装目录若要修改，在自定义mib时需要修改附录中SnmpUMgrDef.h中的getIotProperyFilePath函数中的对应目录
  ```

  ![1568776947339](C:\Users\brains\AppData\Roaming\Typora\typora-user-images\1568776947339.png)

  全部默认回车

  ```
  make && make install   
  //有可能报错，没有perl环境 yum install perl-ExtUtils-MakeMaker package
  ```

##### 第二步，设置配置文件

- net-snmp默认使用的配置文件位置在$HOME/.snmp/snmpd.conf 与 /usr/local/net-snmp/share/snmp/snmpd.conf ，都需要自己创建。

  一般放在/usr/local/net-snmp/share/snmp/snmpd.conf中

  配置文件snmpd.conf如下：

  ```
  #          sec.name       source      community
   rwcommunity public
    com2sec notConfigUser  default       public
  #       groupName       securityModel    securityName
  group   notConfigGroup      v1           notConfigUser  
  group   notConfigGroup      v2c          notConfigUser
  #       name     incl/excl     subtree         mask(optional)
    view  all      included        .1
  #          group        context sec.model sec.level prefix read   write  notif
   access  notConfigGroup ""      any       noauth    exact  all    all     none
  
  
  #dlmod IotDc1 /root/net-snmp-5.7.3/agent/mibgroup/IotDc1.so
  
  #master          agentx 
  
  rouser testUser1
  rwuser testRwUser
  ```

- 启动snmp服务

  为方便使用命令，进行环境变量配置

  ```
  [root@zabbix net-snmp-5.7.3]# vi /etc/profile
  //在开始位置添加
  PATH=/usr/local/net-snmp/bin:/usr/local/net-snmp/sbin:$PATH
  [root@zabbix net-snmp-5.7.3]# source /etc/profile
  ```

  ```
  //后台启动
  [root@zabbix net-snmp-5.7.3]# snmpd
  //查看报错
  [root@zabbix net-snmp-5.7.3]# snmpd -l -Le
  ```

- 检测启动是否成功

  ```
  snmpget  -v2c -c public localhost 1.3.6.1.2.1.1.5.0 
  ```

  