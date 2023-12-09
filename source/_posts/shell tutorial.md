### 一、变量

#### 1、变量分类

1. 环境变量： 系统环境变量定义，如：PATH

2. 全局变量： shell 脚本文件定义的全局性变量

3. 局部变量： shell 脚本定义的局部性变量

   在 *函数* 章节将具体介绍这几种变量的使用

#### 2、变量的类型

shell 中变量类型都是string

#### 3、变量定义以及取值

定义： 变量名=变量值

**变量名只能是英文字符或者数字或者下划线‘_’ ， 且不能是数字开头**

取值：$变量名 

```shell
$ name=liuheng #定义变量，‘=’ 前后不能有空格
$ echo $name   #变量取值
liuheng
```

#### 4、命令替代

将命令的输出结果存到变量

```shell
变量名=`命令`  或者  变量名=$(命令)

$ now=`date`   #获取date 命令值保存在now 变量
$ echo $now    #从变量取值
2021年09月27日 16:48:41
$ now2=$(date) #获取date 命令值保存在now 变量
$ echo $now2
2021年09月27日 16:50:04
```

#### 5、运算式替代

将运算表达式结果保存到变量

```shell
变量名=$[运算表达式]   或者  变量名=$((运算表达式))

$ one=1
$ two=2
$ sum=$[one+two]  	#计算 one+two 的结果保存在sum 变量
$ echo $sum
3
$ sum2=$((one+two)) #计算 one+two 的结果保存在sum 变量
$ echo $sum2
3
```

### 二、条件测试

[  判断条件  ] 或者  test   判断条件   

**注意判断条件与'[' 、']'   之间有空格， 判断条件可以是多个，通过逻辑运算符连接** 

#### 1、数值判断  

eq: 等于

gt:大于

ge:大于等于

lt:小于

le:小于等于

```shell
#数值判断 方式1
$ var=2
$ test $var -eq 2     #判断var 是否等于2 
$ echo $?     		 #返回上条命令的执行结果, 0:true 1:false
0					#true
$ test $var -eq 3     #判断var 是否等于3
$ echo $?			
1					#false
#数值判断 方式2(比较常用) 下文以方式2的格式举例
$ [ $var -eq 2 ]
$ echo $?
0
$ [ $var -eq 3 ]
$ echo $?
1
```

#### 2、字符串判断

= ： 等于 

!=  ：不等于

```shell
#字符串判断
$ str=liuheng
$ [ $str = liuheng ] # 判断变量str是否是liuheng， 注意‘=’ 前后有空格  
$ echo $?
0
$ [ $str = liuheng1 ]
$ echo $?
1
```

#### 3、文件判断

-d  是否是目录

-f  是否是一般文件文件

... 其他

```shell
#文件判断
$ dir=/e/Program_Files/OpenVPN/config
$ [ -d $dir ] #判断dir 是否是个目录
$ echo $?
0
```

#### 4、逻辑运算:与或非

-a :  与，and

-o:  或， or

!: 非

```shell
#多个条件组合
#and
$ name=liuheng
$ age=18
$ [ $age -eq 18 -a $name = liuheng ] #条件1 and 条件2 同时满足为true
$ echo $?
0
$ [ $age -eq 18 -a $name = lucas ]   #条件1 and 条件2 同时满足为true
$ echo $?
1
# or
$ [ $age -eq 8 -o $name = liuheng ] #条件1 or 条件2 满足其一为true
$ echo $?
0
```



### 三、分支结构

#### 1、if  结构

```shell
#if .. elif .. else .. fi
if [ 条件1 ]
then
	#do something
elif [ 条件2 ]
then
	#do something
else
	#do something
fi

#if .. fi
if [ 条件 ]
then
	#do
fi
#if .. else .. fi 
if [ 条件 ]
then
	#do something
else
    #do something
fi

#if/elif 与 then 可以在同一行，但需要‘;’ 分开
if [ 条件 ]; then
	#do something
fi
#if、elif、then、else、fi 是独立的语句需换行或者‘;’ 分割开，否则shel 解释器不能识别
#下面的for、do, while、do 同理  

#例子 if.sh
#! /bin/bash
age=18
if [ $age -ge 18 ]
then
	echo "年龄大于等于18"
elif [ $age -ge 12 ]
then
	echo "年龄大于等于12小于18"
else
	echo "年龄小于12"
fi
$ sh if.sh
年龄大于等于18
```

#### 2、case 结构

```shell
case $变量名 in
"情况1")
#如果变量的值等于情况1，则执行
;; #结束情况1 的逻辑 等价c语言的break
"情况2")
#如果变量的值等于情况2，则执行
;;
*)
#如果变量的值都不是以上的值，则执行此程序
;;
esac

#等价于if结构
if [ 情况1满足 ]; then
elif [ 情况2满足 ]; then
else
	其他情况
fi

#例子 case.sh
var=1
case $var in
1) #$var等于1
	echo "情况1"
	;;
2)#var等于2
	echo "情况2"
	;;
*)#其他 
    echo "其他"
    ;;
esac
$ sh case.sh
情况1
```

### 四、循环结构

#### 1、for  结构

```shell
for item in list 
do
	#do something
done
#for 与 do 同一行 需要';' 分割
for item in list; do 
	#do something
done

#例子 for.sh
#遍历当前目录文件
for file in `ls`
do 
    if [ -f $file ];then
		echo $file is a file
	fi
done
$ sh for.sh
case.sh is a file
for.sh is a file
if.sh is a file
while.sh is a file
```

#### 2、while  结构

```shell
while 条件
do
	#do something
done
#while 与do 同一行需要‘;’ 分割
while 条件; do
	#do something
done

#例子 while.sh
#从10~1 输出
var=10
while [ $var -gt 0 ]
do 
	echo $var
	var=$[var-1]	#计算var-1赋值给var，见上文运算式替代语法, 等价于C 语言的var--
done
$ sh while.sh
10
9
8
7
6
5
4
3
2
1
```

### 五、函数

#### 1、函数定义

```shell
函数名() {
	函数体
}

#例子 func.sh
show() {
   echo "hello"
}
```

#### 2、函数调用

```shell
函数名 [参数列表可选]

#例子func.sh
#函数定义
show() {
    echo "hello"
}
#函数调用
show
$ sh fun.sh
hello
```

##### 	2.1、参数使用 

​	$1、$2..、$n： 分别代表第n个参数，$@ : 代表参数列表

```shell
#例子 arg.sh
#函数定义
show() {
  echo "第一个参数：$1"
  echo "第二个参数：$2"
}
#函数调用
show a b
$ sh arg.sh
第一个参数：a
第二个参数：b
```

#####     2.2 、返回值

函数(命令)的执行结果可分为2部分：状态值与输出值 
状态值：
	通过return 返回状态值，若函数体没有return, 状态值为函数最后一条指令的执行状态，通过$? 可以获取执行状态值;
输出值：
	通过输出函数echo 提供输出， 通过 $(函数名) 获取输出值; 

函数的执行与命令的执行类似，可认为函数就是一个自定义的命令

```shell
#函数中使用return返回函数值时，通过$? 来捕获函数返回值
#例子 return.sh
isdir() {
  if [ -d $1 ]; then
     return 0 #返回true
  else
     return 1 #返回false
  fi
}

isdir `pwd`
echo $? #$? 保存有函数调用的返回值

#判断函数返回值
if isdir `pwd`; then echo 'is dir!'; fi
$ sh return.sh
0
is dir!

#函数中使用echo返回函数值时，通过 $(func_name arg1 arg2...) 来捕获函数返回值,同上文命令替代语法
#例子 return2.sh
isdir() {
  if [ -d $1 ]; then
       echo 0
  else
       echo 1
  fi
}

#isdir `pwd`
#echo $? #$? 保存的是isdir 执行是否出错，与isdir echo 的值无关

ret=$(isdir `pwd`)
echo "ret:$ret"

$ sh return2.sh
0
ret:0
```

##### 2.3、变量使用

```shell
#例子 gvar.sh
gvar=1		 #全局变量,作用于整个文件
#定义函数
addOne() {
  gvar=$[gvar+1]
}
#操作 gvar 递增到5
while [ $gvar -lt 5 ]
do
    addOne
done
#输出gvar
echo "gvar=$gvar"
$ sh gvar.sh
gvar=5

#例子 localvar.sh
gvar=1
addOne() {
  #通过local 标记gvar 变量为局部变量,此变量的作用范围就在此函数，与上文定义的全局变量为2个不同的变量
  local gvar=$[gvar+1] #计算全局变量gvar+1的值 赋值给局部变量gvar
  #gvar=$[gvar+1] #无local标记的话gvar 表示的是全局变量
}
echo "gvar=$gvar"  #打印的是全局变量gvar 值没有被修改
$ sh localvar.sh
gvar=1			

```

### 六、其他

#### 1、脚本传参数

```shell
sh  脚本  参数1 参数2 #与函数传参相似
#例子 arg.sh
echo $1
echo $2

$ sh arg.sh 12 34
12
34

```

#### 2、输入、输出

```shell
#例子 input.sh
read -p "请输入：" name  #将输入保存到name, -p 打印提示
echo "hello $name"
$ sh input.sh
请输入：liuheng
hello liuheng
```

#### 3、隐藏输出以及错误

命令 > /dev/null  2>&1 

```shell
$ ping2  > /dev/null #标准输出重定向到 /dev/null
bash: ping2: command not found
$ ping2 > /dev/null 2>&1 #标准输出以及标准错误重定向 /dev/null

/dev/null linux下的黑洞，类似于垃圾桶，任何东西到里面都会被粉碎
> 重定向 
1 标准输出
2 错误输出
& 表示等同于的意思， 2>&1 表示2的重定向等同于1
```

#### 4、原子处理命令集

(命令1；命令2) ,  使用() 原子执行命令集合, 类似 &&

```shell
$ pwd
/e/Program_Files/OpenVPN/config
$ (cd ..;ls)  #cd 没有改变当前目录(与 && 连接不同)，ls 列出的是 cd .. 后的文件 
bin/  config/  doc/  icon.ico  log/  sample-config/  Uninstall.exe*
$ pwd
/e/Program_Files/OpenVPN/config
# && 连接命令
$ pwd
/e/Program_Files/OpenVPN/config
$ cd .. && ls #cd 改变了当前目录
bin/  config/  doc/  icon.ico  log/  sample-config/  Uninstall.exe*
$ pwd
/e/Program_Files/OpenVPN  
```

#### 5、文本处理工具 awk & sed

```shell
awk 参数 '脚本' 目标文件     #常用方式
其中脚本格式：条件1 ｛操作1｝ 条件2 ｛操作2｝...
条件部分可以没有默认为true, {操作} 等价于 true {操作}

脚本也可以是一个文件，如果脚本部分很复杂的话，可以把脚本写到文件，通过 -f 指定脚本文件
awk 参数 -f 脚本文件 目标文件  #不常用

awk 命令功能相当于下列伪代码：
for line in file:			
	for field  in line		#默认按空格分割行，-F 可以指定列分割符号，按指定的字符对行分割成多列 
       脚本代码				 # if 条件1 操作1  if 条件2  操作2 ...
       $0 : 当前行，$1 当前行的第1列 $2 当前行的第2列 ...

#例子： 遍历/etc/passwd文件的每一行， 对每行数据以“:”做分割并输出分割后的第一列
$ awk -F: '{ print $1 }' /etc/passwd 
root
lucas

#例子 统计root的进程数
#END 为特殊条件表示文件处理完时
#按行处理ps 的输出，统计用户为root的进程个数
$ ps -ef|awk '$1="root" {count=count+1} END {print count}'
170

sed 参数 '脚本'  目标文件
其中脚本部分格式：{匹配目标(位置)}{操作}{操作内容}
操作：增(a/i)、删(d)、改(s)
eg: 
    sed '2a append line'  test.txt #在第2行后追加内容
    sed '2i insert line'  test.txt #在第2行前插入内容
    sed '1d' test.txt              #删除第一行
    sed '1,3d' test.txt            #删除第1~3行
    sed 's/print/echo/g' text.txt  #替换print-->echo
```

#### 6、正则表达式

正则表达式是一种用于匹配字符串的规则，其基本格式为：{元字符} {量词}

元字符： 用于匹配一个字符

量词：用于修饰元字符匹配多少次，既元字符重复的次数

| 元字符 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| ^ 、$  | 开始\结束                                                    |
| \w     | 字母数字下划线                                               |
| \W     | \w 的补集，既非字母数字下划线                                |
| \d     | 数字                                                         |
| \D     | \d 的补集，既非数字                                          |
| \s     | 空白字符，空格、\t、\n                                       |
| \S     | \s的补集, 既非空白字符                                       |
| \t     | 制表符                                                       |
| \n     | 换行符                                                       |
| .      | 非换行符                                                     |
| [...]  | 字符组                                                       |
| [^...] | 字符组的补集                                                 |
| ()     | 分组，将多个元字符作为一组当成一个元字符，方便量词同时修饰多个元字符 |
| \|     | 或                                                           |



| 量词    | 描述    |
| ------- | ------- |
| ？      | 0或1次  |
| +       | 1或多次 |
| *       | 0或多次 |
| ｛n｝   | n次     |
| ｛n,｝  | 至少n次 |
| ｛n,m｝ | n~m 次  |

### 七、练习

1、输入一个用户名判断此用户名在当前Linux 主机系统是否存在，如存在则显示该用户名的用户ID号，否则输出"不存在"

2、统计某个目录下(及其子目录下)后缀为.txt 的文件个数



