# Linux 记录
## Linux环境

> Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

## Linux 安装 vncServer

### 1. 安装 desktop-ubuntu

**如果遇到软件版本不满足，则可以使用 aptitude 进行降级**

~~~
安装 aptitude
sudo apt-get install aptitude

aptitude 命令解释
aptitude install 包名=包的版本号  

~~~ 

Vnc4Server  分辨率修改
~~~
vncserver :1 -geometry 1920x1080 -depth 24
~~~

VNC4Server的 ~/.vnc/xstartup 记录
~~~
#xsetroot -solid grey
#vncconfig -iconic &
#x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
#x-window-manager &
#x-session-manager &
#gnome-panel &
#gnome-settings-daemon &
metacity &
nautilus &
#gnome-terminal &

需要安装
apt-get install xfce4
apt-get install ubuntu-unity-desktop
xfc 桌面
# x-session-manager &
 xfdesktop & xfce4-panel &
 xfce4-menu-plugin &
    xfsettingsd &
    xfconfd &
    xfwm4 &
~~~

阿里云文档链接
https://help.aliyun.com/document_detail/59330.html?spm=a2c4g.11186623.6.607.39ca5c3e8LNu7a