# Ubuntu Server 16.04 安装VMBox增强和挂载Windows共享目录

## 1. 安装VMBox增强功能
因为Ubuntu Server 没有UI界面不能像Ubuntu Desktop那样点击下一步。
第一步：点击VMBox 安装增强功能的菜单
第二步：挂载增强包
  $ sudo mount /dev/cdrom /mnt/
第三步：安装增强包
  $ sudo /mnt/VBoxLinuxAdditions.run

## 2. 挂载Windows共享目录
第一步：在VMBox虚拟机设置中添加共享目录
    如：路径为：D:\folder1
        名称为：folder1
第二步：在虚拟机中挂载
  $ sudo mkdir -p /mnt/folder1
  $ sudo mount -tvboxsf folder1 /mnt/folder1

移除挂载
  $ sudo umount -f /mnt/folder1
