## Shell

> 本小节为选读。我也不太会写 Shell 脚本，都是写的时候，在网络上拼拼凑凑。。。

### Shell 脚本是什么？

一个 Shell 脚本是一个文本文件，包含一个或多个命令。作为系统管理员，我们经常需要使用多个命令来完成一项任务，我们可以添加这些所有命令在一个文本文件(Shell 脚本)来完成这些日常工作任务。

**什么是默认登录 Shell ？**

在 Linux 操作系统，`"/bin/bash"` 是默认登录 Shell，是在创建用户时分配的。

使用 chsh 命令可以改变默认的 Shell 。示例如下所示：

```bash
## chsh <用户名> -s <新shell>
## chsh ThinkWon -s /bin/sh
```

**在 Shell 脚本中，如何写入注释？**

注释可以用来描述一个脚本可以做什么和它是如何工作的。每一行注释以 `#` 开头。例子如下：

```bash
#!/bin/bash
## This is a command
echo “I am logged in as $USER”
```

### 语法级

#### 可以在 Shell 脚本中使用哪些类型的变量？

在 Shell 脚本，我们可以使用两种类型的变量：

- 系统定义变量

  > 系统变量是由系统系统自己创建的。这些变量通常由大写字母组成，可以通过 `set` 命令查看。

- 用户定义变量

  > 用户变量由系统用户来生成和定义，变量的值可以通过命令 `"echo $<变量名>"` 查看。

**Shell脚本中 $? 标记的用途是什么？**

在写一个 Shell 脚本时，如果你想要检查前一命令是否执行成功，在 `if` 条件中使用 `$?` 可以来检查前一命令的结束状态。

- 如果结束状态是 0 ，说明前一个命令执行成功。例如：

  ```bash
  root@localhost:~## ls /usr/bin/shar
  /usr/bin/shar
  root@localhost:~## echo $?
  0
  ```

- 如果结束状态不是0，说明命令执行失败。例如：

  ```bash
  root@localhost:~## ls /usr/bin/share
  ls: cannot access /usr/bin/share: No such file or directory
  root@localhost:~## echo $?
  2
  ```

**Bourne Shell(bash) 中有哪些特殊的变量？**

下面的表列出了 Bourne Shell 为命令行设置的特殊变量。

```bash
内建变量    解释
$0    命令行中的脚本名字
$1    第一个命令行参数
$2    第二个命令行参数
…..    …….
$9    第九个命令行参数
$##    命令行参数的数量
$*    所有命令行参数，以空格隔开
```

**如何取消变量或取消变量赋值？**

`unset` 命令用于取消变量或取消变量赋值。语法如下所示：

```bash
## unset <变量名>
```

#### Shell 脚本中 `if` 语法如何嵌套?

```bash
if [ 条件 ]
then
命令1
命令2
…..
else
if [ 条件 ]
then
命令1
命令2
….
else
命令1
命令2
…..
fi
fi
```

**在 Shell 脚本中如何比较两个数字？**

在 `if-then` 中使用测试命令（ `-gt` 等）来比较两个数字。例如：

```bash
#!/bin/bash
x=10
y=20
if [ $x -gt $y ]
then
echo “x is greater than y”
else
echo “y is greater than x”
fi
```

#### Shell 脚本中 `case` 语句的语法?

基础语法如下：

```bash
case 变量 in
值1)
命令1
命令2
…..
最后命令
!!
值2)
命令1
命令2
……
最后命令
;;
esac
```

#### Shell 脚本中 `for` 循环语法？

基础语法如下：

```bash
for 变量 in 循环列表
do
命令1
命令2
….
最后命令
done
```

#### Shell 脚本中 `while` 循环语法？

如同 `for` 循环，`while` 循环只要条件成立就重复它的命令块。
不同于 `for`循环，`while` 循环会不断迭代，直到它的条件不为真。

基础语法：

```bash
while [ 条件 ]
do
命令…
done
```

**do-while 语句的基本格式？**

`do-while` 语句类似于 `while` 语句，但检查条件语句之前先执行命令（LCTT 译注：意即至少执行一次。）。下面是用 `do-while` 语句的语法：

```bash
do
{
命令
} while (条件)
```

**Shell 脚本中 break 命令的作用？**

`break` 命令一个简单的用途是退出执行中的循环。我们可以在 `while` 和 `until` 循环中使用 `break` 命令跳出循环。

**Shell 脚本中 continue 命令的作用？**

`continue` 命令不同于 `break` 命令，它只跳出当前循环的迭代，而不是整个循环。`continue` 命令很多时候是很有用的，例如错误发生，但我们依然希望继续执行大循环的时候。

#### 如何使脚本可执行?

使用 chmod 命令来使脚本可执行。例子如下：`chmod a+x myscript.sh` 。

**#!/bin/bash 的作用？**

`#!/bin/bash` 是 Shell 脚本的第一行，称为释伴（shebang）行。

- 这里 `#` 符号叫做 hash ，而 `!` 叫做 bang。
- 它的意思是命令通过 `/bin/bash` 来执行。

**如何调试 Shell脚本？**

- 使用 `-x'` 数（`sh -x myscript.sh`）可以调试 Shell脚本。
- 另一个种方法是使用 `-nv` 参数(`sh -nv myscript.sh`)。

**如何将标准输出和错误输出同时重定向到同一位置?**

- 方法一：`2>&1 (如## ls /usr/share/doc > out.txt 2>&1 )` 。
- 方法二：`&> (如## ls /usr/share/doc &> out.txt )` 。

**在 Shell 脚本中，如何测试文件？**

test 命令可以用来测试文件。基础用法如下表格：

```bash
Test         用法
-d 文件名    如果文件存在并且是目录，返回true
-e 文件名    如果文件存在，返回true
-f 文件名    如果文件存在并且是普通文件，返回true
-r 文件名    如果文件存在并可读，返回true
-s 文件名    如果文件存在并且不为空，返回true
-w 文件名    如果文件存在并可写，返回true
-x 文件名    如果文件存在并可执行，返回true
```

#### 在 Shell 脚本如何定义函数呢？

函数是拥有名字的代码块。当我们定义代码块，我们就可以在我们的脚本调用函数名字，该块就会被执行。示例如下所示：

```bash
$ diskusage () { df -h ; }
译注：下面是我给的shell函数语法，原文没有
[ function ] 函数名 [()]
{
命令;
[return int;]
}
```

**如何让 Shell 就脚本得到来自终端的输入?**

read 命令可以读取来自终端（使用键盘）的数据。read 命令得到用户的输入并置于你给出的变量中。例子如下：

```bash
## vi /tmp/test.sh
#!/bin/bash
echo ‘Please enter your name’
read name
echo “My Name is $name”
## ./test.sh
Please enter your name
ThinkWon
My Name is ThinkWon
```

**如何执行算术运算？**

有两种方法来执行算术运算：

- 1、使用 expr 命令：`## expr 5 + 2` 。
- 2、用一个美元符号和方括号（`$[ 表达式 ]`）：`test=$[16 + 4] ; test=$[16 + 4]` 。

### 编程题

#### 判断一文件是不是字符设备文件，如果是将其拷贝到 `/dev` 目录下？

```bash
#!/bin/bash
read -p "Input file name: " FILENAME
if [ -c "$FILENAME" ];then
　　cp $FILENAME /dev
fi
```

#### 添加一个新组为 class1 ，然后添加属于这个组的 30 个用户，用户名的形式为 stdxx ，其中 xx 从 01 到 30 ？

```bash
#!/bin/bash
groupadd class1
for((i=1;i<31;i++))
do
        if [ $i -le 10 ];then
                useradd -g class1 std0$i
        else
                useradd -g class1 std$i
        fi
done
```

**编写 Shell 程序，实现自动删除 50 个账号的功能，账号名为stud1 至 stud50 ？**

```bash
#!/bin/bash
for((i=1;i<51;i++))
do
                userdel -r stud$i
done
```

#### 写一个 sed 命令，修改 `/tmp/input.txt` 文件的内容？

要求：

- 删除所有空行。
- 一行中，如果包含 “11111”，则在 “11111” 前面插入 “AAA”，在 “11111” 后面插入 “BBB” 。比如：将内容为 0000111112222 的一行改为 0000AAA11111BBB2222 。

```bash
[root@~]## cat -n /tmp/input.txt
     1  000011111222
     2
     3  000011111222222
     4  11111000000222
     5
     6
     7  111111111111122222222222
     8  2211111111
     9  112222222
    10  1122
    11

## 删除所有空行命令
[root@~]## sed '/^$/d' /tmp/input.txt
000011111222
000011111222222
11111000000222
111111111111122222222222
2211111111
112222222
1122

## 插入指定的字符
[root@~]## sed 's#\(11111\)#AAA\1BBB#g' /tmp/input.txt
0000AAA11111BBB222
0000AAA11111BBB222222
AAA11111BBB000000222
AAA11111BBBAAA11111BBB11122222222222
22AAA11111BBB111
112222222
1122
```