# 小米摄像头1080P-mjsxj02hl_firmware

这是一个小米摄像头1080P-mjsxj02hl 第三方固件

**注意！此固件已停止维护。我们建议使用 [OpenIPC](https://github.com/OpenIPC/device-mjsxj02hl).**

## 刷入教程

### 准备:

1.下载固件，放在一张**空白 FAT32格式**的TF卡里

https://github.com/kasitoru/mjsxj02hl_firmware/releases

2.下载刷机工具  [HiTool-HM-5.4.9-win32-x86_64](https://github.com/muzihuaner/device-mjsxj02hl/releases/tag/1.0)

3.下载引导  [u-boot-hi3518ev300.bin](https://github.com/muzihuaner/mjsxj02hl_firmware/blob/main/instructions/uboot_usb/u-boot-hi3518ev300.bin) 和[usb-burn.xml](https://github.com/muzihuaner/mjsxj02hl_firmware/blob/main/instructions/uboot_usb/usb-burn.xml)

4.下载[Zadig](https://github.com/muzihuaner/device-mjsxj02hl/blob/master/zadig-2.8.exe). 运行软件并打开完整的设备列表 (`Settings -> List of all devices`).

将摄像头连接到计算机的 USB 端口（套件附带的电线不起作用 - 里面没有数据触点，换数据线）并按下重置按钮，尽快从列表中选择设备HiUSBBurn并安装驱动libusbK程序。可能第一次不会成功（设备在几秒钟后消失，需要非常快地完成所有操作）。

### 使用HiTool

1.选择芯片 **hi3518ev300**

传输方式:USB 

选择按分区烧写，分区表文件选择我们下载的usb-burn.xml ，fastboot 选择u-boot-hi3518ev300.bin

其他不用管，点击烧写

2.使用microusb数据线（原装线，只能充电）连接摄像头，按住reset按钮，插入电脑USB，开始刷机...

3.刷机完成后拔掉USB，插入装有固件的TF卡，按住reset按纽，连接电源启动，当灯从橙色切换到白色时，释放reset按钮

> 如果听到语音提示或指示灯始终未变为白色，则说明操作出错。可能需要将 SD 卡重新插入电脑进行格式化，并删除相机在启动过程中创建的文件。将固件复制到 SD 卡中再次尝试。

4.当指示灯恢复为橙色时，搜索无线网络*MJSXJ02HL*并连接。但请注意，需要手动配置你的电脑网络设置：

```yaml
IP 192.168.1.10
子网掩码: 255.255.255.0
网关: 192.168.1.1
```

之后，电脑应该就可以成功连接。打开浏览器，访问 192.168.1.1，设置 Wi-Fi 网络

5.设置完成的摄像头现在已连接到网络。要查看 IP 地址，可以使用“Fing”之类的应用程序，或者在路由器上查看摄像头的地址。

6.访问摄像头的地址，例如：192.168.123。登录摄像头：
默认  用户名：root，密码：**toor**

 

## 固件说明

虽然该软件基于制造商的原始镜像，但所有与中国服务器交互的功能均已移除，并且控制应用程序代码已完全重写。

#### 使用方法

要连接到 RTSP 服务器，例如，可以使用 [VLC 媒体播放器](https://www.videolan.org/vlc/)（“媒体” -> “打开 URL”）。

网络地址格式为：rtsp://<IP 地址>:<端口>/<通道名称>，其中 <IP 地址>、<端口> 和 <通道名称> 需要替换为你自己的值（默认端口：554，通道名称：primary（主通道）或secondary（辅助通道）。

例如

```
rtsp://192.168.10.104:554/primary
rtsp://192.168.10.104:554/secondary
```

要使用 MQTT 功能，您必须在设置中指定代理连接详细信息。

有关可用主题的信息，请点击[此处](https://github.com/kasitoru/mjsxj02hl_application#mqtt)。

支持使用存储卡上的 run.sh 文件自动运行任意命令

如果需要 Telnet 或 FTP 访问，则必须使用与 Web 界面相同的帐户凭据进行连接。

更新 1. 将摄像头连接到 Home Assistant（如果连接失败，

请尝试在设置中将视频流切换为 h264）：

```
静止图像: "http://192.168.1.10/cgi-bin/get_image.cgi"
视频流: "rtsp://192.168.1.10:554/primary"
camera: - platform: generic name: mjsxj02hl 
still_image_url:
"http://192.168.1.10/cgi-bin/get_image.cgi" stream_source:
"rtsp://192.168.1.10:554/primary"
camera:
 - platform: generic
 name: mjsxj02hl
 still_image_url: "http://192.168.1.10/cgibin/get_image.cgi"
 stream_source: "rtsp://192.168.1.10:554/primary"
或:
input: "rtsp://192.168.1.10:554/primary"
- platform: ffmpeg name: mjsxj02hl input:
"rtsp://192.168.1.10:554/primary"
 - platform: ffmpeg
 name: mjsxj02hl
 input: "rtsp://192.168.1.10:554/primary"
```

更新2：刷机前强烈建议备份出厂配置分区。为此，请使用固件，并在存储卡上创建一个包含以下内容的 run.sh 文件。

```
cat /dev/mtdblock6 > /mnt/mmc/backup_config.bin
#!/bin/sh cat /dev/mtdblock6 > /mnt/mmc/backup_config.bin
#!/bin/sh
cat /dev/mtdblock6 > /mnt/mmc/backup_config.bin
```

更新 3. 型号为 MJSXJ03HL (2K) 的新相机版本目前不受此固件支持！它使用了完全不同的硬件（最重要的是，处理器也不同）。

更新 4：感谢 @ZigFisher，我们已成功在设备上运行 OpenIPC。更多详情请点击[此处](https://github.com/OpenIPC/device-mjsxj02hl)。

您可以从项目 GitHub 代码库的相应页面下载固件。

注意！以上所有操作均需自行承担风险。作者对因使用本页面发布的材料、软件及其他信息而对用户或第三方造成的任何损害不承担任何责任。


# 编译固件

## 准备

1. 安装依赖

```bash
sudo apt install git cmake lib32z1 lib32stdc++6 u-boot-tools dbus python3-pip dos2unix
pip3 install click
```

2. 安装 Hi3518Ev300 工具链：

```bash
tar -zxf arm-himix100-linux.tgz
cd arm-himix100-linux
sudo ./arm-himix100-linux.install
sudo mkdir -p /opt/hisi-linux/x86-arm/arm-himix100-linux/target/usr/app/lib
sudo chmod 777 /opt/hisi-linux/x86-arm/arm-himix100-linux/target/usr/app/lib
gnome-session-quit
```

## 使用

克隆仓库：

```bash
git clone https://github.com/kasitoru/mjsxj02hl_firmware
cd mjsxj02hl_firmware
```

### 构建固件：
```bash
make FIRMWARE_VER=x.y.z
```

### 解压镜像：
```bash
make FIRMWARE_FILE=image.bin FIRMWARE_DIR=unpkg unpack
```

或者（默认输入文件为 `demo_hlc6.bin`，输出目录为 `temp/firmware`）：

```bash
make unpack
```

### 打包镜像：
```bash
make FIRMWARE_DIR=unpkg FIRMWARE_FILE=image.bin pack
```

或者（默认输入目录为`temp/firmware`，输出文件为“demo_hlc6.bin”）：

```bash
make pack
```
