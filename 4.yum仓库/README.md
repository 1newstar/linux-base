# Centos7���𱾵ؾ�����yumԴ

| �汾| ���� | ״̬ |   �޶���  |    ժҪ  |
| ------ | ----- | ----- | ------- | ------ |
| v1.1  | 2017-07-10  | ���� |  ��Դ |  yum�ֿ� |

�����������ķ�������rpm������İ�װ�͸���,����Ҫ��������Internet�����˷Ѵ���,���Ҳ���ȫ Ϊ�˱�����������ı׶ˣ�
���ǿ���ͨ���Լ��������ص�repository���ֿ⣩�ķ�����ʵ��������Ŀ��ٰ�װ�͸���. 

# ���𻷾�

| ����   |   ��ɫ   |   ����ϵͳ |   ����汾  |    ��ע  |
| ------ | ----- | ----- | ------- | ------ |
| hz01-online-ops-yum-01(172.16.8.10)  | yum-master  |   Centos 7.3(x86-64)|  httpd��createrepo��reposync |  ��������|


- ��ȡ�����ƾ���repo������

```
[hz01-online-ops-yum-01 /opt]# wget -O /etc/yum.repos.d/epel.repo https://mirrors.aliyun.com/repo/epel-7.repo
```

- ��װhttpd���������ṩ���yumԴ

```
[hz01-online-ops-yum-01 /opt]# yum install httpd -y 
[hz01-online-ops-yum-01 /opt]# service httpd start
```

ʹ��Ĭ�����ü��ɣ������������������޸������ļ�/etc/httpd/conf/httpd.conf��httpĬ�ϼ�Ŀ¼/var/www/html

- ��װyum-utils�ṩreporsync����

```
[hz01-online-ops-yum-01 /opt]# yum install yum-utils -y
```

- ѡ��ָ���ֿ��ʶ��Ϊ����yumԴ

> ִ��yum repolist����鿴yum�ֿ��ʶ

```
[hz01-online-ops-yum-01 /opt]#yum repolist
�Ѽ��ز����fastestmirror
Loading mirror speeds from cached hostfile
Դ��ʶ                                                                                              Դ����                                                                                                                      ״̬
centos-openshift-origin                                                                             CentOS OpenShift Origin                                                                                                        185
epel/x86_64      #��Ϊ�����Ƶ�Դ                                                                    Extra Packages for Enterprise Linux 7 - x86_64                                                                              12,542
local-centosbase7                                                                                   CentOS-7 - Base                                                                                                              9,363
local-centosextras7                                                                                 CentOS-7 - extras                                                                                                              449
local-centosplus7                                                                                   CentOS-7 - plus                                                                                                                101
local-centosupdates7                                                                                CentOS-7 - updates                                                                                                           2,146
local-epel7                                                                                         epel-7                                                                                                                      12,780
local-mesosphere7                                                                                   mesosphere-7                                                                                                                   119
local-ops                                                                                           local-ops                                                                                                                        7
local-ossec-openvas7                                                                                ossec-openvas-7                                                                                                                718
local-saltstack7                                                                                    saltstack-7                                                                                                                     85
local-zabbix7                                                                                       zabbix-7                                                                                                                       157
repolist: 38,652
```

> ʹ��epel��Ϊ����yumԴ����/var/www/html��Ϊyum�ֿ��Ŀ¼

```
[hz01-online-ops-yum-01 /opt]#reposync -r epel -p /var/www/html/
```

> ����ִ����Ϻ󣬻Ὣ�������е�epelԴͬ��������/var/www/html��,��/var/www/html���Զ�����epelĿ¼���ڴ��rpm������һ��ͬ����ʱ����ܽϳ������1W���rpm����

- createrepo �����/var/www/html/epel�µ� rpm �� ����Ϊ���ص� YUM �ֿ⣬Ŀ����Ϊ����repodataĿ¼���Զ�����������Ϣ

```
[hz01-online-ops-yum-01 /opt]#createrepo -pdo /var/www/html/epel/ /var/www/html/epel/     #��һ��Ŀ¼��repodata���Ŀ¼���ڶ���Ŀ¼����Ҫ����������ϢyumԴ�ֿ�Ŀ¼
Spawning worker 0 with 1572 pkgs
Spawning worker 1 with 1572 pkgs
Spawning worker 2 with 1571 pkgs
Spawning worker 3 with 1571 pkgs
Spawning worker 4 with 1571 pkgs
Spawning worker 5 with 1571 pkgs
Spawning worker 6 with 1571 pkgs
Spawning worker 7 with 1571 pkgs
Workers Finished
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete

[hz01-online-ops-yum-01 /opt]#ll /var/www/html/epel/repodata/
������ 36132
-rw-r--r-- 1 root root 11026375 5��  22 09:53 0935db2c5c884bf50d253da67d459207e8d6be8ef211ea66384de969238df4e4-filelists.sqlite.bz2
-rw-r--r-- 1 root root  3185280 5��  22 09:52 20f75a7c2ef859771944f4abc351b8b8843111a43311e717e2f38458e0347411-other.sqlite.bz2
-rw-r--r-- 1 root root  6630057 5��  22 09:53 2f81c07f7dbf491f09cd29dd9f830f0ca09d73b719bdb6c36dac38dd64434b97-primary.sqlite.bz2
-rw-r--r-- 1 root root 10240542 5��  22 09:52 381807e2fa40041857d8bb57ab8c74879f16227d372ee286e76100240b02b823-filelists.xml.gz
-rw-r--r-- 1 root root  3636059 5��  22 09:52 7f9ec5b7b8e1d98b4611b8f6a18f06c197e6bb999f78626c048ea3a90c1e2202-primary.xml.gz
-rw-r--r-- 1 root root  2269109 5��  22 09:52 8d2c397b4363b2e7e3524eeff348695a21185b8d79a20e1ebd04b8b0f92a6b4d-other.xml.gz
-rw-r--r-- 1 root root     3014 5��  22 09:53 repomd.xml
```

- ��֤����yumԴ�Ƿ�������ʹ�ã�

> ��¼��������������������Ҫ����yumԴ������ͨ�ţ���дrepo�ļ���

������repo�ļ�ɾ�������µ�repo�ļ�
```
[root@hz01-online-ops-opennode-03 /etc/yum.repos.d]#yum repolist
�Ѽ��ز����fastestmirror
Determining fastest mirrors
repolist: 0

[root@hz01-online-ops-opennode-03 /etc/yum.repos.d]#vim epel7-local.repo

```

- ��֤�Ƿ���سɹ�

```
[root@hz01-online-ops-opennode-03 /etc/yum.repos.d]# yum clean all
�Ѽ��ز����fastestmirror
�����������Դ�� epel
Cleaning up everything
Cleaning up list of fastest mirrors

[root@hz01-online-ops-opennode-03 /etc/yum.repos.d]# yum makecache
�Ѽ��ز����fastestmirror
epel                                                                                                                                                                                                           | 2.9 kB  00:00:00     
(1/3): epel/primary_db                                                                                                                                                                                         | 6.3 MB  00:00:00     
(2/3): epel/other_db                                                                                                                                                                                           | 3.0 MB  00:00:00     
(3/3): epel/filelists_db                                                                                                                                                                                       |  11 MB  00:00:01     
Determining fastest mirrors
Ԫ���ݻ����ѽ���

[root@hz01-online-ops-opennode-03 /etc/yum.repos.d]# yum repolist
�Ѽ��ز����fastestmirror
Loading mirror speeds from cached hostfile
Դ��ʶ                                                                                                       Դ����                                                                                                             ״̬
epel                                                                                                         epel7-local                                                                                                        12,570
repolist: 12,570
```

- ��ʱͬ��

```
vim /root/yum-update.sh
#!/bin/bash

datetime=`date +"%Y-%m-%d"`
exec > /var/log/epel.log����#ͬ����־���
reposync -d -r epel -p /var/www/html/  ����#ͬ������Դ
if [ $? -eq 0 ];then
    createrepo --update  /var/www/html/epel ����#ÿ������µ�rpmʱ,�������epel������Ϣ
    echo "SUCESS: $datetime epel update successful"else
    echo "ERROR: $datetime epel update failed"fi
#��ʱ����ÿ�����賿����ͬ��yumԴ
crontab -e
0 3 * * 6 /bin/bash /root/yum-update.sh
```

- ���saltstackԴ

> ����ȥ������ȡsaltstack��repo

```
[hz01-online-ops-yum-01 /opt]#cat saltstack.repo 
[saltstack-repo]
name=SaltStack repo for Red Hat Enterprise Linux $releasever
baseurl=https://repo.saltstack.com/yum/redhat/$releasever/$basearch/2016.11
enabled=1
gpgcheck=1
gpgkey=https://repo.saltstack.com/yum/redhat/$releasever/$basearch/latest/SALTSTACK-GPG-KEY.pub
       https://repo.saltstack.com/yum/redhat/$releasever/$basearch/latest/base/RPM-GPG-KEY-CentOS-7

```

> ��������Դͬ��������
```
[hz01-online-ops-yum-01 /opt]#reposync -r saltstack-repo -p /var/www/html/
```

> ���������ļ�
```
[hz01-online-ops-yum-01 /opt]#createrepo -pdo /var/www/html/saltstack-repo/ /var/www/html/saltstack-repo/
Spawning worker 0 with 10 pkgs
Spawning worker 1 with 10 pkgs
Spawning worker 2 with 10 pkgs
Spawning worker 3 with 10 pkgs
Spawning worker 4 with 9 pkgs
Spawning worker 5 with 9 pkgs
Spawning worker 6 with 9 pkgs
Spawning worker 7 with 9 pkgs
Workers Finished
Saving Primary metadata
Saving file lists metadata
Saving other metadata
Generating sqlite DBs
Sqlite DBs complete

[hz01-online-ops-yum-01 /opt]#ll /var/www/html/saltstack-repo/repodata/
������ 268
-rw-r--r-- 1 root root 76839 5��  22 13:24 0318c5a104feb4541c3eceba4a0da53b8956688fc3465909b42287e249beb82b-filelists.xml.gz
-rw-r--r-- 1 root root 87189 5��  22 13:24 055703cdbd83ee9b537db585998e5886eb9dd3032de2a9fb917043c862a177a3-filelists.sqlite.bz2
-rw-r--r-- 1 root root 16937 5��  22 13:24 0aa49b095b4620c2108e9f7c0e0f8186f086d07ddd1e667e14e1edbdbe40163b-other.xml.gz
-rw-r--r-- 1 root root 31871 5��  22 13:24 4c6cf849557022c8c1c08c83ec9f458109a644a761b68497d1932f099be053e4-primary.sqlite.bz2
-rw-r--r-- 1 root root 26612 5��  22 13:24 f03715e896fc4b9db7b2442801cf7bbde82e8bc316c06ee4d99154cc05ebf9f7-other.sqlite.bz2
-rw-r--r-- 1 root root 19677 5��  22 13:24 fe7eac489d97d90593f17e6d19ae19c615822a763072df970f4e587f4774eb39-primary.xml.gz
-rw-r--r-- 1 root root  2988 5��  22 13:24 repomd.xml
```

�����һ����������������saltԴ

```
[root@hz01-online-ops-opennode-03 /etc/yum.repos.d]#cat epel7-local.repo 
[epel]
name=epel7-local
baseurl=http://172.16.8.42/epel
enabled=1
gpgcheck=0

[salstack]
name=saltstack-7
baseurl=http://172.16.8.42/saltstack-repo/
gpgcheck=0
```

```
[root@hz01-online-ops-opennode-03 /etc/yum.repos.d]#yum list |grep salt
PyYAML.x86_64                           3.11-1.el7                     @local-saltstack7
python-tornado.x86_64                   4.2.1-1.el7                    @local-saltstack7
python-zmq.x86_64                       15.3.0-2.el7                   @local-saltstack7
salt.noarch                             2016.11.7-1.el7                @local-saltstack7
salt-minion.noarch                      2016.11.7-1.el7                @local-saltstack7
python-salttesting.noarch               2015.7.10-1.el7                epel     
salt.noarch                             2016.11.9-2.el7                salstack 
salt-api.noarch                         2016.11.9-2.el7                salstack 
salt-cloud.noarch                       2016.11.9-2.el7                salstack 
salt-master.noarch                      2016.11.9-2.el7                salstack 
salt-minion.noarch                      2016.11.9-2.el7                salstack 
salt-ssh.noarch                         2016.11.9-2.el7                salstack 
salt-syndic.noarch                      2016.11.9-2.el7                salstack 
```

���������Ҫ�ȶ��汾��yumԴ�����������һ���ġ�






