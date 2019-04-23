[TOC]

# Python 开发总结

## 语言版本

### Python2 v.s. Python3

关于 `Python2` 还是 `Python3` 的争论历来已久，但是根据 [PEP373](https://www.python.org/dev/peps/pep-0373/) `Python2` 从2020年开始就不在维护了。所以到现在不应该有什么争论了，假如是历史项目，用 `Python2`就继续用 `Python2`，新项目开始全部使用 `Python3`，这里的 `Python3` 是指`Python3.4+`。项目中使用的 `Python3.5`，`Python3.6` 出来了也可以试试。就版本而言，现在很多第三方库都已经支持 `Python3`，至于有一些库只支持 `Python2`，那么这个库基本就可以不用了，因为在 `Python3` 必然会找到替代的库。

`Python3` 是一门经过重新设计的`Python`语言，单纯就源文件默认编码是utf-8而不是ASCII就是一大进步，文件开头不用特地表明 `# -*- coding: utf-8 -*-` ，也不用为中文编码纠缠半天了。更不提 `Python3` 内置的线程池，异步 IO 支持等等。如果说`Python2` 还有有点，那么大概 就是`Python2`的解释器到目前为止比 `Python3 `还快。

## 语言版本管理 pyenv

### 作用

- 在一台开发机上建立多个版本的 `python` 环境
- 并提供方便的切换方法

### 工作原理

修改环境变量 `PATH`

`pyenv`做的，就是在`PATH`最前面插入一个`$(pyenv root)/shims`目录。这样，`pyenv`就可以通过控制`shims`目录中的Python版本号，来灵活地切换至我们所需的Python版本。

如果还想了解更多细节，可以查看[`pyenv`](https://github.com/pyenv/pyenv)的文档介绍及其源码实现。

### 安装

`pyenv`的安装方式包括多种，重点推荐采用[`pyenv-installer`](https://github.com/pyenv/pyenv-installer)的方式，原因主要有两点：

- 通过`pyenv-installer`可一键安装`pyenv`全家桶，后续也可以很方便地实现一键升级；
- `pyenv-installer`的安装方式基于`GitHub`，可保证总是使用到最新版本的`pyenv`，并且`Python`版本库也是最新最全的。

---


1. 执行安装器 `pyenv-installer`

  ```bash
  $ curl -L https://raw.githubusercontent.com/pyenv/pyenv-installer/master/bin/pyenv-installer | bash
  ```

2. 设置环境变量

   修改 `~/.bashrc` 文件（ 或者 `~/.zshrc `文件，根据个人使用的 shell ），添加以下内容：

   ```bash
   export PATH=$HOME/.pyenv/bin:$PATH
   eval "$(pyenv init -)"
   eval "$(pyenv virtualenv-init -)"
   ```

   执行 `source` 命令，使修改立即生效：

   ```bash
   $ source ~/.bashrc
   ```

   完成以上操作后，`pyenv`就安装完成了。

3. 试用

   ```bash
   $ pyenv -v
   pyenv 1.2.11
   ```

4. 实际上，还自动安装了以下插件

  - `pyenv-doctor`
  - `pyenv-installer`
  - `pyenv-update`
  - `pyenv-virtualenv`
  - `pyenv-which-ext`

### 使用 

1. 安装情况诊断

  ```bash
  $ pyenv doctor
  ```

2. 更新

  ```bash
  $ pyenv update
  ```

3. 查看所有可安装的`Python`版本

   ```bash
   $ pyenv install --list
   Available versions:
     2.1.3
     ...
     2.7.12
     2.7.13
     ...
     3.5.3
     3.6.0
     3.6-dev
     3.6.1
     3.7-dev
   ```

4. 安装指定版本的`Python`环境

   ```bash
   $ pyenv install 3.6.0
   Downloading Python-3.6.0.tar.xz...
   -> https://www.python.org/ftp/python/3.6.0/Python-3.6.0.tar.xz
   Installing Python-3.6.0...
   Installed Python-3.6.0 to /Users/Leo/.pyenv/versions/3.6.0
   ```

5. 查看当前系统中所有可用的`Python`版本

   ```bash
   $ pyenv versions
   * system (set by /Users/Leo/.pyenv/version)
     2.7.13
     3.6.0
   ```

6. 切换`Python`版本

   ```bash
   $ pyenv global 2.7.13
   $ cat ~/.pyenv/version
   2.7.13
   $ pyenv version
   2.7.13 (set by /Users/Leo/.pyenv/version)
   
   $ pyenv global 3.6.0
   $ cat ~/.pyenv/version
   3.6.0
   $ pyenv version
   3.6.0 (set by /Users/Leo/.pyenv/version)
   ```


### 高级

`pyenv`可以从三个维度来管理`Python`环境，简称为：`当前系统`、`当前目录`、`当前shell`。

这三个维度的优先级从左到右依次升高，即`当前系统`的优先级最低、`当前shell`的优先级最高。

分别用以下命令设置：

```bash
$ pyenv global PYTHON_VERSION
$ pyenv local PYTHON_VERSION
$ pyenv shell PYTHON_VERSION
```

具体的：

如果想修改系统全局的Python环境，可以采用`pyenv global PYTHON_VERSION`命令。该命令执行后会在`$(pyenv root)`目录（默认为`~/.pyenv`）中创建一个名为`version`的文件（如果该文件已存在，则修改该文件的内容），里面记录着系统全局的Python版本号。

```
$ pyenv global 2.7.13
$ cat ~/.pyenv/version
2.7.13
$ pyenv version
2.7.13 (set by /Users/Leo/.pyenv/version)

$ pyenv global 3.6.0
$ cat ~/.pyenv/version
3.6.0
$ pyenv version
3.6.0 (set by /Users/Leo/.pyenv/version)
```

通常情况下，对于特定的项目，我们可能需要切换不同的Python环境，这个时候就可以通过`pyenv local PYTHON_VERSION`命令来修改`当前目录`的Python环境。命令执行后，会在当前目录中生成一个`.python-version`文件（如果该文件已存在，则修改该文件的内容），里面记录着当前目录使用的Python版本号。

```bash
$ cat ~/.pyenv/version
2.7.13
$ pyenv local 3.6.0
$ cat .python-version
3.6.0
$ cat ~/.pyenv/version
2.7.13
$ pyenv version
3.6.0 (set by /Users/Leo/MyProjects/.python-version)
$ pip -V
pip 9.0.1 from /Users/Leo/.pyenv/versions/3.6.0/lib/python3.6/site-packages (python 3.6)
```

可以看出，当前目录中的`.python-version`配置优先于系统全局的`~/.pyenv/version`配置。

另外一种情况，通过执行`pyenv shell PYTHON_VERSION`命令，可以修改`当前shell`的Python环境。执行该命令后，会在当前`shell session`（Terminal窗口）中创建一个名为`PYENV_VERSION`的环境变量，然后在`当前shell`的任意目录中都会采用该环境变量设定的Python版本。此时，`当前系统`和`当前目录`中设定的Python版本均会被忽略。

```bash
$ echo $PYENV_VERSION

$ pyenv shell 3.6.0
$ echo $PYENV_VERSION
3.6.0
$ cat .python-version
2.7.13
$ pyenv version
3.6.0 (set by PYENV_VERSION environment variable)
```

顾名思义，`当前shell`的Python环境仅在当前shell中生效，重新打开一个新的shell后，该环境也就失效了。如果想在`当前shell`中取消shell级别的Python环境，采用`unset`命令重置`PYENV_VERSION`环境变量即可。

```bash
$ cat .python-version
2.7.13
$ pyenv version
3.6.0 (set by PYENV_VERSION environment variable)

$ unset PYENV_VERSION
$ pyenv version
2.7.13 (set by /Users/Leo/MyProjects/.python-version)
```

### pyenv-virtualenv

经过以上操作，我们在本地计算机中就可以安装多个版本的`Python`运行环境，并可以按照实际需求进行灵活地切换。然而，很多时候在同一个`Python`版本下，我们仍然希望能根据项目进行环境分离，就跟之前我们使用`virtualenv`一样。

在`pyenv`中，也包含这么一个插件，[`pyenv-virtualenv`](https://github.com/pyenv/pyenv-virtualenv)，可以实现同样的功能。

使用方式如下：

```bash
$ pyenv virtualenv PYTHON_VERSION PROJECT_NAME # 创建新的项目环境
```

其中，`PYTHON_VERSION`是具体的Python版本号，例如，`3.6.0`，`PROJECT_NAME`是我们自定义的项目名称。比较好的实践方式是，在`PROJECT_NAME`也带上Python的版本号，以便于识别。

现假设我们有`XDiff`这么一个项目，想针对`Python 2.7.13`和`Python 3.6.0`分别创建一个虚拟环境，那就可以依次执行如下命令。

```bash
$ pyenv virtualenv 3.6.0 py36_XDiff
$ pyenv virtualenv 2.7.13 py27_XDiff
```

创建完成后，通过执行`pyenv virtualenvs`命令，就可以看到本地所有的项目环境。

```bash
$ pyenv virtualenvs # 查看本地所有的项目环境
  2.7.13/envs/py27_XDiff (created from /Users/Leo/.pyenv/versions/2.7.13)
* 3.6.0/envs/py36_XDiff (created from /Users/Leo/.pyenv/versions/3.6.0)
  py27_XDiff (created from /Users/Leo/.pyenv/versions/2.7.13)
  py36_XDiff (created from /Users/Leo/.pyenv/versions/3.6.0)
```

通过这种方式，在同一个Python版本下我们也可以创建多个虚拟环境，然后在各个虚拟环境中分别维护依赖库环境。

例如，`py36_XDiff`虚拟环境位于`/Users/Leo/.pyenv/versions/3.6.0/envs`目录下，而其依赖库位于`/Users/Leo/.pyenv/versions/3.6.0/lib/python3.6/site-packages`中。

```bash
$ pyenv activate py36_XDiff # 切换到 py36_XDiff 项目环境
$ pip -V
pip 9.0.1 from /Users/Leo/.pyenv/versions/3.6.0/lib/python3.6/site-packages (python 3.6)
```

后续在项目开发过程中，我们就可以通过`pyenv local XXX`或`pyenv activate PROJECT_NAME`命令来切换项目的`Python`环境。

```bash
➜  MyProjects pyenv local py27_XDiff
(py27_XDiff) ➜  MyProjects pyenv version
py27_XDiff (set by /Users/Leo/MyProjects/.python-version)
(py27_XDiff) ➜  MyProjects python -V
Python 2.7.13
(py27_XDiff) ➜  MyProjects pip -V
pip 9.0.1 from /Users/Leo/.pyenv/versions/2.7.13/envs/py27_XDiff/lib/python2.7/site-packages (python 2.7)
```

可以看出，切换环境后，`pip`命令对应的目录也随之改变，即始终对应着当前的Python虚拟环境。

对应的，采用`pyenv deactivate`命令退出当前项目的`Python`虚拟环境。

```bash
$ pyenv deactivate
```

如果想移除某个项目环境，可以通过如下命令实现。

```bash
$ pyenv uninstall PROJECT_NAME
```

### 卸载

由于 pyenv 是自包含 (self-contained) 的，基本上 pyenv 安装的，或创建的东西都存在在 ~/.pyenv 目录下。所以，卸载 pyenv 只需要进行以下步骤：

1. 删除  ~/.pyenv 目录

   ```bash
   $ rm -rf "$HOME/.pyenv"
   ```

2. 删除安装时添加进 ~/.bashrc (或 ~/.zshrc) 文件的类似以下的内容

   ```bash
   export PATH="/home/ubuntu/.pyenv/bin:$PATH"
   eval "$(pyenv init -)"
   eval "$(pyenv virtualenv-init -)"
   ```

### Mac 上安装

```bash
$ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" #安装 homebrew
$ xcode-select --install # 安装 xcode-select
$ brew install openssl readline xz
$ brew update
$ brew install pyenv # 安装 pyenv
$ echo 'export PATH="/Users/${USER}/.pyenv:$PATH"' >> ~/.zshrc
$ echo 'eval "$(pyenv init -)"' >> ~/.zshrc
$ brew install pyenv-virtualenv # 安装 pyenv-virtualenv 插件
$ echo 'if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi' >> ~/.zshrc
$ source ~/.zshrc
```

#### 问题

在 `pyenv install` 某个版本 python 会出现编译问题

```bash
pyenv install 3.7.1
python-build: use openssl from homebrew
python-build: use readline from homebrew
Downloading Python-3.7.1.tar.xz...
-> https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tar.xz
Installing Python-3.7.1...
python-build: use readline from homebrew

BUILD FAILED (OS X 10.14 using python-build 1.2.7-11-g835707da)

Inspect or clean up the working tree at /var/folders/p0/_5lf8w5d0sd92mnyq2zj20dm0000gn/T/python-build.20181027164510.63194
Results logged to /var/folders/p0/_5lf8w5d0sd92mnyq2zj20dm0000gn/T/python-build.20181027164510.63194.log

Last 10 log lines:
checking for --with-icc... no
checking for gcc... clang
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables...
checking whether we are cross compiling... configure: error: in `/var/folders/p0/_5lf8w5d0sd92mnyq2zj20dm0000gn/T/python-build.20181027164510.63194/Python-3.7.1':
configure: error: cannot run C compiled programs.
If you meant to cross compile, use `--host'.
See `config.log' for more details
make: *** No targets specified and no makefile found.  Stop.
```

解决方法如下

```bash
$ cd /Library/Developer/CommandLineTools/Packages/
$ open macOS_SDK_headers_for_macOS_10.14.pkg
```

问题2：

```bash
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
make: *** [python.exe] Error 1
```

解决方案：

````bash
$ brew uninstall binutils
````

### Mac 上卸载

```bash
$ rm -rf $(pyenv root)
$ brew uninstall pyenv
```



## 依赖环境管理 virtualenv

### 作用

- virtualenv 可以搭建虚拟且独立的 python 环境
- 可以使每个项目环境与其他项目独立开来
- 保持环境的干净
- 解决包冲突问题

### 安装

```bash
$ pip install virtualenv
```

### 使用示例

1. 创建新项目

  ```bash
  $ mkdir myproject
  $ cd myproject/
  ```

2. 创建虚拟环境

   ```bash
   $ virtualenv --no-site-packages venv
   ```
   1. 参数 `–no-site-packages`，已经安装到系统`Python`环境中的所有第三方包都不会复制过来，这样，我们就得到了一个不带任何第三方包的“干净”的Python运行环境
   2. 参数 `–system-site-packages`，虚拟环境会继承`/usr/lib/python2.7/site-packages`下的所有库。如果想依赖系统包目录，则可以使用此功能。如果您想要与全局系统隔离，请不要使用此参数。

3. 激活虚拟环境

   ```bash
   $ source venv/bin/activate
   ```

   命令提示符会多一个`venv`前缀，表示当前环境是一个名为`venv`的`Python`环境。 
   下面正常安装各种第三方包，并运行python命令：

4. 退出虚拟环境

   ```bash
   (venv)$ deactivate 
   ```

5. 指定python版本，创建虚拟环境

   ```bash
   $ virtualenv -p /usr/bin/python2.7 venv
   ```

### 总结

问题在于，不用 `pyenv` 的情况下，这里的 `python` 是怎么支持多版本的？似乎 `virtualenv` 只能用于创建干净的项目环境，防止依赖冲突。似乎还是在pyenv中使用`pyenv-virtualenv` 插件更合适。



## 包管理 pip

### 安装

### 使用

### 修改软件源

修改为国内的镜像软件源，提高软件包下载速度

用vim 编辑~/.pip/pip.conf 

```ini
[global]
trusted-host =  mirrors.aliyun.com
index-url = http://mirrors.aliyun.com/pypi/simple
```



## 项目构建

* todo

## 项目打包

* todo

## 项目安装包

* todo

## 推荐 IDE

* todo

## 关键语法特性

* todo

## Anaconda

Anaconda是一个用于科学计算的Python发行版，支持 Linux, Mac, Windows系统，提供python环境管理和包管理功能，可以很方便在多个版本python之间切换和管理第三方包，Anaconda 使用 conda 来进行 Python 环境管理和包管理。

工作方式与前面相似，主要是命令有所差异，可以参考文献 6 进行了解。

## 参考

1. vinta/awesome-python: A curated list of awesome Python frameworks, libraries, software and resources
   https://github.com/vinta/awesome-python
   
2. jobbole/awesome-python-cn: Python资源大全中文版，包括：Web框架、网络爬虫、模板引擎、数据库、数据可视化、图片处理等，由伯乐在线持续更新。
   https://github.com/jobbewesdsole/awesome-python-cn
   
3. 我的Python开发总结 | Wong Page
   https://blog.wongxinjie.com/2017/03/01/%E6%88%91%E7%9A%84Python%E5%BC%80%E5%8F%91%E6%80%BB%E7%BB%93/
   
4. 一文总结学习 Python 的 14 张思维导图 - CSDN资讯 - CSDN博客
   https://blog.csdn.net/csdnnews/article/details/78248699
   
5. 用pyenv和virtualenv搭建多版本python环境 - 简书
   https://we'we's'd'sww.jianshu.com/p/08c9b35b35d6
   
6. virtualenv - 廖雪峰的官方网站
   https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000
   
7. 使用 Anaconda 管理 Python 环境 - 简书
   https://www.jianshu.com/p/cdc03bf45008

### Mac Pyenv install 问题

8. pyenv install cannot work
   https://gist.github.com/tosik/061d3155112332d3c83ef7f7014f2ddd

2. stdio.h file not found error on macOS Sierra · Issue #338 · frida/frida
   https://github.com/frida/frida/issues/338

3. Failed to build on Mac OS 10.11.5 · Issue #640 · pyenv/pyenv
   https://github.com/pyenv/pyenv/issues/640