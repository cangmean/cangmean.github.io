---
title: shell 基础
date: 2018-09-11 12:27:03
tags:
  - shell
categories:
  - 后端
---

一直以来在 Linux 环境下都是用 Python 来写脚本，但很多时候使用 Shell 脚本 更方便一点，之前也都是现查现用，比较麻烦而且耗时，所以学习并记录下来方便之后的查阅。

### 简单的脚本程序

```bash
#!/bin/bash
echo "Hello Shell World!"
```

以上就是一个 Shell 脚本，第一行`#!/bin/bash`告诉系统脚本使用那个解释器来执行，之后只要添加执行权限就可以运行了

```bash
chmod +x xxx.sh
./xxx.sh
```

<!--more-->

### 变量

Shell 中变量只能以数字、字母、下划线开头，使用标点符号关键字等是无效的

#### 变量赋值

```bash
name="mink" # 有效

@name="mink" # 无效
```

除了显式地直接赋值，还可以用语句给变量赋值

```bash
for file in `ls /etc`
或
for file in $(ls /etc)
```

以上语句会把`/etc`目录下的所有文件循环出来

#### 使用变量

使用一个定义的变量，只需在变量名前加一个美元符号即可

```bash
name="mink"
echo "My name is $name"
或
echo "My name is ${name}"
```

使用花括号是限制变量的边界，比如`$namehello`, 会认为变量是`namehello`， 需要用花括号隔开`${name}hello`

重新定义变量不需要添加美元符号，只需要重新赋值

```bash
name="mink"
echo $name
name="mink2"
echo $name
```

#### 只读变量

使用`readonly`设置只读变量，如果给只读变量赋值会报错

```bash
name="mink"
echo $name
readonly name
name="mink2"
```

#### 删除变量

使用`unset`删除变量，以下例子没有任何输出

```bash
name="mink"
unset name
echo $name
```

### 字符串

Shell 中可以使用单引号、双引号、不用引号设置字符串。

```bash
name='mink'
或
name="mink"
或
name=mink
```

以上都是合法

#### 单引号

单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的

```bash
name='mink'

echo 'Hello $name'
```

以上输出会原样返回，不会对字符串进行格式化，所以转义字符等都是无效的。

#### 双引号

双引号可以使用格式化变量，转义字符等

```bash
name="mink"
echo "Hello \"$name\""
```

#### 拼接字符串

拼接字符串只需要将两个字符串连在一起就可以

```bash
name="mink"
echo "Hello"Wrold
echo "Hello"$name
echo "Hello $name"
echo Hello$name
```

#### 获取字符串长度

使用`#`字符在变量前就可以获取字符串的长度

```bash
name="mink"
echo ${#name}
```

#### 提取子字符串

使用`:`开始提取子字符串，以下例子表示从下标 1 开始取 4 个字符

```bash
name="Hello world"
echo ${name:1:3}
```

##### 查找子字符串

查找字符 i 或 o 的位置(哪个字母先出现就计算哪个)

```bash
string="runoob is a great site"
echo `expr index "$string" io`  # 输出 4
```

### 数组

```bash
area=(
  1
  2
  3
  4
)

或
area=(1 2 3 4)

也可以下标赋值
area[0] = 1
area[1] = 2
```

#### 获取数组长度

使用`*`或`@`来获取数组长度

```bash
echo ${#area[*]}
或
echo ${#area[@]}
```

### 运算符

Shell 支持算数运算、关系运算、布尔运算、字符串运算、文件测试运算。

#### 算数运算符

使用`expr`获取表达式的值，运算符与别的编程语言一样

```bash
a=`expr 2 + 2`
echo $a
```

#### 关系运算符
关系运算符只支持数字，不支持字符串，除非字符串的值是数字

**例子**
```bash
a=10
b=20
if [$a -eq $b]
then
  echo "a eq b"
else
  echo "a ne b"
fi
```
**运算符表格**

| 运算符 | 描述     |
| ------ | -------- |
| -eq    | 相等     |
| -ne    | 不相等   |
| -gt    | 大于     |
| -lt    | 小于     |
| -ge    | 大于等于 |
| -le    | 小于等于 |

#### 字符串运算符
字符串运算有`-z`和`-n`来判断字符串长度
```bash
a='hello'

if [ -z $a ]
then
   echo "-z $a : 字符串长度为 0"
else
   echo "-z $a : 字符串长度不为 0"
fi

if [ -n "$a" ]
then
   echo "-n $a : 字符串长度不为 0"
else
   echo "-n $a : 字符串长度为 0"
fi
```

#### 文件测试运算符
文件测试运算符用于检测 Unix 文件的各种属性。

| 运算符  | 描述                   |
| ------- | ---------------------- |
| -d file | 是否是目录             |
| -f file | 是否是文件             |
| -r file | 文件是否可读           |
| -w file | 文件是否可写           |
| -x file | 文件是否可执行         |
| -s file | 文件是否为空(判断大小) |
| -e file | 文件或目录是否存在     |

### 条件语句

```bash
if condition
then
  command1
else
  command2
fi
```
### 循环语句

**for循环**
```bash
for i in 1 2 3 4 5
do
  echo $i
done
```

**while循环**
```bash
i=1
while(($i<5))
do
  echo $i
  a=`expr $a + 1`
```

### 函数

```bash
sayName(){
  echo "mink"
}

echo "-----函数开始执行-----"
sayName
echo "-----函数执行完毕-----"
```

读取命令行输入， 函数返回值在调用该函数后通过 $? 来获得。
```bash
funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
funWithReturn
echo "输入的两个数字之和为 $? !"
```

获取函数的参数

```bash
funWithParam(){
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

### 重定向

一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件
- 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
- 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
- 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。


### 参考
- http://www.runoob.com/linux/linux-shell.html
