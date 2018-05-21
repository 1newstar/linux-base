| �汾 | ���� | ״̬ | �޶��� | ժҪ |
| - | :-: | :-: | :-: | :-: | -: |
| V1.0 | 2018-05-20 | ���α�д | ��־�� | ���α�д |

[TOC]

# һ������������װ

- ��װJava����

JIRA��WIKI��������Ҫ����Java��������װ���ִ��<kbd>java -version</kbd>������JDK�Ƿ�װ�ɹ������������Ϣ��

```
[root@ali02-prod-ops-jirawiki-01 ~]# java -version
java version "1.8.0_65"
Java(TM) SE Runtime Environment (build 1.8.0_65-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.65-b01, mixed mode)
```
> �������е�JDK����ʹ�ýű����а�װ����װ·������/xinguang�������ǽű�����:

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

- ��װMySQL���ݿ�

�ȼ�鱾�����Ƿ�װ�����汾�����ݿ�

```
[root@ali02-prod-ops-jirawiki-01 ~]# rpm -qa | grep -i mariadb                           //�鿴��û�а�װmariadb 
[root@ali02-prod-ops-jirawiki-01 ~]# rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64    //����У�ж��MariaDB 
[root@ali02-prod-ops-jirawiki-01 ~]# rpm -qa | grep -i mysql                   //�鿴��û�а�װmysql,����ж�ؾɰ汾��Mysql
[root@ali02-prod-ops-jirawiki-01 ~]# rm -rf /usr/lib64/mysql            //ɾ��mysql��ɢ���ļ���
[root@ali02-prod-ops-jirawiki-01 ~]# yum -y install autoconf
```

����MySQL5.6����װ

```
[root@ali02-prod-ops-jirawiki-01 ~]# wget https://cdn.mysql.com//Downloads/MySQL-5.6/MySQL-5.6.40-1.el7.x86_64.rpm-bundle.tar
[root@ali02-prod-ops-jirawiki-01 ~]# mkdir mysql && tar -xf MySQL-5.6.40-1.el7.x86_64.rpm-bundle.tar -C mysql
[root@ali02-prod-ops-jirawiki-01 mysql]# rpm -ivh MySQL-client-5.6.40-1.el7.x86_64.rpm
[root@ali02-prod-ops-jirawiki-01 mysql]# rpm -ivh MySQL-devel-5.6.40-1.el7.x86_64.rpm
[root@ali02-prod-ops-jirawiki-01 mysql]# rpm -ivh MySQL-server-5.6.40-1.el7.x86_64.rpm 
[root@ali02-prod-ops-jirawiki-01 ~]# systemctl start mysql && chkconfig mysql on
[root@ali02-prod-ops-jirawiki-01 ~]# cat /root/.mysql_secret    //�鿴���ݿ��root����
[root@ali02-prod-ops-jirawiki-01 ~]# mysql -uroot -p
Enter password: 
mysql> SET PASSWORD = PASSWORD('xg@2018');
mysql> exit
```
> JIRA��WIKI�İ�װ�����ƽ�����ص�ַ�� https://pan.baidu.com/s/1cxDTGJB_H0Rn0CuJTfq0-A


# ������װJIRA 7.3.6�汾

- ��¼MySQL����̨��ִ�������������JIRA���ݿ�

```
mysql> CREATE DATABASE jira DEFAULT CHARACTER SET utf8;
mysql> GRANT ALL ON jira.* TO 'jira' @'%' IDENTIFIED BY 'jira#2018' WITH GRANT OPTION;
mysql> GRANT ALL ON jira.* TO 'jira'@localhost IDENTIFIED BY 'jira#2018' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
```

- ��my.cnf�ļ����������ݿ��ַ���������������

```
[root@ali02-prod-ops-jirawiki-01 ~]# vim /usr/my.cnf
[mysqld]
character_set_server=utf8
max_connections=500
binlog_format=mixed
max_allowed_packet=34M
innodb_log_file_size=256M
[root@ali02-prod-ops-jirawiki-01 ~]# systemctl restart mysql
```

- ʹ��FTP�����ϴ�atlassian-jira-software-7.3.6.zip�ļ�����/xinguang/installĿ¼����ѹ����

```
[root@ali02-prod-ops-jirawiki-01 install]# unzip atlassian-jira-software-7.3.6.zip 
[root@ali02-prod-ops-jirawiki-01 install]# ls
atlassian-jira-software-7.3.6-x64.bin   atlassian-jira-7.3.6-patch

```

- ��ѹ�����װ��ִ��Ȩ�ޣ�Ȼ����н���ʽ��װ�����£�

```
[root@ali02-prod-ops-jirawiki-01 install]# chmod +x atlassian-jira-software-7.3.6-x64.bin
[root@ali02-prod-ops-jirawiki-01 install]# ./atlassian-jira-software-7.3.6-x64.bin 
Unpacking JRE ...
Starting Installer ...
May 20, 2018 5:27:03 PM java.util.prefs.FileSystemPreferences$1 run
INFO: Created user preferences directory.
May 20, 2018 5:27:03 PM java.util.prefs.FileSystemPreferences$2 run
INFO: Created system preferences directory in java.home.

This will install JIRA Software 7.3.6 on your computer.
OK [o, Enter], Cancel [c]
o
Choose the appropriate installation or upgrade option.
Please choose one of the following:
Express Install (use default settings) [1], Custom Install (recommended for advanced users) [2, Enter], Upgrade an existing JIRA installation [3]
1
Details on where JIRA Software will be installed and the settings that will be used.
Installation Directory: /opt/atlassian/jira 
Home Directory: /var/atlassian/application-data/jira 
HTTP Port: 8080 
RMI Port: 8005 
Install as service: Yes 
Install [i, Enter], Exit [e]
i

Extracting files ...
                                                                           

Please wait a few moments while JIRA Software is configured.
Installation of JIRA Software 7.3.6 is complete
Start JIRA Software 7.3.6 now?
Yes [y, Enter], No [n]
y

Please wait a few moments while JIRA Software starts up.
Launching JIRA Software ...
Installation of JIRA Software 7.3.6 is complete
Your installation of JIRA Software 7.3.6 is now ready and can be accessed
via your browser.
JIRA Software 7.3.6 can be accessed at http://localhost:8080
Finishing installation ...

[root@ali02-prod-ops-jirawiki-01 install]# /etc/init.d/jira stop
```

- ��װ��ɺ���ʱ������JIRA����atlassian-jira-7.3.6-patch������ƽ��������ָ��·����Ȼ��������JIRA����

```
[root@ali02-prod-ops-jirawiki-01 install]# cd atlassian-jira-7.3.6-patch/
[root@ali02-prod-ops-jirawiki-01 atlassian-jira-7.3.6-patch]# ls
atlassian-extras-3.2.jar  atlassian-universal-plugin-manager-plugin-2.17.13.jar  mysql-connector-java-5.1.39-bin.jar
[root@ali02-prod-ops-jirawiki-01 atlassian-jira-7.3.6-patch]# cp atlassian-extras-3.2.jar mysql-connector-java-5.1.39-bin.jar /opt/atlassian/jira/atlassian-jira/WEB-INF/lib/
cp: overwrite ��/opt/atlassian/jira/atlassian-jira/WEB-INF/lib/atlassian-extras-3.2.jar��? y
[root@ali02-prod-ops-jirawiki-01 atlassian-jira-7.3.6-patch]# cp atlassian-universal-plugin-manager-plugin-2.17.13.jar /opt/atlassian/jira/atlassian-jira/WEB-INF/atlassian-bundled-plugins/
[root@ali02-prod-ops-jirawiki-01 ~]# /etc/init.d/jira start
```

- ��������������������IP��ַ+�˿ڷ��ʣ�JIRAĬ�϶˿�Ϊ8080�������ʵ�ַ��ϵͳ���Զ���ת��JIRA��Ĭ������ҳ�棬�ڴ�����ѡ���Զ������ã�����ͼ��

 ![image](https://github.com/jinyuchen724/linux-base/raw/master/3.jira&wiki/jira1.png)

- �������ݿ�������Ϣ�������ݿⷽ�棬����ѡ��MySQL���ݿ⼴�ɣ���д��Ϻ���ص����ݿ��ַ���û�������󣬾Ϳ��Ե����һ����ť������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/a8259344-ca05-45df-b894-a7d13637b77e.png)

- ����Ӧ�ó�������ԣ�Ȼ����һ����ť������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/ecc621a2-4b78-43a7-8e77-df1b61a316b7.png)

- �����ͷ��������"����JIRA�������֤"��Ȼ����ת���������룬��Ҫʹ���˺ŵ�½������ʹ��Google�˺ŵ�½��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/9663c7d0-351c-4c39-8b93-a467e50325fb.png)

- ����û����ʽ��License��������Ҫ�˻���½JIRA��Ȼ����������˺�����һ����������30���License�����"Generate License"����ת����һ��ҳ�棬ѡ���Ӧ��Server ID,�ڵ����ĶԻ�����ѡ��"Yes"���£�

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/bc31c66c-708c-4f9d-871a-4e9ef5bd4c36.png)

- �����ͼ������ȷ�Ͽ�����ɵ�key���Զ���䵽������,����ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/e4ea0f2e-e0e5-4af4-ada2-f6e503e238fb.png)

- ���ù���Ա�˻�

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/057cb142-43fa-444f-8424-4690676231b3.png)

- ��ʾ�Ƿ����õ����ʼ�֪ͨ��ѡ���Ժ�����

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/c412cacf-71d5-4bef-820c-027b7f48bf8c.png)

����JIRA 7.3.6����İ�װ�ͻ�������ˡ�

# ����JIRA�����ʼ�����

- ��½JIRA�󣬵�����Ͻǵ�"����"����"ϵͳ"����"���ʣ��ⷢ�ʼ���"

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/0a9390a6-d70b-485e-9f9b-fe31a40d4681.png)


# �ġ���װWiki 6.7.3�汾

- ��¼MySQL����̨��ִ�������������confluence���ݿ�

```
mysql> CREATE DATABASE confluence CHARACTER SET utf8 COLLATE utf8_bin;
mysql> GRANT ALL ON confluence.* TO 'wiki' @'%' IDENTIFIED BY 'wiki#2018' WITH GRANT OPTION;
mysql> GRANT ALL ON confluence.* TO 'wiki' @localhost IDENTIFIED BY 'wiki#2018' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
```

- ʹ��FTP�����ϴ�tlassian-confluence-wiki-6.7.3.zip�ļ�����/xinguang/installĿ¼����ѹ����

```
[root@ali02-prod-ops-jirawiki-01 install]# unzip atlassian-confluence-wiki-6.7.3.zip 
[root@ali02-prod-ops-jirawiki-01 install]# ls
atlassian-confluence-6.7.3-x64.bin  atlassian-confluence-wiki-6.7.3-patch
```

- ��ѹ�����װ��ִ��Ȩ�ޣ�Ȼ����н���ʽ��װ�����£�

```
[root@ali02-prod-ops-jirawiki-01 install]# chmod +x atlassian-confluence-6.7.3-x64.bin
[root@ali02-prod-ops-jirawiki-01 install]# ./atlassian-confluence-6.7.3-x64.bin 
Unpacking JRE ...
Starting Installer ...
May 20, 2018 10:01:19 PM java.util.prefs.FileSystemPreferences$2 run
INFO: Created system preferences directory in java.home.

This will install Confluence 6.7.3 on your computer.
OK [o, Enter], Cancel [c]
o  
Choose the appropriate installation or upgrade option.
Please choose one of the following:
Express Install (uses default settings) [1], 
Custom Install (recommended for advanced users) [2, Enter], 
Upgrade an existing Confluence installation [3]
1
See where Confluence will be installed and the settings that will be used.
Installation Directory: /opt/atlassian/confluence 
Home Directory: /var/atlassian/application-data/confluence 
HTTP Port: 8090 
RMI Port: 8000 
Install as service: Yes 
Install [i, Enter], Exit [e]
i

Extracting files ...
                                                                           

Please wait a few moments while we configure Confluence.
Installation of Confluence 6.7.3 is complete
Start Confluence now?
Yes [y, Enter], No [n]
y

Please wait a few moments while Confluence starts up.
Launching Confluence ...
Installation of Confluence 6.7.3 is complete
Your installation of Confluence 6.7.3 is now ready and can be accessed via
your browser.
Confluence 6.7.3 can be accessed at http://localhost:8090
Finishing installation ...
```

- �����������IP��ַ+�˿ڣ�WIKIĬ�϶˿�Ϊ8090�������ʵ�ַ��ϵͳ���Զ���ת��WIKI��Ĭ������ҳ�棬ѡ��"��Ʒ��װ"������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/1836b5e1-3d27-4db9-a44f-4a749f3eea12.png)

- ��ȡconfluence��չ���������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/e917064b-279e-45d5-a7fe-b0fdc9abe334.png)

- ������Ȩ��ҳ�棬�ȱ���"������ ID",Ȼ��ͣ��confluence���񣬽����ƽⲽ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/7e618542-27ec-4811-a45f-d95a61151a2d.png)

```
[root@ali02-prod-ops-jirawiki-01 install]# /etc/init.d/confluence stop

[root@ali02-prod-ops-jirawiki-01 ~]# cp /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-v2-3.3.0.jar /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-v2-3.3.0.jar.$(date +%F)    //�Ƚ�atlassian-extras-decoder-v2-3.3.0.jar��������
[root@ali02-prod-ops-jirawiki-01 ~]# mv /opt/atlassian/confluence/confluence/WEB-INF/lib/atlassian-extras-decoder-v2-3.3.0.jar /root/atlassian-extras-2.4.jar    //��atlassian-extras-decoder-v2-3.3.0.jar������Ϊatlassian-extras-2.4.jar
[root@ali02-prod-ops-jirawiki-01 ~]# sz atlassian-extras-2.4.jar     //��atlassian-extras-2.4.jarbao������Windows
[root@ali02-prod-ops-jirawiki-01 ~]# sz /xinguang/install/atlassian-confluence-wiki-6.7.3-patch/confluence_keygen.jar  //�������ļ�Ŀ¼��confluence_keygen.jarҲ������Windows��
```

- ��Windows�£�����License Key�������������£�

```
c:\Users\XG\Desktop>java -version
java version "1.8.0_171"
Java(TM) SE Runtime Environment (build 1.8.0_171-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.171-b11, mixed mode)

c:\Users\XG\Desktop>java -jar confluence_keygen.jar
```

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/cb52d616-7ba5-48d4-8978-4ab7fe843d8b.jpg)

- �������ɵ�atlassian-extras-2.4.jar�ϴ�����������Ϊatlassian-extras-decoder-v2-3.3.0.jar�����ƽ��������/opt/atlassian/confluence/confluence/WEB-INF/lib·����

```
[root@ali02-prod-ops-jirawiki-01 ~]# mv atlassian-extras-2.4.jar atlassian-extras-decoder-v2-3.3.0.jar
[root@ali02-prod-ops-jirawiki-01 ~]# mv atlassian-extras-decoder-v2-3.3.0.jar /opt/atlassian/confluence/confluence/WEB-INF/lib/
[root@ali02-prod-ops-jirawiki-01 ~]# cd /xinguang/install/atlassian-confluence-wiki-6.7.3-patch/
[root@ali02-prod-ops-jirawiki-01 atlassian-confluence-wiki-6.7.3-patch]# cp Confluence-6.7.0-language-pack-zh_CN.jar mysql-connector-java-5.1.39-bin.jar /opt/atlassian/confluence/confluence/WEB-INF/lib/

[root@ali02-prod-ops-jirawiki-01 atlassian-confluence-wiki-6.7.3-patch]# /etc/init.d/confluence start    //����confluence����
```

- �ٴδ����������IP��ַ+�˿ڣ�WIKIĬ�϶˿�Ϊ8090�����ʵ�ַ���ڷ������������ɵ�key�ļ���Ȼ����һ��������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/2b918844-11d1-424e-93e2-b96581a9f31b.png)

- �������ݿ⣬����ѡ��"���Լ������ݿ�"������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/3996457e-a680-42d9-bff6-9b687179c4fd.png)

- �������ݿ�������Ϣ��ѡ��MySQL���ݿ⼴�ɣ���д��Ϻ���ص����ݿ��ַ���û�������󣬾Ϳ��Ե����һ����ť������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/1061c17c-1ce4-45d3-98c2-3a8ef4ccf362.png)

- �������ݣ�ѡ��"����"ʾ��վ��"",����ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/59f22203-4baf-4e0c-8333-5df2753a5ba7.png)

- �����û���������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/e23af0e9-7e4c-4a5f-8d9b-6bf989d5cbd4.png)

- ����ϵͳ����Ա�˻�������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/e69d0693-f928-443f-afdf-8d1a353eb666.png)

- �������,����Wiki 6.7.3�Ͱ�װ�����

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/4e273213-73b4-4d13-b580-daf7f581c3d2.png)

# �塢WIKI�ʼ�����������

- ��${HOME}/confluence/confluence/WEB-INF/lib/mail-1.4.5.jar ת�Ƶ�${HOME}/confluence/lib/Ŀ¼��

```
[root@ali02-prod-ops-jirawiki-01 ~]# mv /opt/atlassian/confluence/confluence/WEB-INF/lib/mail-1.4.5.jar /opt/atlassian/confluence/lib/
```

- �޸�server.xml�ļ�����</Context>��һ�������������

```
[root@ali02-prod-ops-jirawiki-01 ~]# vim /opt/atlassian/confluence/conf/server.xml
 14                     <Resource name="mail/QqSMTPServer"
 15                            auth="Container"
 16                            type="javax.mail.Session"
 17                            mail.smtp.host="smtp.exmail.qq.com"
 18                            mail.smtp.port="465"
 19                            mail.smtp.auth="true"
 20                            mail.smtp.user="sysadmin@xxxxx.com"
 21                            password="**********"
 22                            mail.smtp.starttls.enable="true"
 23                            mail.smtp.socketFactory.class="javax.net.ssl.SSLSocketFactory" />
```

- ��½WIKI�󣬵�����Ͻǵ�"����"����"һ������"����"�ʼ�������"����"�����µ�SMTP�ʼ�������"

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/c3b40d21-739c-479d-9f3d-7f23a2031a96.png)

- Confluence�Դ����ʼ��������ò�֧����Ѷ���䣬���������Ҫʹ��JNDI��ʽ:
 
![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/3965340a-b2cf-45e9-aef4-5ebfc60e5474.png)

- ���Ͳ����ʼ�������ͼ��

![](/web_upload/markdown/picture/81eeddbf-5bf1-4d6c-82d7-8f93e55be89d/a094b72c-d1e3-4afa-bbd9-5789854e62b4.png)
