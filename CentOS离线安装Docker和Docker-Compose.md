# CentOS7 离线安装Docker & Docker-Compose
介绍CentOS7下如何离线安装Docker和Docker-Compose，CentOS镜像为 CentOS-7-x86_64-Minimal-1708.iso。

## 安装 Docker
介绍如何离线安装Docker。

1. 下载Docker

    [Docker](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)  下载  docker-ce-17.09.1.ce-1.el7.centos.x86_64.rpm

2. 下载依赖

    [container-selinux-2.9 下载](http://rpm.pbone.net/index.php3/stat/4/idpl/40704222/dir/centos_7/com/container-selinux-2.9-4.el7.noarch.rpm.html)

    [其他依赖下载](https://pkgs.org/download/)

    依赖列表：

        container-selinux-2.9-4.el7.noarch.rpm
        checkpolicy-2.5-4.el7.x86_64.rpm
        bzip2-libs-1.0.6-13.el7.i686.rpm
        audit-libs-python-2.7.6-3.el7.x86_64.rpm
        glibc-2.17-196.el7.i686.rpm
        libcgroup-0.41-13.el7.x86_64.rpm
        libgcc-4.8.5-16.el7.i686.rpm
        libselinux-2.5-11.el7.i686.rpm
        libsemanage-python-2.5-8.el7.x86_64.rpm
        libsepol-2.5-6.el7.i686.rpm
        libstdc++-4.8.5-16.el7.i686.rpm
        libtool-ltdl-2.4.2-22.el7_3.x86_64.rpm
        policycoreutils-python-2.5-17.1.el7.x86_64.rpm
        python-ipython-2.2.0-1.el7.noarch.rpm
        setools-libs-3.3.8-1.1.el7.i686.rpm
        sqlite-3.7.17-8.el7.i686.rpm

      如果你不想去下载这些rpm包我也准备了：
      [百度网盘](https://pan.baidu.com/s/1dEMg6nZ)  密码：fhhi

3. 安装Docker

    离线版本的依赖比较多，如果要理清他们的关系逐个安装存在很大的难度，好在rpm提供了一键安装的功能，将docker的rpm放到一起。

        $ rpm -ivh * --nodeps --force

        注意：在使用这个命令安装的时候，最好把这些rpm和其他文件分开否则会安装报错。

4. 配置当前用户对docker的操作权限

    docker在安装的过程中会创建用户组 docker，如果用户不存在，使用如下命令创建

        $ sudo groupadd docker

    将当前登录用户添加进 docker 用户组

        $ sudo usermod -aG docker $USER

    例如：

        $ sudo usermod -aG docker arthur

5. 设置开机启动

        $ sudo systemctl enable docker

6. 启动Docker

        $ systemctl start docker

        or

        $ service docker start

7. 检查是否启动成功

        $ docker images

    如果没有出现如何错误说明安装Docker安装成功。

## 卸载Docker
1. 查找安装包

        $ rpm -qa docker-ce
        docker-ce-17.09.1.ce-1.el7.centos.x86_64

2. 卸载安装包

        $ rpm -e docker-ce-17.09.1.ce-1.el7.centos.x86_64

3. 删除镜像，容器，和配置数据

        $ rm -rf /var/lib/docker

## 问题
1. 所有依赖都在，但是rpm -ivh * --nodeps --force 报错

   可能是rpm中混入了其他文件。

## Docker-Compose 安装
介绍如何在线和离线安装Docker-Compose

1. 下载Compose

   [Compose](https://github.com/docker/compose/releases)  CentOS下载 docker-compose-Linux-x86_64

2. 拷贝文件

   将docker-compose-Linux-x86_64 拷贝到 /usr/local/bin/ 目录下

       $ cp docker-compose-Linux-x86_64 /usr/local/bin/docker-compose

3. 添加执行的权限

       $ sudo chmod +x /usr/local/bin/docker-compose

4. 检查Compose是否安装成功

       $ docker-compose --version

       结果类似如下说明成功了：
       docker-compose version 1.18.0, build 1719ceb

## 卸载
1. 直接删除docker-compose文件即可

       $ sudo rm /usr/local/bin/docker-compose
