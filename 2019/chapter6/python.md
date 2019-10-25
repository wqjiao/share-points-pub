# Python 学习笔记

## Python 安装地址

[Windows 不同版本号及操作系统](https://www.python.org/ftp/python/3.7.4/)

## Visual C++ vc_redist.x64.exe 安装

[下载路径](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

## cmder 不同环境命令行支持

方便执行代码

## API

### print() 打印输出函数

`print(*objects, sep=' ', end='\n', file=sys.stdout)`

* objects -- 复数，表示可以一次输出多个对象。输出多个对象时，需要用 , 分隔。
* sep -- 用来间隔多个对象，默认值是一个空格。
* end -- 用来设定以什么结尾。默认值是换行符 \n，我们可以换成其他字符串。
* file -- 要写入的文件对象。
* `print()` 打印方法之占位符

整数向下取整 %d
数值浮点数(默认小数点后6位有效数值) %f
    %.1f -- 保留一位小数
    %.nf -- 保留n位小数

```Python
# 整数
a = 2
b = 4
print('%d / %d = %d' % (b, a, (b / a))) # 4 / 2 = 2

# 浮点数
f = float(input('请输入华氏温度: '))
c = (f - 32) / 1.8
print('%.2f华氏度 = %.2f摄氏度' % (f, c))
```

### 注释

* 单行注释以 # 开头
* 多行注释用三个单引号 ''' 或者三个双引号 """ 将注释括起来

```Python
# 这是一个单行注释

'''
这是一个多行注释
'''

"""
这也是一个多行注释
"""

print('Python 注释写法')
```

### 数据类型

六个标准的数据类型：
```
Number（数字）
String（字符串）
List（列表）
Tuple（元组）
Set（集合）
Dictionary（字典）
```

Python3 的六个标准数据类型中：
```
不可变数据（3 个）：Number（数字）、String（字符串）、Tuple（元组）；
可变数据（3 个）：List（列表）、Dictionary（字典）、Set（集合）
```

* `Number` 数字类型包括：int、float、bool、complex（复数）

* `Set` 可以进行集合运算

```Python
a = set('abracadabra')
b = set('alacazam')
 
print(a) # {'d', 'a', 'r', 'c', 'b'}
print(b) # {'l', 'z', 'a', 'c', 'm'}
print(a - b) # a 和 b 的差集 {'r', 'd', 'b'}
print(b - a) # b 和 a 的差集 {'z', 'm', 'l'}
print(a | b) # a 和 b 的并集 {'m', 'l', 'z', 'd', 'a', 'r', 'c', 'b'}
print(a & b) # a 和 b 的交集 {'a', 'c'}
print(a ^ b) # a 和 b 中不同时存在的元素 {'r', 'b', 'l', 'z', 'd', 'm'}
```

* `Dictionary` 字典

```Python
dict = {}
dict['one'] = "1 - 菜鸟教程"
dict[2]     = "2 - 菜鸟工具"
 
tinydict = {'name': 'runoob','code':1, 'site': 'www.runoob.com'}
 
print (dict['one'])       # 输出键为 'one' 的值
print (dict[2])           # 输出键为 2 的值
print (tinydict)          # 输出完整的字典
print (tinydict.keys())   # 输出所有键
print (tinydict.values()) # 输出所有值
```

* 使用 `type()` 检查变量的类型

```Python
a = 100
b = 12.345
c = 1 + 5j
d = 'hello, world'
e = True
print(type(a)) # <class 'int'>
print(type(b)) # <class 'float'>
print(type(c)) # <class 'complex'>
print(type(d)) # <class 'str'>
print(type(e)) # <class 'bool'>
```

* 数据类型转换

| 函数 | 描述 | 实例 |
| :--- | :--- | :-- |
| int(x [,base]) | 将 x(数值或字符串) 转换成整数，可以指定进制 | int('18') = 18 |
| float(x) | 将 x(数值或字符串) 转换到一个浮点数 | float(12) = 12.0 |
| complex(real [,imag]) | 创建一个复数 | complex(2) = 2+0j |
| str(x) | 将对象 x 转换为字符串 | str(18) = '18' |
| repr(x) | 将对象 x 转换为表达式字符串 | repr({'name': 'name'}) = "{'name': 'name'}" |
| eval(str) | 用来计算在字符串中的有效Python表达式,并返回一个对象 | eval('2 ** 3') = 8 |
| tuple(s) | 将序列 s 转换为一个元组 | tuple([2, 3]) = (2, 3) |
| list(s) | 将序列 s 转换为一个列表 | list((2, 3)) = [2, 3] |
| set(s) | 转换为可变集合 | |
| dict(d) | 创建一个字典。d 必须是一个 (key, value)元组序列。 | |
| frozenset(s) | 转换为不可变集合 | |
| chr(x) | 将一个整数转换为一个字符 | chr(65) = 'A' |
| ord(x) | 将一个字符转换为它的整数值 | ord('A') = 65 |
| hex(x) | 将一个整数转换为一个十六进制字符串 | hex(8) = '0x8' |
| oct(x) | 将一个整数转换为一个八进制字符串 | oct(8) = 0o10 |

### 运算符

* 算数运算符

| 运算符 | 描述 | 实例 |
| :---: | :--- | :--: |
| + | 加  - 两个对象相加 | a + b |
| - | 减  - 得到负数或是一个数减去另一个数 | a - b |
| * | 乘  - 两个数相乘或是返回一个被重复若干次的字符串 | a * b |
| / | 除  - x除以y | a / b |
| % | 取模  - 返回除法的余数 | a % b |
| ** | 幂  - 返回x的y次幂 | a ** b |
| // | 取整除  - 返回商的整数部分（向下取整） | a // b |

* 比较运算符

| 运算符 | 描述 | 实例 |
| :---: | :--- | :--- |
| ==	 | 等于 - 比较对象是否相等 | (a == b) 返回 False. |
| != | 不等于 - 比较两个对象是否不相等 | (a != b) 返回 true. |
| <>	 | 不等于 - 比较两个对象是否不相等 |	(a <> b) 返回 true。这个运算符类似 != 。|
| > | 大于 - 返回x是否大于y |	(a > b) 返回 False。|
| < | 小于 - 返回x是否小于y。所有比较运算符返回1表示真，返回0表示假。这分别与特殊的变量True和False等价。|	(a < b) 返回 true。|
| >=	 | 大于等于	- 返回x是否大于等于y。|	(a >= b) 返回 False。|
| <=	 | 小于等于 -	返回x是否小于等于y。|	(a <= b) 返回 true。|

* 赋值运算符

| 运算符 | 描述 | 实例 |
| :---: | :--- | :--- |
| = | 简单的赋值运算符 | c = a + b 将 a + b 的运算结果赋值为 c |
| += | 加法赋值运算符 | c += a 等效于 c = c + a |
| -= | 减法赋值运算符 | c -= a 等效于 c = c - a |
| *= | 乘法赋值运算符 | c *= a 等效于 c = c * a |
| /= | 除法赋值运算符 | c /= a 等效于 c = c / a |
| %= | 取模赋值运算符 | c %= a 等效于 c = c % a |
| **= | 幂赋值运算符 | c **= a 等效于 c = c ** a |
| //= | 取整除赋值运算符 | c //= a 等效于 c = c // a |

* 位运算符

| 运算符 | 描述 | 实例 |
| :---: | :--- | :--- |
| & | 按位与运算符：参与运算的两个值,如果两个相应位都为1,则该位的结果为1,否则为0 | (a & b) 输出结果 12 ，二进制解释： 0000 1100 |
| | | 按位或运算符：只要对应的二个二进位有一个为1时，结果位就为1。 | (a | b) 输出结果 61 ，二进制解释： 0011 1101 |
| ^ | 按位异或运算符：当两对应的二进位相异时，结果为1 | (a ^ b) 输出结果 49 ，二进制解释： 0011 0001 |
| ~ | 按位取反运算符：对数据的每个二进制位取反,即把1变为0,把0变为1 。~x 类似于 -x-1 | (~a ) 输出结果 -61 ，二进制解释： 1100 0011，在一个有符号二进制数的补码形式。 |
| << | 左移动运算符：运算数的各二进位全部左移若干位，由 << 右边的数字指定了移动的位数，高位丢弃，低位补0。 | a << 2 输出结果 240 ，二进制解释： 1111 0000 |
| >> | 右移动运算符：把">>"左边的运算数的各二进位全部右移若干位，>> 右边的数字指定了移动的位数 | a >> 2 输出结果 15 ，二进制解释： 0000 1111 |

```Python
a = 60 # 0011 1100
b = 13 # 0000 1101

c = a&b # 12 = 0000 1100
c = a|b # 61 = 0011 1101
c = a^b # 49 = 0011 0001
c = ~a  # -61 = 1100 0011
c = a << 2 # 240 = 1111 0000
c = a >> 2 # 15 = 0000 1111
```

* 逻辑运算符

a = 10, b = 20
| 运算符 | 描述 | 实例 |
| :---: | :--- | :--- |
| and | 布尔"与" - x and y 相当于js中的 && 与 | (a and b) 返回 20 |
| or | 布尔"或" - x or y 相当于js中的 || 或 | (a or b) 返回 10 |
| not | 布尔"非" - not x 相当于js中的 ! 非 | not(a and b) 返回 False |

* 成员运算符 -- 判断某成员是否在指定序列中，包括字符串、列表或元组

| 运算符 | 描述 | 实例 |
| :---: | :--- | :--- |
| in | 如果在指定的序列中找到值返回 True，否则返回 False | x 在 y 序列中 , 如果 x 在 y 序列中返回 True |
| not in | 如果在指定的序列中没有找到值返回 True，否则返回 False | x 不在 y 序列中 , 如果 x 不在 y 序列中返回 True |

```Python
a = 10
b = [1, 20, 50, 10]

if (a in b):
    print('a 在 b 中')
else:
    print('a 不在 b 中')
```

`Python` 的元组与列表类似，不同之处在于元组的元素不能修改。

```Python
# 1.元组使用小括号，列表使用方括号
tup1 = (50)
type(tup1)     # 不加逗号，类型为整型 <class 'int'>

tup1 = (50,)
type(tup1)     # 加上逗号，类型为元组 <class 'tuple'>

# 2.元组中的元素值是不允许修改的，但我们可以对元组进行连接组合
tup1 = (12, 34.56)
tup2 = ('abc', 'xyz')
 
# 以下修改元组元素操作是非法的。
# tup1[0] = 100
 
# 创建一个新的元组
tup3 = tup1 + tup2
print (tup3)

# 3.元组中的元素值是不允许删除的，但我们可以使用del语句来删除整个元组
tup = ('Google', 'Runoob', 1997, 2000)
 
print (tup)
del tup
print ("删除后的元组 tup : ")
print (tup)
```

* 身份运算符 -- 比较两个对象的存储单元

| 运算符 | 描述 | 实例 |
| :---: | :--- | :--- |
| is | is 是判断两个标识符是不是引用自一个对象 | x is y, 类似 id(x) == id(y) , 如果引用的是同一个对象则返回 True，否则返回 False |
| is not | is not 是判断两个标识符是不是引用自不同对象 | x is not y ， 类似 id(a) != id(b)。如果引用的不是同一个对象则返回结果 True，否则返回 False |

注： id() 函数用于获取对象内存地址。

* 运算符优先级

| 运算符 | 描述 |
| :---: | :--- |
| `**` | 指数 (最高优先级) |
| `~` `+` `-` | 按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@) |
| `*` `/` `%`` //` | 乘，除，取模和取整除 |
| `+` `-` | 加法减法 |
| `>>` `<<` | 右移，左移运算符 |
| `&` | 位 'AND' |
| `^` `\|` | 位运算符 |
| `<=` `<` `>` `>=` | 比较运算符 |
| `<>` `==` `!=` | 等于运算符 |
| `=` `%=` `/=` `//=` `-=` `+=` `*=` `**=` | 赋值运算符 |
| `is` `is not` | 身份运算符 |
| `in` `not in` | 成员运算符 |
| `not` `and` `or` | 逻辑运算符 |

注意：可以使用圆括号 `()` 来确保运算的执行顺序

### 分支结构

* 分支结构 `if ... elif ... else`
不使用花括号构造代码，而是缩进的方式设置代码的层次结构

```Python
x = float(input('x = '))
if x > 1:
    y = 3 * x - 5
elif x >= -1:
    y = x + 2
else:
    y = 5 * x + 3
print('f(%.2f) = %.2f' % (x, y))
```

### 循环结构

* `range(m, n, b)` 方法

    - range(n + 1) 产生 `0 - n` 整数序列
    - range(m, n + 1) 产生 `m - n` 整数序列
    - range(m, n + 1, b) 产生 `m - n` 整数序列，步长为 b(数值序列的增量，默认是1)。

* `for-in`

```Python
sum = 0
for x in range(1, 101):
    if x % 2 == 0:
        sum += x
print(sum)
```

嵌套的循环结构 -- 九九乘法表
```Python
for i in range(1, 10):
    for j in range(1, i + 1):
        print('%d*%d=%d' % (i, j, i * j), end='\t')
        # print('{}x{}={}\t'.format(i, j, i*j), end='') # format() 格式化函数
    print()
```

* `while` -- 循环次数未知

```Python
import random

answer = random.randint(1, 100)
counter = 0
while True:
    counter += 1
    number = int(input('请输入: '))
    if number < answer:
        print('大一点')
    elif number > answer:
        print('小一点')
    else:
        print('恭喜你猜对了!')
        break
print('你总共猜了%d次' % counter)
if counter > 7:
    print('你的智商余额明显不足')
```

### 定义函数

```Python
def main():
    names = ['关羽', '张飞', '赵云', '马超', '黄忠']
    subjs = ['语文', '数学', '英语']
    scores = [[0] * 3] * 5
    for row, name in enumerate(names):
        print('请输入%s的成绩' % name)
        for col, subj in enumerate(subjs):
            scores[row][col] = float(input(subj + ': '))
    print(scores)

if __name__ == '__main__':
    main()
```

### 定义类 -- 定义、创建和使用对象

```Python
# 类中定义方法接收到的第一个参数指向该类的 self
class Student(object):

    # __init__是一个特殊方法用于在创建对象时进行初始化操作
    # 通过这个方法我们可以为学生对象绑定name和age两个属性
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def study(self, course_name):
        print('%s正在学习%s.' % (self.name, course_name))

    # PEP 8要求标识符的名字用全小写多个单词用下划线连接
    # 但是部分程序员和公司更倾向于使用驼峰命名法(驼峰标识)
    def watch_movie(self):
        if self.age < 18:
            print('%s只能观看《熊出没》.' % self.name)
        else:
            print('%s正在观看岛国爱情大电影.' % self.name)

def main():
    # 创建学生对象并指定姓名和年龄
    stu1 = Student('骆昊', 38)
    # 给对象发study消息
    stu1.study('Python程序设计')

if __name__ == '__main__':
    main()
```

### 装饰器

* `@property` -- 访问器 - getter方法
* `@age.setter` -- 修改器 - setter方法
* `@staticmethod` 静态方法
* `@classmethod` 类方法

### 引入模块

```Python
import random
from math import sqrt
```

### 清屏

```Python
import os

os.system('clear')
```

### 用户手动输入值

```Python
input('请输入该参数：')
```

## 打开/读取/关闭 文件

| 操作模式 | 具体含义                         |
| -------- | -------------------------------- |
| `'r'`    | 读取 （默认）                    |
| `'w'`    | 写入（会先截断之前的内容）       |
| `'x'`    | 写入，如果文件已经存在会产生异常 |
| `'a'`    | 追加，将内容写入到已有文件的末尾 |
| `'b'`    | 二进制模式                       |
| `'t'`    | 文本模式（默认）                 |
| `'+'`    | 更新（既可以读又可以写）         |

```python
def main():
    # 打开文件 encoding -> 文件编码方式
    f = open('致橡树.txt', 'r', encoding='utf-8')
    print(f.read())
    f.close()


if __name__ == '__main__':
    main()
```

## pygame

[安装参考网站](https://blog.csdn.net/jiachang98/article/details/81867556)
[pygame 下载网站](https://www.lfd.uci.edu/~gohlke/pythonlibs/#pygame)

* 报错提示
    - 1.`pylint Module 'pygame' has no 'init' member`
    <img src="./assets/pyname-01.png" />
    
    在 `vscode` 编辑器的 `settings.json` 文件中添加 `"python.linting.pylintArgs": [ "----extension-pkg-whitelist=1xml" ],` 配置
