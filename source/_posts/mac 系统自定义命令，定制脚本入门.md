---
title: mac 系统自定义命令，定制脚本入门
date: 2018-12-03 10:16:56
tags: [自定义脚本, linux, mac, alias, .bash_profile]
---
环境: mac osx
寻找一个放自定义脚本的文件夹；当然是可以直接放在根目录 ~ 下的。我为了方便以后自己管理，将自己的所有脚本文件都放到 `/usr/local/etc/bash_profile_self` 文件夹下。ok，我们首先创建一个入口文件 `alias.sh`，其实也就是在这个文件当中定义命令简写；如下：
```bash
cd /usr/local/etc
mkdir bash_profile_self
vim ./bash_profile_self/alias.sh	# cd 到 bash_profile_self，并编辑 alias.sh，如果没有 alias.sh，就创建
```
然后，我们在 alias.sh 中编辑一个自己的 test 命令。键入如下代码：
```bash
alias test='echo "test command"'	# 注意这儿的 ‘=’ 两边都没有空格哈，不要去为了美观加上空格
```
接下来，在 `.bash_profile` 文件中添加：
```bash
source /usr/local/etc/bash_profile_self/alias.sh
```
最后我们需要重新加载一遍 `.bash_profile` 文件。这是因为这个文件在 bash 打开的时候就会自动走一遍，如果修改了配置文件，肯定需要重新加载一下的。
```bash
source ~/.bash_profile
test	# 测试我们刚才写的 test 命令，是不是打印了我们想要的效果
```
ok，上面是我们添加一个简单的自定义命令。接下来，我们来搞一个简单的脚本。

首先还是，cd 到我们的 `bash_profile_self` 这个文件夹下。并创建一个脚本文件
```bash
cd /usr/local/etc/bash_profile_self
vim test.sh
```
在 test.sh 文件中添加一些测试代码，如下：
```bash
basepath=$(cd `dirname $0`; pwd)
echo "$basepath"
echo "$0"
```
ok，我们脚本文件穿件完成了。然后到 alias.sh 中添加一个简单的执行命令。并添加下列 `mobro-echo 1` 的代码到 alias.sh 中。
```bash
vi /usr/local/etc/bash_profile_self/alias.sh

alias mobro-echo='./test.sh' # mobro-echo 1
```
ok，重新加载一下 `.bash_profile` 文件，即可测试 `mobro-echo` 命令了。
如果出现了 permission denied: ./test.sh 这个错误，则修改该文件的权限为 744 即可。不要碰见文件权限都设成 777，个人觉得不太好。
![在这里插入图片描述](/images/201812/20181128150414641.png)

PS: 修改权限
```bash
cd /usr/local/etc/bash_profile_self
chmod -R 744 test.sh
ll	# 查看一下文件权限
```
![在这里插入图片描述](/images/201812/20181128150845751.png)

bingo... 
just so so

----
真正的稳定，是优秀的能力，因为能力，才是这个时代的铁饭碗。 一一 Mobro


