---
abbrlink: ''
categories: []
cover: https://cdn.hllqk.cn/2024/12/104ec47fa39c342fbf18e22e8226c152.jpg
date: '2024-12-19T10:41:43.023108+08:00'
tags: []
title: 算法学习过程——秦九韶算法
updated: '2024-12-19T10:42:36.518+08:00'
---
#### 引言

秦九韶（1208年-1261年），字道古，中国南宋数学家，其传世佳作 《数书九章》 是中国宋元数学的巅峰之作， 代表了世界中世纪数学发展的最高水平，是继 《九章数学》之后又一部具有创造性成就的数学经典。

以上是秦九韶的[个人简介](https://so.csdn.net/so/search?q=%E4%B8%AA%E4%BA%BA%E7%AE%80%E4%BB%8B&spm=1001.2101.3001.7020)。emmmm.......  ， 只能说也是大佬中的大佬，在数学界的创作延续八百年形象至今，秦九韶[算法](https://edu.csdn.net/course/detail/40020?utm_source=glcblog&spm=1001.2101.3001.7020)在求[多项式](https://so.csdn.net/so/search?q=%E5%A4%9A%E9%A1%B9%E5%BC%8F&spm=1001.2101.3001.7020)的值方面仍然是最强最优秀的，在大数取模也用到了秦九韶思想。

##### 介绍

一般地朴素的讲，一元n次多项式的求值需要经过(n+1)\*n/2次乘法和n次加法

而[秦九韶算法](https://so.csdn.net/so/search?q=%E7%A7%A6%E4%B9%9D%E9%9F%B6%E7%AE%97%E6%B3%95&spm=1001.2101.3001.7020)只需要n次乘法和n次加法。

其核心思想在于将一元n次多项式转换为n个一次式

##### 意义

其最大的意义在于将求n次多项式的值转化为求n个一次多项式的值。

在人工计算时，利用秦九韶算法和其中的系数表可以大幅简化运算；

对于计算机程序算法而言，加法比乘法的计算效率要高很多，因此该算法仍有极大的意义，用于减少CPU运算时间。

#### 抛出问题

![f\left ( x \right ) = a_{3}x^{3}+ a_{2}x^{2}+ a_{1}x^{1}+ a_{0}x^{0}]()

**如果是朴素的算法**：![x\times x\times x \times a_{3}](https://latex.csdn.net/eq?x%5Ctimes%20x%5Ctimes%20x%20%5Ctimes%20a_%7B3%7D) 需要 3 次乘法 ， ![x\times x \times a_{2}](https://latex.csdn.net/eq?x%5Ctimes%20x%20%5Ctimes%20a_%7B2%7D)  需要 2 次乘法，

![x \times a_{1}](https://latex.csdn.net/eq?x%20%5Ctimes%20a_%7B1%7D) 需要 1 次乘法 ，共需要 6 次乘法，3 次加法

但若使用**秦九韶算法**进行简化：

![f\left ( x \right ) = a_{3}x^{3}+ a_{2}x^{2}+ a_{1}x+ a_{0}x^{0}]()

![=x\times \left ( a_{3}x^{2} + a_{2} x + a_{1} \right ) + a_{0}]()

![=x\times \left ( x \left ( a_{3}x + a_{2} \right ) + a_{1} \right ) + a_{0}]()

共需要 3 次乘法 3 次加法

#### ***问题延申 ***

#### ![f\left ( x \right ) = a_ {n}x^ {n}+ a_ {n-1}x^ {n-1}+ a_ {n-2}x^ {n-2}+ ... a_ {1}x + a_ {0}]()

**使用朴素算法**：后一项比前一项的指数小 1 ，少算 1 次乘法，成等差数列，

故共进行的乘法数为 ![\frac{n\left ( n+1 \right )}{2}](),加法为 ![n](https://latex.csdn.net/eq?n)，复杂度为![O\left ( n^{2} \right )](https://latex.csdn.net/eq?O%5Cleft%20%28%20n%5E%7B2%7D%20%5Cright%20%29)

**使用秦九韶算法**：将n次多项式的值转化为求n个一次多项式，

只进行 ![n](https://latex.csdn.net/eq?n) 次乘法加法，复杂度为![O\left ( n \right )](https://latex.csdn.net/eq?O%5Cleft%20%28%20n%20%5Cright%20%29)

#### 应用

##### 一、求多项式的值

**核心思想：**将n次多项式的值转化为求n个一次多项式。

**实现方法：**确定 ![n](https://latex.csdn.net/eq?n) 的大小，将系数用大小为 ![n](https://latex.csdn.net/eq?n) 的[数组](https://edu.csdn.net/course/detail/40020?utm_source=glcblog&spm=1001.2101.3001.7020)存储 ![0\sim n](https://latex.csdn.net/eq?0%5Csim%20n) 对应![a_{0}\sim a_{n}](https://latex.csdn.net/eq?a_%7B0%7D%5Csim%20a_%7Bn%7D)。

设结果为 ![res](https://latex.csdn.net/eq?res) 观察上述步骤发现，优先 ![res = a_{n} \times x](https://latex.csdn.net/eq?res%20%3D%20a_%7Bn%7D%20%5Ctimes%20x) ，后 ![res + a_{n-1}]()

然后反复执行，可以用 ![for](https://latex.csdn.net/eq?for) 循环实现

**代码实现：**

```cpp
#define int long long
int qFun(int factors[],int n, int x){//分别表示系数数组，最大项的系数，未知数x赋的值
    int res = factors[n];
    for(int i = n;i >=1 ;-- i)
    {
        res = res * x;
        res = res + factors[i-1];
    }
    return res;
}
```

##### 二、大整数取模

**提问：**如何实现![10^{10^{5}}](https://latex.csdn.net/eq?10%5E%7B10%5E%7B5%7D%7D) 对![101](https://latex.csdn.net/eq?101) 进行取模运算？（**提示：![int](https://latex.csdn.net/eq?int)** 类型能存 9 位数，![long long](https://latex.csdn.net/eq?long%20long) 能存18位数，![10^{10^{5}}](https://latex.csdn.net/eq?10%5E%7B10%5E%7B5%7D%7D) 共有 ![10^{5}](https://latex.csdn.net/eq?10%5E%7B5%7D) 位）

**核心思想：**秦九韶算法思想：从最高位逐步展开，步步取模。

**实现方法：**将大整数用字符串存储起来，再用一个![int](https://latex.csdn.net/eq?int)初始化为![s[0]](https://latex.csdn.net/eq?s%5B0%5D) ,先取模![p](https://latex.csdn.net/eq?p) 再乘![10](https://latex.csdn.net/eq?10)，循环以往操作，可以迭代实现。

**代码实现：**

```cpp
#define int long long
int qmod(string s,int p)
{
    int ans = 0;
    for(auto &i : s)
    {
        ans = (ans * 10 + i - '0') % p;
    }
    return ans;
}
```

#### 总结

将多项式转化为多个一项式，是计算多项式的值最高效的算法。

自用复习以及分享给大家，有任何问题可以留言或私信。
