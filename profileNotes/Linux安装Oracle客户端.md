Linux安装Oracle客户端

1.下载安装包（可用rpm源码安装，也可直接下载zip包，解压后免安装）

此次安装中，我直接下载了zip压缩包，压缩包有三个，分别是:

instantclient-basic-linux.x64-19.3.0.0.0dbru.zip  基础包

instantclient-sdk-linux.x64-19.3.0.0.0dbru.zip    

instantclient-sqlplus-linux.x64-19.3.0.0.0dbru.zip    连接工具包

下载路径https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html

2.新建文件夹Oracle，将三个压缩包解压在同一个目录下（/Oracle）

解压命令：unzip instantclient-basic-linux.x64-19.3.0.0.0dbru.zip

​				  unzip instantclient-sdk-linux.x64-19.3.0.0.0dbru.zip

​                  unzip instantclient-sqlplus-linux.x64-19.3.0.0.0dbru.zip

解压后在/Oracle目录下有新的目录，名为instantclient_19_3

cd instantclient_19_3/network/admin

touch tnsnames.ora 

vi tnsnames.ora

加入如下内容：

```
zkl =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = <hostname>)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = orcl )
      (SERVER=DEDICATED )
　　　 (INSTANCE_NAME = <数据库名>)
    )
```

3.配置环境变量，在/etc/profile文件中加入如下内容：

```python
export ORACLE_HOME=/Oracle/instandclient_19_3
export PATH=$ORACLE_HOME:$PATH
export ORACLE_SID=orcl
export TNS_ADMIN=$ORACLE_HOME/network/admin
export LD_LIBRARY_PATH=$ORACLE_HOME:$LD_LIBRARY_PATH
#export NLS_LANG='simplified chinese_china'.ZHS16GBK
export NLS_LANG='simplified chinese_china'.AL32UTF8
```

之后执行命令 source /etc/profile

最后  测试是否正确连接数据库：sqlplus fckb/fckb@10.8.1y60.20:1521/bjwxolap

oracle+cx_oracle://YWJK_SELECT:lx_picc119!@10.133.201.28/orcl
sqlplus YWJK_SELECT/lx_picc119\!@10.133.201.28/orcl

若遇到报错：bash: sqlplus: command not found

则执行：ln -s $ORACLE_HOME/bin/sqlplus /usr/bin