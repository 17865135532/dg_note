阿里云服务器安装Python3.6 及pip3设 设置默认版本

https://blog.csdn.net/keep_on_growing/article/details/82353632

https://blog.csdn.net/LeonTom/article/details/81289326

https://blog.csdn.net/lcanlup/article/details/84635625



1、centos7安装卸载python3,pip3

```

yum安装python3
    yum install epel-release -y
    yum install https://centos7.iuscommunity.org/ius-release.rpm -y
    yum install python36 u -y
    ln -s /bin/python3.6 /bin/python3           #创建python3的连接符
 
编译安装python3
    https://www.python.org/ftp/python/3.5.3/Python-3.5.3.tgz
    2.制作安装文件
    mkdir python3.5
 
    cd python3.5
    pwd
 
    3.解压到当前文件
    tar -zxvf Python-3.5.3.tgz
 
    4../configure
    5.make
    6.安装
    make install  或者 sudo make install 成功！
    
 
编译安装python3
    cd Python-3.6.2
    ./configure --with-ssl
    make
    sudo make install
    解决pip3不能使用，安装时添加--with--ssl
```



```
安装pip3
        1.首先找到pip3的位置(在python3.6的安装包内)
        cd /usr/local/bin
        ls      (这里可以看见pip3.6的文件了)
 
        ln -s /usr/local/bin/pip3.6 /bin/pip3       #创建pip3命令的连接符
        pip3 -v
        完成！

```



```
卸载python3
      rpm -qa|grep python3|xargs rpm -ev --allmatches --nodeps       卸载pyhton3
      whereis python3 |xargs rm -frv           删除所有残余文件
      成功卸载！
      whereis   python       查看现有安装的python

```





## 一、redis数据库

1、是一个完全开源免费的key-value内存数据库

2、通常被认为是一个数据结构服务器，主要是因为其有着丰富的数据结构。

Redis数据库

通常局限点来说，Redis也以消息队列的形式存在，作为内嵌的List存在满足实时的高并发需求。在使用缓冲的时候，redis比memcached具有更多的优势，并且支持更多的数据类型，把redis当作一个中间存储系统，用来处理高并发的数据库操作。

（1）速度快

使用标准C写，所有数据都在内存中完成，读写速度分别达到10万/20万

（2）持久化

对数据的更新采用Copy-on-write技术，可以异步的保存在磁盘上

（3）自动操作：对不同数据类型的操作都是自动的，很安全

（4）快速的主--从复制，官方提供了一个数据，Slave在21秒即完成了对Amazon网站10G key set的复制。

（5）Sharding技术： 很容易将数据分布到多个Redis实例中，数据库的扩展是个永恒的话题，在关系型数据库中，主要是以添加硬件、以分区为主要技术形式的纵向扩展解决了很多的应用场景，但随着web2.0、移动互联网、云计算等应用的兴起，这种扩展模式已经不太适合了，所以近年来，像采用主从配置、数据库复制形式的，Sharding这种技术把负载分布到多个特理节点上去的横向扩展方式用处越来越多。

Redis的缺点

（1）是数据库容量受到物理内存的限制，不能用于海量数据的高性能读写，因此Redis适合的场景主要局限在比较小数据量的高性能操作和运算上

（2）Redis较难支持在线扩容，在集群容量达到上限时在线