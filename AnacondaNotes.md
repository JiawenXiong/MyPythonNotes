[TOC]

# 使用 Anaconda 管理 Python 环境

## 介绍

Anaconda是一个用于科学计算的Python发行版，支持 Linux, Mac, Windows系统，提供python环境管理和包管理功能，可以很方便在多个版本python之间切换和管理第三方包，Anaconda 使用 conda 来进行 Python 环境管理和包管理。

## 安装

可以从 Anaconda 官方网站下载 [https://www.anaconda.com/](https://link.jianshu.com/?t=https://www.anaconda.com/)。

这里安装的是Linux版本

```bash
./Anaconda2-5.0.0.1-Linux-x86_64.sh
```

安装完成后，Anaconda 会在 `~/.bashrc` 文件中把 `Anaconda2` 的路径加到 `PATH` 里。如下：

```bash
# added by Anaconda2 installer
export PATH="/apps/anaconda2/bin:$PATH"
```

立即生效

```bash
$ source ~/.bashrc
```

配置好之后通过`conda --version`检查是否安装正确。接着可以通过`python --version`检查python的版本的是否正确。

## 设置镜像

```bash
$ conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```



## 管理 python 环境

### 查看当前使用的 Python 版本

```bash
$ python -V
Python 2.7.13 :: Anaconda, Inc.

$ conda info --envs
# conda environments:
#
python27                 /apps/anaconda2/envs/python27
root                  *  /apps/anaconda2
```

### 创建新的 Python 版本环境

这里创建了两个 Python 环境分别使用 2.6。x 和 3.5.x

```bash
# 创建 纯净的 python 3.5 环境
$ conda create --name python35 python=3.5
# 创建 纯净的 python 2.6 环境
$ conda create --name python26 python=2.6
# 创建 预安装 anaconda3 的环境
$ conda create --name python3 python=3.6 anaconda
# 创建 预安装numpy的python3.6
$ conda create --name python3 python=3.6 numpy
# 创建 一个名为myenv的环境，安装numpy包。Python版本为默认的2.7
$ conda create -n myenv numpy
# 克隆 一个默认环境，命名为python_copy
$ conda create -n python_copy --clone root
```

### 查看 Python 环境

```bash
$ conda info --envs
# conda environments:
#
python26                 /apps/anaconda2/envs/python26
python27                 /apps/anaconda2/envs/python27
python35                 /apps/anaconda2/envs/python35
root                  *  /apps/anaconda2
```

其中 * 号表示当前使用的 Python 环境

### 切换 Python 环境

```bash
# 安装好后，使用activate激活环境
$ activate python3 # for Windows
$ source activate python3 # for Linux & Mac
# 此时系统做的就是把系统环境中的2.7移除，改为3.6的路劲

$ python -V
Python 3.5.4 :: Anaconda, Inc.

$ conda info --envs
# conda environments:
#
python26                 /apps/anaconda2/envs/python26
python27                 /apps/anaconda2/envs/python27
python35              *  /apps/anaconda2/envs/python35
root                     /apps/anaconda2
```

如果不想使用当前版本，而想恢复到默认版面，那么

```bash
# 回到默认环境
$ deactivate python3 # for Windows
$ source deactivate python3 # for Linux & Mac
```

### 删除 Python 环境

```bash
$ conda remove --name python26 --all
```

## 管理包

### 查看当前已经安装的包

```bash
$ conda list
# packages in environment at /apps/anaconda2/envs/python35:
#
ca-certificates           2017.08.26           h1d4fec5_0
certifi                   2017.7.27.1      py35h19f42a1_0
libedit                   3.1                  heed3624_0
libffi                    3.2.1                h4deb6c0_3
libgcc-ng                 7.2.0                h7cc24e2_2
libstdcxx-ng              7.2.0                h7a57d05_2
ncurses                   6.0                  h06874d7_1
openssl                   1.0.2l               h077ae2c_5
pip                       9.0.1            py35haa8ec2a_3
python                    3.5.4               he2c66cf_20
readline                  7.0                  hac23ff0_3
setuptools                36.5.0           py35ha8c1747_0
sqlite                    3.20.1               h6d8b0f3_1
tk                        8.6.7                h5979e9b_1
wheel                     0.29.0           py35h601ca99_1
xz                        5.2.3                h2bcbf08_1
zlib                      1.2.11               hfbfcf68_1
```

### 管理包

为当前环境管理包：

```bash
# 安装numpy
$ conda install numpy

# 安装anaconda
$ conda install anaconda

# 查找package信息
$ conda search numpy

# 更新package
$ conda update numpy

# 删除package
$ conda remove numpy

# 查看当前环境安装的packages
$ conda list
```

为特定环境管理包：

```bash
$ conda install -n python3 numpy

$ conda update -n python3 numpy

$ conda remove -n python3 numpy

$ conda uninstall -n python35 psutil
```

## 配合 virtualenv 使用

### 安装

```bash
$ pip install virtualenv
```

### 创建虚拟环境创建虚拟环境

```bash
# 创建了一个名为`myproject`的文件夹
$ mkdir myproject
$ cd myproject

# 创建虚拟环境venv
$ virtualenv --no-site-packages venv
```

命令`virtualenv`就可以创建一个独立的Python运行环境，我们还加上了参数`--no-site-packages`，这样，已经安装到系统Python环境中的所有第三方包都不会复制过来，这样，我们就得到了一个不带任何第三方包的“干净”的Python运行环境。

可使用 `virtualenv --help` 来查看如何使用。可以使用参数 `--python=/usr/bin/python3` 来创建一个已经安装的的Python环境。

### 激活虚拟环境

```bash
# linux
$ source  venv/bin/activate

# Windows
$ venv\scripts\activate
```

会发现命令提示符变了，有个`(venv)`前缀，表示当前环境是一个名为`venv`的Python环境。
 我们可以使用`pip`安装各种第三方的包，而所有的包都只被安装到`venv`的环境下，系统Python环境不受任何影响，退出该环境这些包就没用啦。

### 退出环境

```bash
$ deactivate
```

`virtualenv`是如何创建“独立”的Python运行环境的呢？原理很简单，就是把系统Python复制一份到virtualenv的环境，用命令`source venv/bin/activate`进入一个virtualenv环境时，virtualenv会修改相关环境变量，让命令`python`和`pip`均指向当前的virtualenv环境。

## 配合 virtualenvwrapper

`virtaulenvwrapper`是virtualenv的扩展包，用于更方便管理虚拟环境，它可以将所有虚拟环境整合在一个目录下，更方便的管理和切换虚拟环境。

### 安装方法

```bash
# 安装
$ pip install virtualenvwrapper  

# 创建目录用来存放虚拟环境
$ mkdir ~/.virtualenvs

# 添加到环境变量
$ echo 'export WORKON_HOME=~/.virtualenvs' >> ~/.bashrc
# 路径是python的路径，默认是/usr/local/bin下
$ echo 'source /home/xxx/Python2.7/bin/virtualenvwrapper.sh'>> ~/.bashrc
# 立即生效
$ source ~/.bashrc
```

此时`virtualenvwrapper`就可以使用了。值得注意的是`virtualenv`创建的虚拟环境是不会在virtualenvwrapper上显示的。

### 使用方法

```ini
workon:列出虚拟环境列表

lsvirtualenv:同上

mkvirtualenv :新建虚拟环境

workon [虚拟环境名称]:切换虚拟环境

rmvirtualenv :删除虚拟环境

deactivate: 离开虚拟环境
```

更多请使用`--help`查看。

## 参考

1. 使用 Anaconda 管理 Python 环境 - 简书
   https://www.jianshu.com/p/cdc03bf45008
2. Python环境管理 - 简书
   https://www.jianshu.com/p/2a628a462e87

