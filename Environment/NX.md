# 北洋机甲视觉组—Jetson Xavier NX部署过程

### [开始之前—注意事项]

1.本教程只适合于正版NX，如果你使用的是盗版NX，请立即将其扔掉

2.本教程支持支持ubuntu18.04的安装



### [开始之前—需要的设备]

1.一台可以科学上网的个人电脑

2.一个NX，以及其充电器

3.一张sd卡（根据自己的电脑查看是否需要读卡器）

4.显示屏，显示屏充电线，充电头

5.连接显示屏与NX的线（NX支持DP/Hdmi输出，还需结合你的显示屏支持什么输入）

6.键盘，鼠标

7.沉着，冷静，小心

8.**chatgpt**（可以用来查询你输入的指令有什么意义）



### [安装操作系统]

注意，NX没有内置外存，而是选择让我们使用外接sd卡当作外存。

#### 1. 在官网下载镜像

https://developer.nvidia.com/embedded/downloads

如果要安装18.04的ubuntu，可以选择下载Jetson Xavier NX Developer Kit SD Card Image 4.6

#### 2. 将镜像烧录到sd卡中

请参考https://developer.nvidia.com/embedded/learn/get-started-jetson-xavier-nx-devkit#write

根据你的操作系统类型选择不同的方法

#### 3. 将sd卡插入NX中，接上电源，开机，设置ubuntu操作系统

注意，为了方便队伍内协同工作，账号密码统一设置为tjurm



### [安装环境]

#### 1.联网

#### 2.换源

首先，换源需要根据咱们电脑的ubuntu系统版本以及指令集体系结构去进行选择。

查看ubuntu系统版本的方式：

```bash
lsb_release -a
```

查看体系架构的方式：

```bash
uname -m
```

然后根据查找到的这两种信息去CSDN查询

eg： 我们拿到的官方Jetson Xavier NX，应该查询：ubuntu 18.04 arm换源

这里提供快速对ubuntu_18.04_arm进行换源(华为源)的方法：

```bash
sudo wget -O /etc/apt/sources.list https://repo.huaweicloud.com/repository/conf/Ubuntu-Ports-bionic.list
sudo apt-get update
```

#### 3.安装jtop

因为我们需要使用pip3来安装jtop，所以需要在系统中先安装pip3

```bash
sudo apt install python3-pip
```

[**注意**：linux系统下的普通用户在执行apt install指令时，前面必须加上sudo！但是普通用户要使用sudo的话，是需要输入密码的，所以推荐进行sudo免密的设置！下面有教程的]

接着安装jtop

```bash
sudo -H pip3 install -U jetson-stats
```

重启系统

```
reboot
```

使用jtop

```bash
jtop
```

查看当前cpu，gpu使用状态（点击最下面一行的2GPU/3CPU即可）

设置风扇状态（点击最下面一行的6CTRL，选择manual人工控制，在speed后点击’+‘直到100%）

#### 4.安装大恒相机驱动

https://www.daheng-imaging.com/downloads/

下载Galaxy Linux-armhf-Gige-U3 SDK_CN-EN 以及  Galaxy Linux-Python SDK_CN-EN

解压后按照readme中提示安装！

注意，Galaxy Linux-armhf-Gige-U3 SDK_CN-EN的安装可能需要输入如下指令：

```bash
bash ./Galaxy_camera.run
```

注意，Galaxy Linux-Python SDK_CN-EN要装适配python3的，在readme文档下面，别装2.7的

#### 5.eigen的安装与软链接

安装:

```bash
sudo apt install libeigen3-dev
```

链接：

```bash
cd /usr/include
sudo ln -sf eigen3/Eigen Eigen
sudo ln -sf eigen3/unsupported unsupported
```

#### 6.Tailscale安装

```bash
sudo apt install curl
curl -fsSL https://tailscale.com/install.sh | sh
```

启动
```bash
tailscale up 
```

#### 7.vscode安装

下载debian包，网页打开

```
https://update.code.visualstudio.com/1.66.1/linux-deb-arm64/stable
```

进入到下载目录，输入：

```bash
sudo apt install ./code...
```

#### 8.ssh配置

请参照[Ubuntu: 配置ssh，保姆级教程](https://blog.csdn.net/weixin_44197719/article/details/119888235)，注意这里面要用到vim，请自行学习！

SSH免密登录可以通过在客户端和服务器之间设置公钥认证来实现。具体步骤如下：

在客户端生成公钥和私钥。可以使用以下命令生成：

```
ssh-keygen -t rsa
```

该命令将生成一个RSA密钥对，包括公钥和私钥。按照提示输入文件名和密码（可选），如果不需要密码则直接回车即可。

将客户端公钥复制到服务器上的authorized_keys文件中。可以使用以下命令将公钥复制到服务器上：

```
ssh-copy-id username@servername
```

其中，username是服务器上的用户名，servername是服务器的主机名或IP地址。该命令会将公钥自动添加到服务器上的~/.ssh/authorized_keys文件中。

这样，就设置完成啦，以后ssh连接就不用输入密码了！


#### 8.5 cuda链接

报错：
```bash
    /usr/bin/ld: cannot find -lcudart
    /usr/bin/ld: cannot find -lcublas
```

解决方案：
不行就find找到cudart和libcublas的位置
```bash
    sudo ln -s /usr/local/cuda-11.4/targets/aarch64-linux/lib/libcudart.so.11.4.298 /usr/local/lib/libcudart.so
    sudo ln -s /usr/local/cuda-11.4/targets/aarch64-linux/lib/libcublas.so.11.6.6.84 /usr/local/lib/libcublas.so
```


#### 9.ros环境配置

请参照[ROS安装教程(ubuntu18.04+melodic版本)](https://blog.csdn.net/KIK9973/article/details/118755045)，建议安装桌面完整版！

#### 10.ros cv_bridge环境配置

请参照https://zhuanlan.zhihu.com/p/392939687与https://blog.csdn.net/qq_33980935/article/details/123132452 

ps1:按第一个走的话，在编译的时候会出错，所以在编译前需要按照第二个博客改几个地方！

Ps2:使用cv_bridge的时候一定要按照第一个博客指定cv_bridge的cmake位置！！

#### 11.NX升频

首先，cpu主频越高，处理能力越好。然而，Nvidia Jetson系列所有的设备cpu主频均不高，远低于pc机，更可恶的是，Jetson系列设备默认没有给到满频运行，需要我们手动设置，以便让其运行的稍微再快一点！

```bash
sudo vi /etc/nvpmodel.conf
```

将里面的MAX_FREQ全部改成1907200，这就可以实现手动升频啦awa

[注意这里的数值每种设备都不一样啊，这里以Jetson Xavier Nx为例，其他设备请自查]

#### 12.安装带cuda的opencv

在安装之前，请先确认你是否有安装带cuda opencv的必要，请参考[一文彻底搞懂为什么OpenCV用GPU/cuda跑得比用CPU慢？](https://blog.csdn.net/libaineu2004/article/details/129801112)

完整安装方法请看[Jetson Xavier NX OpenCV 安装](https://zhuanlan.zhihu.com/p/411901208)

[注意在使用上述方法安装时，不仅make的时间会很长，**sudo make install**的时间也会很长，原因未知]

#### 13.安装realsense sdk

[官方教程](https://github.com/IntelRealSense/librealsense/blob/master/doc/distribution_linux.md)

```bash
sudo mkdir -p /etc/apt/keyrings

sudo apt install curl

curl -sSf https://librealsense.intel.com/Debian/librealsense.pgp | sudo tee /etc/apt/keyrings/librealsense.pgp > /dev/null

sudo apt-get install apt-transport-https

echo "deb [signed-by=/etc/apt/keyrings/librealsense.pgp] https://librealsense.intel.com/Debian/apt-repo `lsb_release -cs` main" | \
sudo tee /etc/apt/sources.list.d/librealsense.list

sudo apt-get update

sudo apt-get install librealsense2-utils

realsense-viewer
```

#### 14.安装realsense ros

首先进入ros工作空间的src下

```bash
下载源码到该目录:https://github.com/IntelRealSense/realsense-ros/releases/tag/2.3.0
git clone https://github.com/pal-robotics/ddynamic_reconfigure.git
cd ..
catkin_make
rospack profile
```

#### 15.vins-fusion-gpu部署

首先进入ros工作空间的src下

```bash
git clone https://github.com/pjrambo/VINS-Fusion-gpu.git
```

#### 16.相机标定工具包环境配置

拍摄大恒相机的图片需要安装python的PIL库，但在安装前需要先安装一些依赖，否则PIL无法成功安装，而且不要想当然的认为库的名字是PIL，就应该使用`pip3 install PIL`指令去进行安装！（这点有点类似ros里面的软件包，包的名字不一定是src下的文件夹名！！）

```bash
安装依赖
sudo apt-get install python3-dev python3-setuptools
sudo apt-get install libtiff5-dev libjpeg8-dev libopenjp2-7-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python3-tk libharfbuzz-dev libfribidi-dev libxcb1-dev
装pillow
pip3 install pillow
```



#### 18.sudo免密设置！

首先修改sudoers文件权限（**建议**自行查询并了解chmod的用法）（注意，/etc/sudoers这个文件默认**所有用户都没有**写权限，所以我们需要使用chmod命令提权）

```shell
$ sudo chmod 770 /etc/sudoers
```

使用vim，修改sudoers配置文件

```bash
#%sudo ALL=(ALL:ALL) ALL 					# 找到这一行，在开头添加"#"
%sudo ALL=NOPASSWD: ALL						# 添加新的一行 %sudo ALL=NOPASSWD:ALL
```

将sudoers文件权限改回最初状态（**提问**：chmod 440代表着什么）

```shell
$ sudo chmod 440 /etc/sudoers
```

#### 20.配置科学上网！[**配置失败？好像又成了**，有尝试成功的hxd请联系我交流] 

源码下载

```bash
git clone https://github.com/wanhebin/clash-for-linux.git
```

进入到项目目录，编辑start.sh脚本文件，修改变量URL的值,此值为Clash订阅地址。

（把原来URL=后面的东西删掉，改成Clash订阅地址）

```bash
cd clash-for-linux
vim start.sh
```

启动脚本，开启clash服务

```bash
sudo bash start.sh
```

开启代理

```bash
source /etc/profile.d/clash.sh
proxy_on
```

关闭服务，关闭代理

```bash
sudo bash shutdown.sh
source /etc/profile.d/clash.sh
proxy_off
```

#### 21.QR码识别ros包环境

安装依赖库(QR码识别这一功能主要依靠zbar库实现！)

```bash
sudo apt install libzbar-dev
```

下载源码(当然要下载到ros工作空间里面了)

```bash
git clone https://github.com/mdrwiega/qr_detector.git
```

不全，候补



#### 23.yolov5环境配置[**配置失败**，有尝试成功的hxd请联系我交流] 

[什么？为什么要在nx上面配置yolov5的环境？？当然是因为nx上面有8G显存可以白嫖awa]

[由于直接配置yolov5的环境非常复杂，所以我们直接使用**docker**，请自行了解相关知识]

nx是自带docker的，故我们可以直接拉取**镜像**（注意我们这里选用arm64版本的）

```bash
sudo docker pull ultralytics/yolov5:latest-arm64
```

创造一个镜像实例（称为**容器**）并运行（注意，我们需要挂载数据集到容器里，同时需要开启gpu模式）

#### 24.usb小相机环境配置

```bash
sudo apt install v4l-utils//安装v4l2工具包
sudo v4l2-ctl --list-devices//通过v4l2查看摄像头设备

//当同时连接多个摄像头时，指定摄像头的两种方法
sudo v4l2-ctl -d /dev/video0 --all//查看/dev/video0摄像头所有参数
sudo v4l2-ctl --device=/dev/video1 --all//查看/dev/video1 摄像头所有参数

sudo v4l2-ctl -d /dev/video0 --list-formats//查看当前摄像头支持的视频压缩格式
sudo v4l2-ctl -d /dev/video0 --list-formats-ext//查看当前摄像头支持的分辨率和帧速率
sudo v4l2-ctl -d /dev/video0 --set-parm=30//设置帧率
sudo v4l2-ctl --list-framesizes=MJPG -d /dev/video0//查看摄像头所支持的分辨率
sudo v4l2-ctl --set-fmt-video=width=320,height=240,pixelformat=YUYV

ls /dev/v*//看到"/dev/video0",表示成功驱动
lsusb//查看摄像头型号

//曝光参数调整
v4l2-ctl -d /dev/video0 --list-ctrls
//当exposure_auto=1( V4L2_EXPOSURE_MANUAL )时可设置曝光绝对值
v4l2-ctl --set-ctrl=exposure_auto=1 --device=/dev/video0
v4l2-ctl --set-ctrl=exposure_absolute=15 --device=/dev/video0 

lsmod//列出可用模块
sudo rmmod uvcvideo//移除模块
sudo modprobe uvcvideo nodrop=1 timeout=5000
```

对于需要好几个相机，导致带宽不够的情况，建议参考：[摄像头读取出现VIDIOC_STREAMON: No space left on device 错误](https://blog.csdn.net/houge101/article/details/102665403)

防止链接失效，给出关键内容：

```bash
降低打开视频流的分辨率，改为320x240;并对uvcvideo驱动设置参数，强制为camera分配带宽时计算所需带宽而非申请全部带宽；（只对YUYV格式有效，对有些camera此方法可以支持640x480分辨率）
sudo rmmod uvcvideo
sudo modprobe uvcvideo quirks=128

或者添加 etc/modprobe.d/uvccamera0508.conf 文件，内容如下：
options uvcvideo quirks=128
```

#### 25.zsh安装

- zsh，一款基于bash的，可以安装很多**好看主题**以及**有用插件**（git辅助，自动补全等）的shell

首先是zsh，Oh My Zsh，以及zsh一些插件的安装

```bash
# 安装 Zsh
sudo apt install zsh
# 将 Zsh 设置为默认 Shell
chsh -s /bin/zsh
# 安装 Oh My Zsh
sudo apt install wget
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh
bash ./install.sh
# 安装历史命令补全插件（注意这里clone的是github的仓库，所以你知道该怎么办了吧）
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
# 安装命令行语法高亮插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

接着是zsh配置的修改，位于~/.zshrc，可以使用vim打开（提示：请自行查找vim使用方法，**快速定位**下述配置的位置！）

```bash
# 启动错误命令自动更正
ENABLE_CORRECTION="true"
# 在命令执行的过程中，使用小红点进行提示
COMPLETION_WAITING_DOTS="true"
# 启用已安装的插件
plugins=(
  git zsh-autosuggestions zsh-syntax-highlighting
)
```

