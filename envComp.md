## venv，pyvenv，pyenv，virtualenv，virtualenvwrapper，pipenv

- [**virtualenv**](https://pypi.python.org/pypi/virtualenv)是一个非常流行的工具，为Python库创建独立的Python环境。如果你对这个工具不熟悉，我强烈建议你学习它，因为它是一个非常有用的工具，我将对这个答案的其余部分进行比较。 它通过在一个目录中安装一堆文件（例如：）`env/`，然后修改`PATH`环境变量以将其作为一个自定义`bin`目录（例如：）作为前缀`env/bin/`。这个`python`或者`python3`二进制文件的确切副本放置在这个目录中，但是Python被编程为首先在环境目录中查找相对于它的路径的库。它不是Python标准库的一部分，但是由PyPA（Python Packaging Authority）正式祝福。一旦激活，您就可以使用虚拟环境安装软件包`pip`。
- [**pyenv**](https://github.com/yyuu/pyenv)用于隔离Python版本。例如，您可能想要针对Python 2.6,2.7,3.3,3.4和3.5测试您的代码，因此您需要一种方法在它们之间切换。一旦被激活，它就会在`PATH`环境变量前加上`~/.pyenv/shims`符合Python命令（`python`，`pip`）的特殊文件。这些不是Python提供的命令的副本; 它们是根据`PYENV_VERSION`环境变量，`.python-version`文件或`~/.pyenv/version`文件决定运行哪种Python版本的特殊脚本。`pyenv`使用该命令还可以简化下载和安装多个Python版本的过程`pyenv install`。
- [**pyenv-virtualenv**](https://github.com/yyuu/pyenv-virtualenv)是一个插件`pyenv`由同一作者的`pyenv`，允许你使用`pyenv`和`virtualenv`在同一时间方便。但是，如果您使用Python 3.3或更高版本，`pyenv-virtualenv`将尝试运行（`python -m venv`如果可用），而不是`virtualenv`。如果你不想要便利功能，您可以选择使用`virtualenv`和`pyenv` 而不是 `pyenv-virtualenv`，
- [**virtualenvwrapper**](https://pypi.python.org/pypi/virtualenvwrapper)是一组扩展`virtualenv`（见[文档](http://virtualenvwrapper.readthedocs.io/en/latest/)）。它为您提供了类似的命令`mkvirtualenv`，`lssitepackages`特别是`workon`用于在不同`virtualenv`目录之间切换。如果你想要多个`virtualenv`目录，这个工具特别有用。
- [**pyenv-virtualenvwrapper**](https://github.com/yyuu/pyenv-virtualenvwrapper)是一个`pyenv`由同一个作者插件`pyenv`，方便地融入`virtualenvwrapper`到`pyenv`。
- [**pipenv**](https://pypi.python.org/pypi/pipenv)，Kenneth Reitz（作者`requests`）是该列表中最新的项目。它旨在将Pipfile，pip和virtualenv结合到命令行中的一个命令中。

# 标准库：

- **pyvenv**是随Python 3一起发布的脚本，但[在Python 3.6中](https://docs.python.org/dev/whatsnew/3.6.html#id8)被[弃用，](https://docs.python.org/dev/whatsnew/3.6.html#id8)因为它存在问题（更不用说名称混淆了）。在Python 3.6+中，确切的等价物是`python3 -m venv`。

- [**venv**](https://docs.python.org/3/library/venv.html)是随Python 3一起发布的软件包，您可以使用它`python3 -m venv`（尽管由于某些原因，某些发行版将其分离为单独的发行版软件包，例如`python3-venv`在Ubuntu / Debian上）。它提供了类似的目的`virtualenv`，并且工作方式非常相似，但它不需要复制Python二进制文件（除了在Windows上）。如果你不需要支持Python 2，可以使用它。在撰写本文时，Python社区似乎很满意，但`virtualenv`我没有听说过很多`venv`。

- 我会避免`virtualenv`在Python3.3 +之后使用，而是使用标准的附带库`venv`。要创建一个新的虚拟环境，你应该输入：

  ```javascript
  $ python3 -m venv <MYVENV>  
  ```

  `virtualenv`尝试将Python二进制文件复制到虚拟环境的bin目录中。但是它不会更新嵌入到该二进制文件中的库文件链接，因此如果您将Python从源代码构建到具有相对路径名称的非系统目录中，则Python二进制文件会中断。由于这是您制作可复制分发的Python的方式，因此这是一个很大的缺陷。BTW检查OS X上的嵌入式库文件链接，使用`otool`。例如，在您的虚拟环境中输入：

  ```javascript
  $ otool -L bin/python
  python:
      @executable_path/../Python (compatibility version 3.4.0, current version 3.4.0)
      /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1238.0.0)
  ```

  因此我会避免`virtualenvwrapper`和`pipenv`。`pyvenv`已弃用。`pyenv`似乎经常使用的地方`virtualenv`，但我会远离它也因为我认为`venv`也是`pyenv`建立的。

  `venv`创造新鲜和独立的环境，而且他们不需要`sudo`权限。新鲜的，因为他们只是从Python附带的标准库开始，你必须`pip install`在活动的虚拟环境中重新安装任何其他库。自包含，因为这些新的库安装在虚拟环境之外都不可见，所以您可以删除它，而不用担心会影响基本的python安装。您不需要`sudo`权限来创建虚拟环境或安装库，因为虚拟环境库安装不会将文件泄漏到您的文件夹之外`user`目录树。一旦你的虚拟环境被激活，你的shell环境只能看到创建虚拟环境的python构建，所以它也是一种在python版本之间切换的方法。

  `pyenv`类似于`venv`它可以让你管理多个python环境。但是，`pyenv`如果您无法方便地将库安装回滚到某个开始状态，并且您可能需要`admin`某些特权才能更新库。所以我认为这也是最好的使用`venv`。

  在过去几年中，我发现构建系统（emacs包，python独立应用程序构建器，安装程序......）中存在许多问题，最终导致问题出现`virtualenv`。我认为python会是一个更好的平台，当我们消除这个额外的选项，只使用`venv`。

## 参考

1. venv, pyenv, virtualenv, pipenv有什么异同
   http://vincentsfootprint.com/post/venv_virtualenv_pipenv_difference

2. venv，pyvenv，pyenv，virtualenv，virtualenvwrapper，pipenv等有什么区别？ - 问答 - 云+社区 - 腾讯云
   https://cloud.tencent.com/developer/ask/77974