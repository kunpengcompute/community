- [1 变量](#1-变量)
  - [1.1 禁止读取未经初始化的变量](#11-禁止读取未经初始化的变量)
  - [1.2 避免大量栈分配](#12-避免大量栈分配)
  - [1.3 指向资源句柄或描述符的变量，在资源释放后立即赋予新值](#13-指向资源句柄或描述符的变量在资源释放后立即赋予新值)
  - [1.4 禁止将局部变量的地址返回到其作用域以外](#14-禁止将局部变量的地址返回到其作用域以外)
  - [1.5 资源不再使用时应予以关闭或释放](#15-资源不再使用时应予以关闭或释放)
- [2 整数](#2-整数)
  - [2.1 确保有符号整数运算不溢出](#21-确保有符号整数运算不溢出)
  - [2.2 确保无符号整数运算不回绕](#22-确保无符号整数运算不回绕)
  - [2.3 确保除法和余数运算不会导致除零错误(被零除)](#23-确保除法和余数运算不会导致除零错误被零除)
  - [2.4 校验外部数据中整数值的合法性](#24-校验外部数据中整数值的合法性)
- [3 指针和数组](#3-指针和数组)
  - [3.1 外部数据作为数组索引时必须确保在数组大小范围内](#31-外部数据作为数组索引时必须确保在数组大小范围内)
  - [3.2 禁止通过对数组类型的函数参数变量进行sizeof来获取数组大小](#32-禁止通过对数组类型的函数参数变量进行sizeof来获取数组大小)
  - [3.3 禁止通过对指针变量进行sizeof操作来获取数组大小](#33-禁止通过对指针变量进行sizeof操作来获取数组大小)
- [4 字符串](#4-字符串)
  - [4.1 确保字符串存储有足够的空间容纳字符数据和null结束符](#41-确保字符串存储有足够的空间容纳字符数据和null结束符)
  - [4.2 对字符串进行存储操作，确保字符串有null结束符](#42-对字符串进行存储操作确保字符串有null结束符)
- [5 断言](#5-断言)
  - [5.1 禁止用断言检测程序在运行期间可能导致的错误，可能发生的错误要用错误处理代码来处理](#51-禁止用断言检测程序在运行期间可能导致的错误可能发生的错误要用错误处理代码来处理)
  - [5.2 禁止在断言内改变运行环境](#52-禁止在断言内改变运行环境)
- [6 函数设计](#6-函数设计)
  - [6.1 对所有外部数据进行合法性检查](#61-对所有外部数据进行合法性检查)
- [7 函数使用](#7-函数使用)
  - [7.1 调用格式化输入/输出函数时，禁止format参数受外部数据控制](#71-调用格式化输入输出函数时禁止format参数受外部数据控制)
  - [7.2 调用格式化输入/输出函数时，使用有效的格式字符串](#72-调用格式化输入输出函数时使用有效的格式字符串)
  - [7.3 禁止使用realloc()函数](#73-禁止使用realloc函数)
  - [7.4 禁止使用alloca()函数申请栈上内存](#74-禁止使用alloca函数申请栈上内存)
  - [7.5 禁止外部可控数据作为进程启动函数的参数](#75-禁止外部可控数据作为进程启动函数的参数)
  - [7.6 禁止外部可控数据作为dlopen等模块加载函数的参数](#76-禁止外部可控数据作为dlopen等模块加载函数的参数)
  - [7.7 禁止直接使用外部数据拼接SQL命令](#77-禁止直接使用外部数据拼接sql命令)
  - [7.8 在信号处理例程中调用非异步安全函数](#78-在信号处理例程中调用非异步安全函数)
  - [7.9 使用库函数时避免竞争条件](#79-使用库函数时避免竞争条件)
- [8 文件](#8-文件)
  - [8.1 创建文件时必须显式指定合适的文件访问权限](#81-创建文件时必须显式指定合适的文件访问权限)
  - [8.2 外部文件路径使用前必须进行规范化并校验](#82-外部文件路径使用前必须进行规范化并校验)
  - [8.3 不要在共享目录中创建临时文件](#83-不要在共享目录中创建临时文件)
- [9 内存](#9-内存)
  - [9.1 内存申请前，必须对申请内存大小进行合法性校验](#91-内存申请前必须对申请内存大小进行合法性校验)
  - [9.2 内存分配后必须判断是否成功](#92-内存分配后必须判断是否成功)
  - [9.3 外部输入作为内存操作相关函数的复制长度时，需要校验其合法性](#93-外部输入作为内存操作相关函数的复制长度时需要校验其合法性)
  - [9.4 内存中的敏感信息使用完毕后立即清0](#94-内存中的敏感信息使用完毕后立即清0)
  - [9.5 不要访问已释放的内存](#95-不要访问已释放的内存)
  - [9.6 严禁使用string类存储敏感信息【要求】](#96-严禁使用string类存储敏感信息要求)
- [10 其他](#10-其他)
  - [10.1 不要在信号处理函数中访问共享对象](#101-不要在信号处理函数中访问共享对象)
  - [10.2 禁用rand函数产生用于安全用途的伪随机数](#102-禁用rand函数产生用于安全用途的伪随机数)
  - [10.3 禁止代码中包含公网地址【要求】](#103-禁止代码中包含公网地址要求)

# 1 变量
## 1.1 禁止读取未经初始化的变量
**【描述】**
这里的变量，指的是局部动态变量，并且还包括内存堆上申请的内存块。
因为他们的初始值都是不可预料的，所以禁止未经有效初始化就直接读取其值。

```c
void Foo(int condVal)
{
    int data;
    Bar(data); // 不符合：未初始化就使用
    ...
}
```

如果有不同分支，要确保所有分支都得到初始化后才能使用：
```c
#define CUSTOMIZED_SIZE 100
void Foo(int condVal)
{
    int data;
    if (condVal > 0) {
        data = CUSTOMIZED_SIZE;
    }
    Bar(data); // 不符合：其他分支中(condVal <= 0)，data值未初始化
    ...
}
```

注意：如果编译器允许，变量应按需定义，避免初始化问题。

## 1.2 避免大量栈分配
**【描述】**
程序在运行期间，函数内的局部变量存储在栈中，而栈的大小是有限的，局部变量占用的栈空间过大时，可能导致出现栈溢出错误。
建议在定义函数局部变量时(特别是递归调用、循环体中定义变量)，需要充分考虑单个函数及全调用栈的开销，并对函数中的局部变量大小进行一定限制，避免因占用过多的栈空间导致程序运行失败。

例如，linux内核的默认构建配置文件中，限制函数帧的大小不超过1024字节，如果超出限制则出现编译告警。

**【反例】**
如下代码示例中的`buff[MAX_BUFF]` 数组占用空间过大，可能导致栈空间不够，程序发生 stack overflow 异常。

```c
#define MAX_BUFF 0x1000000

char buff[MAX_BUFF] = {0};
...
```
**【正例】**
如下代码示例中，通过动态分配内存的方式，避免栈空间占用过大的问题：

```c
#define MAX_BUFF 0x1000000
char *buf = (char *)malloc(MAX_BUFF);
if (buf == NULL) {
    ... // 错误处理
}
...
```

如上代码中须检查动态分配函数的返回值，如果分配内存大小的来源不可信，则需要注意校验其值的范围。

**【反例】**
递归调用太深也会造成栈分配过大，因此递归函数必须确保调用深度可控。
如下递归函数，没有控制其递归深度，调用深度随m的值增加而增加，所使用的栈大小也随之增加，可能导致栈空间不足：

```c
uint64_t Sum(uint32_t m)
{
    if (m == 0) {
        return 0;
    }
    return Sum(m - 1) + m;
}
```

**【正例】**
重构上面的递归函数，不使用递归算法，所使用的栈大小不会随m的变化而变化：

```c
uint64_t Sum(uint32_t m)
{
    uint64_t n = (uint64_t)m;
    return (n * (n + 1) / 2);
}
```

## 1.3 指向资源句柄或描述符的变量，在资源释放后立即赋予新值
**【描述】**
指向资源句柄或描述符的变量包括指针、文件描述符、socket描述符以及其它指向资源的变量。
以指针为例，当指针成功申请了一段内存之后，在这段内存释放以后，如果其指针未立即设置为NULL，也未分配一个新的对象，那这个指针就是一个悬空指针。
如果再对悬空指针操作，可能会发生重复释放或访问已释放内存的问题，造成安全漏洞。
消减该漏洞的有效方法是将释放后的指针立即设置为一个确定的新值，例如设置为NULL。对于全局性的资源句柄或描述符，在资源释放后，应该马上设置新值，以避免使用其已释放的无效值；对于只在单个函数内使用的资源句柄或描述符，应确保资源释放后其无效值不被再次使用。

**【反例】**
如下代码示例中，根据消息类型处理消息，处理完后释放掉body指向的内存，但是释放后未将指针设置为NULL。如果还有其他函数再次处理该消息结构体时，可能出现重复释放内存或访问已释放内存的问题。

```c
int Func(void)
{
    SomeStruct *msg = NULL;
    int ret = 0;

    ... // 分配msg，初始化msg->type，分配 msg->body 的内存空间

    if (msg->type == MESSAGE_A) {
        ...
        free(msg->body); // 不符合：释放内存后，未置空
    }
    ...
    if (someError) {
        ...
        goto ERROR_EXIT;
    }
    // 将msg存入全局队列，后续可能使用已释放的body成员
    InsertMsgToQueue(msg);
    return ret;
ERROR_EXIT:
    ...
    free(msg->body);  // 可能再次释放了body的内存
    free(msg);
    return ret;
}
```

**【正例】**
如下代码示例中，立即对释放后的指针设置为NULL，避免重复释放指针。

```c
int Func(void)
{
    SomeStruct *msg = NULL;
    int ret = 0;

    ... // 初始化msg->type，分配 msg->body 的内存空间

    if (msg->type == MESSAGE_A) {
        ...
        free(msg->body);
        msg->body = NULL;
    }
    ...
    if (someError) {
        ...
        goto ERROR_EXIT;
    }
    // 将msg存入全局队列，后续可能使用已释放的body成员
    InsertMsgToQueue(msg);
    return ret;
ERROR_EXIT:
    ...
    free(msg->body); // 马上离开作用域，不必赋值NULL
    free(msg);       // 马上离开作用域，不必赋值NULL
    return ret;
}
```

当free()函数的入参为NULL时，函数不执行任何操作。

**【反例】**
如下代码示例中文件描述符关闭后未赋新值。

```c
SOCKET s = INVALID_SOCKET;
int fd = -1;
...
closesocket(s);
...
close(fd);
...
```

**【正例】**
如下代码示例中，在资源释放后，对应的变量应该立即赋予新值。

```c
SOCKET s = INVALID_SOCKET;
int fd = -1;
...
closesocket(s);
s = INVALID_SOCKET;
...
close(fd);
fd = -1;
...
```

**【反例】**
如下代码示例中，FreeSomeStruct函数中释放p后设置NULL的操作是无效的，导致DoSomeThing函数访问已释放内存。

```c
void FreeSomeStruct(SomeStruct *p)
{
    if (p == NULL) {
        return;
    }
    if (p->content != NULL) {
        free(p->content);
        p->content = NULL;
    }
    free(p);
    p = NULL;
}

void DoSomeThing(void)
{
    SomeStruct *p = ... // 分配结构体内存并初始化
    ...
    if (condition) {
        FreeSomeStruct(p);
    }
    if (p != NULL) {
        // 可能会访问已释放内存
        errno_t ret = memcpy_s(buf, sizeof(buf), p->content, p->contentLen);
        ...
    }
}
```

**【正例】**
如下代码示例中，立即对释放后的指针设置为NULL，避免访问已释放内存。

```c
void FreeSomeStruct(SomeStruct *p)
{
    if (p == NULL) {
        return;
    }
    if (p->content != NULL) {
        free(p->content);
        p->content = NULL;
    }
    free(p);
}

void DoSomeThing(void)
{
    SomeStruct *p = ... // 分配结构体内存并初始化
    ...
    if (condition) {
        FreeSomeStruct(p);
        p = NULL;
    }
    if (p != NULL) {
        errno_t ret = memcpy_s(buf, sizeof(buf), p->content, p->contentLen);
        ...
    }
}
```

**【影响】**

再次使用已经释放的内存，或者再次释放已经释放的内存，或其他使用已释放资源的行为，可能导致拒绝服务或执行任意代码。

**【相关软件CWE编号】** CWE-415，CWE-416

**【业界典型漏洞】** CVE-2015-0057

## 1.4 禁止将局部变量的地址返回到其作用域以外
**【描述】**
如果对象在其生命周期之外被引用，则程序会产生未定义行为。当指针指向的对象到达其生存期结束时，指针的值将变得不确定。
因此，禁止将局部变量的地址返回到其作用域以外。

**【反例】**
如下代码示例中，Func()函数返回了局部变量的地址，在调用处解引用该指针程序会产生未定义行为：

```c
int *Func(void)
{
    int localVar = 0;
    ...
    return &localVar; // 不符合
}

void Caller(void)
{
    int *p = Func();
    ...
    int x = *p;  // 程序会产生未定义行为
}
```

**【正例】**
如下代码示例中，重构Func()函数返回值类型，使其返回一个整数值：

```c
int Func(void)
{
    int localVar = 0;
    ...
    return localVar;
}

void Caller(void)
{
    int x = Func();
    ...
}
```

## 1.5 资源不再使用时应予以关闭或释放
**【描述】**
这里的资源包括计算机内存、文件描述符、socket描述符。
程序员在创建或分配资源后，如果该资源不再被使用，程序员应将其正确的关闭或释放，尤其要注意所有可能的异常路径，避免遗漏。
以计算机内存为例，当动态分配内存不再使用时应予以释放，否则会发生内存泄漏，如果攻击者可以有意触发该漏洞，则内存资源可能被耗尽，造成拒绝服务。
以文件描述符为例，当打开的文件不再使用时应将指向该文件的描述符关闭，否则会发生该文件描述符泄漏，如果攻击者可以有意触发该漏洞，则可用的文件描述符可能被耗尽，造成拒绝服务。

**【反例】**

```c
#define BLOCK_SIZE_MAX    256

char *GetBlock(int fd)
{
    ...
    char *buf = (char *)malloc(BLOCK_SIZE_MAX);
    if (buf == NULL) {
        return NULL;
    }

    if (read(fd, buf, BLOCK_SIZE_MAX) != BLOCK_SIZE_MAX) {
        return NULL;  // 不符合：在异常路径中返回前未释放buf指向的内存资源，存在内存泄漏。
    }
    return buf;
}
```

**【正例】**

```c
#define    BLOCK_SIZE_MAX    256

char *GetBlock(int fd)
{
    ...
    char *buf = (char *)malloc(BLOCK_SIZE_MAX);
    if (buf == NULL) {
        return NULL;
    }

    if (read(fd, buf, BLOCK_SIZE_MAX) != BLOCK_SIZE_MAX) {
        free(buf);  // 符合：在异常路径中返回前释放buf指向的内存资源，并立即将指针置空。
        buf = NULL;
    }
    return buf;
}
```

**【影响】**

如果资源在结束使用前未正确的关闭或释放，会造成系统的内存泄漏、句柄泄漏等资源泄漏漏洞。如果攻击者可以有意触发资源泄漏，则可能能够通过耗尽资源来发起拒绝服务攻击。

**【相关软件CWE编号】** CWE-401，CWE-404

**【业界典型漏洞】** CVE-2005-3119, CVE-2004-0222, CVE-2002-1372

# 2 整数
C语言中整数类型繁多，包括有符号、无符号以及不同大小的整数类型，在不同实现中相同类型的整数的表示形式可能不同，并且不同整数类型间转换规则非常复杂，因此在使用整数前需要了解整数提升规则，了解不同整数类型间转换规则，了解整数常量/表达式中容易出现的问题，以及算数运算导致的整数溢出（overflow）/整数回绕（wrap）所产生的问题。

## 2.1 确保有符号整数运算不溢出
**【描述】**
有符号整数溢出是C语言标准中说明的一种未定义行为。因此，各种编译器的实现对有符号整数溢出行为有多种处理方式，其程序执行结果不可预料，甚至带来严重安全问题。
出于安全考虑，对外部数据中的有符号整数值在如下场景中使用时，需要确保运算不会导致溢出：

- 指针运算的整数操作数(指针偏移值)
- 数组索引
- 变长数组的长度(及长度运算表达式)
- 内存拷贝的长度
- 内存分配函数的参数
- 循环判断条件

在精度低于int的整数类型上进行运算时，需要考虑整数提升。程序员还需要掌握整数转换规则，包括隐式转换规则，以便设计安全的算术运算。

1）加法

**【反例】**
如下代码示例中，参与加法运算的整数是外部数据，在使用前未做校验，可能出现整数溢出。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int sum = numA + numB;
...
```

**【正例】**
如下代码示例中，按照最大允许值进行校验，防止整数溢出。在编程时可根据具体业务场景做更严格的值域校验。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int sum = 0;
if (((numA > 0) && (numB > (INT_MAX - numA))) ||
    ((numA < 0) && (numB < (INT_MIN - numA)))) {
    ... // 错误处理
}
sum = numA + numB;
...
```

2）减法

**【反例】**
如下代码示例中，参与减法运算的整数是外部数据，在使用前未做校验，可能出现整数溢出，进而造成后续的内存复制操作出现缓冲区溢出。
```c
unsigned char *content = ... // 指向报文头的指针
size_t contentSize = ...     // 缓冲区的总长度
int totalLen = ... // 报文总长度
int skipLen = ...  // 从消息中解析出来的需要忽略的数据长度
// 用 totalLen - skipLen 计算剩余数据长度，可能出现整数溢出
errno_t ret = memmove_s(content, contentSize,
                        content + skipLen, totalLen - skipLen);
...
```

**【正例】**
如下代码示例中，重构为使用`size_t`类型的变量表示数据长度，并校验外部数据长度是否在合法范围内。
```c
unsigned char *content = ... //指向报文头的指针
size_t contentSize = ...     // 缓冲区的总长度
size_t totalLen = ... // 报文总长度
size_t skipLen = ...  // 从消息中解析出来的需要忽略的数据长度
if (skipLen >= totalLen || totalLen > contentSize) {
    ... // 错误处理
}
errno_t ret = memmove_s(content, contentSize,
                        content + skipLen, totalLen - skipLen);
...
```

3）乘法

**【反例】**
如下代码示例中，内核代码对来自用户态的数值范围做了校验，但是由于opt是`int`类型，而校验条件中错误的使用了`ULONG_MAX`进行限制，导致整数溢出。
```c
int opt = ... // 来自用户态
if ((opt < 0) || (opt > (ULONG_MAX / (60 * HZ)))) { // 错误的使用了ULONG_MAX做上限校验
    return -EINVAL;
}
... = opt * 60 * HZ; // 可能出现整数溢出
...
```

**【正例】**
一种改进方案是将opt的类型修改为`unsigned long`类型，这种方案适用于修改了变量类型更符合业务逻辑的场景。
```c
unsigned long opt = ... // 将类型重构为 unsigned long 类型。
if (opt > (ULONG_MAX / (60 * HZ))) {
    return -EINVAL;
}
... = opt * 60 * HZ;
...
```

另一种改进方案是将数值上限修改为INT_MAX。
```c
int opt = ... // 来自用户态
if ((opt < 0) || (opt > (INT_MAX / (60 * HZ)))) { // 修改使用 INT_MAX作为上限值
    return -EINVAL;
}
... = opt * 60 * HZ;
```

4）除法

**【反例】**
如下代码示例中，做除法运算前只检查了是否出现被零除的问题，缺少对数值范围的校验，可能出现整数溢出。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
if (numB == 0) {
    ... // 对除数为0的错误处理
}
result = numA / numB; // 可能出现整数溢出
...
```

**【正例】**
如下代码示例中，按照最大允许值进行校验，防止整数溢出，在编程时可根据具体业务场景做更严格的值域校验。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
// 检查除数为0及除法溢出错误
if ((numB == 0) || ((numA == INT_MIN) && (numB == -1))) {
    ... // 错误处理
}
result = numA / numB;
...
```

5）求余数

许多平台以相同的指令实现求余数和除法运算，因此求余数运算也可能出现算数溢出和除零错误的问题。

**【反例】**
如下代码示例中，缺少对数值范围的校验，可能出现整数溢出。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
if (numB == 0) {
    ... // 对除数为0的错误处理
}
result = numA % numB;  // 可能出现整数溢出
...
```

**【正例】**
如下代码示例中，按照最大允许值进行校验，防止整数溢出。在编程时可根据具体业务场景做更严格的值域校验。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
// 检查除数为0及除法溢出错误
if ((numB == 0) || ((numA == INT_MIN) && (numB == -1))) {
    ... // 错误处理
}
result = numA % numB;
...
```

6）一元减

当操作数等于有符号整数类型的最小值时，在二进制补码一元求反期间会发生溢出。

**【反例】**
如下代码示例中，计算前未校验数值范围，可能出现整数溢出。
```c
int numA = ... // 来自外部数据
int result = -numA; // 可能出现整数溢出
...
```

**【正例】**
如下代码示例中，按照最大允许值进行校验，防止整数溢出。在编程时可根据具体业务场景做更严格的值域校验。
```c
int numA = ... // 来自外部数据
int result = 0;
if (numA == INT_MIN) {
    ... // 错误处理
}
result = -numA;
...
```

**【影响】**

整数溢出可能导致程序拒绝服务、缓冲区溢出、甚至执行任意代码。

**【相关软件CWE编号】** CWE-190，CWE-129
**【业界典型漏洞】** CVE-2019-17041

## 2.2 确保无符号整数运算不回绕
**【描述】**
涉及无符号操作数的计算永远不会溢出，因为超出无符号整数类型表示范围的计算结果会按照结果类型可表示的最大值+1的数值取模。
这种行为更多时候被非正式地称为无符号整数回绕。
在精度低于int的整数类型上进行运算时，需要考虑整数提升。程序员还需要掌握整数转换规则，包括隐式转换规则，以便设计安全的算术运算。
出于安全考虑，对外部数据中的无符号整数值在如下场景中使用时，需要确保运算不会导致回绕：  
- 指针运算的整数操作数(指针偏移值)
- 数组索引
- 变长数组的长度(及长度运算表达式)
- 内存拷贝的长度
- 内存分配函数的参数
- 循环判断条件

1）加法

**【反例】**
如下代码示例中，校验下一个子报文的长度加上已处理报文的长度是否超过了整体报文的最大长度，在校验条件中的加法运算可能会出现整数回绕，造成绕过该校验的问题。
```c
size_t totalLen = ... // 报文的总长度
size_t readLen = 0    // 记录已经处理报文的长度
...
size_t pktLen = ParsePktLen();     // 从网络报文中解析出来的下一个子报文的长度
if (readLen + pktLen > totalLen) { // 可能出现整数回绕
  ... // 错误处理
}
...
readLen += pktLen;
...
```

**【正例】**
由于readLen变量记录的是已经处理报文的长度，必然会小于totalLen，因此将代码中的加法运算修改为减法运算，避免条件绕过。
```c
size_t totalLen = ... // 报文的总长度
size_t readLen = 0;   // 记录已经处理报文的长度
...
size_t pktLen = ParsePktLen(); // 来自网络报文
if (pktLen > totalLen - readLen) {
  ... // 错误处理
}
...
readLen += pktLen;
...
```

2）减法

**【反例】**
如下代码示例中，校验len合法范围的运算可能会出现整数回绕，导致条件绕过。
```c
size_t len = ... // 来自用户态输入
if (SCTP_SIZE_MAX - len < sizeof(SctpAuthBytes)) { // 减法操作可能出现整数回绕
    ... // 错误处理
}
... = kmalloc(sizeof(SctpAuthBytes) + len, gfp); // 可能出现整数回绕
...
```

**【正例】**
如下代码示例中，调整减法运算的位置（需要确保编译期间减法表达式的值不翻转），避免整数回绕问题。
```c
size_t len = ... // 来自用户态输入
if (len > SCTP_SIZE_MAX - sizeof(SctpAuthBytes)) { // 确保编译期间减法表达式的值不翻转
    ... // 错误处理
}
... = kmalloc(sizeof(SctpAuthBytes) + len, gfp);
...
```

3）乘法

**【反例】**
如下代码示例中，使用外部数据计算申请内存长度时未校验，可能出现整数回绕。
```c
size_t width = ... // 来自外部数据
size_t hight = ... // 来自外部数据
unsigned char *buf = (unsigned char *)malloc(width * hight);
```

无符号整数回绕可能导致分配的内存不足。

**【正例】**
如下代码是一种解决方案，校验参与乘法运算的整数数值范围，确保不会出现整数回绕。
```c
size_t width = ... // 来自外部数据
size_t hight = ... // 来自外部数据
if (width == 0 || hight == 0) {
    ... // 错误处理
}
if (width > SIZE_MAX / hight) {
    ... // 错误处理
}
unsigned char *buf = (unsigned char *)malloc(width * hight);
```

**【例外】**
为正确执行程序，必要时无符号整数可能表现出模态（回绕）。建议将变量声明明确注释为支持模数行为，并且对该整数的每个操作也应明确注释为支持模数行为。

**【影响】**

整数回绕可能导致程序缓冲区溢出以及执行任意代码。

**【相关软件CWE编号】** CWE-190

**【业界典型漏洞】** CVE-2007-0776 CVE-2008-3526

## 2.3 确保除法和余数运算不会导致除零错误(被零除)
**【描述】**
整数的除法和取余运算的第二个操作数值为0会导致程序产生未定义的行为，因此使用时要确保整数的除法和余数运算不会导致除零错误(被零除，下同)。

1）除法

**【反例】**
有符号整数类型的除法运算如果限制不当，会导致溢出。
如下示例对有符号整数进行的除法运算做了防止溢出限制，确保不会导致溢出，但不能防止有符号操作数numA和numB之间的除法过程中出现除零错误。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
if ((numA == INT_MIN) && (numB == -1)) {
    ... // 错误处理
}
result = numA / numB; // 可能出现除零错误
...
```

**【正例】**
如下代码示例中，添加numB是否为0的校验，防止除零错误。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
if ((numB == 0) || ((numA == INT_MIN) && (numB == -1))) {
    ... // 错误处理
}
result = numA / numB;
...
```

2）求余数

**【反例】**
如下代码，同除法的错误代码示例一样，可能出现除零错误，因为许多平台以相同的指令实现求余数和除法运算。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
if ((numA == INT_MIN) && (numB == -1)) {
    ... // 错误处理
}
result = numA % numB; // 可能出现除零错误
...
```

**【正例】**
如下代码示例中，添加numB是否为0的校验，防止除零错误。
```c
int numA = ... // 来自外部数据
int numB = ... // 来自外部数据
int result = 0;
if ((numB == 0) || ((numA == INT_MIN) && (numB == -1))) {
    ... // 错误处理
}
result = numA % numB;
...
```

**【影响】**

除零错误可能导致拒绝服务。

**【相关软件CWE编号】** CWE-369

## 2.4 校验外部数据中整数值的合法性
**【描述】**
对源自外部数据的所有整数值，我们应执行严格评估，以确定它们是否具有可被识别的上限和下限。
对源自污染源的整数值限制其输入在合法范围内有助于防止整数溢出，回绕，截断和其他直接或间接由整数值限定不当引起的风险。
此外，在使用前限制和纠正输入问题要比在后期发生错误时再追溯定位错误的输入更加容易，成本更低。

**【反例】**(无限循环)
如下示例中，由于循环条件受外部输入的报文内容控制，可能进入无限循环：

```c
unsigned char *FindAttr(unsigned char type, const unsigned char *msg, size_t inputMsgLen)
{
    const unsigned char *content = msg;
    ...
    contentLength = content[RD_LEA_PKT_LENGTH]);
    ... // 参考inputMsgLen对contentLength进行校验
    while (contentLength < RD_LEA_PKT_LENGTH + 1) {
        mAttrType = content[0];
        mAttrLength = content[RD_LEA_PKT_LENGTH];
        ...
        contentLength -= mAttrLength;
        content += mAttrLength;
    }
    ...
}
```

此例中，需要检查报文的实际可读长度，报文内容提供的循环增量（避免为0），以防止缓冲区溢出。

**【正例】**
通过检查消息剩余长度，避免后续运算中无符号整数回绕。
```c
unsigned char *FindAttr(unsigned char type, const unsigned char *msg，size_t inputMsgLen)
{
    const unsigned char *content = msg;
    ...
    contentLength = content[RD_LEA_PKT_LENGTH]);
    ... // 参考inputMsgLen对contentLength进行校验
    while (contentLength < RD_LEA_PKT_LENGTH + 1) {
        mAttrType = content[0];
        mAttrLength = content[RD_LEA_PKT_LENGTH];
        ...
        if (contentLength < mAttrLength) {
            ... // 错误处理
            break;
        }
        contentLength -= mAttrLength;
        content += mAttrLength;
    }
    ...
}
```

**【反例】**（分配内存）
下面示例中，maxValue 取自用户定义的环境变量的值，因此是不可信的。该变量的值用于动态分配的大小。代码可以防止无符号整数回绕，但没有对申请内存的大小施加任何合理上限，可能会导致程序使用过多的内存。
```c
char **CreateMap(void)
{
    const char *limitStr = getenv("USER_LIMITS");
    const size_t maxValue =
        (limitStr == NULL) ? strtoul(limitStr, NULL, 10) : 0;

    // 限制未根据实际场景，范围太大，等同于没限制
    if ( (maxValue == 0) || (maxValue > SIZE_MAX / sizeof(unsigned char *))) {
        return NULL;   // 向上层调用函数反馈错误
    }

    const size_t dataSize = maxValue * sizeof(unsigned char *);
    unsigned char **map = (unsigned char **)malloc(dataSize);
    if (map == NULL) {
        return NULL;   // 向上层调用函数反馈错误
    }
    ... // 初始化 map

    return map;
}
```

因为maxValue的值是由用户控制的，所以该值可能会导致分配大量的内存，或者可能导致调用malloc()失败。根据实施错误处理的方式，可能会导致拒绝服务攻击或其他错误。

**【正例】**
如下解决方案定义了可接受的范围maxValue为`[1, MAX_MAP_SIZE]`。该maxValue声明为`size_t`，因此没有必要检查maxValue负值。

```c
#define MAX_MAP_SIZE 1024

char **CreateMap(void)
{
    const char *limitStr = getenv("DATA_MAP_SIZE");
    const size_t maxValue =
        (limitStr == NULL) ? strtoul(limitStr, NULL, 10) : 0;

    // 根据实际场景做出上限限制
    if ( (maxValue == 0) || (maxValue > MAX_MAP_SIZE)) {
        return NULL;   // 返回错误
    }

    const size_t dataSize = maxValue * sizeof(unsigned char *);
    unsigned char **map = (unsigned char **)malloc(dataSize);
    if (map == NULL) {
        return NULL;   // 向上层调用函数反馈错误
    }
    ... // 初始化 map

    return map;
}
```

**【反例】**(数组索引)
如下示例中，污染数据index用于作为数组索引：
```c
const char *GetFruit(void)
{
    static const char *fruit[] = { "apple", "banana", "grape", "orange" };

    // 通过GetUserInputIndex取到的索引值index是受污染的
    int index = 0;
    bool success = GetUserInputIndex(&index);
    if (!success) {
        return NULL;   // 返回错误
    }

    return fruit[index]; // 可能有读取数组索引越界错误
}
```

**【正例】**
如下解决方案将index的可接受范围定义为`[0, ARRAY_NUMBER)`。
```c
#define ARRAY_NUMBER(x) (sizeof(x) / sizeof((x)[0]))

const char *GetFruit(void)
{
    static const char *fruit[] = { "apple", "banana", "grape", "orange" };

    // 通过GetUserInputIndex取到的索引值index是受污染的
    int index = 0;
    bool success = GetUserInputIndex(&index);
    if (!success) {
        return NULL;   // 返回错误
    }
    if ((index < 0) || (index >= ARRAY_NUMBER(fruit))) {
        return NULL;   // 返回错误
    }

    return fruit[index];
}
```

**【反例】**
如下代码片段直接使用了外部传入的数值作为循环拷贝的长度，存在风险。
```c
typedef struct {
    unsigned long octetLen;
    unsigned char *octs;
} AsnOcts;

#define MAX_INT_DIGITS 516
typedef struct {
    unsigned int length;
    unsigned char valueList[MAX_INT_DIGITS];
} BigInt;

BigInt *SAsnOctsToBigInt(const AsnOcts *pAsnOcts) // pAsnOcts来自程序外部数据
{
    BigInt *pBigInt = NULL;
    int ret = 0;

    ... // 检查入参有效性
    pBigInt = malloc(sizeof(BigInt));
    ... // 处理内存分配失败情况

    // 由于拷贝长度受程序外部数据控制且未校验，如下拷贝存在风险
    for (unsigned long i = 0; i < pAsnOcts->octetLen; i++) {
        pBigInt->valueList[i] = pAsnOcts->octs[i];
    }
    ...
}
```

**【正例】**
对外部传入的拷贝长度，需校验后才能使用。
```c
typedef struct {
    unsigned long octetLen;
    unsigned char *octs;
} AsnOcts;

#define MAX_INT_DIGITS 516
typedef struct {
    unsigned int length;
    unsigned char valueList[MAX_INT_DIGITS];
} BigInt;

BigInt *SasnOctsToBigInt(const AsnOcts *pAsnOcts) // pAsnOcts来自程序外部数据
{
    BigInt *pBigInt = NULL;

    ... // 检查入参有效性
    pBigInt = malloc(sizeof(BigInt));
    ... // 处理内存分配失败情况

    // pAsnOcts->octetLen的准确性应由调用方保证，此处仅校验合法性
    if (pAsnOcts->octetLen > MAX_INT_DIGITS) {
        ... // 处理错误
    }

    for (unsigned long i = 0; i < pAsnOcts->octetLen; i++) {
        pBigInt->valueList[i] = pAsnOcts->octs[i];
    }
    ...
}
```

**【影响】**

未对外部数据中的整数值进行限制可能导致拒绝服务，信息泄露，或执行任意代码。


# 3 指针和数组
## 3.1 外部数据作为数组索引时必须确保在数组大小范围内
**【描述】**
外部数据作为数组索引对内存进行访问时，必须对数据的大小进行严格的校验，确保数组索引在有效范围内，否则会导致严重的错误。
当一个指针指向数组元素时，可以指向数组最后一个元素的下一个元素的位置，但是不能读写该位置的内存：

> 如果指针操作数和指针运算的结果指针指向同一数组对象的元素，或者指向数组对象最后一个元素的后一个元素，则求值不应产生溢出； 否则，行为是不确定的。
> 如果指针指向数组对象最后一个元素的后一个元素，则不应将其用作一元*操作符的操作数。


**【反例】**
如下代码示例中,SetDevId()函数存在差一错误，当 index 等于 `DEV_NUM` 时，恰好越界写一个元素；
同样GetDev()函数也存在差一错误，虽然函数执行过程中没有问题，但是当解引用这个函数返回的指针时，程序会产生未定义行为。

```c
...
#define DEV_NUM 10
#define MAX_NAME_LEN 128
typedef struct {
    int id;
    char name[MAX_NAME_LEN];
} Dev;

static Dev devs[DEV_NUM];

int SetDevId(size_t index, int id)
{
    if (index > DEV_NUM) {    // 不符合：差一错误。
        ... // 错误处理
    }

    devs[index].id = id;
    return 0;
}

static Dev *GetDev(size_t index)
{
    if (index > DEV_NUM) {    // 不符合：差一错误。
        ... // 错误处理
    }

    return devs + index;
}
```

**【正例】**
如下代码示例中，修改校验索引的条件，避免差一错误。

```c
#define DEV_NUM 10
#define MAX_NAME_LEN 128
typedef struct {
    int id;
    char name[MAX_NAME_LEN];
} Dev;

static Dev devs[DEV_NUM];

int SetDevId(size_t index, int id)
{
    if (index >= DEV_NUM) {
        ... // 错误处理
    }
    devs[index].id = id;
    return 0;
}

static Dev *GetDev(size_t index)
{
    if (index >= DEV_NUM) {
        ... // 错误处理
    }
    return devs + index;
}
```

**【影响】**

未对外部数据中的整数值进行限制可能导致拒绝服务，缓冲区溢出，信息泄露，或执行任意代码。

**【相关软件CWE编号】** CWE-119，CWE-123，CWE-125
## 3.2 禁止通过对数组类型的函数参数变量进行sizeof来获取数组大小
**【描述】**

使用sizeof操作符求其操作数的大小（以字节为单位），其操作数可以是一个表达式或者加上括号的类型名称，例如：`sizeof(int)`或`sizeof(int *)`。

> 当将sizeof应用于具有数组或函数类型的参数时，sizeof操作符将得出调整后的（指针）类型的大小。

函数参数列表中声明为数组的参数会被调整为相应类型的指针。例如：`void Func(int inArray[LEN])`函数参数列表中的inArray虽然被声明为数组，但是实际上会被调整为指向int类型的指针，即调整为`void Func(int *inArray)`。
在这个函数内使用`sizeof(inArray)`等同于`sizeof(int *)`，得到的结果通常与预期不相符。例如：在IA-32架构上，`sizeof(inArray)` 的值是 4，并不是inArray数组的大小。

**【反例】**
如下代码示例中，函数ArrayInit的功能是初始化数组元素。该函数有一个声明为`int inArray[]`的参数，被调用时传递了一个长度为256的int类型数组data。
ArrayInit函数实现中使用`sizeof(inArray) / sizeof(inArray[0])`方法来计算入参数组中元素的数量。
但由于inArray是函数参数，所以具有指针类型，结果，`sizeof(inArray)`等同于`sizeof(int *)`。
无论传递给ArrayInit函数的数组实际长度如何，表达式的`sizeof(inArray) / sizeof(inArray[0])`计算结果均为1，与预期不符。

```c
#define DATA_LEN 256
void ArrayInit(int inArray[])
{
    // 不符合：这里使用sizeof(inArray)计算数组大小
    for (size_t i = 0; i < sizeof(inArray) / sizeof(inArray[0]); i++) {
        ...
    }
}

void FunctionData(void)
{
    int data[DATA_LEN];

    ...
    ArrayInit(data); // 调用ArrayInit函数初始化数组data数据
    ...
}
```

**【正例】**
如下代码示例中，修改函数定义，添加数组长度参数，并在调用处正确传入数组长度。

```c
#define DATA_LEN 256
// 函数说明：入参len是入参inArray数组的长度
void ArrayInit(int inArray[], size_t len)
{
    for (size_t i = 0; i < len; i++) {
        ...
    }
}

void FunctionData(void)
{
    int data[DATA_LEN];

    ArrayInit(data, sizeof(data) / sizeof(data[0]));
    ...
}
```

**【反例】**
如下代码示例中，`sizeof(inArray)`不等于`ARRAY_MAX_LEN * sizeof(int)`，因为将sizeof操作符应用于声明为具有数组类型的参数时，即使参数声明指定了长度，也会被调整为指针，`sizeof(inArray)`等同于 `sizeof(int *)`：

```c
#define ARRAY_MAX_LEN 256

void ArrayInit(int inArray[ARRAY_MAX_LEN])
{
    // 不符合：sizeof(inArray)，得到的长度是指针的大小，不是数组的长度，和预期不符。
    for (size_t i = 0; i < sizeof(inArray) / sizeof(inArray[0]); i++) {
        ...
    }
}

int main(void)
{
    int masterArray[ARRAY_MAX_LEN];

    ...
    ArrayInit(masterArray);

    return 0;
}
```

**【正例】**
如下代码示例中，使用入参len表示指定数组的长度：

```c
#define ARRAY_MAX_LEN 256

// 函数说明：入参len是入参数组的长度
void ArrayInit(int inArray[], size_t len)
{
    for (size_t i = 0; i < len; i++) {
        ...
    }
}

int main(void)
{
    int masterArray[ARRAY_MAX_LEN];

    ArrayInit(masterArray, ARRAY_MAX_LEN);
    ...

    return 0;
}
```

## 3.3 禁止通过对指针变量进行sizeof操作来获取数组大小
**【描述】**
将指针当做数组进行sizeof操作时，会导致实际的执行结果与预期不符。例如：变量定义 `char *p = array`，其中array的定义为`char array[LEN]`，表达式`sizeof(p)`得到的结果与 `sizeof(char *)`相同，并非array的长度。

**【反例】**
如下代码示例中，buffer和path分别是指针和数组，程序员想对这2个内存进行清0操作，但由于程序员的疏忽，将内存大小误写成了`sizeof(buffer)`，与预期不符。

```c
char path[MAX_PATH];
char *buffer = (char *)malloc(SIZE);
...

...
memset(path, 0, sizeof(path));

// sizeof与预期不符，其结果为指针本身的大小而不是缓冲区大小
memset(buffer, 0, sizeof(buffer));
```

**【正例】**
如下代码示例中，将`sizeof(buffer)`修改为申请的缓冲区大小：

```c
char path[MAX_PATH];
char *buffer = (char *)malloc(SIZE);
...

...
memset(path, 0, sizeof(path));
memset(buffer, 0, SIZE); // 使用申请的缓冲区大小
```

# 4 字符串
## 4.1 确保字符串存储有足够的空间容纳字符数据和null结束符
**【描述】**
将数据复制到不足以容纳数据的缓冲区，会导致缓冲区溢出。缓冲区溢出经常发生在字符串操作中。为了避免这种错误，截断拷贝的数据以限制字符串的字节长度是一种防御方法，但是最好的措施是确保目的缓冲区的大小足以容纳复制数据和null结束符。当字符串存储在堆空间时，确保分配内存时已分配了足够的空间。
部分字符串处理函数由于设计时安全考虑不足，或者存在一些隐含的目的缓冲区长度要求，容易被误用，导致缓冲区写溢出。此类典型函数包括不在C标准库函数中的itoa()，realpath()函数。

**【反例】**(itoa)
有些函数如itoa(), realpath()需要在对传入的缓冲区指针位置进行写入操作，但函数并没有提供缓冲区长度。因此，在调用这些函数前，必须提供足够的缓冲区。
如下代码示例中，试图将数字转为字符串，但是目标存储空间的预留长度不足：

```c
int num = ...
char str[8];
itoa(num, str, 10);  // 10进制整数的最大存储长度是12个字节
```
**【正例】**
使用安全函数实现整数转换为10进制形式的字符串。
```c
int num = ...
char str[16]; // 有时会考虑字节对齐定义冗余的长度，这里选择了16
int ret = sprintf_s(str, sizeof(str), "%d", num);
...  // 处理错误
```

**【反例】**(realpath)
如下代码示例中，试图将路径标准化，但是目标存储空间的长度不足：

```c
#define MAX_PATH_LEN 100
char  resolvedPath[MAX_PATH_LEN];

/*
 * realpath函数的存储缓冲区长度是由PATH_MAX常量定义，
 * 或是由_PC_PATH_MAX系统值配置的，通常都大于100字节
 */
char *res = realpath(path, resolvedPath);
...
```

**【正例】**
可以将realpath的第二个参数传入NULL, 以让系统自动分配合适的内存。
```c
char *resolvedPath = NULL;

resolvedPath = realpath(path, NULL);
if (resolvedPath == NULL) {
    ... // 处理错误
}
...
if (resolvedPath != NULL) {
    free(resolvedPath);
    resolvedPath = NULL;
}
...
```

**【反例】**
如下代码示例中，在对外部数据进行解析并将内容保存到name中，未考虑name的大小：
```c
int ProcessMessage(unsigned char *msg, size_t length)
{
    ...
    char name[MAX_NAME];
    size_t i = 0;
    // 必须考虑msg不包含预期的字符'\n'
    while (i < length &&
           msg[i] != '\0' &&
           msg[i] != '\n') {
        name[i] = msg[i];
        i++;
    }
    name[i] = '\0';
    ...
}
```

**【正例】**
如下代码示例中，在对外部数据进行解析并将内容保存到name中，考虑了name的大小：

```c
int ProcessMessage(unsigned char *msg, size_t length)
{
    ...
    char name[MAX_NAME];
    size_t i = 0;
    // 必须考虑msg不包含预期的字符'\n'
    while (i < length &&
           msg[i] != '\0' &&
           msg[i] != '\n' &&
           i < MAX_NAME - 1) { // 使用 MAX_NAME - 1 保留结束符空间
        name[i] = msg[i];
        i++;
    }
    name[i] = '\0';
    ...
}
```

**【反例】**（差一错误）
如下代码示例中，展现了一个差一错误。代码中的循环将数据从src复制到dest。但是因为循环没有考虑'\0'结束符，它可能错误的写入dest缓冲区结束位置之后的一个字节。

```c
...
char dest[ARRAY_SIZE];
char src[ARRAY_SIZE];
...
size_t i;
for (i = 0; src[i] != '\n' && i < ARRAY_SIZE; i++) {
    dest[i] = src[i];
}
dest[i] = '\0'; // 不符合：越界写了一个字节
```

**【正例】**（差一错误）
如下代码示例中，修改了循环条件，避免差一错误导致的写越界。

```c
...
char dest[ARRAY_SIZE];
char src[ARRAY_SIZE];
...
size_t i;
for (i = 0; src[i] != '\n' && i < ARRAY_SIZE - 1; i++) {
    dest[i] = src[i];
}
dest[i] = '\0';
```

**【反例】**（gets函数）
gets函数在C99技术勘误3中被弃用，从C11标准中删除，它本是不安全的，绝不应该使用，因为它没有提供任何控制从stdin读取缓冲区数据量的办法。
如下代码示例中，假设gets函数不会读取超过`(BUFFER_SIZE - 1)`个字符，但是这是个无效的假设，可能造成缓冲区溢出。
gets函数从stdin中读取字符写入目标数组，直到遇到文件结束符或换行符结束，并将换行符丢弃，在读入数组的最后一个字符后面写入'\0'结束符。

```c
#define BUFFER_SIZE 128

char buf[BUFFER_SIZE];
if (gets(buf) == NULL) {
    ... // 错误处理
}
```

**【正例】**（fgets函数）
如下代码示例中，fgets函数从流中读取到数组的字符数最多不超过第二个参数减一，并保证目标字符串有'\0'结束符，因此不会造成缓冲区溢出。

```c
#define BUFFER_SIZE 128

char buf[BUFFER_SIZE];
...
if (fgets(buf, sizeof(buf), stdin) == NULL) {
    ... // 错误处理
}
... // 继续处理换行符等
```
fgets函数不是gets函数的严格替代品，这是因为fgets函数会保留换行符（如果读到的话），也可能返回一个不完整的行。使用fgets函数安全地处理太长而无法保存在目标数组中的行是可能的，但是由于性能原因，不建议这么做。

**【正例】**（gets_s函数）

gets_s函数最多从stdin流读取指定数量 - 1个字符到目标数组，丢弃换行符并保证有'\0'结束符。 
如下代码示例中，使用gets_s读取数据，避免缓冲区溢出：

```c
#define BUFFER_SIZE 128

char buf[BUFFER_SIZE];
if (gets_s(buf, sizeof(buf)) == NULL) {
    ... // 错误处理
}
```

**【反例】**(getchar函数)

一次读取一个字符提供了控制行为的更大灵活性，但是需要附加的性能开销。如下代码示例中，一次读一个字符直到遇到文件结束或换行符为止，丢弃换行符，并在最后一个读到的字符之后添加'\0'结束符，但是这段代码存在缓冲区溢出风险，没有校验指针是否超出了buf的有效范围：

```c
#define BUFFER_SIZE 128

char buf[BUFFER_SIZE];
char *p = buf;
int ch;

while ((ch = getchar()) != '\n' && ch != EOF) {
    *p = (char)ch;
    p++;
}
*p = '\0';
...
```

**【正例】**(getchar函数)

如下代码示例中，添加了校验代码。当`count == BUFFERSIZE - 1`时，字符不会再复制到buf，并为结束符留出空间。循环将继续读取字符值直到遇到换行或文件结束为止：

```c
#define BUFFER_SIZE 128

char buf[BUFFER_SIZE];
size_t count = 0;
int ch;

while ((ch = getchar()) != '\n' && ch != EOF) {
    if (count < sizeof(buf) - 1) {
        buf[count] = (char)ch;
        count++;
    }
    ...
}
buf[count] = '\0';  // 设置字符串结束符
...
```

**【反例】**(scanf函数)
如下代码示例中，scanf函数调用可能造成写入到字符数组buf之外：

```c
#define BUFFERSIZE 128

char buf[BUFFER_SIZE];
if (scanf("%s", buf) != 1) {
    ... // 错误处理
}
... // 函数实现其它部分
```

**【正例】**(scanf函数)
如下代码示例中，调用`scanf_s`函数以避免缓冲区溢出：

```c
#define BUFFER_SIZE 128

char buf[BUFFER_SIZE];
if (scanf_s("%s", buf, sizeof(buf)) != 1) {
    ... // 错误处理
}
... // 函数实现其它部分
```

**【反例】**(strcpy)

```c
#define MAX_ENV_LENGTH 256

char buff[MAX_ENV_LENGTH];
char *editor = getConfig("EDITOR");
if (editor == NULL) {
    ...
} else {
    strcpy(buff, editor);
}
...
```

**【正例】**(strcpy)
复制环境变量时限制其长度，避免出现缓冲区溢出：

```c
#define MAX_ENV_LENGTH 256

char buff[MAX_ENV_LENGTH];
char *editor = getConfig("EDITOR");
if (editor == NULL) {
    ...
} else {
    if (strcpy_s(buff, sizeof(buff), editor) ！= EOK) {
        ... // 错误处理
    }
}
...
```

**【反例】**(sprintf函数)

在如下代码示例中，name引用一个外部字符串；它可能来自用户输入、文件系统或者网络。程序从这个字符串构造一个文件名，准备打开该文件：

```c
#define MAX_NAME_LEN 128

char filename[MAX_NAME_LEN];
sprintf(filename, "%s.txt", name);
...
```

**【正例】**(sprintf函数)

一个解决方案是使用安全函数`sprintf_s`替代sprintf函数，避免出现缓冲区溢出：

```c
#define MAX_NAME_LEN 128

char filename[MAX_NAME_LEN];
int ret = sprintf_s(filename, sizeof(filename),"%s.txt", name);
if (ret < 0) {
     ... // 错误处理
}
...
```

**【影响】**

违反本条款可能导致拒绝服务，缓冲区溢出，信息泄露，或执行任意代码。

**【相关软件CWE编号】** CWE-119，CWE-120，CWE-123，CWE-125，CWE-676

**【历史漏洞】** CAN-2003-0352

## 4.2 对字符串进行存储操作，确保字符串有null结束符
**【描述】**
部分字符串处理函数操作字符串时，将截断超出指定长度的字符串，如strncpy()函数最多复制n个字符到目的缓冲区，如果源字符串长度大于n，则目的缓冲区的内容为n个被复制的字符，null结束符不会被写入到目的缓冲区。使用这类函数时，可能会无意截断导致数据丢失，并在某些情况下会导致软件漏洞。
因此，对字符串进行存储操作，必须确保字符串有null结束符（如使用字符串安全函数生成字符串，或显式对字符数组赋null结束符），否则在后续的调用strlen等操作中，可能会导致内存越界访问漏洞。

**【反例】**
在如下代码示例中，使用strncpy函数复制字符串时可能会发生截断（发生条件为：`strlen(name) > sizeof(filename) - 1`）。当发生截断时，filename的内容是不完整的，并且缺少'\0'结束符，后续对filename的操作可能会导致软件漏洞：

```c
#define FILENAME_LEN 128

char filename[FILENAME_LEN];
strncpy(filename, name, sizeof(filename) - 1);
...
```

**【正例】**
使用安全函数`strcpy_s`复制字符串，并检查安全函数返回值，如果成功，则确保filename字符串是完整的并且包含'\0'结束符：

```c
#define FILENAME_LEN 128

char filename[FILENAME_LEN];
errno_t ret = strcpy_s(filename, sizeof(filename), name);
if (ret != EOK) {
     ... // 处理错误
}
...
```

**【相关软件CWE编号】** CWE-170，CWE-464

# 5 断言
断言是一种调试诊断机制，用于验证代码是否符合程序员的预期。程序员在开发期间应该对函数的参数、代码中间执行结果合理地使用断言机制，确保程序的缺陷尽量在测试阶段被发现。

断言可以用于代码中说明各种假定，包括前提条件(preconditions)和后置条件(postconditions)。例如，可以对仅在模块内部使用的函数体内，用断言来声明调用该函数的前提条件，以帮助模块内的调用者正确传入参数。对于模块对外提供的接口函数，由于其实现对外是不可见的，因此不能通过断言来告知调用者需要遵循的约定，也不能通过使用断言来减少对接口函数参数的实际校验。

在调试版本中，断言被触发后，说明程序出现了不应该出现的严重错误，程序会立即提示错误，并终止执行。典型的严重错误如参数在同一模块的上层函数已经校验过，但传递到下层函数后参数不正确，或程序模块内部发送的状态值未定义。

断言必须用宏进行定义，只在调试版本有效，最终发布版本不允许出现assert函数，例如可以按下面的代码实现：

```c
#include <assert.h>
#ifdef DEBUG
#define ASSERT(f)  assert(f)
#else
#define ASSERT(f)  ((void)0)
#endif
```

如下的函数VerifyUser，上层调用者会保证传进来的参数是合法的字符串，不可能出现传递非法参数的情况。因此，在该函数的开头，加上4个ASSERT进行校验。

```c
bool VerifyUser(const char *userName, const char *password)
{
    ASSERT(userName != NULL);
    ASSERT(strlen(userName) > 0);
    ASSERT(password != NULL);
    ASSERT(strlen(password) > 0);
    ...
}
```

## 5.1 禁止用断言检测程序在运行期间可能导致的错误，可能发生的错误要用错误处理代码来处理
**【描述】**
断言主要用于调试期间，在发布版本中应将其关闭。因此，断言应该用于防止不正确的程序员假设，而不能用在发布版本上检查程序运行过程中发生的错误。

断言永远不应用于验证是否存在运行时（与逻辑相对）错误，包括但不限于：
- 无效的用户输入（例如：命令行参数和环境变量）
- 文件错误（例如：打开、读取或写入文件时出错）
- 网络错误（例如：网络协议错误）
- 内存不足的情况（例如：malloc()类似的故障）
- 系统资源耗尽（例如：文件描述符、进程、线程）
- 系统调用错误（例如：执行文件、锁定或解锁互斥锁时出错）
- 无效的权限（例如：文件、内存、用户）

例如，防止缓冲区溢出的代码不能使用断言实现，因为该代码必须编译到发布版本的可执行文件中。
如果服务器程序在网运行时由恶意用户触发断言失败，会导致拒绝服务攻击。在这种情况下，更适合使用软故障模式，例如写入日志文件和拒绝请求。

**【反例】**
以下代码的所有ASSERT的用法都是错误的。例如，错误的使用ASSERT宏来验证内存分配是否成功，因为内存的可用性取决于系统的整体状态，并且在程序运行的任何时候都可能耗尽，所以必须以具有韧性的方式来妥善处理并将程序从内存耗尽中恢复。因此，使用ASSERT宏来验证内存分配是否成功将是不合适的，因为这样做可能导致进程突然终止，从而开启了拒绝服务攻击的可能性。

```c
FILE *fp = fopen(path, "r");
ASSERT(fp != NULL);  // 不符合：文件有可能打开失败
char *str = (char *)malloc(MAX_LINE);
ASSERT(str != NULL); // 不符合：内存有可能分配失败
ReadLine(fp, str);
char *p = strstr(str, "age="");
ASSERT(p != NULL);   // 不符合：文件中不一定存在该字符串
char *end = NULL;
long age = strtol(p + 4, &end, 10);
ASSERT(age > 0);     // 不符合：文件内容不一定符合预期
```

**【正例】**
下面代码演示了如何重构上面的错误代码
```c
FILE *fp = fopen(path, "r");
if (fp == NULL) {
    ... // 错误处理
}
char *str = (char *)malloc(MAX_LINE);
if (str == NULL) {
    ... // 错误处理
}
ReadLine(fp, str);
char *p = strstr(str, "age=");
if (p == NULL) {
    ... // 错误处理
}
char *end = NULL;
long age = strtol(p + 4, &end, 10);
if (age <= 0) {
    ... // 错误处理
}
```

**【相关软件CWE编号】** CWE-190

## 5.2 禁止在断言内改变运行环境
**【描述】**
在程序正式发布阶段，断言不会被编译进去，为了确保调试版和正式版的功能一致性，严禁在断言中使用任何赋值、修改变量、资源操作、内存申请等操作。

例如，以下的断言方式是错误的：

```c
ASSERT(p1 = p2);        // p1被修改
ASSERT(i++ > 1000);     // i被修改
ASSERT(close(fd) == 0); // fd被关闭
```

# 6 函数设计
权限、并发、资源管理策略的编程设计方法，设计模式的最佳实践由于其描述的复杂性，需要单独的文档说明，下面将尽量不涉及有关内容。
## 6.1 对所有外部数据进行合法性检查
**【描述】**
外部数据的来源包括但不限于：网络、用户输入、命令行、文件（包括程序的配置文件）、环境变量、用户态数据（对于内核程序）、进程间通信（包括管道、消息、共享内存、socket、RPC等，特别需要注意的是设备内部不同单板间通讯也属于进程间通信）、API参数、全局变量。

来自程序外部的数据通常被认为是不可信的，在使用这些数据之前，需要进行合理的检查。
如果不对这些外部数据进行检查，将可能导致不可预期的安全风险。

对来自程序外部的数据要校验处理后才能使用。典型的使用场景包括：

**作为数组索引**
    将不可信的数据作为数组索引，可能导致超出数组上限，从而造成非法内存访问。
**作为内存偏移地址**
    将不可信数据作为指针偏移访问内存，可能造成非法内存访问，并可以造成进一步的危害，如任意地址读/写。
**作为内存分配的尺寸参数**
    例如进行0字节长度分配可能造成非法内存访问，或未限制分配内存大小造成的过度资源消耗。
**作为循环条件**
    将不可信数据作为循环限定条件，可能会引发缓冲区溢出、内存越界读/写、死循环等问题。
**作为除数**
    参见除零错误(被零除)。
**作为命令行参数**
    参见“禁止外部可控数据作为进程启动函数的参数”。
**作为数据库查询语句的参数**
    参见“禁止直接使用外部数据拼接SQL命令”。
**作为输入/输出格式化字符串**
    参见“调用格式化输入/输出函数时，禁止format参数受外部数据控制”。
**作为内存拷贝长度**
    当作为拷贝长度时，可能造成目标缓冲区溢出。
**作为文件路径**
    直接打开不可信路径，可能会导致目录遍历攻击，操作了攻击者无权操作的文件，使得系统被攻击者所控制。

输入校验包括但不局限于：
- 校验数据长度
- 校验数据范围
- 校验数据类型和格式
- 校验输入只包含可接受的字符（“白名单”形式），尤其需要注意一些特殊情况下的特殊字符。

**外部数据校验原则**
**1.信任边界**
由于外部数据不可信，因此系统在运行过程中，如果数据传输与处理跨越不同的信任边界，为了防止攻击蔓延，必须对来自信任边界外的其他模块的数据进行合法性校验。
（a）so（或者dll）之间
so或dll作为独立的第三方模块，用于对外导出公共的api函数，供其他模块进行函数调用。so/dll无法确定上层调用者是否传递了合法参数，因此so/dll的公共函数需要检查调用者提供的参数合法性。so/dll应该设计成低耦合、高复用性，尽管有些软件的so/dll当前设计成只在本软件中使用，但仍然应该将不同的so/dll模块视为不同的信任边界。
（b）进程与进程之间
为防止通过高权限进程提权，进程与进程之间的IPC通信（包括单板之间的IPC通信、不同主机间的网络通信），应视为不同信任边界。
（c）应用层进程与操作系统内核
操作系统内核具有比应用层更高的权限，内核向应用层提供的接口，应该将来自应用层的数据作为不可信数据处理。
（d）可信执行环境内外环境
为防止攻击蔓延至可信执行环境，TEE、SGX等对外提供的接口，应该将来自外部的数据作为不可信数据处理。



**2.外部数据校验**
外部数据进入到本模块后，必须经过合法性校验才能使用。被校验后的合法数据，在本模块内，其他内部子函数中不需要重复校验。
下面的例子，函数Foo处理外部数据，由于buffer不一定是’\0’结尾， strlen 的返回值 nameLen 有可能超过 len，导致越界读取数据。本例中采用 strnlen 进行字符串长度计算，随后解析出 name 字符串，在后续的 Foo2 调用中可以直接使用 strlen。

**【反例】**

```c
void Foo(const unsigned char *buffer, size_t len)
{
    if (buffer == NULL) { // 必须做参数合法性检查
        // 错误处理
        ...
    }

    // buffer不一定是'\0'结尾
    size_t nameLen = strlen((const char *)buffer);
    char *name = (char *)malloc(nameLen + 1);
    if (name != NULL) {
        errno_t ret = memcpy_s(name, nameLen + 1, buffer, nameLen);
        name[nameLen] = '\0';
    }
    ...
}
```

**【正例】**
应该是调用 strnlen 避免读越界

```c
void Foo(const unsigned char *buffer, size_t len)
{
    if (buffer == NULL || len >= MAX_BUFFER_LEN) { // 必须做参数合法性检查
        // 错误处理
        ...
    }

    // buffer不一定是'\0'结尾
    size_t nameLen = strnlen((const char *)buffer, len);
    char *name = (char *)malloc(nameLen + 1);
    if (name != NULL) {
        memcpy_s(name, nameLen + 1, buffer, nameLen);
        name[nameLen] = '\0';
        foo2(name);    // foo2内可以直接使用 strlen
    }
    ...
}
```
下面的代码处理外部数据，数据格式如下:

MODULE_A_Foo 和MODULE_A_Foo2是MODULE_A的二个函数，MODULE_A_Foo处理外部数据，将name解析出来，传递给MODULE_A_Foo2处理，MODULE_A_Foo2又调用外部模块MODULE_B的MODULE_B_Foo处理。
```c
// MODULE_A_Foo2 为 MODULE_A 模块的内部函数，约定为由调用者保证参数的合法性
static void MODULE_A_Foo2(const char *name)
{
    // 如果以下的ASSERT触发，表示调用处违反了约定，调用处必须进行修改
    ASSERT(name != NULL);

    size_t nameLen = strlen(name);     //不需要检查name合法性
    MODULE_B_Foo(name);                //调用MODULE_B中的函数
}

void MODULE_A_Foo(const unsigned char *buffer, size_t len)
{
    if (buffer == NULL || len <= sizeof(int)) { // 必须做参数合法性检查
        // 错误处理
        ...
    }
    int nameLen = *(int *)buffer;
    // nameLen 是不可信数据，必须检查合法性
    if (nameLen <= 0 || (size_t)nameLen > len - sizeof(int)) {
        // 错误处理
        ...
    }
    char *name = (char *)malloc(nameLen + 1);
    if (name == NULL) {
        // 内存分配失败，错误处理
        ...
    }
    errno_t err = memcpy_s(name, nameLen + 1, buffer + sizeof(int), nameLen);
    if (err != EOK) {
        // 错误处理
        ...
    }
    name[nameLen] = '\0';   // 此时name是一个具有\0结尾的合法字符串
    MODULE_A_Foo2(name);    // 调用本模块内内部函数
    ...
}
```
以下是MODULE_B模块中的代码：
```c
/*
 * MODULE_B_Foo 为 MODULE_B 模块的公共函数，
 * 其约定为，如果参数name不为NULL,那么必须是一个具有’\0’结尾的合法字符串并且长度大于0
 */
void MODULE_B_Foo(const char *name)
{
    if (name == NULL || name[0] == '\0') { // 必须做参数合法性检查
        // 错误处理
        ...
    }
    size_t nameLen = strlen(name);    // 不需要使用strnlen
    ...
}
```
对于模块A来说， buffer 是外部不可信输入，必须做严格的校验，从 buffer 解析出来的 name，在解析过程中进行了合法性检查，在模块A内部属于合法数据，作为参数传递给内部子函数时不需要再做合法性检查（如果要继续对 name 内容进行解析，那么仍然必须对 name 内容进行校验）。
如果模块A中的 name 继续跨越信任面传递给其他模块（在本例中是直接调用模块B的公共函数，也可以是通过文件、管道、网络等方式），那么对于B模块来说， name 属于不可信数据，必须做合法性检查。

# 7 函数使用
## 7.1 调用格式化输入/输出函数时，禁止format参数受外部数据控制

**【描述】**
调用格式化函数时，如果format参数由外部数据提供，或由外部数据拼接而来，会造成格式化字符串漏洞。
攻击者如果能够完全或者部分控制格式字符串内容，可以使被攻击的进程崩溃、查看栈内容、查看内存内容或者在任意内存位置写入数据。结果是，攻击者能够以被攻击进程的权限执行任意代码。
格式化输出函数特别危险，这是因为许多程序员没有意识到它们是具有攻击能力的。比如：格式化输出函数可以使用%n转换符，向指定地址写入一个整数值。
这些格式化函数有：
    格式化输出函数: xxxprintf;
    格式化输入函数: xxxscanf;
    格式化错误消息函数: err(), verr(), errx(), verrx(), warn(), vwarn(), warnx(), vwarnx(), error(), error_at_line();
    格式化日志函数: syslog(), vsyslog().

**【反例】**
如下代码示例中的IncorrectPassword()函数的功能是在身份验证无效时（指定用户没有找到或者密码不正确），显示一条错误信息。
该函数接受一个源自用户的字符串数据user，而user是未验证的，是外部可控的。
该函数将user构造一条错误信息，然后用C语言标准函数fprintf打印到stderr。
```c
// 调用者需保证入参user的长度被限制为256个字节或者更少
void IncorrectPassword(const char *user)
{
    int ret = -1;
    static const char msgFormat[] = "%s cannot be authenticated.\n";

    size_t len = strlen(user) + 1 + sizeof(msgFormat);
    char *msg = (char *)malloc(len);
    if (msg == NULL) {
        ... // 错误处理
    }

    ret = snprintf_s(msg, len, len - 1, msgFormat, user);
    if (ret == -1) {
        ... // 错误处理
    } else {
        fprintf(stderr, msg); // msg中有来自未验证的外部数据，存在格式化字符串漏洞
    }

    free(msg);
}
```
示例代码中首先计算了消息的长度，然后分配内存，接着利用`snprintf_s()`函数拼接了消息内容。因此消息内容中包含了msgFormat的内容和用户的内容。
当入参user中含有用户输入的格式符（如`%s,%p,%n`等）后，`fprintf()`在执行时，会将msg作为一个格式化字符串来进行解析，而不是直接输出消息内容。
也就是说此时msg中的内容不会被直接打印到stderr中，反而会将一些未知的数据打印到stderr，引发程序产生未定义行为。这是一个非常严重的格式化字符串漏洞。

**【正例】**
下面是第一种推荐做法，代码中使用fputs()来代替fprintf()函数，fputs()会直接将msg的内容输出到stderr中，而不会去解析它。
```c
// 入参user的长度被限制为256个字节或者更少
void IncorrectPassword(const char *user)
{
    int ret = -1;
    static const char msgFormat[] = "%s cannot be authenticated.\n";

    // 这里加法运算不会整数溢出，因为user有限制
    size_t len = strlen(user) + 1 + sizeof(msgFormat);
    char *msg = (char *)malloc(len);
    if (msg == NULL) {
        ... // 错误处理
    }

    ret = snprintf_s(msg, len, len - 1, msgFormat, user);
    if (ret == -1) {
        ... // 错误处理
    } else {
        fputs(stderr, msg); // 使用fputs函数代替fprintf函数
    }
    free(msg);
}
```

**【正例】**
下面是第二种推荐做法，代码中将不受信任的用户输入user作为fprintf()的可选参数之一，用“%s”将user以字符串的形式固定下来，然后输出到stderr中，而不作为格式字符串的一部分，这样就消除了格式化字符串漏洞出现的可能性。
```c
void IncorrectPassword(const char *user)
{
    static const char msgFormat[] = "%s cannot be authenticated.\n";
    fprintf(stderr, msgFormat, user);
}
```

**【反例】**
如下代码示例中，使用了POSIX函数syslog()[IEEE Std 1003.1：2013]函数，但是syslog()函数也可能出现格式化字符串漏洞。
```c
void Foo(void)
{
    char *msg = GetMsg();
    ...
    syslog(LOG_INFO, msg); // 存在格式化字符串漏洞
}
```

**【正例】**
下面是推荐做法，代码中将不受信任的用户输入msg作为syslog()的可选参数之一，用“%s”将msg以字符串的形式固定下来，然后输出到系统日志中，而不作为格式字符串的一部分，这样就消除了格式化字符串漏洞出现的可能性。
```c
void Foo(void)
{
    static const char msgFormat[] = "%s cannot be authenticated.\n";
    char *msg = GetMsg();
    ...
    syslog(LOG_INFO, msgFormat, msg); // 这里没有格式化字符串漏洞
}
```

**【影响】**

如果格式串被外部可控，攻击者可以使进程崩溃、查看栈内容、查看内存内容或者在任意内存位置写入数据，进而以被攻击进程的权限执行任意代码。

## 7.2 调用格式化输入/输出函数时，使用有效的格式字符串

**【描述】**
格式化输入/输出函数（如fscanf()/fprintf()及相关函数）在format字符串控制下进行转换、格式化、打印其实参。

在创建格式化字符串时的常见错误包括：

- format中参数个数与实参个数不一致；
- 使用无效的转换指示符；
- 使用与转换指示符不兼容的标志字符；
- 使用与转换指示符不兼容的长度修饰符；
- format中转换指示符与实参类型不匹配；
- 使用实参指定宽度或者精度时，实参的类型不是int类型；

不要为格式化输入/输出函数提供未知的或者无效的转换规格，以及标志字符、精度、长度修饰符、转换指示符的无效组合。同样，不要提供与格式化字符串中的转换指示符类型不匹配的实参。这可能会使程序产生未定义行为。

**【反例】**
如下代码示例中，printf()的实参infoLevel类型与对应的转换指示符's'不匹配，正确的转换指示符要使用'd'。同样，实参infoMsg类型与对应的转换指示符'd'不匹配，正确的转换指示符要使用's'。
这些用法会使程序产生未定义行为，比如：printf()将把infoLevel实参解释为指针，试图从infoLevel包含的地址中读取一个字符串，从而发生非法访问。
```c
void Foo(void)
{
    const char *infoMsg = "Information seed to user.";
    int infoLevel = 3;

    ...

    printf("infoLevel: %s, infoMsg: %d\n", infoLevel, infoMsg);

    ...
}
```

**【正例】**
正确的做法是确保printf()函数的实参匹配format的转换指示符。
```c
void Foo(void)
{
    const char *infoMsg = "Information seed to user.";
    int infoLevel = 3;

    ...

    printf("infoLevel: %d, infoMsg: %s\n", infoLevel, infoMsg);

    ...
}
```

**【影响】**

错误的格式串可能造成内存破坏或者程序异常终止。

## 7.3 禁止使用realloc()函数

**【描述】**
realloc()是一个非常特殊的函数，原型如下：
```C
void *realloc(void *ptr, size_t size);
```
随着参数的不同，其行为也是不同：
 - 当ptr不为NULL，且size不为0时，该函数会重新调整内存大小，并将新的内存指针返回，并保证最小的size的内容不变；
 - 参数ptr为NULL，但size不为0，那么其行为等同于malloc(size)；
 - 参数size为0，则realloc的行为等同于free(ptr)。

由此可见，一个简单的C函数，却被赋予了3种行为，这不是一个设计良好的函数。虽然在编程中提供了一些便利性，如果认识不足，使用不当，是却极易引发各种bug。

**【反例】**
如下代码示例中，使用realloc不当导致内存泄漏。
代码中希望对ptr的空间进行扩充，当realloc()分配失败的时候，会返回NULL。但是参数中的ptr的内存是没有被释放的，如果直接将realloc()的返回值赋给ptr，那么ptr原来指向的内存就会丢失，造成内存泄漏

```C
// 当realloc()分配内存失败时会返回NULL，导致内存泄漏
char *ptr = (char *)realloc(ptr, NEW_SIZE);
if (ptr == NULL) {
  .. // 错误处理
}
```
**【正例】**
使用malloc()函数代替realloc()函数
```c
// 使用malloc()函数代替realloc()函数
char *newPtr = (char *)malloc(NEW_SIZE);
if (newPtr == NULL) {
  ... // 错误处理
}

errno_t ret = memcpy_s(newPtr, NEW_SIZE, oldPtr, oldSize);
... // 校验ret，确保安全函数执行成功

... // 返回前，释放oldPtr
```

## 7.4 禁止使用alloca()函数申请栈上内存
**【描述】**
POSIX和C99均未定义alloca()的行为，在有些平台下不支持该函数，使用alloca会降低程序的兼容性和可移植性，该函数在栈帧里申请内存，申请的大小很可能超过栈的边界，影响后续的代码执行。
请使用malloc从堆中动态分配内存。

** 【影响】**
程序栈的大小非常有限，如果分配导致栈溢出，则程序会产生未定义行为

## 7.5 禁止外部可控数据作为进程启动函数的参数
**【描述】**
本条款中进程启动函数包括system、popen、execl、execlp、execle、execv、execvp等。
system()、popen()等函数会创建一个新的进程，如果外部可控数据作为这些函数的参数，会导致注入漏洞。
使用execl()等函数执行新进程时，如果使用shell启动的新进程，则同样存在命令注入风险。
使用execlp()、execvp()、execvpe()函数依赖于系统的环境变量PATH来搜索程序路径，使用它们时应充分考虑外部环境变量的风险，或避免使用这些函数。
因此，总是优先考虑使用C标准函数实现需要的功能。如果确实需要使用这些函数，请使用白名单机制确保这些函数的参数不受任何外来数据的影响。 

**【反例】**
如下代码示例中，使用 system() 函数执行 cmd 命令串来自外部，攻击者可以执行任意命令：

```c
char *cmd = GetCmdFromRemote();
if (cmd == NULL) {
    ... // 处理错误
}
system(cmd);
```

如下代码示例中，使用 system() 函数执行 cmd 命令串的一部分来自外部，攻击者可能输入 'some dir;useradd xxx'字符串，创建一个xxx的用户：

```c
char cmd[MAX_LEN];
int ret = 0;
char *name = GetDirNameFromRemote();
if (name == NULL) {
    ... // 处理错误
}
...
ret = sprintf_s(cmd, sizeof(cmd), "ls %s", name)
...
system(cmd);
```

使用exec系列函数来避免命令注入时，注意exec系列函数中的path、file参数禁止使用命令解析器(如/bin/sh)。

```c
int execl(const char *path, const char *arg, ...);
int execlp(const char *file, const char *arg, ...);
int execle(const char *path, const char *arg, ...);
int execv(const char *path, char *const argv[]);
int execvp(const char *file, char *const argv[]);
int execvpe(const char *file, char *const argv[], char *const envp[]);
```

例如，禁止如下使用方式：

```c
char *cmd = GetDirNameFromRemote();
execl("/bin/sh", "sh", "-c", cmd, NULL);
```

**【反例】**  (使用库函数)

在Linux下的部分命令有对应的库函数，或者可以通过少量的编码来避免使用system函数调用命令，如mkdir()函数可以实现mkdir命令的功能。下面实现了一个文件内容拷贝的功能，不必要的通过system函数调用了系统 cat 命令。
```c
int WriteDataToFile(const char *dstFile, const char *srcFile)
{
    ...
    ...  // 入参的合法性检查
    ret = sprintf_s(buffer, bufLen, "cat %s > %s", context, filepath);
    if (ret < 0) {
        ...
    }
    system(buffer);
    ...
}
```

**【正例】**  (使用库函数)

一些命令功能可以通过少量的编码来实现。如下代码实现了文件拷贝的功能，避免了对 cat 或 cp 命令的调用。需要注意的是，为简化描述，下面代码未考虑信号中断的影响。

```c
#define OPEN_SRC_FILE_ERROR -1
#define OPEN_DST_FILE_ERROR -2
#define WRITE_DST_FILE_ERROR -3
#define READ_DST_FILE_ERROR -4

int WriteDataToFile(const char *dstFile, const char *srcFile)
{
    int ret = 0;
    ssize_t len = 0;
    unsigned char buf[MAX_BUF_SIZE];

    ... // 入参的合法性检查
    int sourceFile = open(srcFile, O_RDONLY);
    if (sourceFile < 0) {
        return OPEN_SRC_FILE_ERROR;
    }
    int destFile = open(dstFile,
                        O_CREAT | O_WRONLY | O_EXCL,
                        S_IRUSR | S_IWUSR);
    if (destFile <0) {
        ret = OPEN_DST_FILE_ERROR;
        goto EXIT2;
    }
    while ((len = read(sourceFile, buf, sizeof(buf))) > 0) {
        if (write(destFile, buf, (size_t)len) != len) {
            ret = WRITE_DST_FILE_ERROR;
            goto EXIT1;
        }
    }
    if (len < 0) {
        ret = READ_DST_FILE_ERROR;
    }
EXIT1:
    close(destFile);
EXIT2:
    close(sourceFile);
    return ret;
}
```

**【正例】**  (使用exec系列函数)
可以通过库函数简单实现的功能（如上例），需要避免调用命令处理器来执行外部命令。如果确实需要调用单个命令，应使用exec*函数来实现参数化调用，并对调用的命令实施白名单管理。应避免使用execlp、execvp、execvpe函数。

```c
pid_t pid;
char * const envp[] = { NULL };
...
char *filename = GetDirNameFromRemote();
if (filename == NULL) {
    ... // 处理错误
}
...
if ((pid = fork()) < 0) {
    ...
} else if (pid == 0) {
    // 使用some_tool对指定文件进行加工
    execle("/bin/some_tool", "some_tool", filename, NULL, envp);
    _Exit(-1);
}
...
int status;
waitpid(pid, &status, 0);
FILE *fp = fopen(filename, "r");
...
```
此时，外部输入的filename仅作为some_tool命令的参数，没有命令注入的风险。

**【正例】**  (使用白名单)

对输入的文件名基于合理的白名单检查，避免命令注入。
```c
char *cmd = GetCmdFromRemote();
if (cmd == NULL) {
    ... // 处理错误
}

// 使用白名单检查命令是否合法，仅允许"some_tool_a", "some_tool_b"命令，外部无法随意控制
if (!IsValidCmd(cmd)) {
    ... // 处理错误
}
system(cmd);
...
```

**【影响】**

如果传递给system()、popen()或其他命令处理器函数的命令字符串是外部可控的，则攻击者可能会以被攻击进程的权限执行系统上存在的任意命令。


**【相关软件CWE编号】**  CWE-676，CWE-88

## 7.6 禁止外部可控数据作为dlopen等模块加载函数的参数
**【建议】**
建议使用pg_dlopen代替dlopen。

**【描述】**
这些函数会加载外部模块，如果外部可控数据作为这些函数的参数，有可能会加载攻击者事先预制的模块。

如果要使用这些函数，可以采用如下措施之一：
- 使用白名单机制，确保这些函数的参数不受任何外来数据的影响；
- 使用数字签名机制保护要加载的模块，充分保证其完整性；
- 在设备本地加载的动态库通过权限与访问控制措施保证了本身安全性后，通过特定目录自动被程序加载；
- 在设备本地的配置文件通过权限与访问控制措施保证了本身安全性后，自动加载配置文件中指定的动态库。

**【反例】**
以下代码从外部获取数据后直接作为LoadLibrary函数的参数，有可能导致程序被植入木马。

```c
char *msg = GetMsgFromRemote();
LoadLibrary(msg);
```

**【影响】**

如果动态库文件是外部可控的，则攻击者可替换该库文件，在某些情况下可以造成任意代码执行漏洞。

## 7.7 禁止直接使用外部数据拼接SQL命令
**【描述】**
SQL注入是指SQL查询被恶意更改成一个与程序预期完全不同的查询。执行更改后的查询可能会导致信息泄露或者数据被篡改。而SQL注入的根源就是使用外部数据来拼接SQL语句。C/C++语言中常见的使用外部数据拼接SQL语句的场景有（包括但不局限于）：

- 连接MySQL时调用mysql_query()，Execute()时的入参
- 连接SQL Server时调用db-library驱动的dbsqlexec()的入参
- 调用ODBC驱动的SQLprepare()连接数据库时的SQL语句的参数
- C++程序调用OTL类库中的otl_stream()，otl_column_desc()时的入参
- C++程序连接Oracle数据库时调用ExecuteWithResSQL()的入参

防止SQL注入的方法主要有以下几种：

- 参数化查询（通常也叫作预处理语句）：参数化查询是一种简单有效的防止SQL注入的查询方式，应该被优先考虑使用。支持的数据库有MySQL，Oracle（OCI）。
- 参数化查询（通过ODBC驱动）：支持ODBC驱动参数化查询的数据库有Oracle、SQLServer、PostgreSQL和GaussDB。
- 对外部数据进行校验（对于每个引入的外部数据推荐“白名单”校验）。
- 对外部数据中的SQL特殊字符进行转义。

**【反例】**
下列代码拼接用户输入，没有进行输入检查，存在SQL注入风险：

```c
char name[NAME_MAX];
char sqlStatements[SQL_CMD_MAX];
int ret = GetUserInput(name, NAME_MAX);
...
ret = sprintf_s(sqlStatements,
                SQL_CMD_MAX,
                "SELECT childinfo FROM children WHERE name= ‘%s’",
                name);
...
ret = mysql_query(&myConnection, sqlStatements);
...
```

**【正例】**
使用预处理语句进行参数化查询可以防御SQL注入攻击：

```c
char name[NAME_MAX];
...
MYSQL_STMT *stmt = mysql_stmt_init(myConnection);
char *query = "SELECT childinfo FROM children WHERE name= ?";
if (mysql_stmt_prepare(stmt, query, strlen(query))) {
    ...
}
int ret = GetUserInput(name, NAME_MAX);
...
MYSQL_BIND params[1];
(void)memset_s(params, sizeof(params), 0, sizeof(params));
...
params[0].bufferType = MYSQL_TYPE_STRING;
params[0].buffer = (char *)name;
params[0].bufferLength = strlen(name);
params[0].isNull = 0;

bool isCompleted = mysql_stmt_bind_param(stmt, params);
...
ret = mysql_stmt_execute(stmt);
...
```

**【影响】**

如果拼接SQL语句的字符串是外部可控的，则攻击者可以通过注入特定的字符串欺骗程序执行恶意的SQL命令，造成信息泄露、权限绕过、数据被篡改等问题。

## 7.8 在信号处理例程中调用非异步安全函数
**【描述】**
在信号处理程序中只调用异步安全函数：

> 如果信号是通过调用abort()或raise()函数产生的，则信号处理程序中不能调用raise()函数。
> 如果信号不是通过调用abort()或raise()函数产生的，则如下情况，程序的行为是未定义的：
> ...信号处理程序中调用了标准库中除abort()函数，_Exit()函数，quick_exit()函数、 signal()函数(其第一个参数等于触发信号处理程序的信号编号)之外的任何函数，...。

除了C语言标准函数以外，其他系统函数也提供了一些的异步安全函数，在信号处理程序中使用这些函数之前，应确保调用的函数在所有可能的执行环境下均是异步安全的。

**【反例】**
如下代码示例中，信号处理函数中调用了非异步安全函数printf()：

```c
void Handler(int num)
{
    printf("receive signal = %d\n", SIGINT);
}
int main(int argc, char **argv)
{
    if (signal(SIGINT, Handler) == SIG_ERR) {
        ... // 错误处理
    }
    while (true) {
        ... // 程序主循环代码
    }
    return 0;
}
```
**【正例】**
如下代码示例中，尽量不在信号处理函数中调用其他函数，仅在信号处理程序中修改volatile sig_atomic_t的变量：

```c
static volatile sig_atomic_t g_flag = 0;

void Handler(int num)
{
    g_flag = 1;
}

int main(int argc, char **argv)
{
    if (signal(SIGINT, Handler) == SIG_ERR) {
        ... // 错误处理
    }
    while (true) {
        if (g_flag != 0) {
            printf("receive signal = %d\n", SIGINT);
        }
        ... // 程序主循环代码
    }
    ...
    return 0;
}
```

**【影响】**

从信号处理函数中调用非异步信号安全函数会导致程序产生未定义行为。


**【相关软件CWE编号】** CWE-479

## 7.9 使用库函数时避免竞争条件
**【描述】**
C语言标准库中有一些函数是不可重入函数。不可重入函数在多线程环境下的执行结果可能不能达到预期效果，需谨慎使用。
例如strtok()和asctime()等函数对每个进程返回一个保存结果的指针，保存在函数分配的内存中。另外的一些函数（如rand()）会基于每个进程在该函数分配的内存中存储状态信息。多个线程调用相同函数可能造成条件竞争问题，这种问题常常导致异常行为，并且可能造成更严重的漏洞，例如异常终止、拒绝服务攻击、损害数据完整性。

下表中左列给出的库函数是不可重入的，在多线程中被调用时，可能存在条件竞争问题。

| 函数 | 安全措施 |
| :-- | :-- |
| rand(), srand() | 禁用rand函数产生用于安全用途的伪随机数 |
| getenv(), getenv_s() | 不要存储函数返回的指针 |
| strtok() | C11标准中的strtok_s()， POSIX中的strtok_r() |
| strerror() | C11标准中的strerror_s()，POSIX中的strerror_r() |
| asctime(), ctime(), localtime(), gmtime() | C11标准中的asctime_s(), ctime_s(), localtime_s(), gmtime_s() |
| setlocale() | 使用互斥方法保护区域特定API的多线程访问 |
| ATOMIC_VAR_INIT, atomic_init() | 不要试图从多个线程初始化一个原子变量 |
| tmpnam() | C11标准中的tmpnam_s()，POSIX中的tmpnam_r() |
| mbrtoc16(), c16rtomb(), mbrtoc32(), c32rtomb() | 调用时，参数mbstate_t *禁止为空 |

另外还需要注意的函数有：gethostbyaddr(), gethostbyname(), inet_ntoa(), localeconv()等。
localeconv()返回的结果必须视为const限定类型，或者返回的指针所指向对象状态需要立即使用，重复调用函数会导致保存的状态丢失。

**【反例】**
以下示例是期望通过getenv函数取到TABLE_WX_ENV和TABLE_BD_ENV两个环境变量的值，再判断这两个值是否相同。
但是由于getenv()是不可重入的，在第二次调用该函数时，可能会覆盖上一次取到的字符串内容，所以即使两个环境变量是不同的，但是最终两者wxEnv和bdEnv在比较时可能相同。

```c
void Foo(void)
{
    char *wxEnv = NULL;
    char *bdEnv = NULL;

    wxEnv = getenv("TABLE_WX_ENV");
    if (wxEnv == NULL) {
        ... // 错误处理
        return;
    }

    bdEnv = getenv("TABLE_BD_ENV");
    if (bdEnv == NULL) {
        ... // 错误处理
        return;
    }

    if (strcmp(wxEnv, bdEnv) == 0) {
        printf("They are the same.\n");
    } else {
        printf("They are NOT the same.\n");
    }
}
```

**【正例】**
如下代码示例中，先将取到的环境变量保存起来，然后再比较。

```c
// 函数是将srcEnv复制一份。成功返回复制之后的首地址，失败返回NULL
char *TransEnv(const char *srcEnv)
{
    ... // 具体实现略。strcpy_s...
}

void Function(void)
{
    char *wxEnv = NULL;
    char *bdEnv = NULL;

    const char *envVal = getenv("TABLE_WX_ENV");
    if (envVal != NULL) {
        wxEnv = TransEnv(envVal);
        ... // 判断TransEnv函数返回值并正确处理异常
    } else {
        ... // 错误处理
        return;
    }

    envVal = getenv("TABLE_BD_ENV");
    if (envVal != NULL) {
        bdEnv = TransEnv(envVal);
        ... // 判断TransEnv函数返回值并正确处理异常
    } else {
        ... // 错误处理
        return;
    }

    // 比较两个副本
    if (strcmp(wxEnv, bdEnv) == 0) {
        printf("They are the same.\n");
    } else {
        printf("They are NOT the same.\n");
    }

    ... // 退出前清理资源
}
```

**【影响】**

多线程调用同一个库函数引起的条件竞争可能导致应用程序异常终止、损害数据完整性或者造成拒绝服务。


**【相关软件CWE编号】** CWE-330，CWE-377，CWE-676

# 8 文件
## 8.1 创建文件时必须显式指定合适的文件访问权限
**【描述】**
创建文件时，如果不显式指定合适访问权限，可能会让未经授权的用户访问该文件，造成信息泄露，文件数据被篡改，文件中被注入恶意代码等风险。

虽然文件的访问权限也依赖于文件系统，但是当前许多文件创建函数（例如POSIX open函数）都具有设置（或影响）文件访问权限的功能，所以当使用这些函数创建文件时，必须显式指定合适的文件访问权限，以防止意外访问。

**【反例】**
使用POSIX open()函数创建文件但未显示指定该文件的访问权限，可能会导致文件创建时具有过高的访问权限。这可能会导致漏洞（例如CVE-2006-1174）。

```c
void Foo(void)
{
    int fd = -1;
    char *filename = NULL;

    ... // 初始化 filename

    fd = open(filename, O_CREAT | O_WRONLY); // 没有显式指定访问权限
    if (fd == -1) {
        ... // 错误处理
    }
    ...
}
```

**【正例】**
应该在open的第三个参数中显式指定新创建文件的访问权限。可以根据文件实际的应用情况设置何种访问权限。

```c
void Foo(void)
{
    int fd = -1;
    char *filename = NULL;

    ... // 初始化 filename 和指定其访问权限

    // 此处根据文件实际需要，显式指定其访问权限
    int fd = open(filename, O_CREAT | O_WRONLY, S_IRUSR | S_IWUSR);
    if (fd == -1) {
        ... // 错误处理
    }
    ...
}
```

**【影响】**

创建访问权限弱的文件，可能会导致对这些文件的非法访问。

## 8.2 外部文件路径使用前必须进行规范化并校验
**【描述】**
当文件路径来自外部数据时，必须对其做合法性校验，如果不校验，可能造成系统文件的被任意访问。但是禁止直接对其进行校验，正确做法是在校验之前必须对其进行路径规范化处理。这是因为同一个文件可以通过多种形式的路径来描述和引用，例如既可以是绝对路径，也可以是相对路径；而且路径名、目录名和文件名可能包含使校验变得困难和不准确的字符（如：“.”、“..”）。此外，文件还可以是符号链接，这进一步模糊了文件的实际位置或标识，增加了校验的难度和校验准确性。所以必须先将文件路径规范化，从而更容易校验其路径、目录或文件名，增加校验准确性。

因为规范化机制在不同的操作系统和文件系统之间可能有所不同，所以最好使用符合当前系统特性的规范化机制。例如：在linux下，使用realpath函数，在windows下，使用PathCanonicalize函数进行文件路径的规范化。

一个简单的案例说明如下：

```c
当文件路径来自外部数据时，需要先将文件路径规范化，如果没有作规范化处理，攻击者就有机会通过恶意构造文件路径进行文件的越权访问。
例如，攻击者可以构造“../../../etc/passwd”的方式进行任意文件访问。
```

**【反例】**
在此错误的示例中，inputFilename包含一个源于受污染源的文件名，并且该文件名已打开以进行写入。在使用此文件名操作之前，应该对其进行验证，以确保它引用的是预期的有效文件。
不幸的是，inputFilename引用的文件名可能包含特殊字符，例如目录字符，这使验证变得困难，甚至不可能。而且，inputFilename中可能包含可以指向任意文件路径的符号链接，即使该文件名通过了验证，也会导致该文件名是无效的。
这种场景下，对文件名的直接验证即使被执行也是得不到预期的结果，对fopen()的调用可能会导致访问一个意外的文件。

```c
...

if (!verify_file(inputFilename) {    // 没有对inputFilename做规范化，直接做校验
    ... // 错误处理
}

if (fopen(inputFilename, "w") == NULL) {
    ... // 错误处理
}

...
```

**【正例】**
规范化文件名是具有一定难度的，因为这需要了解底层文件系统。
POSIX realpath()函数可以帮助将路径名转换为规范形式。
- 该realpath()函数应从所指向的路径名派生一个filename的绝对路径名，两者指向同一文件，绝对路径其文件名不涉及“ .”，“ ..”或符号链接。
在规范化路径之后，还必须执行进一步的验证，例如确保两个连续的斜杠或特殊文件不会出现在文件名中。有关如何执行路径名解析的更多详细信息。
使用realpath()函数有许多需要注意的地方，详细可以翻看Linux程序员手册。
在了解了以上原理之后，对上面的错误代码示例，我们采用如下解决方案：

```c
char *realpathRes = NULL;

...

// 在校验之前，先对inputFilename做规范化处理
realpathRes = realpath(inputFilename, NULL);
if (realpathRes == NULL) {
    ... // 规范化的错误处理
}

// 规范化以后对路径进行校验
if (!verify_file(realpathRes) {
    ... // 校验的错误处理
}

// 使用
if (fopen(realpathRes, "w") == NULL) {
    ... // 实际操作的错误处理
}

...

free(realpathRes);
realpathRes = NULL;
...
```

**【正例】**
根据我们的实际场景，我们还可以采用的第二套解决方案，说明如下：
如果`PATH_MAX`被定义为 limits.h 中的一个常量，那么使用非空的`resolved_path`调用realpath()也是安全的。
在本例中realpath()函数期望`resolved_path`引用一个字符数组，该字符数组足够大，可以容纳规范化的路径。
如果定义了PATH_MAX，则分配一个大小为`PATH_MAX`的缓冲区来保存realpath()的结果。正确代码示例如下：

```c
char *realpathRes = NULL;
char *canonicalFilename = NULL;
size_t pathSize = 0;

...

pathSize = (size_t)PATH_MAX;

if (VerifyPathSize(pathSize) == true) {
    canonicalFilename = (char *)malloc(pathSize);

    if (canonicalFilename == NULL) {
        ... // 错误处理
    }

    realpathRes = realpath(inputFilename, canonicalFilename);
}

if (realpathRes == NULL) {
    ... // 错误处理
}

if (VerifyFile(realpathRes) == false) {
    ... // 错误处理
}

if (fopen(realpathRes, "w") == NULL ) {
    ... // 错误处理
}

...

free(canonicalFilename);
canonicalFilename = NULL;
...
```

**【反例】**
下面的代码场景是从外部获取到文件名称，拼接成文件路径后，直接对文件内容进行读取，导致攻击者可以读取到任意文件的内容：

```c
char *filename = GetMsgFromRemote();
...
int ret = sprintf_s(untrustPath, sizeof(untrustPath), "/tmp/%s", filename);
...
char *text = ReadFileContent(untrustPath);
```

**【正例】**
正确的做法是，对路径进行规范化后，再判断路径是否是本程序所认为的合法的路径：

```c
char *filename = GetMsgFromRemote();
...
sprintf_s(untrustPath, sizeof(untrustPath), "/tmp/%s", filename);
char path[PATH_MAX];
if (realpath(untrustPath, path) == NULL) {
    ... // 处理错误
}
if (!IsValidPath(path)) {    // 检查文件的位置是否正确
    ... // 处理错误
}
char *text = ReadFileContent(path);
```

**【例外】**

运行于控制台的命令行程序，通过控制台手工输入文件路径，可以作为本条款例外。

```c
int main(int argc, char **argv)
{
    int fd = -1;

    if (argc == 2) {
        fd = open(argv[1], O_RDONLY);
        ...
    }

    ...
    return 0;
}

```

**【影响】**

未对不可信的文件路径进行规范化和校验，可能造成对任意文件的访问。

## 8.3 不要在共享目录中创建临时文件
**【描述】**
共享目录是指其它非特权用户可以访问的目录。程序的临时文件应当是程序自身独享的，任何将自身临时文件置于共享目录的做法，将导致其他共享用户获得该程序的额外信息，产生信息泄露。因此，不要在任何共享目录创建仅由程序自身使用的临时文件。

临时文件通常用于辅助保存不能驻留在内存中的数据或存储临时的数据，也可用作进程间通信的一种手段（通过文件系统传输数据）。例如，一个进程在共享目录中创建一个临时文件，该文件名可能使用了众所周知的名称或者一个临时的名称，然后就可以通过该文件在进程间共享信息。这种通过在共享目录中创建临时文件的方法实现进程间共享的做法很危险，因为共享目录中的这些文件很容易被攻击者劫持或操纵。这里有几种缓解策略：

1. 使用其他低级IPC（进程间通信）机制，例如套接字或共享内存。
2. 使用更高级别的IPC机制，例如远程过程调用。
3. 使用仅能由程序本身访问的安全目录(多线程/进程下注意防止条件竞争)。

同时，下面列出了几项临时文件创建使用的方法，产品根据具体场景执行以下一项或者几项，同时产品也可以自定义合适的方法。

1. 文件必须具有合适的权限，只有符合权限的用户才能访问
2. 创建的文件名是唯一的、或不可预测的
3. 仅当文件不存在时才创建打开(原子创建打开)
4. 使用独占访问打开，避免竞争条件
5. 在程序退出之前移除

同时也需要注意到，当某个目录被开放读/写权限给多个用户或者一组用户时，该共享目录潜在的安全风险远远大于访问该目录中临时文件这个功能的本身。

在共享目录中创建临时文件很容易受到威胁。例如，用于本地挂载的文件系统的代码在与远程挂载的文件系统一起共享使用时可能会受到攻击。安全的解决方案是不要在共享目录中创建临时文件。

**【反例】**
如下代码示例，程序在Linux系统的共享目录/tmp下创建临时文件来保存临时数据，且文件名是硬编码的。
由于文件名是硬编码的，因此是可预测的，攻击者只需用符号链接替换文件，然后链接所引用的目标文件就会被打开并写入新内容。

```c
void ProcData(const char *filename)
{
    FILE *fp = fopen(filename, "wb+");
    if (fp == NULL) {
        ... // 错误处理
    }

    ... // 写文件

    fclose(fp);
}

int main(void)
{
    // 不符合：1.在系统共享目录中创建临时文件；2.临时文件名硬编码
    char *pFile = "/tmp/data";
    ...

    ProcData(pFile);

    ...
    return 0;
}
```

**【正确案例】**
```c
Linux下的/tmp目录是一个所有用户都可以访问的共享目录，不应在该目录下创建仅由程序自身使用的临时文件。
```

**【影响】**

不安全的创建临时文件，可能导致文件非法访问，并造成本地系统上的权限提升。


**【业界典型漏洞】**CVE-2004-2502

# 9 内存
## 9.1 内存申请前，必须对申请内存大小进行合法性校验
**【描述】**
当申请内存大小由程序外部输入时，内存申请前，要求对申请内存大小进行合法性校验，防止申请0长度内存，或过多地、非法地申请内存。
当申请内存的数值过大（可能一次就申请了非常大的超预期的内存；也可能循环中多次申请内存），很可能会造成非预期的资源耗尽。
当请求的大小为0时，内存分配函数如malloc()，calloc()的行为由实现定义。此外，当请求0字节时，如果分配成功，则所分配的大小是未指定的（C11）。此时，在内存分配函数返回非空指针的情况下，对分配内存区域的读写将导致程序产生未定义行为。
大小不正确的参数、不当的范围检查、整数溢出或者截断都可能造成实际分配的缓冲区不符合预期。如果申请内存受攻击者控制，还可能会发生缓冲区溢出等安全问题。

**【反例】**
场景1：申请内存大小的变量可以明确值为0
- 错误示例：

```c++
#include <stdlib.h>
#include <stdint.h>

int32_t GetZero()
{
    return 0;
}

void TestBadCase01()
{
    int32_t size = GetZero();
    // POTENTIAL FLAW: 内存大小值一定为0
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void TestBadCase02()
{
    int32_t size = 0;
    // POTENTIAL FLAW:  内存大小值一定为0
    char *msg = (char *)malloc(size++);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void TestBadCase03()
{
    int32_t size = 0;
    // POTENTIAL FLAW: 内存大小值一定为0
    char *msg = new char[size];
    ...
    delete[] msg;
}

void TestBadCase4()
{
    int32_t size = 0;
    // POTENTIAL FLAW: 内存大小值一定为0
    char *msg = new char[size++];
    ...
    delete[] msg;
}
```
场景2：申请内存大小的变量无法明确值为0时。

本场景需在202209版本及以后需要模型中，配置checkSizeNotVerify项，开启enable="true"，才会检查。
在202206版本及以前会默认检查。
- 错误示例：

```c++
#include <stdlib.h>
#include <stdint.h>

void TestBadCase01(int32_t size)
{
    // POTENTIAL FLAW: 没有对内存大小变量的值进行校验
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void TestBadCase02()
{
    int32_t size = (rand() - 1) * 1000;
    // POTENTIAL FLAW: 没有对内存大小变量的值进行校验
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void FuncRet(int32_t *p);

// 申请大小为入参引用
void TestBadCase03()
{
    int32_t size = 10;
    FuncRet(&size);
    // POTENTIAL FLAW: 没有对内存大小变量的值进行校验
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

// 使用new申请内存未经校验
void TestBadCase04()
{
    int32_t size = 10;
    FuncRet(&size);
    // POTENTIAL FLAW: 没有对内存大小变量的值进行校验
    char *msg = new char[size];
    ...
    delete[] msg;
}
```

**【正例】**
场景1：申请内存大小的变量可以明确值为0
- 修复示例1：变量需要在使用前，判断值大于0，并小于一个符合业务预期的最大可申请内存值。

```c++
#include <stdlib.h>
#include <stdint.h>

int32_t GetZero()
{
    return 0;
}

void TestGoodCase01()
{
    int32_t size = GetZero();
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void TestGoodCase02()
{
    int32_t size = 0;
    size++;
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void TestGoodCase03()
{
    int32_t size = 0;
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = new char[size];
    ...
    delete[] msg;
}

void TestGoodCase4()
{
    int32_t size = 0;
    size++;
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = new char[size];
    ...
    delete[] msg;
}
```
场景2：申请内存大小的变量无法明确值为0时。

- 修复示例1：变量需要在使用前，判断值大于0，并小于一个符合业务预期的最大可申请内存值。

```c++
#include <stdlib.h>
#include <stdint.h>

void TestGoodCase01(int32_t size)
{
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void TestGoodCase02()
{
    int32_t size = (rand() - 1) * 1000;
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

void FuncRet(int32_t *p);

// 申请大小为入参引用
void TestGoodCase03()
{
    int32_t size = 10;
    FuncRet(&size);
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = (char *)malloc(size);
    if (msg == nullptr) {
        return;
    }
    ...
    free(msg);
}

// 使用new申请内存未经校验
void TestGoodCase04()
{
    int32_t size = 10;
    FuncRet(&size);
    // POTENTIAL FLAW GOOD: 申请的大小经过校验
    if (size <= 0 || size > FOO_MAX_LEN) {
        return;
    }
    char *msg = new char[size];
    ...
    delete[] msg;
}
```

## 9.2 内存分配后必须判断是否成功
**【描述】**
内存分配一旦失败，那么后续的操作会导致程序产生未定义行为的风险。比如malloc申请失败返回了空指针，对空指针的解引用会导致程序产生未定义行为。
内存申请函数返回的指针变量未判空场景，包括局部变量，成员变量，全局变量等。内存申请操作包括malloc、kmalloc等。

**【反例】**
场景1：使用malloc函数返回未判空。
- 错误示例：
```c
void NotCheckMemoryAllocRetcase01Bad01()
{
    /* 申请内存 */
    char *tmp = (char *)malloc(sizeof(char) * NUM);
    for (int i = 0; i < NUM; i++) {
        tmp[i] = 0;
    }
    free(tmp);
    /* POTENTIAL FLAW: 内存申请之后未判断是否成功 */
}
```
场景2：全局变量申请内存未判空。
- 错误示例：
```c
struct Flts {
    unsigned char uc1;
    unsigned char uc2;
    unsigned short us;
    unsigned char arr[100];
} Fts;

extern void *VOSMemAlloc(int h, int uc, int size);
extern void VOSMemFree(void *p);
Flts *gf1 = NULL;
Flts *gf2 = NULL;

void NotCheckGlobalMemoryAllocRetcase01Bad01(int size)
{
    gf1 = (Flts *)VOSMemAlloc(1, 1, size);
    gf2 = (Flts *)VOSMemAlloc(1, 1, size);
    if (gf1 == NULL) {
        /* POTENTIAL FLAW: gf2未判空，需要在模型中打开"report_global"选项才能告警 */
        return;
    }
}
```

**【正例】**
场景1：使用malloc函数返回未判空。
- 修复示例：
```c
void NotCheckMemoryAllocRetcase01Good01()
{
    char *tmp = (char *)malloc(sizeof(char) * NUM);
    /* POTENTIAL FLAW GOOD: 内存申请之后立刻判断是否成功 */
    if (tmp != NULL) {
        for (int i = 0; i < NUM; i++) {
            tmp[i] = 0;
        }
        free(tmp);
    }
}
```
场景2：全局变量申请内存未判空。
- 修复示例：
```c
struct Flts {
    unsigned char uc1;
    unsigned char uc2;
    unsigned short us;
    unsigned char arr[100];
} Fts;

extern void *VOSMemAlloc(int h, int uc, int size);
extern void VOSMemFree(void *p);
Flts *gf1 = NULL;
Flts *gf2 = NULL;

void NotCheckGlobalMemoryAllocRetcase01Good01(int size)
{
    gf1 = (Flts *)VOSMemAlloc(1, 1, size);
    if (gf1 == NULL) {
        return;
    }
    gf2 = (Flts *)VOSMemAlloc(1, 1, size);
    /* POTENTIAL FLAW GOOD:  */
    if (gf2 == NULL) {
        VOSMemFree(gf1);
        gf1 = NULL;
        return;
    }
}
```

## 9.3 外部输入作为内存操作相关函数的复制长度时，需要校验其合法性
**【描述】**
将数据复制到容量不足以容纳该数据的内存中会导致缓冲区溢出。为了防止此类错误，必须根据目标容量的大小限制被复制的数据大小，或者必须确保目标容量足够大以容纳要复制的数据。典型的内存操作相关的函数（例如memcpy_s、memmove_s等），如果复制长度来自外部数据，则必须校验其合法性，否则容易导致内存溢出。

**【反例】**
```cpp
typedef struct {
    size_t count;
    int val[MAX_NUMBERS];
} ValueTable;

ValueTable *ValueTableDup(const ValueTable *inputTable)
{
    ValueTable *OutputTable = ... // 分配内存
    ...
    for (size_t i = 0; i < inputTable->count; i++) {
        OutputTable ->val[i] = inputTable->val[i];
    }
    ...
}
```

**【正例】**
```cpp
typedef struct {
    size_t count;
    int val[MAX_NUMBERS];
} ValueTable;

ValueTable *ValueTableDup(const ValueTable *inputTable)
{
    ValueTable *OutputTable = ... // 分配内存
    ...
    /*
     * 根据应用场景，对来自外部报文的循环长度inputTable->count
     * 与OutputTable->val数组大小做校验，避免造成缓冲区溢出
     */
    if (inputTable->count >
        sizeof(OutputTable->val) / sizeof(OutputTable->val[0]) {
        return NULL;
    }
    for (size_t i = 0; i < inputTable->count; i++) {
        OutputTable ->val[i] = inputTable->val[i];
    }
    ...
}
```

## 9.4 内存中的敏感信息使用完毕后立即清0
**【描述】**
内存中的口令、密钥等敏感信息使用完毕后立即清0，避免被攻击者获取或者无意间泄露给低权限用户。这里所说的内存包括但不限于：
- 动态分配的内存
- 静态分配的内存
- 自动分配（堆栈）内存
- 内存缓存
- 磁盘缓存

通常内存在释放前不需要清除内存数据，因为这样在运行时会增加额外开销，所以在这段内存被释放之后，之前的数据还是会保留在其中。如果这段内存中的数据包含敏感信息，则可能会意外泄露敏感信息。为了防止敏感信息泄露，必须先清除内存中的敏感信息，然后再释放。

**【反例】**
在如下代码示例中，存储在所引用的动态内存中的敏感信息secret被复制到新动态分配的缓冲区newSecret，最终通过free()释放。因为释放前未清除这块内存数据，这块内存可能被重新分配到程序的另一部分，之前存储在newSecret中的敏感信息可能会无意中被泄露。

```C
char *secret = NULL;
/*
* 假设 secret 指向敏感信息，敏感信息的内容是长度小于SIZE_MAX个字符，
* 并且以null终止的字节字符串
*/
size_t size = strlen(secret);
char *newSecret = NULL;
newSecret = (char *)malloc(size + 1);
if (newSecret == NULL) {
    ... // 错误处理
} else {
    errno_t ret = strcpy_s(newSecret, size + 1, secret);
    ... // 处理 ret
    ... // 处理 newSecret...
    free(newSecret);
    newSecret = NULL;
}
...
```

**【正例】**
如下代码示例中，为了防止信息泄露，应先清除包含敏感信息的动态内存（用'\0'字符填充空间），然后再释放它。
```c
char *secret = NULL;
/*
* 假设 secret 指向敏感信息，敏感信息的内容是长度小于SIZE_MAX个字符，
* 并且以null终止的字节字符串
*/
size_t size = strlen(secret);
char *newSecret = NULL;
newSecret = (char *)malloc(size + 1);
if (newSecret == NULL) {
    ... // 错误处理
} else {
    errno_t ret = strcpy_s(newSecret, size + 1, secret);
    ... // 处理 ret

    ... // 处理 newSecret...

    (void)memset_s(newSecret, size + 1, 0, size + 1);
    free(newSecret);
    newSecret = NULL;
}
```

## 9.5 不要访问已释放的内存
**【描述】**
如果指针所指向的内存空间已被释放，那么再次使用这些指针值会导致程序产生未定义行为（如解引用已释放内存的指针，将这些指针作为free()函数的参数再次进行释放等）。
再次使用已释放内存的指针，可能因访问无效内存造成程序崩溃，在精心构造的条件下，还可能因破坏内存的管理机制造成恶意代码执行的问题。
建议：
1、不要访问已释放的内存
2、不要重复释放内存

**【反例】**
如下代码，data释放以后，由于程序逻辑设计疏忽，在后面的代码中再次使用了其成员ctx，造成错误。

```cpp
...
struct MemCb *data = NULL;
...  // 初始化data结构
if (DealingData(data) == NULL) { // 处理data数据
    DBG_LOG("Dealing Data Error");
    free(data);
}
...
ret = ProcessMemCbCtx(data->ctx); // 错误引用了已释放内存data中的成员
...
```

**【正例】**
如下代码在指针释放后，将其重置为空值，并在使用前进行了判空，避免了使用已释放内存的问题。
```cpp
...
struct MemCb *data = NULL;
...  // 分配并初始化data结构
if (DealingData(data) == NULL) { // 处理data数据，如果失败，则释放data内存
    DBG_LOG("Dealing Data Error");
    free(data);
    data = NULL;  // 释放data内存后，将NULL赋给指针
}
...
if (data != NULL) { // 使用data及其成员前，先判断指针是否为NULL
    ret = ProcessMemCbCtx(data->ctx);
    ...
}
...
```
## 9.6 严禁使用string类存储敏感信息【要求】
**【描述】**
string类是C++标准库中的一个类，用于存储和操作字符串。string类内部维护了一个字符数组，用于存储字符串的内容。当使用string类存储敏感信息（如密码、密钥等）时，需要注意以下几点：
1. 敏感信息不应存储在string类对象中，因为string类对象是可以被访问的，而敏感信息存储在其中会增加泄露的风险。
2. 当需要对敏感信息进行操作时（如比较、加密等），应该使用char数组而不是string类对象。因为char数组是可以被直接访问的，而string类对象是不能被直接访问的。

# 10 其他
## 10.1 不要在信号处理函数中访问共享对象
**【描述】**
如果在信号处理程序中访问和修改共享对象，可能会造成竞争条件，使数据处于不确定的状态。
这条规则有两个不适用的场景：

- 读写不需要加锁的原子对象;
- 读写volatile sig_atomic_t类型的对象，因为具有volatile  sig_atomic_t类型的对象即使在出现异步中断的时候也可以作为一个原子实体访问，是异步安全的。

此外，在信号处理程序中，如果要调用函数，请仅调用异步信号安全函数。

**【反例】**
在这个信号处理过程中，程序打算将`g_msg`作为共享对象，当产生SIGINT信号时更新共享对象的内容，但是该`g_msg`变量类型不是`volatile sig_atomic_t`，所以不是异步安全的。

```c
#define MAX_MSG_SIZE 32
static char g_msgBuf[MAX_MSG_SIZE] = {0};
static char *g_msg = g_msgBuf;

void SignalHandler(int signum)
{
    // 下面代码操作g_msg不合规，因为不是异步安全的
    (void)memset_s(g_msg, MAX_MSG_SIZE, 0, MAX_MSG_SIZE);
    errno_t ret = strcpy_s(g_msg, MAX_MSG_SIZE, "signal SIGINT received.");
    ... // 处理 ret
}

int main(void)
{
    errno_t ret = strcpy_s(g_msg, MAX_MSG_SIZE, "No msg yet."); // 初始化消息内容
    ... // 处理 ret

    signal(SIGINT, SignalHandler); // 设置SIGINT信号对应的处理函数

    ... // 程序主循环代码

    return 0;
}
```
**【正例】**
如下代码示例中，在信号处理函数中仅将`volatile sig_atomic_t`类型作为共享对象使用。

```c
#define MAX_MSG_SIZE 32
volatile sig_atomic_t g_sigFlag = 0;

void SignalHandler(int signum)
{
    g_sigFlag = 1; // 符合
}

int main(void)
{
    signal(SIGINT, SignalHandler);
    char msgBuf[MAX_MSG_SIZE];
    errno_t ret = strcpy_s(msgBuf, sizeof(msgBuf), "No msg yet."); // 初始化消息内容
    ... // 处理 ret

    ... // 程序主循环代码

    if (g_sigFlag == 1) {  // 在退出主循环之后，根据g_sigFlag状态再刷新消息内容
        ret = strcpy_s(msgBuf, sizeof(msgBuf), "signal SIGINT received.");
        ... // 处理 ret
    }

    return 0;
}
```

**【影响】**

在信号处理程序中访问或修改共享对象，可能造成以不一致的状态访问数据。


**【相关软件CWE编号】** CWE-662，CWE-828

## 10.2 禁用rand函数产生用于安全用途的伪随机数
**【描述】**
C语言标准库rand()函数生成的是伪随机数，所以不能保证其产生的随机数序列质量。根据C11标准，rand()函数产生的随机数范围是`[0, RAND_MAX(0x7FFF)]`，因为范围相对较短，所以这些数字可以被预测。
所以禁止使用rand()函数产生的随机数用于安全用途，必须使用安全的随机数产生方式，如：类Unix平台的/dev/random文件，Windows平台的BCryptGenRandom。

典型的安全用途场景包括(但不限于)以下几种：

- 会话标识SessionID的生成；
- 挑战算法中的随机数生成；
- 验证码的随机数生成；
- 用于密码算法用途（例如用于生成IV、盐值、密钥等）的随机数生成。

**【反例】**
程序员期望生成一个唯一的不可被猜测的HTTP会话ID，但该ID是通过调用rand()函数产生的数字随机数，它的ID是可猜测的，并且随机性有限。

**【正例】(POSIX)**
在类Unix平台上，可以使用/dev/random文件得到随机数。需要注意的是，设备刚启动时，由于硬件输入的熵可能不足，读取该接口可能产生阻塞问题。

**【影响】**

使用rand()函数可能造成可预测的随机数。

## 10.3 禁止代码中包含公网地址【要求】
**【描述】**

代码或脚本中包含用户不可见，不可知的公网地址，可能会引起客户质疑。

对产品发布的软件（包含软件包/补丁包）中包含的公网地址（包括公网IP地址、公网URL地址/域名、邮箱地址）要求如下：
1、禁止包含用户界面不可见、或产品资料未描述的未公开的公网地址。
2、已公开的公网地址禁止写在代码或者脚本中，可以存储在配置文件或数据库中。

对于开源/第三方软件自带的公网地址必须至少满足上述第1条公开性要求。

**【例外】**
- 对于标准协议中必须指定公网地址的场景可例外，如soap协议中函数的命名空间必须指定的一个组装的公网URL、http页面中包含w3.org网址等。