---
layout: post
title: "Mac系统更新Python"
date: 2013-05-19 09:20
comments: true
categories: Mac Python
---
Mac的系统都有预装python，这给我们的开发工作带来了极大的便利。不过苹果似乎对维护这些三方的语言（包括ruby）并没有什么热情，因此如果我们想要用上最新版的python，就需要自己动手了。

以我自己的mountain lion系统为例，在terminal输入
{% codeblock lang:bash%}
$python --version
{% endcodeblock %}
会显示'python 2.7.3'，这是我们当前的python版本。而在[python](http://www.python.org)官网上目前的版本为2.7.5，接下来我们就来演示如何将系统的版本升级为2.7.5.

#### 1. 下载并安装最新版Python
去[这里](http://www.python.org/download)选择适合自己的python版本，这里我选择的是*Python 2.7.5 Mac OS X 64-bit/32-bit x86-64/i386 Installer*。使用下载的DMG进行安装，系统会自动将python安装至**/Library/Frameworks/Python.framework**。需要注意的是，系统的python版本是在**/System/Library/Frameworks/Python.framework**，接下来我们就将新安装的版本替换至此。

#### 2. 替换Python版本
这里比较简单，直接执行
{% codeblock lang:bash %}
$sudo rm -R /System/Library/Frameworks/Python.framework/Versions/2.7
$sudo mv /Library/Frameworks/Python.framework/Versions/2.7 /System/Library/Frameworks/Python.framework/Versions/2.7
{% endcodeblock %}

#### 3. 修复Group属性
{% codeblock lang:bash %}
$sudo chown -R root:wheel /System/Library/Frameworks/Python.framework/Versions/2.7
{% endcodeblock %}

#### 4. 修复Current链接
这里我们需要将**/System/Library/Frameworks/Python.framework/Versions/Current**链接到替换后的Python版本，执行
{% codeblock lang:bash %}
$sudo rm /System/Library/Frameworks/Python.framework/Versions/Current
$sudo ln -s /Library/Frameworks/Python.framework/Versions/2.7 /System/Library/Frameworks/Python.framework/Versions/Current
{% endcodeblock %}

#### 5. 替换/usr/bin中的版本
这一步比较关键，而且容易忘记。因为系统在/usr/bin目录中会存放Python相关的一些可执行文件，所以如果我们不替换此目录的文件，会导致Python执行的时候依然会使用旧版本。这里我们需要将**/System/Library/Frameworks/Python.framework/Versions/Current**中对应的文件替换至此，具体需要替换的是**pydoc**、**python**、**pythonw**、**python-config**这四个文件。执行
{% codeblock lang:bash %}
$sudo rm /usr/bin/pydoc
$sudo rm /usr/bin/python
$sudo rm /usr/bin/pythonw
$sudo rm /usr/bin/python-config
$sudo ln -s /System/Library/Frameworks/Python.framework/Versions/Current/bin/pydoc /usr/bin/pydoc
$sudo ln -s /System/Library/Frameworks/Python.framework/$Versions/Current/bin/pydoc /usr/bin/python
$sudo ln -s /System/Library/Frameworks/Python.framework/Versions/Current/bin/pydoc /usr/bin/pythonw
$sudo ln -s /System/Library/Frameworks/Python.framework/Versions/Current/bin/pydoc /usr/bin/python-config
{% endcodeblock %}

#### 6. 编辑.bash_profile
在第1步Python进行安装的时候会自动修改～/.bash_profile文件，主要是指定可执行文件的路径。由于我们已经将系统版本进行过替换，所以不需要再在此文件进行任何修改。这里我们只需要删除新加的修改
{% codeblock lang:bash %}
PATH="System/Library/Frameworks/Python.framework/Versions/2.7/bin:${PATH}"
export PATH
{% endcodeblock %}
，或者将备份文件.bash_profile.pysave恢复即可。

至此，所有的替换工作已经完成，我们可以进行一下验证。重启terminal，执行
{% codeblock lang:bash %}
$python --version
{% endcodeblock %}
，成功的话就会打印出我们所替换的最新版本。

此方法不但可以替换2.x的版本，对于3.x的版本也同样适用。

#### 参考
[wolfpaulus.com/jounal/mac/installing_python_osx](wolfpaulus.com/jounal/mac/installing_python_osx)