---
title: Ubuntu 配置
date: 2019-05-04 21:30:26
author: Larry
tags: 
    [tech,ubuntu]
categories: 奇奇怪怪的教程
---
---
#### 最后编辑
2019.5.16
version:1.08

---
这是我今天第四次重装ubuntu,心情非常悲痛;
## 反思
-   之前崩的原因是搜狗输入法安装的时候没有提前安装fcix框架,导致乱码然后系统就傻掉了;
-   重装很多次的原因是因为没有在重装之前完全的格式化分区,我建议每一次玩具坏了都要用windows格式化一次呜呜呜

## 复活操作

#### 基本配置
- 管理员权限,换源,安装vim
    <pre><code>  sudo passwd(修改sudo密码)
    sudo apt-get update
    sudo apt-get install vim
    </code></pre>
-   更换国内源,这里我选择的是[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)用下面的命令打开文件,并且注释里面的所有内容,
     <pre><code> sudo vim /etc/apt/sources.list
    </code></pre> 
    -   然后粘贴下面的内容到打开的文件里面
    <pre><code># 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    </code></pre>
    如果你学过vim,就知道:w :q的含义,如果没有可以在终端使用vimtutor学习一下;
-   安装搜狗输入法(之前几次都因为它炸了我不信了……)，我参考了这一篇[博客]( https://blog.csdn.net/qq_33159059/article/details/85019467)
-   然后搭建基本的C语言环境,主要参考啦蒋老师的PA讲义
    <pre><code>su
    apt-get install build-essential
    apt-get install man                # on-line reference manual
    apt-get install gcc-doc            # manual for GCC
    apt-get install gdb                # GNU debugger
    apt-get install git                # reversion control system
    apt-get install libreadline-dev    # a library to use compile the project later
    apt-get install libsdl2-dev        # a library to use compile the project later
    apt-get install qemu-system-x86    # QEMU
    </code></pre>
-   安装chrome:请使用bing搜索;用gmail同步很香;

#### 科学的看世界
-  我选择的是shadowsocks-libev(因为我qt5以及普通的pip安装的shadowsocks就没有成功过)
    <pre><code> mkdir shadowsocks
    cd shadowsocks
    touch shadowsocks.json
    vim shadowsocks.json
    </code></pre>
-   将下面的内容根据自己的配置放进去:
    <pre><code>
    {
      "server":"my_server_ip",
      "server_port":53450,
      "local_address": "127.0.0.1",
      "local_port":1080,
      "password":"密码",
      "timeout":300,
      "method":"aes-256-gcm",
      "fast_open": false
    }
    </code></pre>
-   然后: ss-local -c ~/shadowsocks/shadowsocks/json & 
    自己测试一下有没有问题;
-   感谢阿姨的提醒,我决定用别名+脚本来启动shadowsocks(因为每次开机输入上面的东西实在没有效率)：
    -   先写一个自启动脚本：
    <pre><code> touch ~/.ssstart.sh
     vim ~/.ssstart.sh
    </code></pre>
    内容是:
    <pre><code>#!/bin/bash
    ss-local -c ~/shadowsocks/shadowsocks.json 
    </code></pre>
    -   然后在终端里面起别名:
    <pre><code>vim ~/.bashrc
    在末尾添加:
    alias ss='. ~/.ssstart.sh'
    :wq 保存,退出
    在终端里面: 
    source ~/.bashrc
    (如果是zsh:source ~/.zshrc)
    </code></pre>
    尝试一下在终端输入ss,它lei了;
-  因为后面的netdata需要**终端**翻墙,我也就尝试了一下,如果没有需求可以跳过这一步:
    -   主要参考的是谷歌出来的[网站](http://www.totorocyx.me/2018/10/02/ubuntu_shadowsocks/)
    -   首先用pip -V康康有没有pip,没有的话使用sudo apt-get install python-pip安装
    -   下面尝试全局代理(我也不确定能不能成功)：
        -   sudo pip install genpac
        -   选择安装配置文件的目录,我选择的是:<pre><code>/home/larryytr/shadowsocks</code></pre>
        -   然后执行以下命令:<pre><code>sudo genpac --proxy="SOCKS5 127.0.0.1:1080" --gfwlist-proxy="SOCKS5 127.0.0.1:1080" -o autoproxy.pac --gfwlist-url="https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt"</code></pre>
        -   下面是一句搬运,我没有遇到过:<pre><code>注意：如果报错“fetch gfwlist fail.online: https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt local:None”，可以使用后面的语句：sudo genpac --proxy="SOCKS5 127.0.0.1:1080" -o autoproxy.pac --gfwlist-url="https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt"
        </pre></code>
        -   执行完之后,目录下面会有一个autoproxy.pac文件。
        -   然后在右上角,打开系统设置——网络——网络代理：“方法”选择“自动”，“配置URL”填写：
            <pre><code>file:///home/larryytr/shadowsocks/autoproxy.pac (请根据自己的实际情况修改)</code></pre>
    -   然后使得终端也能使用代理。我们需要**privoxy**代理工具:
        -   安装很自然:sudo apt-get install privoxy
        -   然后编辑配置文件<pre><code>sudo vim /etc/privoxy/config </pre></code>
        -   在文档中搜索(vim 使用/搜索)“**listen-address**”（即监听地址），找到如下一行：**listen-address localhost:8118** 确保它没有被注释（如果这一行有#号，就手动删除）。再查找“**forward-socks5t**”，找到如下一行：**forward-socks5t / 127.0.0.1:1080** . 同样确保它没有被注释。如果没有这一行，就手动添加（注意！句尾那个点 . 是要写的！）。然后保存退出，再执行以下命令启动privoxy：
            <pre><code>sudo privoxy --user privoxy /etc/privoxy/config</pre></code>
        -   最后，再配置/etc/profile：<pre><code>
                # 先进入编辑模式
            sudo vim /etc/profile
                 # 在末尾添加以下三行：
            export http_proxy=http://127.0.0.1:8118
            export https_proxy=http://127.0.0.1:8118
            export ftp_proxy=http://127.0.0.1:8118
                # 退出之后记得执行
                source /etc/profile
            </code></pre>
        -  验证是否成功:curl www.google.com或wget www.google.com判断是否可以访问


#### 优化美化
-   官网安装网易云
-   官网安装vscode
-   配置zsh,tmux,vim:

##### zsh安装与美化
-   学习了:https://www.sysgeek.cn/install-zsh-shell-ubuntu-18-04/ 
https://segmentfault.com/a/1190000013612471这两篇教程;
-   感谢何伟的配置文件;
-   相应的setting请参考我的github[相关内容](https://github.com/larryytr/Note_for_blog/tree/master/ubuntu).
-   安装zsh:
    <pre><code>sudo apt-get update
	sudo apt-get install zsh
	chsh -s /bin/zsh (设置zsh为默认)
    </code></pre>
-   重启你的ubuntu
-   安装oh-my-zsh插件:
    <pre><code> wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
    </code></pre>
- 不改theme一无所有
- 准备使用powerline主题  
-   首先安装powerline字体：
    <pre><code>
    git clone https://github.com/powerline/fonts.git --depth=1
    # install
    cd fonts
    ./install.sh
    # clean-up a bit
    cd ..
    rm -rf fonts
    </code></pre>
- 安装完字体之后要记得使用：终端-编辑-首选项-文本-文本外观-自定义字体打勾-选一个带有powerline的。(星际玩家找了好久)
-   安装powerline: sudo apt install powerline 
-   我的配置见[相关内容](https://github.com/larryytr/Note_for_blog/tree/master/ubuntu)的setting .zshrc
-   颜色选择困难请: <pre><code>for code ({000..255}) print -P -- "$code: %F{$code}This is how your text would look like%f"</code></pre>
-   改完请source ~/.zshrc

##### tmux
-   tmux是一个很优秀的分屏软件,介绍可以看jyy的PA讲义以及自己搜索教程;
-   我使用了何伟的配置,具体见[相关内容](https://github.com/larryytr/Note_for_blog/tree/master/ubuntu)的setting
-  我又加了一个插件使得tmux在重启之后状态可以恢复:
    -   主要参考这个[知乎教程](https://zhuanlan.zhihu.com/p/24660412)
    -   <pre><code>git clone https://github.com/tmux-plugins/tmux-resurrect ~/tmux_tmp
        </code></pre>
    -   在~/.tmux.conf.local里面加上:
        <pre><code>run-shell ~/tmux_tmp/resurrect.tmux
        </code></pre>
    -   最后载入这个配置：<pre><code>tmux source-file ~/.tmux.conf
        </code></pre>

##### vim的美化
-   使用啦懒人vim: spf13-vim美化
-   请看[相关内容](https://github.com/larryytr/Note_for_blog/tree/master/ubuntu)的setting，找到并且下载spf13-vim.sh,然后bash spf13-vim.sh
-   我的配置同样在[相关内容](https://github.com/larryytr/Note_for_blog/tree/master/ubuntu)的setting里面;
-   这个时候的vim没有办法和系统剪切版交互,我根据https://www.cnblogs.com/memory4young/p/could-not-use-system-clipboard-in-vim.html 下载了其他一些插件:
    <pre><code>sudo apt-get install vim-scripts vim-gtk vim-gnome</pre></code>
    这样 vim --version|grep "cliboard" 会看到 +clipboard;
    然后就可以用+y,+p实现系统剪切版和vim剪切版的交互啦！

#### 其他内容:
- OSlab还需要:
  - sudo apt-get install curl
  - sudo apt-get install gcc-multilib
- git 配置请搜索廖雪峰
- ctags 可以参考Mengzelev的[博客](https://mengzelev.github.io/2018/10/04/pa-inspirations/)
-   感谢xnr给我推荐的network来查看linux的运行情况
    -   这是netdata的官方网站:https://github.com/netdata/netdata#user-base
    -   但是由于GFW,安装会出现报错,事实上需要终端翻墙才行
    -   可以通过这篇[教程](https://blog.csdn.net/zhangvalue/article/details/80270169)
        <pre><code> sudo apt-get install net-tools
          ifconfig
        </code></pre>
        查看inet 之后的内容来得知自己的server_ip
    -   成功之后,进入 http://127.0.0.1:19999/ (:19999前面的是自己的server_ip地址,请按需要更改),得到炫酷的体验
    -   相应配置可以参考这篇[博客](https://cloud.tencent.com/developer/article/1181577)或者自己搜索
-   OSlab的kvm bug处理方法：https://bugzilla.redhat.com/show_bug.cgi?id=1479558
    <pre><code>chmod 666 /dev/kvm to get it working right now. Then to fix future reboots, create a file /lib/udev/rules.d/99-kvm.rules with this content:
    KERNEL=="kvm", GROUP="kvm", MODE="0666"</code></pre>
-   texlive 安装
    <pre><code>sudo apt install texlive-full</code></pre>
    相关的vscode配置可以抄我的[setting](https://github.com/larryytr/Note_for_blog/tree/master/setting)
    vscode的保存即编译请<pre><code>Ctrl+Shift+p,搜索setting,搜索Build,Latex-workshop › Synctex › After Build: Enabled打勾；</code></pre>
-   ubuntu的截图:我参考了这篇[博客](https://blog.csdn.net/qq_17448289/article/details/56480805)
    -   打开右上角的设置-->设备-->键盘-->快捷键,点击+
    -   显然的配置好按键,然后在命令里面写<pre><code>gnome-screenshot -a</code></pre>
    -   hint:上面的命令终端输入也有效
        截屏的图在文件夹的图片(picture)里面;

#### To be continued
-   有空再研究怎么换主题;




