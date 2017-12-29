# CentOS7 安装 vsftp 和 配置虚拟用户
介绍在 CentOS7 中 安装 vsftp 和 配置虚拟用户。

### 1. 安装vsftp

    $ yum install -y vsftpd

### 2. 创建 vsftp 虚拟宿主账户

    $ useradd vsftp -s /sbin/nologin

### 3. 修改 vsftpd.conf

    $ vi /etc/vsftpd/vsftpd.conf

    # 禁止匿名访问
    anonymous_enable=NO

    # 设定启用虚拟用户功能
    guest_enable=YES

    # 指定虚拟宿主账户
    guest_username=vsftp

    # 设定虚拟用户的权限符合他们的宿主用户
    virtual_use_local_privs=YES

    # 设定虚拟用户配置文件存放路径
    user_config_dir=/etc/vsftpd/vconf

    # 开启用户不能出根目录
    chroot_local_user=YES
    allow_writeable_chroot=YES

    注意：有些较低版本只需要配置chroot_local_user=YES 如果配置了 allow_writeable_chroot=YES 启动会出现 500 oops 错误，测试过的vsftpd 2.2.2 会出现，根据情况调整。

  其它配置项保持默认即可。

### 4. 创建虚拟用户

    $ mkdir /etc/vsftpd/vconf
    $ vi /etc/vsftpd/vconf/users

    输入：
    # 用户名
    th
    # 密码
    th123
    # 用户名
    tiger
    # 密码
    tiger666

  注意：users 文件中奇数行为用户名，偶数行为密码。

### 5. 生成数据库

    $ db_load -T -t hash -f /etc/vsftpd/vconf/users /etc/vsftpd/vconf/users.db

  执行完成以后在 users 所在的目录，生成了一个 users.db 文件。

### 6. 修改数据库文件的访问权限

    $ chmod 600 /etc/vsftpd/vconf/users
    $ chmod 600 /etc/vsftpd/vconf/users.db

### 7. 修改 vsftp 登录时候的源

    $ vi /etc/pam.d/vsftpd

    末尾添加：
    auth      required  pam_userdb.so   db=/etc/vsftpd/vconf/users
    account   required  pam_userdb.so   db=/etc/vsftpd/vconf/users

### 8. 创建用户配置文件
  控制某一个用户的访问权限,这里以 th 为例，在vconf下创建一个和 th 同名的文件。

    $ vi /etc/vsftpd/vconf/th

    内容如下：

    # 虚拟用户的根目录，根目录必须放到vsftp中
    local_root=/home/vsftp/th
    anonymous_enable=NO
    write_enable=YES
    local_umask=022
    anon_upload_enable=NO
    anon_mkdir_write_enable=NO
    idle_session_timeout=600
    data_connection_timeout=120
    max_clients=10
    max_per_ip=5

    # 本地用户的最大传输速度，单位是Byts/s，这里设定10M
    local_max_rate=1048576

    # 是否允许下载文件  
    download_enable=YES

    # 是否允许递归查询文件
    ls_recurse_enable=YES
    dirlist_enable=YES

    # 禁止删除文件和目录
    cmds_denied=DELE,RMD

    # 允许创建文件夹
    cmds_allowed=MKD

  cmds的指令如下：

| 指令     | 操作     |
| :-------------: | :-------------: |
| ABOR       | 终止文件传输       |
| CWD       | 切换工作目录       |
| DELE       | 删除文件       |
| LIST       | 列表文件       |
| MDTM       | 返回文件的修改时间       |
| MKD       | 创建文件夹       |
| NLST       | 列表 ftp 中文件名       |
| PASS       | 发送密码       |
| PASV       |        |
| PORT       | 开启一个数据端口       |
| PWD       | 打印工作目录路径       |
| QUIT       | 断开连接       |
| RETR       |        |
| RMD       | 删除文件夹       |
| RNFR       |        |
| RNTO       | 重命名为XX       |
| SITE       |        |
| SIZE       | 返回文件大小       |
| STOR       | 保存一个文件到远程主机       |
| TYPE       | 删除传输类型       |
| USER       | 发送用户名       |
| APPE       | 追加内容到文件       |
| STAT       | 返回 ftp 服务状态       |
| MODE       | 设置传输模式       |
| ...       | ...       |


### 9. 创建虚拟用户根目录

    $ mkdir /home/vsftp/th

    # 修改目录的所属用户 为 vsftp 不然上传文件时出现 553 错误
    $ chown vsftp.vsftp /home/vsftp/th

### 10. 配置SSL

#### 1. 生成SSL证书

    $ mkdir /etc/vsftpd/.ssl
    $ cd /etc/vsftpd/.ssl
    $ openssl req -new -x509 -nodes -out vsftpd.pem -keyout vsftpd.pem -days 3650

#### 2. 配置证书

    $ vi /etc/vsftpd/vsftpd.conf

    追加如下：

    # 启用ssl
    ssl_enable=YES

    # 指定 vsftpd 支持TLS v1
    ssl_tlsv1=YES

    # 指定vsftpd支持SSL v2
    ssl_sslv2=YES

    # 指定vsftpd支持SSL v3
    ssl_sslv3=YES

    # 强制匿名用户使用加密登陆和数据传输，若为NO时，可选加密或者不加密
    force_local_logins_ssl=YES
    force_local_data_ssl=YES

    # SSL证书路径
    rsa_cert_file=/etc/vsftpd/.sslkey/vsftpd.pem

## 问题

1. 553 Could not create file.严重文件传输错误

  虚拟用户的主目录拥有者不对，应该是 vsftp 虚拟宿主账户。

    $ chown vsftp.vsftp /home/vsftp/th

2. 登录500 OOPS: vsftpd: refusing to run with writable root inside chroot()

  严重错误: 无法连接到服务器  
  配置了用户只能访问自己的主目录 chroot_local_user=YES 但是未添加allow_writeable_chroot=YES配置。

  chroot_local_user=YES  
  allow_writeable_chroot=YES  
  高版本 vsftp 必须同时配置。
