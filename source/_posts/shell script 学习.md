---
title: shell script 学习
date: 2018-12-03 10:16:56
tags: [linux, shell, shell script]
---

### 变量
+ Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。
```bash
#!/bin/bash
# #! 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。
```
脚本运行方式：
1. 作为可执行程序 
```bash
chmod +x ./test.sh
./test.sh
```
2. 作为解释器参数
```bash
/bin/bash test.sh
/bin/sh test.sh
/bin/php test.php
```
+ 定义变量时，变量名不加美元符号（$，PHP语言中变量需要）
```bash
## 声明变量
test_name="mobro.site"
for file in `ls /etc`
for file in $(ls /etc)

## 使用变量
echo $test_name
echo ${test_name}	# 为了帮助解释器识别变量的边界
# eg
for skill in Ada Coffe Action Java; do
    echo "I am good at ${skill}Script"
done
readonly test_name 	# 将 test_name 变成只读变量
unset test_name	# 删除变量；unset 不能删除只读变量
```
变量类型
运行shell时，会同时存在三种变量：
1) 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
2) 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
3) shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

+ **单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；双引号里可以有变量，双引号里可以出现转义字符**
```bash
greeting="hello, "$test_name" !"	# 字符串拼接
echo ${#greeting}	# 获取字符串长度
echo ${greeting:1:4}	# 提取字符
echo `expr index "$greeting" el`	# 查找 el 的位置（先找到哪个字符就先计算哪个）
```
+ bash支持一维数组（不支持多维数组），并且没有限定数组的大小。
```bash
## 定义数组的形式 =>>>> 数组名=(值1 值2 ... 值n)
array=(
1
2
3)
array1=(1 2 3)
array[3]=4
array[4]=5
## 读数组
val=${array[0]}
echo $val
echo $array[@]	# 获取整个数组
echo `length: "${#array[@]}"`	# 获取数组长度
echo `length: "${#array[*]}"`	# 获取数组长度
echo `length: "${#array[2]}"`	# 获取数组第二元素的长度
## 多行注释 EOF可以为其他任意符号
:<< EOF
注释内容...
注释内容...
EOF

:<<'
还是注释...
'
```
### shell 传递参数
**脚本内获取参数的格式为：$n。n 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数**
```bash
# Shell 传递参数实例！
chmod +x test.sh 
./test.sh 1 2 3
:<< '
第一个参数为：1
第二个参数为：2
第三个参数为：3
'
$#		# 传递到脚本的参数个数
$*		# 以一个单字符串显示所有向脚本传递的参数。
		# 如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
$$ 	# 脚本运行的当前进程 ID
$@		# 以一个单字符串显示所有向脚本传递的参数。
		# 如"$*"用「"」括起来的情况、以"$1" "$2" … "$n"的形式输出所有参数。
$-		# 显示 shell 使用的当前选项
$?		# 显示最后命令的退出状态。0 表示没有错误，其他表示有错误
```
`$*` 和 `$@` 区别： 假设在脚本运行时写了三个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）。看如下代码：
```bash
echo "-- \$* 演示 ---"
for i in "$*"; do
    echo $i
done

echo "-- \$@ 演示 ---"
for i in "$@"; do
    echo $i
done
```
执行结果：
$ chmod +x test.sh 
$ ./test.sh 1 2 3
-- $* 演示 ---
1 2 3
-- $@ 演示 ---
1
2
3

### shell 运算符
```bash
#!/bin/bash

val=`expr 2 + 2`
echo "2 + 2 = $val"
```
+ 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
+ 完整的表达式要被 ` ` 包含，注意这个字符不是常用的单引号。

常见的运算有:	`+`, `-`, `*`, `/`, `%`, `=`, `==`, `!=`；值得注意的是：
 **条件表达式要放在方括号之间，并且要有空格，例如: [$a==$b] 是错误的，必须写成 [ $a == $b ]**
```bash
val1=`expr 2 \* 2`
val2=`expr 2 / 2`
#:<< eof
val3=[ $val2 == 1 ]
echo "$val3"
上面个这是一种错误写法，不能使用 `val3=[ $val2 == 1 ]` 这种写法, 一般 == 或者 != 是用于条件判断时用
#eof
if [ $val2 == 1 ]
then
	echo "val2 = $val2"
fi
```
+ 在 MAC 中 shell 的 expr 语法是：$((表达式))，此处表达式中的 "*" 不需要转义符号 "\" 。
+ 乘号(*)前边必须加反斜杠(\)才能实现乘法运算；

常见的关系运算符： 
关系运算符只支持数字，不支持字符串，除非字符串的值是数字。
```bash
# -eq 相等
# -ne 不相等
# -gt 左 大于 右
# -lt 左 小于 右
# -ge 左 大于等于 右
# -le 左 小于等于 右
if [ $a -eq $b ]
then
	echo "$a -eq $b: a 等于 b"
else
	echo "不等于"
fi
```
常见的布尔运算
```bash
# ! 非运算
# -o 或运算
# -a 与运算
a=99
b=15
if [ $a -lt 100 -a $b -ge 15 ]
then
	echo "是的，满足条件"
fi
```
常见的逻辑运算
```bash
# && 逻辑and
# || 逻辑or
a=99
b=15
# if [ $a -lt 100 && $b -ge 15 ] 	# 这样写是不合理的，应该如下写。但是为何呢？
if [[ $a -lt 100 && $b -ge 15]]
then
	echo " ----- true "
fi
```
### [echo 命令](http://www.runoob.com/linux/linux-shell-echo.html)
+ echo 的 `""` 是可以省略的
+ read 命令从标准输入中读取一行,并把输入行的每个字段的值指定给 shell 变量
```bash
# -e 开启转义

# 显示结果指定到某个文件
echo "it's a test" > log

# 取日期
echo `date +%y-%m-%d`

read firstStr secondStr	# read 命令一个一个词组地接收输入的参数，每个词组需要使用空格进行分隔；如果输入的词组个数大于需要的参数个数，则多出的词组将被作为整体为最后一个参数接收。

# 实例，test.sh 代码：
read -p "请输入一段文字:" -n 6 -t 5 -s password
echo -e "\npassword is $password"

# -p 输入提示文字
# -n 输入字符长度限制
# -t 输入限时
# -s 隐藏输入内容
```

### printf 命令
+ 相比于 echo 移植性更好。
+ 使用引用文本或空格分隔的参数，外面可以在 printf 中使用格式化字符串，还可以制定字符串的宽度、左右对齐方式等。默认 printf 不会像 echo 自动添加换行符，我们可以手动添加 \n。
```bash
# printf 语法
printf  format-string  [arguments...]
# format-string 格式控制字符串

printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234 
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543 
printf "%-10c %-8s %-4.2f\n" 郭芙 女 47.9876 
```
%s %c %d %f都是格式替代符
%-10s 指一个宽度为10个字符（-表示左对齐，没有则表示右对齐），任何字符都会被显示在10个字符宽的字符内，如果不足则自动以空格填充，超过也会将内容全部显示出来。
%-4.2f 指格式化为小数，其中.2指保留2位小数。

### test 命令
用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。
在代码中 [] 执行基本的算数运算。
```bash
num1=100
num2=100
if test ${num1} -eq $[num2]
then
	printf "\n -------- 分割线 -------- \n%s == %s" $num1 $num2
else
	echo '不等'
fi
```
结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181129113332289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTYxMDE3OA==,size_16,color_FFFFFF,t_70)
_?? 为什么结果最后有个 % 呢？_
_?? -eq, =, == 在 test 命令中好像是通用的_

### 流程控制
+ if else if else
```bash
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```
+ 循环
```bash
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
# 写成一行
for var in item1 item2 ... itemN; do command1; command2; ... done;

# while 循环
int=1
while(( $int<=5 ))
do
	echo $int
	let "int++"
done

# eg: 
for loop in 1 2 3 4 5
do
 echo "$loop"
done
# until 循环
# until 循环执行一系列命令直至条件为 true 时停止。
a=0

until [ ! $a -lt 10 ]
do
   echo $a
   a=`expr $a + 1`
done
```
使用中使用了 Bash let 命令，它用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量

+ 无限循环
```bash
while :
do
	command
done

while true
do
	command
done

for (( ; ; ))
```

+ case

取值后面必须为单词in，每一模式必须以右括号结束。取值可以为变量或常数。匹配发现取值符合某一模式后，其间所有命令开始执行直至 ;;。
取值将检测匹配的每一个模式。一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。
```bash
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```
+ 跳出循环
在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环，Shell使用两个命令来实现该功能：break和continue。

### shell 函数
1、可以带function fun() 定义，也可以直接fun() 定义,不带任何参数。
2、参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255）
```bash
[ function ] funname [()]
{
    action;
    [return int;]
}
```
1. **函数返回值在调用该函数后通过 `$?` 来获得。**
2. **所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。**
3. **调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值**
```bash
funWithParam()
{
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

