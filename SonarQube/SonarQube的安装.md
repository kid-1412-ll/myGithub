## 1.环境

使用`VM VirtualBox + Vagrant`专门为`SonarQube`启动的虚拟机，具体环境如下。

linux版本

```shell
[root@sonarqube ~]# cat /proc/version
Linux version 3.10.0-1127.el7.x86_64 (mockbuild@kbuilder.bsys.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC) ) #1 SMP Tue Mar 31 23:36:51 UTC 2020
[root@sonarqube ~]# cat /etc/redhat-release
CentOS Linux release 7.8.2003 (Core)
```

docker版本

```shell
[root@sonarqube ~]# docker --version
Docker version 20.10.21, build baeda1f
```

内存

```shell
[root@sonarqube ~]# cat /proc/meminfo | grep MemTotal
MemTotal:        2914280 kB
```

## 2.运行SonarQube

选择7.8版本的`SonarQube`，这是支持`Java8`的最高版本。

```shell
docker pull sonarqube:7.8-community
docker run -d --name sonarqube sonarqube:7.8-community
```

创建想要挂载的目录

```shell
mkdir -p sonarqube/{conf,logs,data,extensions}
docker cp sonarqube:/opt/sonarqube/conf /mydata/sonarqube
docker cp sonarqube:/opt/sonarqube/data /mydata/sonarqube
docker cp sonarqube:/opt/sonarqube/logs /mydata/sonarqube
docker cp sonarqube:/opt/sonarqube/extensions /mydata/sonarqube
```

## 3.配置MySQL持久存储

查看配置文件，开头就是有关数据库的部分。

`vim /mydata/sonarqube/conf/sonar.properties`

```shell
# Property values can:
# - reference an environment variable, for example sonar.jdbc.url= ${env:SONAR_JDBC_URL}
# - be encrypted. See https://redirect.sonarsource.com/doc/settings-encryption.html

#--------------------------------------------------------------------------------------------------
# DATABASE
#
# IMPORTANT:
# - The embedded H2 database is used by default. It is recommended for tests but not for
#   production use. Supported databases are MySQL, Oracle, PostgreSQL and Microsoft SQLServer.
# - Changes to database connection URL (sonar.jdbc.url) can affect SonarSource licensed products.

# User credentials.
# Permissions to create tables, indices and triggers must be granted to JDBC user.
# The schema must be created first.
#sonar.jdbc.username=
#sonar.jdbc.password=

#----- Embedded Database (default)
# H2 embedded database server listening port, defaults to 9092
#sonar.embeddedDatabase.port=9092

#----- DEPRECATED 
#----- MySQL >=5.6 && <8.0
# Support of MySQL is dropped in Data Center Editions and deprecated in all other editions
# Only InnoDB storage engine is supported (not myISAM).
# Only the bundled driver is supported. It can not be changed.
#sonar.jdbc.url=jdbc:mysql://localhost:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false
```

安装MySQL

```shell
sudo docker pull mysql:5.7
sudo docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7
```

修改MySQL配置

```shell
vim /mydata/mysql/conf/my.conf

[client]
default-character-set=utf8
[mysql]
default-character-set=utf8
[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve

docker restart mysql
```

创建数据库和用户

```shell
docker exec -it mysql /bin/bash
mysql -u root -p
create database sonar;
CREATE USER 'sonar'@'%' IDENTIFIED WITH mysql_native_password BY 'sonar';
GRANT ALL PRIVILEGES ON *.* TO 'sonar'@'%';
```

重新启动

```shell
docker run -d --name sonarqube \
-p 9000:9000 -p 9092:9092 \
-e SONARQUBE_JDBC_URL="jdbc:mysql://192.168.56.100:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false" \
-v /mydata/sonarqube/logs:/opt/sonarqube/logs \
-v /mydata/sonarqube/conf:/opt/sonarqube/conf \
-v /mydata/sonarqube/data:/opt/sonarqube/data \
-v /mydata/sonarqube/extensions:/opt/sonarqube/extensions \
sonarqube:7.8-community
```

## 4.报错：Process exited with exit value [es]: 1

这是因为挂载的目录权限不够导致es启动失败，修改权限重新启动容器。

```shell
chmod -R 777 /mydata/sonarqube/
docker restart sonarqube
```

## 5.报错：Process exited with exit value [es]: 143

查看日志可知`max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`

```shell
vim /etc/sysctl.conf 
vm.max_map_count=262144
sysctl -p
docker restart sonarqube
```

## 6.中文插件

中文插件版本与`SonarQube`版本有着严格的对应关系，将插件放置到`/mydata/sonarqube/extensions/plugins`目录下重启容器即可。

|               |         |         |         |         |         |         |         |         |         |         |
| ------------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- | ------- |
| **SonarQube** | **9.0** | **9.1** | **9.2** | **9.3** | **9.4** | **9.5** | **9.6** | **9.7** |         |         |
| sonar-l10n-zh | 9.0     | 9.1     | 9.2     | 9.3     | 9.4     | 9.5     | 9.6     | 9.7     |         |         |
| **SonarQube** | **8.0** | **8.1** | **8.2** | **8.3** | **8.4** | **8.5** | **8.6** | **8.7** | **8.8** | **8.9** |
| sonar-l10n-zh | 8.0     | 8.1     | 8.2     | 8.3     | 8.4     | 8.5     | 8.6     | 8.7     | 8.8     | 8.9     |
| **SonarQube** | **7.0** | **7.1** | **7.2** | **7.3** | **7.4** | **7.5** | **7.6** | **7.7** | **7.8** | **7.9** |
| sonar-l10n-zh | 1.20    | 1.21    | 1.22    | 1.23    | 1.24    | 1.25    | 1.26    | 1.27    | 1.28    | 1.29    |
| **SonarQube** | **6.0** | **6.1** | **6.2** | **6.3** | **6.4** | **6.5** | **6.6** | **6.7** |         |         |
| sonar-l10n-zh | 1.12    | 1.13    | 1.14    | 1.15    | 1.16    | 1.17    | 1.18    | 1.19    |         |         |
| **SonarQube** |         |         |         |         | **5.4** | **5.5** | **5.6** |         |         |         |
| sonar-l10n-zh |         |         |         |         | 1.9     | 1.10    | 1.11    |         |         |         |
| **SonarQube** | **4.0** | **4.1** |         |         |         |         |         |         |         |         |
| sonar-l10n-zh | 1.7     | 1.8     |         |         |         |         |         |         |         |         |
| **SonarQube** |         | **3.1** | **3.2** | **3.3** | **3.4** | **3.5** | **3.6** | **3.7** |         |         |
| sonar-l10n-zh |         | 1.0     | 1.1     | 1.2     | 1.3     | 1.4     | 1.5     | 1.6     |         |         |

## 7.报错：Failed to upload report - An error has occurred. Please contact your administrator

在进行项目分析时报错，查看日志可知

```java
Caused by: com.mysql.jdbc.PacketTooBigException: Packet for query is too large (7079751 > 4194304). You can change this value on the server by setting the max_allowed_packet' variable.
        at com.mysql.jdbc.MysqlIO.send(MysqlIO.java:3681)
        at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:2512)
        at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2683)
        at com.mysql.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:2486)
        at com.mysql.jdbc.PreparedStatement.executeInternal(PreparedStatement.java:1858)
        at com.mysql.jdbc.PreparedStatement.executeUpdateInternal(PreparedStatement.java:2079)
        at com.mysql.jdbc.PreparedStatement.executeUpdateInternal(PreparedStatement.java:2013)
        at com.mysql.jdbc.PreparedStatement.executeLargeUpdate(PreparedStatement.java:5104)
        at com.mysql.jdbc.PreparedStatement.executeUpdate(PreparedStatement.java:1998)
        at org.apache.commons.dbcp2.DelegatingPreparedStatement.executeUpdate(DelegatingPreparedStatement.java:136)
        at org.apache.commons.dbcp2.DelegatingPreparedStatement.executeUpdate(DelegatingPreparedStatement.java:136)
        at org.sonar.db.ce.CeTaskInputDao.insert(CeTaskInputDao.java:53)
```

解决方案：修改数据库配置重启容器即可。

## 8.设置自启

```shell
docker update --restart=always mysql
docker update --restart=always sonarqube 
```

