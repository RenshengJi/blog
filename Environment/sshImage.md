## 一、安装必要软件支持（自己的电脑和服务器端）
### ubuntu
```bash
    sudo apt update
    sudo apt install x11-apps vim ssh
```
### win/mac
    安装XQuartz：<https://www.xquartz.org>

## 二、配置自己电脑 和服务器端
### 1、服务器端 linux
#### 打开服务器配置文件
```bash
    sudo vim /etc/ssh/sshd_config
```
#### 在配置文件中确保以下没有被注释（无#），且yes或no正确对应
```bash
    PermitRootLogin yes
    X11Forwarding yes
    X11UseLocalhost no
    PasswordAuthentication yes
```
### 2、客户端（自己电脑）(ubuntu/mac)(win还没试过)
#### 打开客户端配置文件
```bash
    sudo vim /etc/ssh/ssh_config
```
#### 在配置文件中确保以下没有被注释（无#），且yes或no正确对应
```bash
    ForwardAgent yes
    ForwardX11 yes
    ForwardX11Trusted yes
```

## 三、开始连接（每次启动重复）
### 1、允许SSH服务器的X界面连接过来（+号不可少）

在自己的电脑（客户端）输入如下：
```bash
    xhost + <服务器ip>
```
### 2、SSH连接到服务器
```bash
    ssh -XY <服务器用户名>@<服务器ip>
```
### 3、服务器的显示重定位

(可以在服务器端，也可以在客户端的ssh连接窗口)
#### 方法一：
```bash
    export DISPLAY=<客户端ip>:0.0
```
#### 方法二：
```bash
    sudo vim /home/.bashrc
```
添加 export DISPLAY="<客户端ip>:0.0"
```bash
    source /home/.bashrc
```

### 4、测试(出来一个时钟窗口就对了)
```bash
    xclock
```

## 四、配置vscode(可选)
需要的插件有两个:
    remote X11
    remote X11（ssh）
配置ssh连接如下：
```
    Host $XXX$
    User $服务器名$
    HostName $服务器ip$
    Port 22（服务器端口）
    ForwardX11 yes
    ForwardX11Trusted yes
    ForwardAgent yes
```