---
title: Android源码下载编译
date: 2019-06-16 11:24:20
tags: android source
---

android学习必不可少的需要下载源码研究学习，这里记录下源码下载编译的问题

#### Aosp下载

由于不太友好的外网环境，给技术人员获取知识增加了些许难度，为了节省自己的时间和精力还是推荐用清华的镜像。以下工具全部用镜像站提供的资源

##### 下载repo工具

```shell
mkdir ~/bin
PATH=~/bin:$PATH
curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo -o repo
chmod +x repo
```

repo的运行过程中会尝试访问官方的git源更新自己，如果想使用tuna的镜像源进行更新，可以将如下内容复制到你的`~/.bashrc`里 

```shell
export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/'
```

并重启终端模拟器

##### 使用每月更新的初始化包

使用初始化包同样是为了节省我们宝贵的生命和有限的头发

初始化包的使用方法如下

```shell
wget -c https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-20160806.tar # 下载初始化包
tar xf aosp-latest.tar
cd AOSP   # 解压得到的 AOSP 工程目录
# 这时 ls 的话什么也看不到，因为只有一个隐藏的 .repo 目录
repo sync # 正常同步一遍即可得到完整目录
# 或 repo sync -l 仅checkout代码
```

可以看到我用了20160806的包，因为这个比较小。希望使用其它的初始化包可以到以下目录挑选

`https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly`

下载下来后可以用md5值校验是否完整`md5sum aosp-20160806.tar`

**注意**  确保已经安装配置好了git，不然会报如下错误

```
Traceback (most recent call last):
  File "/home/nemoli/aosp/.repo/repo/main.py", line 531, in <module> _Main(sys.argv[1:])
  File "/home/nemoli/aosp/.repo/repo/main.py", line 507, in _Main result = repo._Run(argv) or 0
  File "/home/nemoli/aosp/.repo/repo/main.py", line 158, in _Runcopts, cargs = cmd.OptionParser.parse_args(argv)
  File "/home/nemoli/aosp/.repo/repo/command.py", line 67, in OptionParser self._Options(self._optparse)
  File "/home/nemoli/aosp/.repo/repo/subcmds/sync.py", line 189, in _Options self.jobs = self.manifest.default.sync_j
  File "/home/nemoli/aosp/.repo/repo/manifest_xml.py", line 352, in default self._Load()
  File "/home/nemoli/aosp/.repo/repo/manifest_xml.py", line 392, in _Load b = m.GetBranch(m.CurrentBranch).merge
  File "/home/nemoli/aosp/.repo/repo/project.py", line 733, in GetBranch return self.config.GetBranch(name)
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 200, in GetBranch b = Branch(self, name)
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 716, in __init__ self.merge = self._Get('merge')
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 759, in _Get return self._config.GetString(key, all_keys = all_keys)
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 131, in GetString v = self._cache[_key(name)]
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 248, in _cache self._cache_dict = self._Read()
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 254, in _Read d = self._ReadGit()
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 296, in _ReadGit d = self._do('--null', '--list')
  File "/home/nemoli/aosp/.repo/repo/git_config.py", line 321, in _do capture_stderr = True)
  File "/home/nemoli/aosp/.repo/repo/git_command.py", line 238, in __init__raise GitError('%s: %s' % (command[1], e))
error.GitError: config: [Errno 2] No such file or directory
```

只要从repo中checkout出代码就完成了源码准备工作。现在开始进入下一步。

##### 编译环境要求

编译系统Google推荐ubuntu，为顺利编译强烈建议用ubuntu

| Android版本                | 编译要求的Ubuntu最低版本 |
| -------------------------- | ------------------------ |
| Android 6.0至AOSP master   | Ubuntu 14.04             |
| Android 2.3.x至Android 5.x | Ubuntu 12.04             |
| Android 1.5至Android 2.2.x | Ubuntu 10.04             |

JDK版本要求

| Android版本                  | 编译要求的JDK版本 |
| ---------------------------- | ----------------- |
| AOSP的Android主线            | OpenJDK 8         |
| Android 5.x至android 6.0     | OpenJDK 7         |
| Android 2.3.x至Android 4.4.x | Oracle JDK 6      |
| Android 1.5至Android 2.2.x   | Oracle JDK 5      |

更具体的可以参看:[Google源码编译要求](https://link.jianshu.com/?t=https://source.android.com/source/requirements.html)

***本次例子编译环境 Ubuntu 14.04 android-6.0.1 版本号23***

安装OpenJDK 7 如下

```
sudo apt-get install openjdk-7-jdk
```

[搭建编译环境的官方文档](https://source.android.com/source/initializing.html)

##### 安装所需的程序包 (Ubuntu 14.04)

```shell
sudo apt-get install git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev libxml2-utils xsltproc unzip
```



启动Android模拟器命令

```Shell
emulator @Nexus_6_API_23 -system /Users/nemoli/Desktop/sourceImg/system.img -data /Users/nemoli/Desktop/sourceImg/userdata.img -ramdisk /Users/nemoli/Desktop/sourceImg/ramdisk.img
```

