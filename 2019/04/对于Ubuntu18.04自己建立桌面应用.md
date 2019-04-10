### 前言：

在将我的电脑变成Ubuntu18.04之后啊，旋即需要安装一系列的JetBrains的软件比如：Clion，Pycharm，Goland等等，但是有一个问题就是，Ubuntu软件中心提供的版本很难一次性安装成功，并且存在版本落后的现象，并且比如Goland这种都只有社区版。那么许多人就会选择在官网进行下载。

下载的形式一般情况下需要运行
	
	/下载目录/pycharm/bin/pycharm.sh
来运行程序，但是每次都这样做就太麻烦了，而且也不能收藏在收藏夹。因此我搜了一下，找到了建立应用图标的办法。

### 正餐：

1. 首先你需要下载好压缩包
2. 在终端找到 ```/usr/share/applications/```
你会发现许多的 ```.desktop```文件，这个时候就开始创立了。我们一创建pycharm为例。
3. 首先新建一个```.desktop```文件

```vim Pycharm.desktop```

文件内容如下：

```
[Desktop Entry]
Encoding=UTF-8
Name=Pycharm
Comment=Pycharm IDE
Exec=/home/song-ruyang/下载/pycharm-2018.3.3/bin/pycharm.sh #这里是我的文件目录
Icon=/home/song-ruyang/下载/pycharm-2018.3.3/bin/pycharm.svg #图标
Terminal=false
StartupNotify=false
Type=Application
Categories=Application;Development;

```

然后保存就可以了，在你的应用面板里找到你的应用了。。。就是这么简单。。。
