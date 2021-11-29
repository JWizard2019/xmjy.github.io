[toc]

# HydroOJ开发者环境部署

## 安装MongoDB

```
wget -qO - https://www.mongodb.org/static/pgp/server-4.4.asc | sudo apt-key add -
```

```
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
```

```
sudo apt-get update
```

```
sudo systemctl start mongod
```

通过以下命令检验mongod是否启动

```
sudo systemctl status mongod
```



![image-20211119023642486](C:\Users\76065\AppData\Roaming\Typora\typora-user-images\image-20211119023642486.png)

设置mongod自启动

```
sudo systemctl enable mongod
```



## 安装MINIO

在浏览器中输入下面的网址，下载minio二进制文件

```
https://tjc-download.ftn.qq.com/ftn_handler/33df16d09d6c084413db62c87d2f1616c19c58087c736ad823dd5f00a92521f7e7bd2c385e9d1d4e54259fb3cb657f493c277e33b475f8dc6a06b5b1dd1266c5/?fname=minio&k=5f6531373238049badc6671a1438534e045653550a5b02024806000e0115550550011c55575b044c56500403040e515153045004323d610c0c0b58583205&code=ee1728aa&fr=00&&txf_fid=97a89841da8a8d9cb6ed04b046046620cea9a054&xffz=94044160
```

在包含该二进制文件的文件夹中执行命令：

```
chmod +x minio
```

```
MINIO_ROOT_USER=admin MINIO_ROOT_PASSWORD=password ./minio server /mnt/data --console-address ":9001"
```

新建一个终端



## 安装NODEJS

```
apt install curl
```

```bash
curl -o- https://cdn.jsdelivr.net/gh/nvm-sh/nvm@v0.36.0/install.sh | sed 's/raw.githubusercontent.com\/nvm-sh\/nvm\//cdn.jsdelivr.net\/gh\/nvm-sh\/nvm@/g' | sed 's/github.com\/nvm-sh\/nvm.git/gitee.com\/imirror\/nvm/g' | bash
export NVM_DIR=/root/.nvm
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
NVM_NODEJS_ORG_MIRROR=https://mirrors.tuna.tsinghua.edu.cn/nodejs-release nvm install 14
nvm use 14
npm i yarn -g
```



## 安装Clash代理

[Releases · Dreamacro/clash (github.com)](https://github.com/Dreamacro/clash/releases)

在上面的链接下载linux-amd64版本，解压

```bash
gunzip clash-linux-amd64-v1.8.0.gz
```

```bash
wget -O config.yaml [订阅地址]
chmod +x clash-linux-amd64-v1.8.0
./clash-linux-amd64-v1.8.0 -d .
```

在设置-网络中，点击network proxy右侧的按钮，输入如下参数

![image-20211119035550019](../AppData/Roaming/Typora/typora-user-images/image-20211119035550019.png)

```bash
apt install git
```

让git命令也通过代理：

```
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890
```



## 安装Hydro

```bash
git clone https://gitee.com/JWizard/Hydro.git /root/Hydro --recursive # 下载至 /root/Hydro 文件夹
cd /root/Hydro # 进入工作目录
```

```
yarn # 安装依赖包
yarn build:ui:production # 编译前端
```

```bash
npx hydrooj addon add @hydrooj/ui-default
yarn build:ui
yarn debug --port=2333
```

在浏览器打开localhost:2333，进入如下页面，点击confirm

![image-20211119040520295](../AppData/Roaming/Typora/typora-user-images/image-20211119040520295.png)

![image-20211119040618550](../AppData/Roaming/Typora/typora-user-images/image-20211119040618550.png)

ctrl+c退出当前运行，再次输入`yarn debug --port=2333`重启



## 初始化

![image-20211119040943290](../AppData/Roaming/Typora/typora-user-images/image-20211119040943290.png)

注册一个账号后，查看其UID

![image-20211119041234874](../AppData/Roaming/Typora/typora-user-images/image-20211119041234874.png)

设置其为管理员

```
sudo su
cd /root/Hydro/
npx hydrooj cli user setSuperAdmin 2
```

登出后，重新登陆，可以看到控制面板

点击控制面板-系统设置

配置如下信息

![image-20211130005830217](../AppData/Roaming/Typora/typora-user-images/image-20211130005830217.png)



## 重启电脑后hydro如何重启

进入之前的minIO二进制文件所在文件夹

重启minIO

```bash
MINIO_ROOT_USER=admin MINIO_ROOT_PASSWORD=password ./minio server /mnt/data --console-address ":9001"
```

如果遇到如下报错

![image-20211130002957279](../AppData/Roaming/Typora/typora-user-images/image-20211130002957279.png)

或

![image-20211130005030166](../AppData/Roaming/Typora/typora-user-images/image-20211130005030166.png)

需要删除之前的状态文件

```bash
ls -a /mnt/data
```

![image-20211130003100081](../AppData/Roaming/Typora/typora-user-images/image-20211130003100081.png)

删除.minio.sys内的所有文件

```bash
cd /mnt/data
cd .minio.sys/
rm -rf *
```

然后重启minIO，显示如下界面为正常

![image-20211130005106379](../AppData/Roaming/Typora/typora-user-images/image-20211130005106379.png)

如出现端口冲突、drives不正确的情况

登录http://127.0.0.1:9001/dashboard  查看drives数量，以下为正确配置时的情况

![image-20211130005308065](../AppData/Roaming/Typora/typora-user-images/image-20211130005308065.png)

如果发现配置不正确，重启电脑/虚拟机后重新再试



重新开一个终端窗口

```bash
sudo su
cd /root/Hydro
export NVM_DIR=/root/.nvm
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
NVM_NODEJS_ORG_MIRROR=https://mirrors.tuna.tsinghua.edu.cn/nodejs-release nvm install 14
```

```bash
yarn debug --port=2333
```



如果虚拟机在重启后无法上网，把网络设置为automatic

![image-20211130003656196](../AppData/Roaming/Typora/typora-user-images/image-20211130003656196.png)

或者进入clash文件目录下开启clash

```bash
./clash-linux-amd64-v1.8.0 -d .
```

