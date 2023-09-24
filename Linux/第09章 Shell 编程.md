# Shell 编程
## Shell 是什么
![](attachment/Pasted%20image%2020230913154948.png)

Shell 是一个命令行解释器，它为用户提供了一个向 Linux 内核发送请求以便运行程序的界面系统级程序，用户可以用 Shell 来启动、挂起、停止甚至是编写一些程序.

## Shell 脚本的执行方式

脚本格式要求
1. 脚本以`#!/bin/bash` 开头
2. 脚本需要有可执行权限

编写第一个Shell 脚本
需求说明
创建一个 Shell 脚本，输出 hello world!

```shell
#!/bin/bash
echo "hello,world"
```

脚本的常用执行方式
方式 1：输入脚本的**绝对路径**或**相对路径**
- 首先要赋予 helloworld.sh  脚本的+x 权限
- 执行脚本
![](attachment/Pasted%20image%2020230913155517.png)

方式 2(sh+脚本)，不推荐
说明：不用赋予脚本+x 权限，直接执行即可
![](attachment/Pasted%20image%2020230913155539.png)

## shell 变量

Linux Shell 中的变量分为，**系统变量**和**用户自定义变量**。
- 系统变量：`$HOME、$PWD、$SHELL、$USER `等等，比如: `echo $HOME` 等等..
![](attachment/Pasted%20image%2020230913155907.png)

- 显示当前 shell 中所有变量：set


 **shell 变量的定义**
基本语法
- 定义变量：`变量=值 `
- 撤销变量：`unset 变量`
- 声明静态变量：`readonly 变量`，注意：不能 `unset`

快速入门
案例 1：定义变量 A
案例 2：撤销变量 A
![](attachment/Pasted%20image%2020230913160053.png)

案例 3：声明静态的变量 B=2，不能 unset
![](attachment/Pasted%20image%2020230913160126.png)

案例 4：可把变量提升为全局环境变量，可供其他 shell 程序使用

**定义变量的规则**
- 变量名称可以由字母、数字和下划线组成，但是不能以数字开头。
- 等号两侧不能有空格
- 变量名称一般习惯为大写

**将命令的返回值赋给变量（重点）**
- A=\`ls -la\` 反引号，运行里面的命令，并把结果返回给变量 A
- A=$(ls -la) 等价于反引号

![](attachment/Pasted%20image%2020230913160442.png)

---

**设置环境变量**

基本语法
```txt
export 变量名=变量值 （功能描述：将 shell 变量输出为环境变量）
source  配置文件     （功能描述：让修改后的配置信息立即生效）
echo $变量名         （功能描述：查询环境变量的值）
```
![](attachment/Pasted%20image%2020230913161012.png)

**快速入门**

在/etc/profile 文件中定义 TOMCAT_HOME 环境变量
![](attachment/Pasted%20image%2020230913161050.png)

查看环境变量 TOMCAT_HOME 的值
```txt
echo $TOMCAT_HOME
```

在另外一个 shell 程序中使用 TOMCAT_HOME
![](attachment/Pasted%20image%2020230913161130.png)

注意：在输出 TOMCAT_HOME 环境变量前，需要让其生效
```txt
source /etc/profile
```

### 位置参数变量

当我们执行一个 shell 脚本时，如果希望获取到命令行的参数信息，就可以使用到位置参数变量，比如 ：` ./myshell.sh 100 200 `,  这个就是一个执行 shell 的命令行，可以在 `myshell`  脚本中获取到参数信息

基本语法
```txt
$n （功能描述：n 为数字，$0 代表命令本身，$1-$9 代表第一到第九个参数，十以上的参数，十以上的参数需要用大括号包含，如${10}）

$* （功能描述：这个变量代表命令行中所有的参数，$*把所有的参数看成一个整体）

$@（功能描述：这个变量也代表命令行中所有的参数，不过$@把每个参数区分对待）

$#（功能描述：这个变量代表命令行中所有参数的个数）
```

**位置参数变量应用实例**
案例：编写一个 shell 脚本`positionPara.sh` ， 在脚本中获取到命令行的各个参数信息

![](attachment/Pasted%20image%2020230913161544.png)

![](attachment/Pasted%20image%2020230913161549.png)

### 预定义变量

预定义变量就是 shell 设计者事先已经定义好的变量，可以直接在 shell 脚本中使用

**基本语法**
```txxt
$$ （功能描述：当前进程的进程号（PID））

$! （功能描述：后台运行的最后一个进程的进程号（PID））

$？ （功能描述：最后一次执行的命令的返回状态。如果这个变量的值为 0，证明上一个命令正确执行；如果这个变量的值为非 0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确了。）
```

**应用实例**
在一个 shell 脚本中简单使用一下预定义变量
![](attachment/Pasted%20image%2020230913185359.png)


## 运算符

**基本语法**
```txt
“$((运算式))”或“$[运算式]”

expr m + n

注意 expr 运算符间要有空格

expr m - n

expr    \*, /, %        乘，除，取余
```

应用实例
案例 1：计算（2+3）X4 的值
`$((运算式))`
![](attachment/Pasted%20image%2020230913185702.png)

`$[运算式]`
![](attachment/Pasted%20image%2020230913185727.png)

`expr`
![](attachment/Pasted%20image%2020230913185746.png)

案例 2：请求出命令行的两个整数参数的和
![](attachment/Pasted%20image%2020230913185813.png)

## 条件判断

**if判断语句**

基本语法
```shell
[ condition ]
# 注意 condition 前后要有空格
# 非空返回 true，可使用$?验证（0 为 true，>1 为 false）
```

应用实例

```shell
[ atguigu ] # 返回 true 
[] # 返回 false

[condition] && echo OK || echo notok
# 条件满足，执行后面的语句
```

**常用判断条件**

两个整数的比较
- = 字符串比较
- -lt 小于
- -le 小于等于
- -eq 等于
- -gt 大于
- -ge 大于等于
- -ne 不等于

按照文件权限进行判断
- -r 有读的权限 \[ -r 文件 ]
- -w 有写的权限
- -x 有执行的权限

按照文件类型进行判断
- -f 文件存在并且是一个常规的文件
- -e 文件存在
- -d 文件存在并是一个目录

**应用实例**

案例 1："ok"是否等于"ok"
![](attachment/Pasted%20image%2020230913190720.png)

案例 2：23 是否大于等于 22
![](attachment/Pasted%20image%2020230913190745.png)

案例 3：/root/install.log 目录中的文件是否存在
![](attachment/Pasted%20image%2020230913190815.png)

## 流程控制

基本语法
```txt
if [ 条件判断式 ];
then
	程序
fi
```

```txt
if [ 条件判断式 ] 
then
	程序
elif [条件判断式]
then
	程序
fi
```

注意事项：
- \[ 条件判断式 ]，中括号和条件判断式之间必须有空格
- 推荐使用第二种方式

应用实例
案例：请编写一个 shell 程序，如果输入的参数，大于等于 60，则输出 "及格了"，如果小于 60，则输出 "不及格"
![](attachment/Pasted%20image%2020230913191114.png)

---

**case 语句**

基本语法
```txt
case $变量名 in "值 1"）
	如果变量的值等于值 1，则执行程序 1
;;
"值 2"）
	如果变量的值等于值 2，则执行程序 2
;;
…省略其他分支…
*）
	如果变量的值都不是以上的值，则执行此程序
;;
esac
```

应用实例
案例 1  ：当命令行参数是 1  时，输出 "周一",  是 2  时，就输出"周二"， 其它情况输出"other"

![](attachment/Pasted%20image%2020230913191336.png)


--- 

**for 循环**

基本语法1 
```txt
for 变量 in 值 1  值 2  值 3… 
do
	程序
done
```

应用实例
案例 1  ：打印命令行输入的参数  【会使用到$* $@】
![](attachment/Pasted%20image%2020230913191451.png)

基本语法 2
```txt
for (( 初始值;循环控制条件;变量变化 )) 
do
	程序
done
```

应用实例
案例 1 ：从 1 加到 100 的值输出显示
![](attachment/Pasted%20image%2020230913191609.png)

---

while 循环

基本语法 1
```txt
while [ 条件判断式 ] 
do
	程序
done
```

应用实例
例 1  ：从命令行输入一个数 n，统计从 1+..+ n  的值是多少？
![](attachment/Pasted%20image%2020230913191720.png)

## read 读取控制台输入

基本语法
```txt
read(选项)(参数)
```

选项
- -p：指定读取值时的提示符；
- -t：指定读取值时等待的时间（秒），如果没有在指定的时间内输入，就不再等待了

参数
- 变量：指定读取值的变量名

应用实例
案例 1：读取控制台输入一个 num 值
案例 2：读取控制台输入一个 num 值，在 10 秒内输入。
![](attachment/Pasted%20image%2020230913192009.png)

## 函数

shell 编程和其它编程语言一样，有系统函数，也可以自定义函数。系统函数中，我们这里就介绍两个。

### 系统函数

**basename 基本语法**

功能：返回完整路径最后 / 的部分，常用于获取文件名
```txt
basename [pathname] [suffix]
```
`basename [string] [suffix]`（功能描述：basename 命令会删掉所有的前缀包括最后一个（‘/’） 字符，然后将字符串显示出来。

选项：
- suffix 为后缀，如果 suffix 被指定了，basename 会将 pathname 或 string 中的 suffix 去掉。

--- 

**dirname 基本语法**
功能：返回完整路径最后 / 的前面的部分，常用于返回路径部分

dirname 文件绝对路径 （功能描述：从给定的包含绝对路径的文件名中去除文件名（非目录的部分），然后返回剩下的路径（目录的部分））

应用实例
案例 1：请返回 /home/aaa/test.txt 的 "test.txt" 部分
![](attachment/Pasted%20image%2020230913192538.png)

案例 2：请返回 /home/aaa/test.txt 的 /home/aaa
![](attachment/Pasted%20image%2020230913192548.png)

### 自定义函数

基本语法
```txt
[ function ] funname[()]
{
	Action; [return int;]
}
```

调用直接写函数名：`funname  [值]`

应用实例
案例 1：计算输入两个参数的和（read）， getSum
![](attachment/Pasted%20image%2020230913192714.png)

## Shell 编程综合案例
需求分析
1. 每天凌晨 2:10 备份 数据库 atguiguDB 到 /data/backup/db 2)
2. 备份开始和备份结束能够给出相应的提示信息
3. 备份后的文件要求以备份时间为文件名，并打包成 .tar.gz 的形式，比如：`2018-03-12_230201.tar.gz`
4. 在备份的同时，检查是否有 10 天前备份的数据库文件，如果有就将其删除。

编写一个 shell 脚本。
思路分析：
![](attachment/Pasted%20image%2020230913192824.png)

代码实现：
![](attachment/Pasted%20image%2020230913192833.png)

![](attachment/Pasted%20image%2020230913192839.png)

![](attachment/Pasted%20image%2020230913192843.png)

