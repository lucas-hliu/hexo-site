### 一、变量

#### 变量的类型

- 数值型
- 布尔型
- 字符型
- 抽象结构（容器类+自定义class）

type(变量名)： 获取变量的类型

特殊类型变量：None 表示空、假...

类型转化：str(变量) 转化成字符串， int(变量) 转化成数字 

```python
name ="liuheng"
type(name)
<class 'str'>
type(int(age))
<class 'int'>
type(1.0)
<class 'float'>
type(None)
<class 'NoneType'>
type(True)
<class 'bool'>
```

#### 变量的定义

定义：变量名 = 变量值

#### 数据容器

数据容器(数据结构)变量：用来装一组数据的变量

| 容器        | 定义        | 操作                                                         |
| ----------- | ----------- | ------------------------------------------------------------ |
| 元组(tuple) | names=()    | 取值：names[0]，不可修改                                     |
| 列表(list)  | names=[]    | 取值：names[0]<br />追加：names.append("clr")<br />插入：names.insert(0,"clr") <br />删除： names.pop(0) |
| 集合(set)   | names=set() | 添加：names.add("clr")<br />删除：names.pop(0)/names.remove("clr") |
| 字典(dict)  | names ={}   | 取值：names["liuheng"]<br />添加/修改：names["liuheng"] = 18<br />删除：names.pop("liuheng") |

```python
# 元组 tuple 不可修改
print("-------------------tuple---------------------")
names = ("liuheng", "lucas", "clr") #用() 定义元组，括号里为元组的元素
type(names)
print(names)
names[1]						# 通过[索引] 获取元素 	
# TypeError: 'tuple' object does not support item assignment
# names[1] = "222"
names = names[1:]				# 切片,从元组中截取部分元素
print(names)

print("------------------list----------------------")
# 列表 list
names = ["liuheng", "lucas"]   # 通过[] 定义列表
print(names)
# 追加
names.append("clr")
print(names)
# 插入
names.insert(0, "chenglirong")
print(names)
# 删除
names.pop(1)
print(names)
# 访问
print(names[0])
# 遍历
for name in names:
    print(name)

# 列表生成式
[x for x in range(10) if x % 2 == 0]
new_names = ["_" + name for name in names]
print(new_names)
# 切片
print(new_names[1:])

print("------------------set----------------------")
# 集合 set 去重，集合中的元素唯一
names = set(["liuheng", "liuheng", "lucas"])
print(names)
# 集合生成式
names = {x + "xxx" for x in names}
print(names)

print("------------------dict----------------------")
# 字典 dict
persons = {"liuheng": 18, "lucas": 12}
print(persons)
print(type(persons))
# 增
persons["crl"] = 18
print(persons)
# 改
persons["liuheng"] = 9
print(persons)
# 删
persons.pop("lucas")
print(persons)

# 遍历
for k in persons.keys():
    print(k)

for v in persons.values():
    print(v)

for k, v in persons.items():
    print(k + ":" + str(v))

# 字典生成式
d = {i: i * i for i in range(6)}
print(d)

```

### 二、分支结构

```python
if 条件1:		#语句后有':' 			
	...		 #语句下的代码块需与if空四个空格
	...
elif 条件2：
	...
else:
	...

#例子 if.py    
print("请输入年龄：")
age = int(input())

if age >= 18:
    print("年龄大于18")
elif age > 12:
    print("年龄大于12，小于18")
else:
    print("年龄小于12")
```

### 三、循环结构

#### for 结构

```python
for item in list:	#语句末尾加‘:’
	...			   #语句块与for 空4个空格
	...			   #语句块与for 空4个空格
#例子 for.py	
names=["lucas", "liuhneg"]
for name in names:
    print("hello "+name)

#运行结果
hello lucas
hello liuhneg
```

#### while 结构

```python
while 条件:		#语句末尾加‘:’
	...			 #语句块与while 空4个空格
	...			 #语句块与while 空4个空格
    
#例子 while.py
i = 0
total = 0
while i < 10:
    total += i
    i += 1
print(total)

#运行结果
45
```

### 四、函数

#### 函数定义

```python
def 函数名: #语句末尾加‘:’
	...    #语句块与def 空4个空格
	...

#例子 func.py    
def add(x, y):
    return x+y			#通过return 返回结果

print(add(1, 2))

#运行结果
3
```

#### 函数参数

默认参数： 函数定义时给默认值，调用时如果没有传就用定义时的默认值

```python
#例子  func.py
def echo(name="liuheng"):
	print(name)

echo()			# 使用默认参数，等价于echo("liuheng")
echo("lucas")

#运行结果
liuheng
lucas
```

可变参数： 调用时可以传任意个数的参数（实际接收的是一个tuple）

```python
#例子  func.py
def calc(*nums):		#与普通参数不同的是多个*
    print(type(nums))	 #nums 实际是个tuple
    s = 0
    for num in nums:
        s = s+num
    return s


print(calc(1, 2, 3))

#运行结果
<class 'tuple'>
6
```

字典参数： 调用时传多个k-v 数据对

```python
#例子  func.py
def person(**kw):			#与普通参数不同的是多2个*
    print(type(kw))			#kw 变量实际是个dict
    for k, v in kw.items():
        print(k+":" + str(v))


person(name="liuheng", age=18)

#运行结果
<class 'dict'>
name:liuheng
age:18

# 限制key, 通过*与其他参数分割，*后指定key 
def person2(*, city, job):
    print(city + ":" + job)


# 调用是字典参数的key 只能是city,job
person2(city="jx", job="rd")
# TypeError: person2() got an unexpected keyword argument 'job2'
person2(city="jx", job2="rd")   
```

#### 装饰器

```python
# 函数作为参数
def hello(callback):
    callback("1122")

#匿名参数 lambda 参数: 处理逻辑
#通过lambda 定义一个匿名函数作为参数传给hello
hello(lambda x: print(x))   

# ------装饰器------------
# 定义装饰器函数
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)

    return wrapper


# 通过@ 语法使用log装饰器包装echo函数
# @log 相当于做了 echo = log(echo)
@log
def echo(name):
    print(name)


echo("111")

#运行结果
1122
call echo():
111
```

#### 生成器 &&  协程

```python
# ----------  生成器------------
def gen():
    n = 0
    while True:
        print("gen before yield:" + str(n))
        yield n  #生成器通过 yield 返回数据
        print("gen after yield:" + str(n))
        n = n + 1


print(type(gen()))
g = gen()  # 函数gen()没有实际执行，只是返回一个生成器
print("before next")
m = g.__next__()  # 调用生成器的next 方法将触发gen() 函数执行，直到yield指令停止
print("after next")
print(m)
print("before next2")
m = g.__next__()  # 从上次停止的地方继续开始执行，直到yield指令
print("after next2")
print(m)

#运行结果
<class 'generator'>
before next
gen before yield:0
after next
0
before next2
gen after yield:0
gen before yield:1
after next2
1



# 基于生成器实现协程， 生产者-消费者
def consumer():
    print("consumer start....")
    r = None
    while True:
        n = yield r
        if not n:
            return
        print("consumer.........." + str(n))
        r = n*n


def produce(c):
    c.send(None)
    n = 0
    while n < 5:
        n = n + 1
        print("produce..........." + str(n))
        r = c.send(n)	#send 功能等价于__next__, 不同的是参数可以传递给上一个yield的返回值
        print(r)


produce(consumer())

#运行结果
consumer start....
produce...........1
consumer..........1
1
produce...........2
consumer..........2
4
produce...........3
consumer..........3
9
produce...........4
consumer..........4
16
produce...........5
consumer..........5
25
```

### 五、IO

#### 输入、输出

```python
#例子 input.py
print("请输入：")
age = input()
print(type(age))
print(age)

#运行结果
请输入：
12
<class 'str'>
12
```

#### 文件读写

```Python
f = open("io.py", 'r')
for line in f.readlines():
    print(line)
f.close()

# 加异常处理 
f = None
try:
    f = open("io.py", 'r')
    for line in f.readlines():
        print(line)
    f.close()
except Exception as e:
    print(e)
finally:
    if f:
        f.close()

# 语法糖 with 自动关闭文件
with open("io.py", 'r') as f:
  for line in f.readlines():  
      print(line)
```

#### 异步IO

```python
import asyncio
from datetime import datetime


async def hello():
    # print(datetime.now())
    await asyncio.sleep(5)
    # print(datetime.now())


loop = asyncio.get_event_loop()
print(datetime.now())
# 并行执行2个task(hello())
loop.run_until_complete(asyncio.wait([hello(), hello()]))
loop.close()
print(datetime.now())
```

### 六、模块

```Python
import os	 # 通过import 导入模块

print(os.name)
# print(os.uname())
print(os.environ)
```

### 七、面向对象

```python
# 定义Animal 类
class Animal(object):     #class 类名(基类)：
    def echo(self):
        print("Animal")

#  定义Dog 类继承自Animal
class Dog(Animal):
    def __init__(self, name):	# 构造函数
        self.__name = name

    def echo(self):
        print("Dog-" + self.__name)

#  定义Cat 类继承自Animal
class Cat(Animal):
    def __init__(self, name):
        self.__name = name

    def echo(self):
        print("Cat-" + self.__name)


animal = Dog("wongwong")   #创建对象 
animal.echo()

animal = Cat("miaomiao")
animal.echo()

#  @property 定义属性, 实现setter、getter
class Person(object):
    def __init__(self, name):
        self.__name = name

    @property
    def name(self):
        return self.__name

    @name.setter
    def name(self, value):
        self.__name = value


p = Person("liuheng")
print(p.name)          # 像访问成员变量一样访问属性，等价于调用 p.name()
p.name = "lucas"
print(p.name)
```

### 八、错误处理

```python
# try ... except ... finally 语句捕获异常
def err(a):
    try:
        10 / a
    except Exception as e:
        print("except")
        print(e)
    finally:
        print("finally")


err(0)

f = None
try:
    f = open("io0000.py", 'r')
    for line in f.readlines():
        print(line)
    f.close()
except Exception as e:
    print(e)
finally:
    if f:
        f.close()
```

