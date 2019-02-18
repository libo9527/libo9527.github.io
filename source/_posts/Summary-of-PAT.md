---
title: Summary of PAT
description: PAT总结
comments: false
top: false
date: 2019-02-15 11:45:26
categories:
 - PAT
tags:
 - PAT
---
<img src="https://i.loli.net/2019/01/11/5c3813443dce6.jpg" width="100%"/>

<!-- more -->

## PAT总结

### 最佳实践

- 输入使用`scanf`，输出使用`printf`
- 题目要求 10^9 以内或者说 32 位整数，使用 int
- 题目要求 10^10 以内或者说 64 位整数，使用 double
- 浮点型数据使用 double 存储，不要用 float
- 小写字母的 ASCLL 码比大写字母的 ASCLL 码值 32
- 常量使用 const 方式定义，不要用**宏定义**
- 无穷大的数 INF 设置为 `(1 << 31) - 1`
- 数组大小大于 10^6，则将其定义在主函数外面
- 只用 memset 函数给数组赋 0 或 -1，使用 fill 函数赋其他值

### PAT OJ 坑

- PTA OJ 使用 `get()` 函数，C++ 会编译不通过，C 编译可以通过，但注意将头文件修改成 C 语言格式。

  解决方法：

   1. 使用 `scanf("%s", a)` 替代

      sacanf() 无法接受带有空格的字符串。

   2. 使用 `fgets(char* a,int maxlens,stdin)` 替代

      fgets 函数在**未读取到我们要求的大小时**（例如 maxlens=10，但只输入了3个字符），遇到换行符，那么读取到的数据就是： 已经读到的数据（遇到换行符前的数据）+ 换行符 + NULL，这会导致 strlen() 函数求得的长度比实际值多 1。

### C / C++ 语法

#### 数据类型

**四种基本数据类型**

| 类型                      | 大致范围               |
| ------------------------- | ---------------------- |
| int（long int）           | -2 x 10^9 ~ 2 x 10^9   |
| long long（long long int) | -9 x 10^18 ~ 9 x 10^18 |
| float                     | 实际精度 6 ~ 7 位      |
| double                    | 实际精度 15 ~ 16 位    |
| char                      | -128 ~ 127             |
| bool                      | 0(false) or 1(true)    |

long long 型赋大于 2^31 - 1 的初值时，需要在初值后面加上 LL

#### 格式符

**scanf 格式符**

| 类型                | 格式符 |
| ------------------- | ------ |
| int                 | %d     |
| long long           | %lld   |
| folat               | %f     |
| double              | %lf    |
| char                | %c     |
| 字符串（char 数组） | %s     |

除了 `%s` 外，scanf 对其他格式符的输入是以空白符（空格、换行）为结束判断标志。

**printf 格式符**

| 类型                | 格式符         |
| ------------------- | -------------- |
| int                 | %d             |
| long long           | %lld           |
| folat               | %f             |
| double              | %f（不是 %lf） |
| char                | %c             |
| 字符串（char 数组） | %s             |

| 格式符 | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| %md    | 使不足 m 位的 int 型变量以 m 位进行右对齐输出，高位用**空格**补齐 |
| %0md   | 类似`%md`，不过高位用 0 补齐                                 |
| %.mf   | 浮点数保留 m 位小数，采用**四舍六入五成双**规则              |

***想要输出 `%`,`\`需要在前面再加上一个`%`,`\`***

#### 函数

**常用 math 函数**

使用时需要添加 `<cmath` 头文件。

1. 绝对值：`fabs(double x)`

2. 取整：`floor(double x)`，`ceil(double x)`

3. 指数：`sqrt(double x)`

4. 对数：`log(double x)`

换底公式：`log(a, b) = log(e, b) / log(e, a)`

5. 三角：`sin(double x), cos(double x), tan(double x)`

6. 反三角：`asin(double x), acos(double x), atan(double x)`

7. 四舍五入：`round(double x)`

**memset**

使用时需要添加 `<cstring>` 头文件

对数组每个元素赋相同的值。

**常见 string 函数**

需要添加 `<cstring>` 头文件

1. 取长：`strlen()`

   strlen() 函数返回的类型是一个无符号整数类型，这就带来了一些需要***注意的问题***。

   比如
   1  `if( strlen(str1) >= strlen(str2) )......`
   2  `if( strlen(str1) - strlen(str2) >= 0)......`

   第一个 if 能够正常工作，但是第二个就不能了，因为 strlen() 的返回值是无符号整型值，所以 >=  左边的整个式子的结果就是一个无符号整型值。也就是说第二个 if 的结果永远都为真（无符号数不可能大于0）。

2. 比较：`strcmp()`

3. 复制：`strcpy()`

4. 拼接：`strcat()`

数组可以作为函数的参数，但**不能作为返回值类型**

#### 数组

数组大小必须是整数常量，不可以是变量

整型数组部分初始化后其余默认位 0，但如果都没有初始化，则每个元素都是一个随机数。

字符数组可以直接赋值字符串类初始化，但**仅限于初始化**，其他位置不允许。

#### 指针

- 指针就是变量的地址
- 指针是一个 unsigned 类型的整数
- `*` 一般放在变量前，但**星号是类型的一部分**

#### 引用

- 引用不产生副本，而是给原变量**起别名**
- 引用不是取地址。`&` 只有在函数的参数列表中出现才叫**引用**，其余地方叫**取址符**

#### 结构体

- 访问结构体内的元素有两种方法：`.` 和 `->`

#### 浮点数的比较

精度 eps 一般取 10^-8

`const double eps = 1e-8`

1. 等于

`#define Equ(a, b) ((fabs((a) - (b))) < (eps))`

2. 大于

`#define More(a, b) (((a) - (b)) > (eps))`

3. 小于

`#define Less(a, b) (((a) - (b) < (eps)))`

4. 大于等于

`#define MoreEqu(a, b) (((a) - (b) > (-eps)))`

5. 小于等于

`#define LessEqu(a, b)(((a) - (b) < (eps)))`

#### 复杂度

**时间复杂度**

O(1) < O(logn) < O(n) < O(n^2)

**空间复杂度**

一般都够用，只要不开好几盒 10^7 以上的数组即可。

### 评测结果

#### AC（Accepted 答案正确）

#### CE（Compile Eroor 编译错误）

#### WA（Wrong Answer 答案错误）

#### TLE（Time Limit Exceeded 运行超时）

- 时间复杂度太高
- 死循环

#### RE（Runtime Error 运行错误）

- 段错误
- 非法访问内存
- 数组越界
- 指针乱指
- 浮点错误
- 除数为 0
- 模数为 0
- 递归爆栈
- 递归层数太深

#### MLE（Memory Limit Exceeded 内存超限）

- 数组太大

#### PE（Presentation Error 格式错误）

- 空格
- 换行

#### OLE（Output Limit Exceeded 输出超限）

- 调试信息
- 死循环输出