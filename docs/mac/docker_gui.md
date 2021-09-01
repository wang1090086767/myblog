# docker容器在mac上的可视化配置

> 使用docker容器技术替换虚拟机和部署服务是当前的潮流和趋势。大公司使用docker+k8s可以进行计算资源的管理，对物理服务进行管理，部署服务和版本发布时按实际所需资源进行分配，做到资源的充分利用，最典型的是云服务的计算资源调度。而个人使用docker技术好处也很多，比如不同的业务和应用构建不同的镜像保证环境依赖的独立性；每个业务和应用使用不同的镜像，避免了在不同的机器上重新安装环境导致依赖版本不一致带来的结果不一致等问题；构建同一个镜像，不同的用户可以在任何地方快速的部署，高效快捷。

> 但默认的docker技术运行服务需要进行UI显示时就会碰到困难，因为默认的docker相当于专门的服务器，是没有图形化UI的。针对这个问题，本文介绍如何在mac上配置docker的可视化。即使用mac启动docker容器并运行程序时，显示的UI会映射到母机上，给人感觉就像是母机启动了该程序。

## 安装socat
> 安装socat，用于和mac主机之间的通讯。

~~~
brew install socat     # 可能需要cask命令安装： brew cask install socat
~~~
也可以使用源码安装

下载socat源码：http://www.dest-unreach.org/socat/download/
~~~
./configure --disable-fips
make
make install
~~~

## 安装xquartz
> 该工具是用于显示的工具，功能与linux的x11-xserver-utils一样
~~~
brew install xquartz
~~~
安装好之后重启电脑，在输入
~~~
~: echo $DISPLAY
~: /private/tmp/com.apple.launchd.nzm51qjuIW/org.macosforge.xquartz:0   # DISPLAY的值，若不重启，则其值为空
~~~
打开 xquartz
~~~
open -a xquartz
~~~
打开后在程序坞中可以看到其相应的图片

点击该图片，默认会弹出一个终端（因为这里使用的是企业微信截图，正常点击xquartz后企业微信这里显示“XQuartz”），如下：

点击左上方的“XQuartz”（上图中“企业微信”的位置）的偏好设置的“安全性”栏，将“鉴定连接”勾选（默认是没有勾选的）

配置后，关闭xquartz。

启动UI服务
启动socat
在母机终端输入如下命令，因为命令中使用了DISPLAY环境变量，所以启动前需要确认环境变量值的正确性。
~~~
socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"
~~~

启动服务后，如下，看着像卡住了一样，只要没有报错就说明启动成功了。

启动xquartz
open -a xquartz

容器配置
母机ip查询
在母机终端输入：
~~~
ifconfig en0
~~~

查询结果如下：红框部分为ip。


tips: docker容器访问母机，可使用host.docker.internal替代ip，这样就可以避免每次查询ip

容器环境变量DISPLAY配置
~~~
docker run启动容器，配置环境变量
export DISPLAY=ip:0  # 将ip替换成母机ip，或：	export DISPLAY=host.docker.internal:0
~~~
配置环境变量也可以在docker run时通过 -e 参数指定。

tips: 母机重启或每隔一段时间可能其ip会变，所以每次启动容器时需要动态查询ip并配置DISPLAY变量，否则运行程序之后，不会报错，也不会显示程序对应的UI。

应用测试
matplotlib显示
容器需要先安装python即matplotlib，numpy等相关的包，然手输入：
~~~ 
import matplotlib.pyplot as plt
import numpy as np
x = np.linspace(-1, 1, 20)
y = 2 * x
plt.plot(x, y)
plt.show()
~~~
母机上弹出一个弹框：

qt UI显示
使用python的qt构建图形UI时，直接运行会报错，如下：

qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "" even though it was found.
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.
Available platform plugins are: eglfs, linuxfb, minimal, minimalegl, offscreen, vnc, wayland-egl, wayland, wayland-xcomposite-egl, wayland-xcomposite-glx, webgl, xcb.
Aborted (core dumped)

此时需要安装依赖：
~~~
apt-get install libxkbcommon-x11-0
~~~
再次运行qt程序即可正常显示UI。

tips: 若通过母机显示的乱码，则在镜像中安装以下依赖
~~~ shell
apt-get update;
apt-get install -y locales \
 ttf-wqy-microhei ttf-wqy-zenhei xfonts-wqy;
~~~

至此，docker容器在mac上进行GUI展示配置就完成了。可以尽情的享受docker技术带来的便利了。

参考文献
https://www.csdn.net/article/2015-07-30/2825340
https://my.oschina.net/u/4325121/blog/3415939
https://my.oschina.net/u/4325121/blog/3415939