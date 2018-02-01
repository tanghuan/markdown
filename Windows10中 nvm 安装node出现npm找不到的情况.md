解决Window10中使用nvm安装node，出现npm找不到的情况。

默认情况下nodejs是安装在 C:\Program Files\nodejs 中的

![node_home](https://github.com/tanghuan/markdown/blob/docker-attachment/node_home.png)

默认情况nvm是安装在 C:\Users\Arthur\AppData\Roaming\nvm

![nvm_home](https://github.com/tanghuan/markdown/blob/docker-attachment/nvm_home.png)

正常情况下 nvm 安装node的同时会下载安装 npm 可以在对应的node版本的 node_modules 中查看

![node_modules](https://github.com/tanghuan/markdown/blob/docker-attachment/node_v894.png)
![npm_module](https://github.com/tanghuan/markdown/blob/docker-attachment/node_v894_modules_npm.png)

如果 npm -v 也是找不到命令，说明我们的npm根本没安装

![node_modules](https://github.com/tanghuan/markdown/blob/docker-attachment/node_v950.png)
![npm_module](https://github.com/tanghuan/markdown/blob/docker-attachment/node_v950_modules.png)

当我们在使用 nvm 切换版本的时候 nvm 实际上市使用脚本将 nvm 下对应版本的node 连接到 C:\Program Files\nodejs 上

![nvm_change_node](https://github.com/tanghuan/markdown/blob/docker-attachment/nvm_change_node.png)

所以node真正存放的地址是在 nvm 下面。

解决办法：
1. 删除 C:\Program Files\中 nodejs 连接 (一定要删除)

2. 删除 C:\Users\Arthur\AppData\Roaming\npm-cache 文件夹 (我是删除了的)
![npm-cache](https://github.com/tanghuan/markdown/blob/docker-attachment/nvm_npm-cache.png)

3. nvm 目录下对应的 node 版本

4. 重新安装