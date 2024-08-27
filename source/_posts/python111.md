---
abbrlink: ''
categories: []
cover: https://i.hllqk.cn/file/d90fb2d79afa81438f825.png
date: '2024-08-27T18:35:12.815471+08:00'
tags: []
title: python调用另一个.py文件中的类和函数或直接运行另一个.py文件
updated: '2024-08-27T18:38:11.714+08:00'
---
参考文章 [python调用另一个.py文件中的类和函数](https://www.cnblogs.com/AmyHu/p/10654500.html)

## 同一文件夹下的调用

### 1.调用函数

A.py文件如下：

```bash
def add(x,y):
    print('和为：%d'%(x+y))
```

在B.py文件中调用A.py的add函数如下：

```bash
import A
A.add(1,2)
```

或

```bash
from A import add
add(1,2)
```

### 2.调用类

A.py文件如下:

```bash
class A:
    def __init__(self,xx,yy):
        self.x=xx
        self.y=yy
    def add(self):
        print("x和y的和为：%d"%(self.x+self.y))
```

在B.py文件中调用A.py的add函数如下：

```bash
from A import A
a=A(2,3)
a.add()
```

或

```bash
import A
a=A.A(2,3)
a.add()
```

## 在不同文件夹下调用

A.py文件的文件路径为：C:\\AmyPython\\Test1

B.py中调用A.py文件：

```bash
import sys
sys.path.append(r'C:\AmyPython\Test1')
#python import模块时，是在sys.path里按顺序查找的。
#sys.path是一个列表，里面以字符串的形式存储了许多路径。
#使用A.py文件中的函数需要先将他的文件路径放到sys.path中
import A
a=A.A(2,3)
a.add()
```

还有一种方法是创建 `__init__.py` 文件，参考[文章](https://blog.csdn.net/qq_31829611/article/details/89093693)【更为推荐，感觉比添加路径的方式更好】
如果这文件不在一个路径下面，import就比较麻烦了，需要在被import的文件路径下面新建一个\_\_init\_\_.py文件，光创建就行，不需要往里面添加内容。
比如被import的文件路径是…A/b.py下面，那么只要在文件夹A下面分别新建一个\_\_init\_\_.py文件，然后按照下面的语句引用即可：

```python
import A.b
```

### 进一步说明

现在文件结构是这样的

```
root
|
|--A文件夹
|    |--main.py
|
|--B文件夹
     |--b.py
```

现在主程序在A文件夹里，想调用B文件夹下的b.py文件，该怎么办呢？
本来以为只需要在a.py中写上：

```python
sys.path.append('../B')
import b as bb
```

但实际会报错说 No module named ‘b’
ps. 这种直接添加相对路径的方式在Jupyter中就没有报错，不知道什么原因。
其实这个时候用 sys.path 来查看路径的话，会发现确实添加进来了，但添加进来的是一个相对路径，所以添加路径的时候相对路径的参考地址不是当前文件，而是[python](https://so.csdn.net/so/search?q=python&spm=1001.2101.3001.7020)根目录。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/2e2f7037a62b95d287b38bd291d24129.png)
关于这个问题，解决方法有3个：
**1、添加绝对路径**

```python
sys.path.append('/home/gxp/Documents/B')
```

**2、用拼接路径的方式添加绝对路径（本质和1相同）**

```python
sys.path.append(os.path.join(os.path.dirname(__file__),'../B'))
```

这里join是连接两个路径，\_\_file\_\_是代表当前文件，dirname返回文件夹的绝对路径。所以这里添加的路径本质上是`'/home/gxp/Documents/A/../B'`，也就是说，`sys.path.append('/home/gxp/Documents/B')`和`sys.path.append('/home/gxp/Documents/A/../B')`的效果是一样的。

**3、采用相对引用或其他方法**
这种做法存在不少问题，有些到最后也没整明白。
这种做法和上面说的创建 `__init__.py` 文件的方法似乎是一样的，但这里发现没添加这个文件似乎也正常运行了。
考虑如下文档结构，主程序为 b1.py 或者 main.py。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4f52295b5fffbb4804a7f95cd49eed8c.png)
如果是main.py 的话会好说很多，因为它在根目录下，直接 `import B.b`就可以。
但如果是b1.py的话，可以采用**相对导入**的方式，可参考文章 [Python的绝对引用 vs 相对引用全面讲解](https://blog.csdn.net/ning13481193737/article/details/107165586/)，但是自己在用的时候会报错，就像下面这种方式是不能正常运行的，会报错说`ImportError: attempted relative import with no known parent package`。
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/61a9a9b74c51b9614fe036730c636a7f.png)
这似乎是非常非常普遍的问题，但也似乎没有统一的解决方案。
可以参考Stackoverflow上的文章：

* [Relative imports for the billionth time](https://stackoverflow.com/questions/14132789/relative-imports-for-the-billionth-time)
* [VSCode 1.39.x & Python 3.7.x: “ImportError: attempted relative import with no known parent package” - when started without debugging (CTRL+F5))](https://stackoverflow.com/questions/58709973/vscode-1-39-x-python-3-7-x-importerror-attempted-relative-import-with-no-kn)

好像在Pycharm中间接引用就不会出问题，所以可能是VSCode的原因？
但不管怎么说间接引用其实不是一种很推荐的方式，就像全局变量一样，程序大了容易乱。
尤其是如果各个子文件夹中的模块还存在互相调用的话，那写起来真的就是乱七八糟了。
如果一定要解决的话，上面Stack Overflow第二篇文章中有一个解决方案。

```python
import sys
from pathlib import Path
sys.path[0] = str(Path(sys.path[0]).parent.parent)  
# 将工作目录改为项目根目录，这里每一个.parent就是向上翻一级，sys.path[0]就是当前目录

from B.B1 import b2 as b2
b2.b2()
```

这样一来，虽然主程序在一个子文件夹下，但工作目录却是根目录。所以可以像 main.py 那样通过 `B.B1`来解决。不过这种情况下如果各个子文件夹下的模块存在相互调用的话，也都需要在 import 时做好设置，也有些麻烦但也不是不可以。
所以整来整去，比较好的解决方案还是：
1、主程序不要放在子文件夹下，放在根目录下，然后用 `import B.B1.b2`的方式比较好。
2、如果主程序放在子文件夹下，一种方案是添加路径，这种方案的好处是可复用性好，但坏处是没有自动提示而且有不识别模块的警告；另一种方案就是把工作目录更改到根目录下，这种方案的好处是有自动提示写起来舒服，坏处是如果各个子文件夹内存在相互调用的话复用性会受影响。（自己还是更倾向于第二种方案，大多数人的程序其实也都有子文件夹内模块相互调用，其实问题不大）

## 运行另一个.py文件

参考文章 [https://blog.csdn.net/heart\_arsonists/article/details/106988694](https://blog.csdn.net/heart_arsonists/article/details/106988694)

### 同一目录下

```python
import os
os.system("python filename.py")
12'运行运行
```

### 不同目录下

如果按照上面的方法会报错

> error sh: /Users//Desktop/.py: Permission denied

正确方式为

```python
import os
os.system('{} {}'.format('python',loc))
# 或者是
os.system('python {}'.format(loc))
```

这里的loc为字符串，用相对路径即可。
但这样并不能把运行的文件得到的变量输出出来，如果想把结果输出出来并进一步运算，参考[文章](https://blog.csdn.net/HunGRy_FOOliSHhh/article/details/115305848)，所采用的方法是

```python
# 所采用的方法如下
# from 变量所在文件名 import 变量名

import os
# os.system('{} {}'.format('python','A/main.py'))
os.system('python {}'.format('A/main.py'))

from A.main import c  # 如果在同一目录下则直接 from main import c 即可
d = c+1
print('d=',d)
```
