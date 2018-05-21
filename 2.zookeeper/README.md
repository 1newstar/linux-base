| �汾 | ���� | ״̬ | �޶��� | ժҪ |
| - | :-: | :-: | :-: | :-: | -: |
| V1.0 | 2018-05-20 | ���α�д | ��־�� | ���α�д |

[TOC]

# һ����Ⱥ����˵�� 

| �������� | ����ϵͳ���� | ����IP��ַ |
| - | :-: | :-: | -: |
| ali03-prod-toko-zookeeper-01 | CentOS Linux release 7.4 | 10.11.11.22 |
| ali03-prod-toko-zookeeper-02 | CentOS Linux release 7.4 | 10.11.11.27 |
| ali03-prod-toko-zookeeper-03 | CentOS Linux release 7.4 | 10.11.11.28 |


# ��������Zookeeper��Ⱥ

- ��װJava����

Zookeeper����Java��д����Ҫ����Java�����л�������װ���ִ��<kbd>java -version</kbd>������JDK�Ƿ�װ�ɹ������������Ϣ��
```
[root@ali03-prod-toko-zookeeper-01 ~]# java -version
java version "1.8.0_65"
Java(TM) SE Runtime Environment (build 1.8.0_65-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode)
```
> �������е�JDK����ʹ�ýű����а�װ����װ·������/xinguang/�� ��װ���̾��Թ��������ǽű�����:

```
#!/bin/bash

SRC_URI="https://tol-res.oss-cn-shanghai-internal.aliyuncs.com/java/jdk-8u65-linux-x64.tar.gz"
PKG_NAME=`basename $SRC_URI`
DIR=`pwd`
DATE=`date +%Y%m%d%H%M%S`

\mv /xinguang/java /xinguang/java.bak.$DATE &> /dev/null
mkdir -p /xinguang/java
mkdir -p /xinguang/install
cd /xinguang/install

if [ ! -s $PKG_NAME ]; then
  wget -c $SRC_URI
fi

mv jdk1.8.0_65 jdk1.8.0_65_bak.$DATE &> /dev/null

tar zxvf $PKG_NAME
mv jdk1.8.0_65/*  /xinguang/java
rm -rf jdk1.8.0_65
#add PATH
if ! cat /etc/profile | grep 'export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH' &> /dev/null;then
	echo "export JAVA_HOME=/xinguang/java" >> /etc/profile
	echo "export JRE_HOME=/xinguang/java/jre" >> /etc/profile
	echo 'export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH' >> /etc/profile
	echo 'export PATH=$PATH:$JAVA_HOME/bin' >> /etc/profile
fi

cd $DIR
source /etc/profile
bash
```

- ����Zookeeper��װ��

��Apache�ֿ�������Zookeeper�Ķ�����ִ���ļ������������õ�Zookeeper�汾Ϊ3.4.6

```
[root@ali03-prod-toko-zookeeper-01 ~]# wget https://archive.apache.org/dist/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz
```

- ��ѹ����zoo.cfg�ļ�

�����غõ�Zookeeper�������ļ�����ѹ��/xinguang�£�����Zookeeper����������Ϊroot������zoo.cfg�����ļ�

```
[root@ali03-prod-toko-zookeeper-01 ~]# tar -xf zookeeper-3.4.6.tar.gz -C /xinguang
[root@ali03-prod-toko-zookeeper-01 ~]# cd /xinguang && mv zookeeper-3.4.6 zookeeper
[root@ali03-prod-toko-zookeeper-01 ~]# chown -R root:root /xinguang/zookeeper/
[root@ali03-prod-toko-zookeeper-01 ~]# cd /xinguang/zookeeper/conf && cp zoo_sample.cfg zoo.cfg
```

- ����zoo.cfg�ļ�

������ali03-prod-toko-zookeeper-01�ڵ�Ϊ������Zookeeper�������ļ�������2���ڵ�������ļ�Ҳ��ͬ������

```
[root@ali03-prod-toko-zookeeper-01 ~]# vim /xinguang/zookeeper/conf/zoo.cfg 
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/xinguang/zookeeper/data
dataLogDir=/xinguang/zookeeper/logs
clientPort=2181
server.1=10.11.11.22:2888:3888
server.2=10.11.11.27:2888:3888
server.3=10.11.11.28:2888:3888
```

- ����myid�ļ�

������ali03-prod-toko-zookeeper-01�ڵ㴴��myid�ļ�

```
[root@ali03-prod-toko-zookeeper-01 ~]# mkdir /xinguang/zookeeper/{data,logs}
[root@ali03-prod-toko-zookeeper-01 ~]# echo "1" > /xinguang/zookeeper/data/myid 
```

������ali03-prod-toko-zookeeper-02�ڵ㴴��myid�ļ�

```
[root@ali03-prod-toko-zookeeper-02 ~]# mkdir /xinguang/zookeeper/{data,logs}
[root@ali03-prod-toko-zookeeper-02 ~]# echo "2" > /xinguang/zookeeper/data/myid 
```

������ali03-prod-toko-zookeeper-03�ڵ㴴��myid�ļ�

```
[root@ali03-prod-toko-zookeeper-03 ~]# mkdir /xinguang/zookeeper/{data,logs}
[root@ali03-prod-toko-zookeeper-03 ~]# echo "3" > /xinguang/zookeeper/data/myid 
```

> myidֵ��Ҫ���ݸ����ڵ�������,�ڵ�1 echo "1"���ڵ�2 echo "2"���ڵ�3 echo "3"

- ���û�������

��Zookeeper���������׷����ϵͳ��PATH���������С��༭/etc/profile�ļ�����������ı��������ļ�ĩβ

```
[root@ali03-prod-toko-zookeeper-01 ~]# vim /etc/profile 
#Zookeeper 3.4.6
export ZOOKEEPER_HOME=/xinguang/zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

ִ��source����ʹ�޸ĵ�������Ч

```
[root@ali03-prod-toko-zookeeper-01 ~]# source /etc/profile
```

- ����Zookeeper���������ļ�

���½ű�����ΪZookeeper���������ű�����ŵ�/etc/init.d/·����

```
[root@ali03-prod-toko-zookeeper-01 ~]# vim /etc/init.d/zookeeper 
#!/bin/bash

#chkconfig:2345 20 90
#description:zookeeper
#processname:zookeeper

export JAVA_HOME=/xinguang/java
export JRE_HOME=/xinguang/java/jre
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$PATH:$JAVA_HOME/bin

case "$1" in
    start)
        /xinguang/zookeeper/bin/zkServer.sh start
        ;;
    stop)
        /xinguang/zookeeper/bin/zkServer.sh stop
        ;;
    restart)
        /xinguang/zookeeper/bin/zkServer.sh restart
        ;;
    status)
        /xinguang/zookeeper/bin/zkServer.sh status
        ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart}"
        exit 2
        ;;
esac
```

- ���ű���ӿ�ִ��Ȩ�޲�����Zookeeper��������

```
[root@ali03-prod-toko-zookeeper-01 ~]# chmod +x /etc/init.d/zookeeper
[root@ali03-prod-toko-zookeeper-01 ~]# chkconfig --add zookeeper
```

- ����zookeeper���鿴״̬

������ali03-prod-toko-zookeeper-01�ڵ�״̬��Ϣ

```
[root@ali03-prod-toko-zookeeper-01 ~]# /etc/init.d/zookeeper start
JMX enabled by default
Using config: /xinguang/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

[root@ali03-prod-toko-zookeeper-01 ~]# /etc/init.d/zookeeper status
JMX enabled by default
Using config: /xinguang/zookeeper/bin/../conf/zoo.cfg
Mode: follower
```

������ali03-prod-toko-zookeeper-02�ڵ�״̬��Ϣ

```
[root@ali04-prod-xls-zookeeper-02 ~]# /etc/init.d/zookeeper start
JMX enabled by default
Using config: /xinguang/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

[root@ali04-prod-xls-zookeeper-02 ~]# /etc/init.d/zookeeper status
JMX enabled by default
Using config: /xinguang/zookeeper/bin/../conf/zoo.cfg
Mode: leader
```

������ali03-prod-toko-zookeeper-03�ڵ�״̬��Ϣ

```
[root@ali04-prod-xls-zookeeper-03 ~]# /etc/init.d/zookeeper start
JMX enabled by default
Using config: /xinguang/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED

[root@ali04-prod-xls-zookeeper-03 ~]# /etc/init.d/zookeeper status
JMX enabled by default
Using config: /xinguang/zookeeper/bin/../conf/zoo.cfg
Mode: follower
```
