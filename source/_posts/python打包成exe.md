title: python打包成exe
author: John Doe
date: 2021-08-25 09:52:02
tags:
---
参考：https://blog.csdn.net/libaineu2004/article/details/112612421

安装Pyinstaller：
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pyinstaller

打包：
pyinstaller -F spooldb1.py
注：这是打包成命令行程序。其它选项请参考最上面的链接。