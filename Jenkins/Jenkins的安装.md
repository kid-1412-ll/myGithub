## 准备工作

[Jenkins中文官网](https://www.jenkins.io/zh/doc/pipeline/tour/getting-started/)

第一次使用 Jenkins，您需要：

- 机器要求：
  - 256 MB 内存，建议大于 512 MB
  - 10 GB 的硬盘空间（用于 Jenkins 和 Docker 镜像）
- 需要安装以下软件：
  - Java 8 ( JRE 或者 JDK 都可以)
  - [Docker](https://www.docker.com/) （导航到网站顶部的Get Docker链接以访问适合您平台的Docker下载）

## 使用Docker安装

下载镜像并启动容器，已经提前创建好了挂载目录。

```shell
docker pull jenkins/jenkins:2.346.2
docker run \
> -d \
> -u root \
> -p 10100:8080 \
> -p 50000:50000 \
> --name jenkins \
> -v /home/jenkins_home:/mydata/jenkins \
> -v /etc/localtime:/etc/localtime \
> jenkins/jenkins:2.346.2
```

网页访问`ip:10100`，等待Jenkins初始化完成

![Jenkins](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227164048.png)

在对应的文件中或者启动日志中获取初始的管理员密码

```shell
WARNING: Please consider reporting this to the maintainers of org.codehaus.groovy.vmplugin.v7.Java7$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
2022-12-27 08:37:30.774+0000 [id=28]	INFO	jenkins.install.SetupWizard#init: 

*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

3d157c402834468f9c5b261529a67278

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************

2022-12-27 08:38:29.606+0000 [id=42]	INFO	h.m.DownloadService$Downloadable#load: Obtained the updated data file for hudson.tasks.Maven.MavenInstaller
2022-12-27 08:38:29.606+0000 [id=42]	INFO	hudson.util.Retrier#start: Performed the action check updates server successfully at the attempt #1
2022-12-27 08:38:29.613+0000 [id=42]	INFO	hudson.model.AsyncPeriodicWork#lambda$doRun$1: Finished Download metadata. 60,911 ms
2022-12-27 08:40:01.084+0000 [id=28]	INFO	jenkins.InitReactorRunner$1#onAttained: Completed initialization
2022-12-27 08:40:01.127+0000 [id=22]	INFO	hudson.lifecycle.Lifecycle#onReady: Jenkins is fully up and running
```

![Jenkins Password](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227164223.png)

选择需要的安装模式，我这里选择了推荐的插件。

![Jenkins Plug](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227164548.png)

等待插件安装完成，速度取决于网络环境。

![Jenkins install plug](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227165041.png)

创建管理员账号

![account](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227165636.png)

设置URL

![URL](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227165806.png)

安装完成

![finish](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227165837.png)

Jenkins首页

![index](https://cdn.jsdelivr.net/gh/kid-1412-ll/blog-imgbed@master/img/20221227165926.png)