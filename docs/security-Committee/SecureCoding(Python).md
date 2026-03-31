- [1 运算符和表达式](#1-运算符和表达式)
  - [1.1 对除法运算和模运算中的除数为0的情况做相应保护](#11-对除法运算和模运算中的除数为0的情况做相应保护)
- [2 异常处理](#2-异常处理)
  - [2.1 禁止通过异常泄露敏感数据](#21-禁止通过异常泄露敏感数据)
- [3 代码工程](#3-代码工程)
  - [3.1 不用的代码段直接删除，不要注释掉](#31-不用的代码段直接删除不要注释掉)
  - [3.2 正式发布的代码及注释内容不应包含开发者个人信息](#32-正式发布的代码及注释内容不应包含开发者个人信息)
  - [3.3 禁止代码中包含公网地址](#33-禁止代码中包含公网地址)
- [4 文件](#4-文件)
  - [4.1 在多用户系统中创建文件时应根据需要指定合适的权限](#41-在多用户系统中创建文件时应根据需要指定合适的权限)
  - [4.2 校验文件路径之前应该先对其进行规范化处理](#42-校验文件路径之前应该先对其进行规范化处理)
  - [4.3 禁止使用tempfile.mktemp创建临时文件](#43-禁止使用tempfilemktemp创建临时文件)
  - [4.4 临时文件使用完毕应及时删除](#44-临时文件使用完毕应及时删除)
  - [4.5 解压文件必须进行安全检查](#45-解压文件必须进行安全检查)
- [5 序列化](#5-序列化)
  - [5.1 禁止使用pickle.load、_pickle.load和shelve模块加载外部数据](#51-禁止使用pickleload_pickleload和shelve模块加载外部数据)
  - [5.2 禁止序列化未加密的敏感数据](#52-禁止序列化未加密的敏感数据)
  - [5.3 禁止使用yaml模块的load函数](#53-禁止使用yaml模块的load函数)
  - [5.4 禁止使用jsonpickle模块的encode/decode或dumps/loads函数](#54-禁止使用jsonpickle模块的encodedecode或dumpsloads函数)
  - [5.5 在序列化操作时建议使用较为安全的json模块](#55-在序列化操作时建议使用较为安全的json模块)
- [6 外部数据校验](#6-外部数据校验)
  - [6.1 检验跨信任边界传递的外部数据](#61-检验跨信任边界传递的外部数据)
  - [6.2 禁止使用eval和exec函数执行不可信代码](#62-禁止使用eval和exec函数执行不可信代码)
  - [6.3 禁止使用OS命令解析器或"危险函数"调用系统命令](#63-禁止使用os命令解析器或危险函数调用系统命令)
  - [6.4 避免在命令解析器中使用通配符](#64-避免在命令解析器中使用通配符)
  - [6.5 禁止使用subprocess模块中的shell=True选项](#65-禁止使用subprocess模块中的shelltrue选项)
  - [6.6 禁止直接使用外部数据来拼接SQL语句](#66-禁止直接使用外部数据来拼接sql语句)
  - [6.7 不受信任的外部数据禁止使用.format()进行格式化](#67-不受信任的外部数据禁止使用format进行格式化)
  - [6.8 防止正则表达式引起的ReDos攻击](#68-防止正则表达式引起的redos攻击)
  - [6.9 禁止直接使用外部数据来拼接XML](#69-禁止直接使用外部数据来拼接xml)
  - [6.10 禁止在处理XML数据时解析不可信的实体](#610-禁止在处理xml数据时解析不可信的实体)
- [7 日志](#7-日志)
  - [7.1 禁止直接使用外部数据记录日志](#71-禁止直接使用外部数据记录日志)
- [8 代码测试](#8-代码测试)
  - [8.1 禁止在生产版本的业务代码中使用assert](#81-禁止在生产版本的业务代码中使用assert)
- [9 数据安全处理](#9-数据安全处理)
  - [9.1 将敏感对象发送出信任区域前进行签名并加密](#91-将敏感对象发送出信任区域前进行签名并加密)
  - [9.2 安全场景下必须使用密码学意义上的安全随机数](#92-安全场景下必须使用密码学意义上的安全随机数)
  - [9.3 必须使用ssl.SSLSocket代替socket.Socket来进行安全数据交互](#93-必须使用sslsslsocket代替socketsocket来进行安全数据交互)
  - [9.4 禁止在用户界面、日志中暴露不必要信息](#94-禁止在用户界面日志中暴露不必要信息)

# 1 运算符和表达式
## 1.1 对除法运算和模运算中的除数为0的情况做相应保护
**【描述】**
对除法运算和模运算中的除数为0的情况做相应保护。

如果除法或模运算中的除数为零可能会导致程序终止，因此需要对除数为0的情况做相应保护。

**【修改建议】**
1. 在除法运算前，对除数是否为0进行判断。
2. 通过捕获除0异常ZeroDivisionError的方式，来防止程序意外终止。

**【正例】**
1：未对除数进行非零判断
- 修复示例1：对除数进行非零判断
```python
dividen_num = 0
divisor_num = 0

# 符合：在进行除法运算前，先对除数做非0判断
if divisor_num != 0:
  division_result = dividen_num / divisor_num
  remainder_result = dividen_num % divisor_num
else:
  ...
```
- 修复示例2：捕获除零异常

```python
dividen_num = 0
divisor_num = 0

# 符合：通过捕获除0异常ZeroDivisionError的方式，来防止程序意外终止
try:
  division_result = dividen_num / divisor_num
except ZeroDivisionError:
  ...
else:
  ...
```

**【反例】**
1：未对除数进行非零判断
- 错误示例：未对除数进行非零判断
```python
dividen_num = 0
divisor_num = 0

# 不符合：未对除数进行非0判断
division_result = dividen_num / divisor_num
remainder_result = dividen_num % divisor_num
```

# 2 异常处理
## 2.1 禁止通过异常泄露敏感数据
**【描述】**
禁止通过异常泄露敏感数据。

如果在传递异常的时候未对其中的敏感信息进行过滤，会导致信息泄露，可能帮助攻击者尝试发起进一步的攻击。攻击者可以通过构造恶意的输入参数来发掘应用的内部结构和机制。
异常中的错误信息，以及异常本身的类型都可能泄露敏感数据。
当异常会被传递到信任边界以外时，必须同时对敏感的异常信息和敏感的异常类型进行过滤。
需要注意，在某些场景下，不进行异常处理，反而会泄漏敏感数据，必须慎重审视跨信任边界的交互。
敏感数据的具体范围取决于产品的应用场景，产品应根据风险进行分析和判断。典型的敏感数据包括认证凭据、个人数据和通信内容等。

例如，在对公网用户开放的web服务接口，返回的错误信息中禁止包含`ImportError`和`OSError`等潜在泄漏内部文件目录结构的内容。
提供身份认证的函数，当发生用户名或密码认证失败时，返回的异常类型禁止明确标明是用户名错误或是密码错误，调用者如果捕获异常并将异常类型记录到日志中，攻击者可能结合日志文本，通过暴力探测获取用户名清单。

**【修改建议】**
当异常会被传递到信任边界以外时，必须同时对敏感的异常信息和敏感的异常类型进行过滤。

**【正例】**
1：通过异常泄露敏感数据
- 修复示例：异常中没有敏感数据
```python
try:
    password = 'password@123'
    print(int(password))
# 异常中没有密码信息
except ValueError:
    print('Invalid value')  # 符合
except Exception:
    print('Invalid value')  # 符合
```

**【反例】**
1：通过异常泄露敏感数据
- 错误示例：通过异常泄露敏感数据
```python
try:
    password = 'password@123'
    print(int(password))
# 在异常中泄露了密码信息
except Exception as e:
    print(e)  # 不符合
```

# 3 代码工程
## 3.1 不用的代码段直接删除，不要注释掉
**【描述】**
基于python语言运行时编译的特殊性，如果在提供代码的时候提供的是py文件，该文件中包含被注释掉的代码，当企图恢复使用这段代码时，极有可能引入易被忽略的缺陷；尤其是某些接口函数，
如果不在代码中进行彻底删除，某些本应被屏蔽的功能可能在不知情的情况下就被启用了。另外，同样禁止以False为条件语句或者其他任何形式使之成为无法执行到的无效代码。

**【修改建议】**
对不使用的旧代码应该及时删除，以免暴露程序接口，造成不安全的因素。

**【正例】**
1：被注释的代码
- 修复示例：删除注释的代码
```python
 if __name__ == "__main__":
     if sys.argv[1].startswith('--'):
         option = sys.argv[1][2:]
         if option == "load":
             # 安装应用
             LoadCmd(option, sys.argv[2:3][0])
         elif option == "unload":
             # 卸载应用
             UnloadCmd(sys.argv[2:3][0])
         elif option == "unloadproc":
             # 卸载流程
             UnloadProcessCmd(sys.argv[2:3][0])
         else:
             Loginfo("Command {} is unknown".format(sys.argv[1]))
```

**【反例】**
1：被注释的代码
- 错误示例：程序中的两个屏蔽的接口，容易造成不安全的因素，注释及被屏蔽的代码段应该直接删除
```python
if __name__ == "__main__":
    if sys.argv[1].startswith('--'):
        option = sys.argv[1][2:]
        if option == "load":
            # 安装应用
            LoadCmd(option, sys.argv[2:3][0])
        elif option == "unload":
            # 卸载应用
            UnloadCmd(sys.argv[2:3][0])
        elif option == "unloadproc":
            # 卸载流程
            UnloadProcessCmd(sys.argv[2:3][0])
        # elif option == 'active':  不符合，被注释的代码
        #   ActiveCmd(sys.argv[2:3][0])
        # elif option == 'inactive':
        #   InActiveCmd(sys.argv[2:3][0])
        else:
            Loginfo("Command {} is unknown".format(sys.argv[1]))
```

## 3.2 正式发布的代码及注释内容不应包含开发者个人信息
**【描述】**
发布的代码及注释内容不应包含个人敏感信息，如：身份证号码。

由于从应用程序中提取字符串很容易，因此敏感信息不应硬编码，否则很容易落入攻击者手中。对于分布式的应用程序尤其如此。

**【修改建议】**
删除身份证号码等敏感信息。应存储在代码外部的受强保护的加密配置文件或数据库中。

**【正例】**
1：代码中包含个人信息
- 修复示例：代码中没有个人信息
```python
# 符合: 代码中没有个人信息
line_no = 1
```

**【反例】**
1：代码中包含个人信息
- 错误示例：代码中包含个人信息
```python
# 不符合: 代码中包含了个人工号信息
employee_no = 'a00123456'
```

## 3.3 禁止代码中包含公网地址
**【描述】**
IP地址、密码硬编码。

代码及注释内容不应包含敏感信息。敏感信息检查分类包括：IP地址、密码。

**【修改建议】**
删除IP地址、密码等敏感信息。

**【正例】**
```Python
删除IP地址、密码等敏感信息
```

**【反例】**
```python
def badTest():
    # POTENTIAL FLAW: Sensitive information is hard coded in the program
    pwd = 'xxx'
```

# 4 文件
## 4.1 在多用户系统中创建文件时应根据需要指定合适的权限

**【描述】**
在多用户系统中创建文件时应根据需要指定合适的权限。

多用户系统中的文件通常归属于一个特定的用户。文件的主人能够指定系统中哪些其他用户能够访问该文件的内容。这些文件系统使用权限和许可模型来保护文件访问。当一个文件被创建时，文件访问许可规定了哪些用户可以访问或者操作这个文件。当一个程序在创建文件时没有对文件的访问许可做足够的限制，攻击者可能在程序修改此文件的访问权限之前对其进行读取或者修改。另外在跨环境场景也有类似问题，需要在创建文件时指定合适的权限。

**【修改建议】**
使用os.open函数，并指定第三个参数为0o755或更低级别。建议按照文件使用场景严格限制文件访问许可，阻止未授权的访问。

**【正例】**
1：创建文件时没有指定权限
- 修复示例：创建文件时指定了合适的权限

```python
import os
import stat

flags = os.O_WRONLY | os.O_CREAT | os.O_EXCL  # 注意根据具体业务的需要设置文件读写方式
mode = stat.S_IWUSR | stat.S_IRUSR  # 注意根据具体业务的需要设置文件权限
with os.fdopen(os.open('testfile.txt', flags, mode), 'w') as fout:  # 符合：创建文件时在os.open的第三个参数指定了合适的权限
    fout.write('secrets!')
```

**【反例】**
1：创建文件时没有指定权限
- 错误示例：创建文件时没有指定权限

```python
with open('testfile.txt', 'w') as fout:  # 不符合：创建文件时没有指定权限
    fout.write('hi, 2012')
```

## 4.2 校验文件路径之前应该先对其进行规范化处理
**【描述】**
路径遍历，校验文件路径之前应该先对其进行规范化处理。

绝对路径或者相对路径中可能会包含如符号（软）链接（symbolic [soft] links）、硬链接（hard links）、快捷方式（shortcuts）、影子文件（shadows）、别名（aliases）和连接文件（junctions）等形式，在进行文件验证操作之前必须完整解析这些文件链接。路径中也可能会包含如下所示的文件名，使得验证变得困难：
1. "." 指当前目录。
2. 在一个目录内，".." 指该目录的上一级目录。

除此之外，还有与特定操作系统和特定文件系统相关的命名约定，也会使验证变得困难。
同一个目录或者文件，可以通过多种路径名来引用它，所以在文件路径校验前必须使用os.path.realpath方法对文件路径进行规范化处理，可以使文件路径校验较为容易。
当试图限制用户只能访问某个特定目录中的文件时，或者当基于文件名或者路径名来做安全决策时，校验是必须的。攻击者可能会利用目录遍历（directory traversal）或者等价路径（path equivalence）漏洞的方式来绕过这些限制。
目录遍历漏洞使得攻击者能够转移到一个特定目录进行I/O操作，等价路径漏洞使得攻击者可以使用与某个资源名不同但是等价的名称来绕过安全检查。
在程序获取一个文件标准路径与打开这个文件之间，会有一个固有的时间竞争窗口。在对规范化的文件路径进行校验时，文件系统可能被修改，规范化的路径名可能不再指向原始的有效文件。值得庆幸的是，可以使用规范化的路径名来判断引用的文件名是否在安全目录中，来消除条件竞争。如果引用的文件是在一个安全目录之中，很明显攻击者无法篡改文件，也无法利用条件竞争。

**【修改建议】**
使用os.path.realpath方法，它能在所有的平台上对所有别名、快捷方式以及符号链接进行一致地解析。特殊的文件名，比如..会被移除，这样输入在验证之前会被简化成其规范形式。当使用规范化的文件路径来做校验时，攻击者将无法使用../序列来跳出特定目录，同时为了防止软链接攻击，有必要对规范化之后的路径进行预期判断，以防止最终操作的路径为攻击者意图访问的路径。

**【正例】**
1：未对文件路径进行规范化处理
- 修复示例：对文件路径进行校验
```python
import os

def path_verify(path, safe_start):
    # 校验函数里是对参数进行realpath方法生成真实路径，并对生成的路径进行预期校验，预期校验是根据业务的角度去校验的，常见的预期校验方法如：startswith、正则等。
    # 注：os.path.exists不属于预期校验
    real_path = os.path.realpath(path)  # 清理告警第一步：用realpath方法
    if real_path.startswith(safe_start):  # 清理告警第二步：预期校验
        return real_path

path = input()  # 污染源，例如input、sys.argv、os.environ 等
safe_start = "/usr/test"
try:
    # 对文件路径进行校验
    verified_path = path_verify(path, safe_start)  # 污染源数据经过特定函数名的校验函数，从而从不可信数据变为可信数据
    if verified_path:
        os.removedirs(verified_path)  # 符合
except Exception as ex:
    print(ex)
```

**【反例】**
1：未对文件路径进行规范化处理
- 错误示例：
```python
import os

path = input()
try:
    # 未对文件路径进行规范化处理
    os.removedirs(os.path.abspath(path))  # 不符合
except Exception as ex:
    print(ex)
```

## 4.3 禁止使用tempfile.mktemp创建临时文件
**【描述】**
禁止使用tempfile.mktemp创建临时文件。

mktemp 函数返回的临时文件名可能存在重名（与时间强相关）产生未知风险。禁止使用已知有风险的`tempfile.mktemp`函数创建临时文件。要求使用安全的方法创建临时文件，包括但不限于`tempfile`模块的`mkstemp`, `mkdtemp`, `NamedTemporaryFile`函数。os.tempnam和os.tmpnam也存在同样的风险。

**【修改建议】**
可以使用替代函数NamedTemporaryFile() 、mkstemp() 和 mkdtemp() ，这些函数调用的同时立即创建临时文件，避免攻击者利用时间差进行符号链接攻击。

**【正例】**
1：使用tempfile.mktemp创建临时文件
- 修复示例：使用安全的函数创建临时文件

```python
import os
import shutil
import tempfile

tmp_file_one = tempfile.mkstemp()  # 符合：(3, '/tmp/tmpyo8ddjhq') 创建临时文件
tmp_file_two = tempfile.mkdtemp()  # 符合：/tmp/tmpf_od5le5 创建临时目录
tmp_file_three = tempfile.NamedTemporaryFile(delete=False)  # 符合：<tempfile._TemporaryFileWrapper object at 0x000001E3D182BE88>

if os.path.isfile(tmp_file_one[1]):
    os.close((tmp_file_one[0]))
    os.remove(tmp_file_one[1])

if os.path.isfile(tmp_file_three.name):
    tmp_file_three.close()
    os.remove(tmp_file_three.name)

if os.path.isdir(tmp_file_two):
    os.mkdir(os.path.join(tmp_file_two, '1.txt'))
    shutil.rmtree(tmp_file_two)
```

**【反例】**
1：使用tempfile.mktemp创建临时文件
- 错误示例：使用tempfile.mktemp创建临时文件

```python
import tempfile

filename = tempfile.mktemp()  # 不符合：生成的文件名与时间强相关，有可能引起重名风险
with open(filename, "w+") as tmp_file:
    ...
```

## 4.4 临时文件使用完毕应及时删除
**【描述】**
临时文件使用完毕应及时删除。

在全局可写的目录中创建临时文件，例如，POSIX系统下的/tmp与/var/tmp目录，Windows系统下的C:\TEMP目录。这类目录中的文件可能会被定期清理，例如，每天晚上或者重启时。然而，如果文件未被安全地创建或者用完后还是可访问的，具备本地文件系统访问权限的攻击者便可以利用共享目录中的文件进行恶意操作。删除已经不再需要的临时文件有助于对文件名和其他资源（如二级存储）进行回收利用。每一个程序在正常运行过程中都有责任确保删除已使用完毕的临时文件。

**【修改建议】**
临时文件使用完毕后使用os.remove或os.unlink删除

**【正例】**
1：临时文件使用完毕未及时删除
- 修复示例1：使用os.remove或os.unlink函数删除
```python
import os

diy_file = "diyData.txt"
content = "Data"
try:
   file_handle = open(diy_file, os.O_WRONLY, int("0600", 8))
   try:
       file_handle.write(content)
   except Exception as e:
       doing_something()
   finally:
       file_handle.close()
except Exception as ex:
   print(ex)

# 建议使用with语句实现上述功能
# 结束时，显式的删除它
if os.path.isfile(diy_file):
    os.remove(diy_file)  # 符合
```

- 修复示例2：这个示例创建临时文件时用到了NamedTemporaryFile ()方法，该方法会新建一个随机的文件名。文件使用try-finally构造块，在finally处手动关闭文件。而不管是否有异常发生，由于在打开文件时用到了delete选项，使得文件在关闭后会被自动删除。
```python
import tempfile

tmp_file_handle = tempfile.NamedTemporaryFile(delete=True)  # 符合
print(tmp_file_handle.name)
try:
   tmp_file_handle.file.write(b"abc")
except Exception as e:
   doing_something()
finally:
   tmp_file_handle.close()
```

**【反例】**
1：临时文件使用完毕未及时删除
- 错误示例：
```python
import os
import tempfile

fd, path = tempfile.mkstemp()  # 不符合
try:
    with os.fdopen(fd, "w") as f:
        f.write('Hello, world!')
except Exception as e:
    doing_something()
```

## 4.5 解压文件必须进行安全检查
**【描述】**
解压文件必须进行安全检查。

攻击者有可能上传一个很小的zip文件，但是完全解压缩之后达到几百万GB甚至更多，消耗完磁盘空间导致系统或你的程序无法正常运行。因此在解压缩文件时须要限制所使用的磁盘空间。

**【修改建议】**
禁止直接一次性递归解压压缩包全部内容，在解压前最少进行以下几点基础安全检查：
1. 文件个数是否超出业务预期个数；
2. 文件大小是否超出业务预期大小；
3. 文件大小是否超出目标目录所在磁盘空间。

建议根据需要，依赖其它工程手段加强防范，这些工程手段包括并不限于：
1. 为解压文件单独分区，限定大小；
2. 对压缩包做健康检查，包括其中内容是否符合业务预期；
3. 对CPU和内存的使用量进行限定，防止大量压缩包中海量小文件等攻击手段。（需结合程序实际运行情况估计，慎用）

以上几点建议**非强制执行**，正例中展示的是常用的校验方法，不能规避所有问题。

**【正例】**
1：未进行安全检查
- 修复示例：解压前最少进行以下几点基础安全检查
```python
import os
import zipfile
import psutil

class MyZip:  # 符合
    # 限制解压后大小不能超过1M，文件个数不能超过10个
    MAX_SIZE = 1 * 1024 * 1024
    MAX_FILE_CNT = 10

    @staticmethod
    def unzip(path, zip_file):
        file_path = path + os.sep + zip_file
        dest_dir = path

        with zipfile.ZipFile(file_path, 'r') as src_file:
            # 检查点1：检查文件个数，文件个数大于预期值时上报异常退出
            file_count = len(src_file.infolist())
            if file_count >= MyZip.MAX_FILE_CNT:
                raise IOError(f'zipfile({zip_file}) contains {file_count} files exceed max file count')

            # 检查点2：检查第一层解压文件总大小，总大小超过设定的上限值
            total_size = sum(info.file_size for info in src_file.infolist())
            if total_size >= MyZip.MAX_SIZE:
                raise IOError(f'zipfile({zip_file}) size({total_size}) exceed max limit')

            # 检查点3：检查第一层解压文件总大小，总大小超过磁盘剩余空间
            dest_dir_partition = '/'  # 解压目录所在分区
            if total_size >= psutil.disk_usage(dest_dir_partition).free:
                raise IOError(f'zipfile({zip_file}) size({total_size}) exceed remain target disk space')

            # 所有检查通过之后，解压所有文件
            src_file.extractall(dest_dir)
```

**【反例】**
1：未进行安全检查
- 错误示例：
```python
import zipfile
import os

def unzip(path, zip_file):  # 不符合，没有进行安全检查
    file_path = path + os.sep + zip_file
    dest_dir = path
    src_file = zipfile.ZipFile(file_path, 'r')
    src_file.extractall(dest_dir)
```

# 5 序列化
## 5.1 禁止使用pickle.load、_pickle.load和shelve模块加载外部数据

**【描述】**
禁止使用pickle.load、_pickle.load和shelve模块加载外部数据。

模块 `pickle` 实现了对一个 Python 对象结构的二进制序列化和反序列化。 *"pickling"* 是将 Python 对象及其所拥有的层次结构转化为一个字节流的过程，而 *"unpickling"* 是相反的操作，会将（来自一个 `binary file` 或者 `bytes-like object` 的）字节流转化回一个对象层次结构。
`pickle`模块存在安全风险，攻击者通过构造恶意数据，在`pickle`模块反序列化时触发任意代码执行。因此禁止对外部数据和可能被篡改过的数据进行反序列化。
`_pickle`模块是用C语言实现的序列化模块，同样存在安全风险。当`_pickle`模块存在时，`pickle`模块会优先使用`_pickle`模块进行序列化和反序列化；反之`pickle`模块会使用python语言实现的序列化模块。
`shelve`模块使用`pickle`模块实现反序列化数据，数据保存在`shelve`模块Shelf类的实例中。因此使用`shelve`模块加载外部数据也是不安全的。

**【修改建议】**
使用 `hmac` 对序列化数据进行签名，确保数据没有被篡改。

**【正例】**
1：使用pickle加载外部数据
- 修复示例：使用json加载外部数据
```python
import json

with open('data.json', 'r') as f:
    # 使用json.loads加载外部数据
    data = json.loads(f.read())  # 符合
```

**【反例】**
1：使用pickle加载外部数据
- 修复示例：使用pickle加载外部数据
```python
import pickle

with open('data.pkl', 'rb') as f:
    # 使用pickle.loads加载外部数据
    data = pickle.loads(f.read())  # 不符合
```

## 5.2 禁止序列化未加密的敏感数据
**【描述】**
禁止序列化未加密的敏感数据。

虽然序列化可以将对象的状态保存为一个字节序列，之后通过反序列化该字节序列又能重新构造出原来的对象，但是它并没有提供一种机制来保证序列化数据的安全性。可访问序列化数据的攻击者可以借此获取敏感信息并确定对象的实现细节。敏感数据序列化之后是是对外暴露着的，因此序列化信息中不应该包括：密钥、数字证书、以及那些在序列化时引用敏感数据的类。此条规则的意义在于防止敏感数据被无意识的序列化导致敏感信息泄露。

**【修改建议】**
在将某个包含敏感数据的类序列化时，程序必须确保敏感数据不被序列化。这包括阻止包含敏感信息的数据成员被序列化，以及不可序列化或者敏感对象的引用被序列化。

**【正例】**
1：序列化未加密含敏感信息
- 修复示例：序列化之前删除敏感数据
```python
import pickle

class Password:
    def __init__(self, password, id):
        self.password = password
        self.id = id

    def __getstate__(self):
        state = dict(self.__dict__)
        del state['password']  # 符合
        return state

dump_str = pickle.dumps(Password(12, 3))
```

**【反例】**
1：序列化未加密含敏感信息
- 错误示例：
```python
import pickle

class Password:
    def __init__(self, password, id):
        self.password = password
        self.id = id

    def __getstate__(self):
        state = dict(self.__dict__)
        return state

dump_str = pickle.dumps(Password(12, 3))  # 不符合
```
## 5.3 禁止使用yaml模块的load函数
**【描述】**
禁止使用yaml模块的load函数。

yaml模块在数据序列化和配置文件中使用比较广泛，其在解析数据的时候遇到特定格式的数据类型会自动转换为Python的对象，比如会将时间类型的数据自动转化Python时间对象。这个特点让攻击者有机可乘。

**【修改建议】**
下载PyYAML模块的最新版本，并使用其提供的安全函数：

- yaml.load(data, Loader=SafeLoader)
- yaml.load_all(data, Loader=SafeLoader)
- yaml.safe_load(data)
- yaml.safe_load_all(data)

**【正例】**
1：使用yaml.load加载外部数据
- 修复示例：使用安全的函数加载外部数据
```python
import yaml

poc = "!!python/object/apply:subprocess.check_output [[\\"calc.exe\\"]]"
# 符合：等价于 yaml.load(poc, Loader=yaml.SafeLoader)
yaml.safe_load(poc)
```

**【反例】**
1：使用yaml.load加载外部数据
- 错误示例1：使用yaml.load加载外部数据
```python
# PyYAML<5.1 版本成功启动计算器
import yaml

poc = "!!python/object/apply:subprocess.check_output [[\\"calc.exe\\"]]"
# 不符合：等价于 yaml.load(poc, Loader=yaml.Loader)
yaml.load(poc)
```
- 错误示例2：使用yaml.load加载外部数据
```python
# PyYAML<5.4 版本成功启动计算器
import yaml

poc = """!!python/object/new:tuple 
        - !!python/object/new:map 
        - !!python/name:eval
        - [ "__import__('os').system('calc.exe')" ]"""
# 不符合：等价于 yaml.load(poc, Loader=yaml.Loader)
yaml.load(poc)
```

## 5.4 禁止使用jsonpickle模块的encode/decode或dumps/loads函数
**【描述】**
禁止使用jsonpickle模块的encode/decode或dumps/loads函数。

jsonpickle模块支持将任意对象转化为json。
使用jsonpickle模块加载来源不可信的JSON字符串存在潜在的安全风险。 jsonpickle模块不会对输入进行校验，这导致攻击者可构造恶意输入获取执行权限。

**【修改建议】**
使用其它安全的序列化模块，否则就要进行严格的白名单、黑名单过滤。

**【正例】**
1：代码中使用了jsonpickle模块
- 修复示例：使用json模块
```python
import json

poc = '{"py/reduce": [{"py/type": "subprocess.Popen"}, {"py/tuple": [{"py/tuple": ["cmd.exe", "/c", "calc.exe"]}]}]}'
json.decode(poc)  # 符合
```

**【反例】**
1：代码中使用了jsonpickle模块
- 错误示例：
```python
import jsonpickle

poc = '{"py/reduce": [{"py/type": "subprocess.Popen"}, {"py/tuple": [{"py/tuple": ["cmd.exe", "/c", "calc.exe"]}]}]}'
# 成功启动计算器
jsonpickle.decode(poc)  # 不符合
```

## 5.5 在序列化操作时建议使用较为安全的json模块
**【描述】**
JSON是一种轻量级的数据交换格式。Python3提供了json模块对JSON数据进行序列化，dump/dumps用来序列化，load/loads用来反序列化。当处理外部数据序列化的需求时，推荐使用较为安全的json模块，并且json模块支持跨平台跨语言操作。

**【正例】**
```python
import json

d = dict(name='Bob', age=20, score=88)
json_str = json.dumps(d)
print(json_str)
```

对于类的实例对象，使用json序列化需要提供专门的转换函数
```python
import json

class Student(object):
    def __init__(self, name, age, score):
        self.name = name
        self.age = age
        self.score = score

s = Student('Bob', 20, 88)
print(json.dumps(s, default=lambda obj: obj.__dict__))
```

# 6 外部数据校验
## 6.1 检验跨信任边界传递的外部数据
**【描述】**
程序可能会接收来自用户、网络连接或其他来源的外部数据，并将这些数据跨信任边界传递到目标系统（如浏览器、数据库等）。由于目标系统可能无法区分处理畸形的外部数据，未经校验的外部数据可能会引起某种注入攻击，对系统造成严重影响，因此，必须对外部数据进行校验，且数据校验必须在信任边界以内进行（如对于web应用，需要在服务端做校验）
外部数据的范围包括但不限于：网络、用户输入（包括命令行、界面）、命令行、文件（包括程序的配置文件）、环境变量、进程间通信（包括管道、消息、共享内存、socket等、RPC）、跨信任域方法参数（对于API）等。

对于外部数据的具体校验，要结合实际的业务场景要采用与之相对的校验方式来消除安全隐患，对于缺少校验规则的场景，可结合其他的措施进行防护，保证不会存在安全隐患

对外部数据的校验包括但不局限于：
- 校验API接口参数合法性
- 校验数据长度
- 校验数据范围
- 校验数据类型和格式
- 校验集合大小
- 校验外部数据是否只包含可接受的字符（白名单校验，尤其需要注意一些特殊情况下的特殊字符）

**【正例】**
白名单校验
```python
def whitelist_verify(input_str):
    if not re.match("^[0-9A-Za-z_]+$", input_str):
        return remove_illegal_char(input_str)
    return input_str
```

## 6.2 禁止使用eval和exec函数执行不可信代码
**【描述】**
使用eval和exec函数执行不可信代码。

* eval函数的定义为`eval(expression[, globals[, locals]])` ,它将字符串expression当成有效 Python 表达式来求值，并返回计算结果。 
* exec函数的定义为`exec(object[, globals[, locals]])` ,它可以接受大量Python代码块。 

如果eval和exec使用了外部输入的字符串，且外部输入的字符串中存在恶意的代码，将造成由于代码注入导致的安全问题。
例如：下例中系统命令ls将被执行，同样的问题在调用exec时也是存在。

```python
please input string: __import__('os').system('ls -l')

eval(input('please input string: '))
```

要严格校验输入的字符串表达式的合法性。如果校验缺失，用户可以访问代码名称空间的变量，会引起信息泄露。
另外注意，eval函数还可以使用\_\_import__导入任意模块。

**【修改建议】**
使用白名单对传入eval、exec等函数的不可信参数进行校验。

**【正例】**
1：外部输入的数据作为参数传入可执行代码的函数（eval、exec等），且参数未经校验
- 修复示例：对外部数据进行校验
```python
def string_verify(input_str):
    # 检查输入字符串的长度
    if not input_str or len(input_str) > 20:
        raise ValueError("输入字符串无效")
    # 删除或转义输入字符串中的特殊字符
    verified_str = input_str.replace("'", "").replace('"', "").replace("\\\\", "")
    return verified_str

code = input("请输入代码：")
# 校验外部数据
verified_code = string_verify(code)
eval(verified_code)  # 符合
```

**【反例】**
1：外部输入的数据作为参数传入可执行代码的函数（eval、exec等），且参数未经校验
- 错误示例：
```python
code = input("请输入代码：")
# 直接使用外部数据执行eval函数
eval(code)  # 不符合
```

## 6.3 禁止使用OS命令解析器或"危险函数"调用系统命令
**【描述】**
禁止使用OS命令解析器或"危险函数"调用系统命令。

使用未经校验的不可信输入作为系统命令的参数或命令的一部分，可能导致命令注入漏洞。对于命令注入漏洞，命令将会以与Python应用程序相同的特权级别执行，它向攻击者提供了类似系统shell的功能。在Python中，os.system 或 os.popen 经常被用来调用一个新的进程，如果被执行的命令来自于外部输入，则可能会产生命令和参数注入。
执行命令的时候，必须注意以下几点：
1. 命令执行的字符串不要去拼接输入的参数，如果必须拼接时，要对输入参数进行白名单过滤
2. 对传入的参数要做类型校验，例如：整数数据，可以对数据进行整数强制转换
3. 对于要求限定格式化字符串类型的业务场景，要保证占位符的正确性。例如：int类型参数的拼接，使用`{:d}`，不能用`{:s}`

**【修改建议】**
使用白名单对os.system等命令执行函数的不可信参数进行校验，建议减少在python代码中调用命令执行函数，使用python提供的安全API替代shell命令操作。

**【正例】**
1：使用os.system执行外部命令
- 修复示例1：使用系统函数
```python
import os
import sys

try:
   # 使用os.listdir来列举目录下的内容
   print(os.listdir(sys.argv[1]))  # 符合
except Exception as ex:
   print(ex)
```

- 修复示例2：对外部数据进行校验
```python
import os
import re
import sys

def args_verify(args_list):
    # 校验数据长度
    if len(args_list) > 20:
        print('Parameter length error.')
        sys.exit()
    # 校验数据内容
    if re.match(r"^[.0-9A-Za-z@]+$", args_list[1]):
        return args_list[1]

try:
    # 校验外部数据
    verified_arg = args_verify(sys.argv)
    if verified_arg:
        print(os.system("ls " + verified_arg))  # 符合
except Exception as ex:
    print('exception:', ex)
```

**【反例】**
1：使用os.system执行外部命令
- 错误示例：使用os.system直接执行外部命令
```python
import os
import sys

try:
    # 使用os.system直接执行外部命令
    print(os.system("ls " + sys.argv[1]))  # 不符合
except Exception as ex:
    print('exception:', ex)
```

## 6.4 避免在命令解析器中使用通配符
**【描述】**
避免在命令解析器中使用通配符。

通配符是一种特殊符号，主要有星号（*）和问号（？），用来模糊匹配字符串（比如文件名，参数名），Linux系统中许多命令接受通配符，如果攻击者将软链接或者一些特性文件置于给定的路径位置，则可能造成严重后果。

**【修改建议】**
函数'chown','chmod'，'subprocess'等操作文件访问许可的函数或执行子进程的函数参数中不要使用包含有‘*’通配符

**【正例】**
1：执行子进程的函数参数中包含"*"通配符
- 修复示例：执行子进程的函数参数中不要包含"*"通配符
```python
import subprocess
import os

file_path = 'test.txt'
subprocess.Popen(['/bin/chmod', '640', file_path], shell=False)  # 符合
subprocess.Popen(['/bin/chown', 'user:user', file_path], shell=False)  # 符合
os.chown(file_path, uid, gid, follow_symlinks=False)  # 符合，禁止对软链接对应的目标文件操作
```

**【反例】**
1：执行子进程的函数参数中包含"*"通配符
- 错误示例：
```python
import subprocess
import os

os.system('/bin/chmod 640 *')  # 不符合
subprocess.Popen('/bin/chown user:user *', shell=True)  # 不符合
```

## 6.5 禁止使用subprocess模块中的shell=True选项
**【描述】**
禁止使用subprocess模块中的shell=True选项。

subprocess模块帮助开发者执行外部命令和程序，为开发者带来便利的同时也带来了风险，该模块下多个接口（如call、check_call、Popen、check_output等）都可以传递shell参数。 
shell=True这个参数的功能是Python解释器先运行一个shell环境，再用这个shell来执行Popen函数的第一个命令行字符串参数。 
推荐调用子进程的方式是使用run()函数，对于其他需要使用到更高阶的用法的可以使用底层Popen()接口，需要注意的一点是使用Popen方式时，当stdout=PIPE或者stderr=PIPE时有可能会造成阻塞，除非调用Popen.communicate()才能解决。

**【修改建议】**
安全的做法是将shell参数置为False，前面的参数转成list列表，这样传入的参数里即使有注入指令也不会被解析执行。切记第1个参数中的list列表的第一个元素不允许是“bash”、“cmd”、“/bin/sh”，第二个元素不允许是“-c”；只有满足了这两个前提条件，参数shell=False的情况才是安全的。

**【正例】**
1：在subprocess模块中开启shell=True
- 修复示例：设置shell=False
```python
import subprocess

# 符合：设置shell=False
subprocess.check_call(['/bin/ls', '-l'], shell=False)
```

**【反例】**
1：在subprocess模块中开启shell=True
- 错误示例：在subprocess模块中开启shell=True
```python
import subprocess

# 不符合：开启shell=True
subprocess.check_call('/bin/ls -l', shell=True)
subprocess.Popen('/bin/ls *', shell=True)
subprocess.Popen('/bin/ls %s' % ('something',), shell=True)
subprocess.Popen('/bin/ls *', shell=True)
subprocess.Popen('/bin/ls %s' % ('something',), shell=True)
subprocess.Popen('/bin/ls {}'.format('something'), shell=True)
```

## 6.6 禁止直接使用外部数据来拼接SQL语句
**【描述】**
禁止直接使用外部数据来拼接SQL语句。

SQL注入是指使用外部数据构造的SQL语句所代表的数据库操作与预期不符，这样可能会导致信息泄露或者数据被篡改。

**【修改建议】**
SQL注入产生的根本原因是使用外部数据直接拼接SQL语句，防护措施主要有以下三类：
1. 使用参数化查询：最有效的防护手段;
2. 对外部数据进行白名单校验;
3. 对外部数据中的与SQL注入相关的特殊字符进行转义：适用于必须通过字符串拼接构造SQL语句的场景，转义仅对由引号限制的字段有效。

**【正例】**
1：直接使用外部数据拼接SQL语句
- 修复示例：对外部数据进行校验
对于字符型的字段进行处理时，要对输入做转义，将单引号替换为两个单引号。
```python
import sqlite3
import sys

def string_verify(input_str):
    # Replace single quotes with two single quotes
    verified_str = input_str.replace("'", "''")
    return verified_str

def bad():
    conn = sqlite3.connect("e:/test_sqlite3.db")
    con_cursor = conn.cursor()

    username = sys.argv[0]
    # 校验外部数据
    verified_username = string_verify(username)
    cmd = "select * from COMPANY where NAME = '{:s}'".format(verified_username)
    con_cursor.execute(cmd)  # 符合

    conn.commit()
    conn.close()
```

**【反例】**
1：直接使用外部数据拼接SQL语句
- 错误示例：直接使用外部数据拼接SQL语句
```python
import sqlite3
import sys

def bad():
    conn = sqlite3.connect("e:/test_sqlite3.db")
    con_cursor = conn.cursor()

    # SOURCE：从 system 中读取数据
    username = sys.argv[0]
    # 直接使用外部数据拼接SQL语句
    cmd = "select * from COMPANY where NAME = '{:s}'".format(username)
    # SINK: 数据直接连接到execute()中使用的SQL语句中，可能会导致SQL注入
    con_cursor.execute(cmd)  # 不符合

    conn.commit()
    conn.close()
```

## 6.7 不受信任的外部数据禁止使用.format()进行格式化
**【描述】**
不受信任的外部数据禁止使用.format()进行格式化。

Python 2.6版本引入str.format字符串格式化方法，支持通过string类的format函数处理复杂变量替换以及值的格式化。如果格式化字符串可控，可能导致系统中的敏感信息泄露。

**【修改建议】**
禁止使用.format()对不受信任的外部数据进行格式化，若不可避免，则需通过白名单方式对外部数据进行校验或净化。

**【正例】**
1： 直接对外部数据进行格式化
- 修复示例：格式化前校验外部数据
```python
import re

class User:
    def __init__(self, name, password):
        self.name = name
        self.password = password

def args_verify(input_str):
    # 白名单检验：根据具体场景定制合法输入正则表达式
    safe_pattern = re.compile(r'^[0-9a-zA-z]{6}')
    if safe_pattern.search(input_str):
        return True
    return False

def string_verify(input_str):
    # 黑名单校验：过滤存在风险的字符
    risk_pattern = re.compile(r'[\{\}\._"\'\[\]]')
    if risk_pattern.search(input_str):
        return False
    return True

name = input('Please input name: ')
password = input('Please input password: ')
# 校验外部数据
if args_verify(name) and args_verify(password):
    user = User(name, password)
    data_format = input('Please input data format: ')
    # 校验外部数据
    if string_verify(data_format):
        print(data_format.format(u=user))  # 符合
```

**【反例】**
1： 直接对外部数据进行格式化
- 错误示例：
```python
class User:
    def __init__(self, name, password):
        self.name = name
        self.password = password

name = input('Please input name: ')
password = input('Please input password: ')
data_format = input('Please input data format: ')
user = User(name, password)
# 直接对外部数据进行格式化
print(data_format.format(u=user))  # 不符合
```

## 6.8 防止正则表达式引起的ReDos攻击
**【描述】**
防止正则表达式引起的ReDos攻击。

Python语言的正则引擎本质上是非确定型有穷自动机（NFA），通过回溯机制尝试正则表达式的所有可能执行路径。NFA引擎功能强大，但回溯机制存在效率问题，在最坏的情况下，正则表达式的回溯路径在输入的大小上呈指数级增长。若直接使用外部输入数据进行正则匹配，攻击者可通过构造特殊的正则表达式或目标字符串触发"回溯陷阱"，造成拒绝服务，这种攻击方式称为正则表达式拒绝服务（ReDoS）。

**【修改建议】**
禁止直接使用外部数据作为正则匹配的表达式或目标字符串，若不可避免，则必须使用白名单或黑名单对外部数据进行校验和净化；此外，应检视程序内的正则表达式，避免使用存在风险的表达式结构，包括但不限于：
```
(a+)+b([a-zA-Z]+)*(a|aa)+(a|a?)+(.*a){x} for x > 10(a[ab]*)+...(a+)+b
([a-zA-Z]+)*
(a|aa)+
(a|a?)+
(.*a){x} for x > 10
(a[ab]*)+
...
```

**【正例】**
1：可能引起redos攻击的正则表达式
- 修复示例：安全的正则表达式
```python
import re

# 符合：安全的正则表达式
pattern1 = re.compile(r'^(\d+)$')
pattern2 = re.compile(r'^(\d*)$')
```

**【反例】**
1：可能引起redos攻击的正则表达式
- 错误示例：可能引起redos攻击的正则表达式
```python
import re

# 不符合：具有自我重复的重复性分组的正则表达式
pattern1 = re.compile(r'^(\d+)+$')
pattern2 = re.compile(r'^(\d*)*$')
pattern3 = re.compile(r'^(\d+)*$')
pattern4 = re.compile(r'^(\d+|\s+)*$')

# 不符合：包含替换的重复性分组的正则表达式
pattern5 = re.compile(r'^(\d|\d|\d)+$')
pattern6 = re.compile(r'^(\d|\d?)+$')
```

## 6.9 禁止直接使用外部数据来拼接XML
**【描述】**
禁止直接使用外部数据来拼接XML。

若写入XML文档的数据可控，则攻击者可通过构造特殊输入对XML文档内容进行篡改，例如插入意外的标签、引起XML解析器异常等。一个典型的攻击场景是，攻击者可以通过控制XML文档修改电子商务中的认证凭据，或修改商品的价格，对商家造成经济损失。

**【修改建议】**
禁止直接使用外部数据拼接XML，若无法避免，则需对外部数据进行严格校验和净化；此外，建议使用defusedxml模块处理XML数据，详情可参考defusedxml官方文档。

**【正例】**
1：直接使用外部数据拼接XML
- 修复示例1：白名单方式校验示例一（使用正则表达式）
```python
import xml.etree.ElementTree as ET
import re

def my_clean(input_str):
    # 白名单校验：使用正则表达式
    compile_pattern = re.compile('^\d+$')
    if compile_pattern.match(input_str):
        return input_str
    return '0'

root = ET.Element("root")
child = ET.Element("child")
amount = input()
# 校验外部数据
verified_amount = my_clean(amount)
child.text = '<order>' \
             '<item>laptop</item>' \
             '<price>2800.00</price>' \
             '<amount>' + verified_amount + '</amount>' \
             '</order>'
root.append(child)  # 符合
```

- 修复示例2：白名单方式校验示例二（使用字符串方法isdigit()判断字符串中是否只包含数字字符）
```python
import xml.etree.ElementTree as ET

def my_clean(input_str):
    # 白名单校验：判断字符串中是否只包含数字字符
    if input_str.isdigit():
        return input_str
    return '0'

root = ET.Element("root")
child = ET.Element("child")
amount = input()
# 校验外部数据
verified_amount = my_clean(amount)
child.text = '<order>' \
             '<item>laptop</item>' \
             '<price>2800.00</price>' \
             '<amount>' + verified_amount + '</amount>' \
             '</order>'
root.append(child)  # 符合
```

- 修复示例3：黑名单方式校验
```python
import xml.etree.ElementTree as ET
import re

def my_clean(input_str):
    # 黑名单校验：使用正则表达式
    compile_pattern = re.compile('[<>"\'＆]+')
    if compile_pattern.search(input_str):
        return '0'
    return input_str

root = ET.Element("root")
child = ET.Element("child")
amount = input()
# 校验外部数据
verified_amount = my_clean(amount)
child.text = '<order>' \
             '<item>laptop</item>' \
             '<price>2800.00</price>' \
             '<amount>' + verified_amount + '</amount>' \
             '</order>'
root.append(child)  # 符合
```

**【反例】**
1：直接使用外部数据拼接XML
- 错误示例：以下XML文档定义了电子商城中笔记本电脑的价格，同时接受外部数据修改笔记本电脑的数量
```python
import xml.etree.ElementTree as ET

root = ET.Element("root")
child = ET.Element("child")
amount = input()
# 直接使用外部数据拼接XML
child.text = '<order>' \
             '<item>laptop</item>' \
             '<price>2800.00</price>' \
             '<amount>' + amount + '</amount>' \
             '</order>'
root.append(child)  # 不符合
```

## 6.10 禁止在处理XML数据时解析不可信的实体
**【描述】**
禁止在处理XML数据时解析不可信的外部实体。

XML实体包括内部实体和外部实体。外部实体格式： `<!ENTITY 实体名 SYSTEM "URI"\>` 或者 `<!ENTITY 实体名 PUBLIC "public_ID" "URI"\>`。
XXE（XML External Entity）漏洞全称为XML外部实体漏洞，XML文档中可能包含带有URI的外部实体，解析该文档时可通过URI访问其指向的内容，若XML文档中的URI可控，则攻击者可以修改URI指向特定的恶意文件，执行拒绝服务攻击，或未经授权访问系统文件。

XML内部实体格式： `<!ENTITY 实体名 "实体的值"\>` 。XEE（XML Entity Expansion）漏洞全称为XML实体扩展漏洞，又称十亿狂笑（Billion Laughs）或XML炸弹（XML Bomb），若在处理XML数据时允许使用DTD功能定义XML文档结构，且构造该文档结构的数据可控，则攻击者可在XML文档中构造存在大量嵌套或递归结构的实体，导致解析该文档时数据爆炸性增长，从而造成拒绝服务。因此，在处理XML数据时必须关闭外部实体解析开关，若无法避免解析外部实体，则需对外部实体进行严格校验和净化；此外，建议使用defusedxml模块处理XML数据，详情可参考defusedxml官方文档。

**【修改建议】**
在处理XML数据时必须关闭外部实体解析开关，若无法避免解析外部实体，则需对外部实体进行严格校验和净化；此外，建议使用defusedxml模块处理XML数据，详情可参考defusedxml官方文档。

**【正例】**
1：XML外部实体漏洞
- 修复示例：设置resolve_entities=False
将resolve_entities参数设置为False：
```python
from lxml import etree

xml_str = ''
with open('xml_file.xml', 'r') as fp:    
    xml_str = fp.read()

parserSafe = etree.XMLParser(resolve_entities=False)  # 符合：将resolve_entities参数设置为False
root = etree.XML(xml_str.encode('utf-8'), parser=parserSafe)
_config = list()
for GuestOSType_node in root.getchildren():    
    one_guestos = {}    
    for item in GuestOSType_node.items():        
        one_guestos["@" + item[0]] = item[1]    
    for node in GuestOSType_node.getchildren():        
        one_guestos[node.tag] = node.text    
    _config.append(one_guestos)
print(_config)
```

2：XML实体扩展漏洞
- 修复示例：使用defusedxml模块
使用defusedxml模块：
```python
import defusedxml.minidom as dom

xml_path = input()
result = dom.parse(xml_path)  # 符合：使用defusedxml模块
print(result)
```

**【反例】**
1：XML外部实体漏洞
- 错误示例：存在XML外部实体漏洞
假设存在用户可配置的XML文件（xml_file.xml）如下：
```xml
<OSCONFIG version="v001">	
    <GuestOSType id="1" name="CentOS 7.4" OSType="linux" OSbit="64">		
        <MaxCPU>96</MaxCPU>		
        <MaxMemory>128</MaxMemory>	
    </GuestOSType>
</OSCONFIG>
```
2：XML实体扩展漏洞
- 错误示例：存在XML实体扩展漏洞
```python
import xml.dom.minidom as dom

xml_path = input()
result = dom.parse(xml_path)  # 不符合：存在XML实体扩展漏洞
print(result)
```

# 7 日志
## 7.1 禁止直接使用外部数据记录日志
**【描述】**
禁止直接使用外部数据记录日志。

直接将外部数据记录到日志中，可能存在以下风险：
- 日志注入：恶意用户可利用回车、换行等字符注入一条完整的日志； 
- 敏感信息泄露：当用户输入敏感信息时，直接记录到日志中可能会导致敏感信息泄露；
- 垃圾日志或日志覆盖：当用户输入的是很长的字符串，直接记录到日志中可能会导致产生大量垃圾日志；当日志被循环覆盖时，这样还可能会导致有效日志被恶意覆盖。

所以外部数据应尽量避免直接记录到日志中，如果必须要记录到日志中，要进行必要的校验及过滤处理，对于较长字符串可以截断。对于记录到日志中的数据含有敏感数据时，对于秘钥、口令类的敏感信息，参考\[G.LOG.04 禁止在日志中记录口令、密钥等敏感信息\]将这些敏感信息替换为固定长度的*，对于其他类的敏感信息（如手机号、邮箱等），可进行匿名化处理。

**【修改建议】**
外部数据应尽量避免直接记录到日志中，如果必须要记录到日志中，要进行必要的校验及过滤处理，对于较长字符串可以截断。

**【正例】**
```python
def replaceCRLF(message):
    if message:
        return message.replace('\n', '_').replace('\r', '_');
    else:
        ...
```

**【反例】**
```python
json_data = get_request_body_data(request)
if not validate_request_data(data):
    logger.error("Request data validate fail! Request Data :  " + json_data)
```

# 8 代码测试
## 8.1 禁止在生产版本的业务代码中使用assert
**【描述】**
禁止在生产版本的业务代码中使用assert。

在编译时编译优化参数如果大于等于1，编译器就会删除`assert`语句。 如果在业务代码中使用了`assert`语句做了一些特殊的业务匹配，同时使用了编译优化，则可能会出现不可预期的结果。
所以`assert`语句通常只在测试代码中使用，禁止在生产版本的业务代码中使用`assert`，具体的关于`assert`的信息可以查看官方介绍。

**【修改建议】**
正式发布代码中应删除assert语句。

**【正例】**
1：在业务代码中使用assert
- 修复示例：不使用assert
```python
import sys

class NotSupportedError(Exception):
    def __str__(self):
        return "Code is Linux only"

def check_system():
    if "linux" in sys.platform:
        return
    raise NotSupportedError()

check_system()  # 符合：不使用assert
```

**【反例】**
1：在业务代码中使用assert
- 错误示例：使用了assert
```python
import sys

def check_system():
    assert ("linux" in sys.platform), "Code is Linux only"  # 不符合：使用了assert

check_system()
```

# 9 数据安全处理
## 9.1 将敏感对象发送出信任区域前进行签名并加密
**【描述】**
将敏感对象发送出信任区域前进行签名并加密。

敏感数据传输过程中存在被窃取和恶意篡改的可能。使用安全的加密算法加密传输对象可以保护数据，防止对象被非法篡改，保持其完整性。在以下场景中，需要对对象加密和数字签名来保证数据安全:
1. 序列化或传输敏感数据；
2. 没有使用类似于SSL传输通道；
3. 敏感数据需要长久保存（比如在硬盘驱动器上）。

**【修改建议】**
敏感信息跨信任域传递时要进行签名并加密。

**【正例】**
1：敏感信息跨信任域传递时未进行签名并加密
- 修复示例：加密，以SHA256算法为例
```python
import crypt

sensitive_data = "********"
file_path = 'xxxx'
sensitive_data = crypt.crypt(sensitive_data, crypt.METHOD_SHA256)  # 符合，加密，以SHA256算法为例
with open(file_path, 'w') as file:
    file.write(sensitive_data)
```

**【反例】**
1：敏感信息跨信任域传递时未进行签名并加密
- 错误示例：
```python
sensitive_data = "********"
file_path = 'xxxx'

with open(file_path, 'w') as file:
    file.write(sensitive_data)  # 不符合
```

## 9.2 安全场景下必须使用密码学意义上的安全随机数
**【描述】**
安全场景下必须使用密码学意义上的安全随机数。

Python的random模块实现了基于各种分布的伪随机数生成器。产生的随机数可以是均匀分布、高斯分布、对数正态分布、负指数分布以及alpha、beta分布，但是这些随机数都是伪随机数，禁止应用于安全加密目的的应用中。出于安全加密目的的应用中禁止使用random模块生成的伪随机数，必须使用安全随机数。

**【修改建议】**
出于安全加密目的的应用中禁止使用random模块生成的伪随机数，必须使用安全随机数。

**【正例】**
1：使用random模块生成伪随机数
- 修复示例：Python3.6以上版本的os.urandom方法以阻塞模式从系统指定的随机源获取随机字节，因此使用os.urandom方法生成随机数是安全的：
```python
import os
import platform

# 长度请参见密码算法规范，不同场景要求长度不一样
rand_length = 16

# 符合：Python3.6以上版本使用os.urandom生成随机数
_rand_lst = list(os.urandom(rand_length))
print(_rand_lst)
_rand_bytes = os.urandom(rand_length)
print(_rand_bytes)
```
- 修复示例：推荐使用secrets模块生成随机数
```python
import secrets

# 符合：使用secrets模块生成随机数
number = secrets.randbelow(10)
print("Secure random number is ", number)
# Secure random number is 0
secrets_generator = secrets.SystemRandom()
random_number = secrets_generator.randint(0, 50)
print("Secure random number is ", random_number)
# Secure random number is 26

# 指定范围并设置步长
random_number = secrets_generator.randrange(5, 50, 5)
print("Secure random number within range is ", random_number)
# Secure random number within range is 15

# 从指定的数据集中选择
number_list = [6, 12, 18, 24, 30, 36, 42, 48, 54, 60]
secure_choice = secrets_generator.choice(number_list)
print("Secure random choice using secrets is ", secure_choice)
# Secure random choice using secrets is 30
secure_sample = secrets_generator.sample(number_list, 3)
print("Secure random sample using secrets is ", secure_sample)
# Secure random sample using secrets is [54, 6, 42]

# 随机安全浮点数
secure_float = secrets_generator.uniform(2.5, 25.5)
print("Secure random float number using secrets is ", secure_float)
# Secure random float number using secrets is 9.445927455984885
```

**【反例】**
1：使用random模块生成伪随机数
- 错误示例：使用random模块生成伪随机数
```python
import random

# 不符合：使用random模块生成伪随机数
sr = random.randint(0, 100)
```

## 9.3 必须使用ssl.SSLSocket代替socket.Socket来进行安全数据交互
**【描述】**
禁止使用socket.Socket传输敏感数据。

必须使用ssl.SSLSocket代替socket.Socket来进行安全数据交互。

Python提供的socket.Socket类可用于创建套接字，传输敏感数据时必须使用ssl.SSLSocket类创建安全套接字，该类提供了SSL/TSL等安全传输协议来确保传输通道不受攻击者的监听或恶意篡改。

SSLSocket类提供的主要保护包括：
1. 完整性保护：SSL防止消息被主动窃取者篡改。
2. 认证：在大多数模式下，SSL都对对端进行认证。服务器通常都被认证，如果服务器要求，客户端也可以被认证。
3. 保密性：在大多数模式下，SSL对客户端和服务器之间传输的数据进行加密。这样保护了数据的隐私性，被动窃听器不能监听诸如财务或者个人信息等敏感信息。

**【修改建议】**
使用SSL/TLS安全协议保护传输的报文。

**【正例】**
1：服务端
- 修复示例：使用ssl.SSLSocket进行安全数据交互

```python
import socket
import time
import ssl

# 使用ssl.SSLSocket
context = ssl.SSLContext(ssl.PROTOCOL_TLSv1_2)
context.load_cert_chain(certfile="zxcert.pem", keyfile="zxkey.pem")

bindsocket = socket.socket()
print("socket create success")
bindsocket.bind(('127.0.0.1', 10023))
print("socket bind success")
bindsocket.listen(5)
print("socket listen success")

def do_something(connstream, data):
    print("data length:", len(data))
    return True

def deal_with_client(connstream):
    t_recv = 0
    t_send = 0
    n = 0
    t1 = time.perf_counter()
    data = connstream.recv(1024)
    t2 = time.perf_counter()
    print("receive time:", t2 - t1)
    # empty data means the client is finished with us
    while data:
        if not do_something(connstream, data):
            # we'll assume do_something returns False
            # when we're finished with client
            break
        n = n + 1
        t1 = time.perf_counter()
        connstream.send(b'b' * 1024)  # 符合
        t2 = time.perf_counter()
        t_send += t2 - t1
        print("send time:", t2 - t1)
        t1 = time.perf_counter()
        data = connstream.recv(1024)  # 符合
        t2 = time.perf_counter()
        t_recv += t2 - t1
        print("receive time:", t2 - t1)
    print("avg send time:", t_send / n, "avg receive time:", t_recv / n)

# finished with client
while True:
    newsocket, fromaddr = bindsocket.accept()
    print("socket accept one client")
    connstream = context.wrap_socket(newsocket, server_side=True)
    try:
        deal_with_client(connstream)
    finally:
        connstream.shutdown(socket.SHUT_RDWR)
        connstream.close()
```
2：客户端
- 修复示例：使用ssl.SSLSocket进行安全数据交互

```python
import pprint
import socket
import time
import ssl

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
print("socket create success")
# require a certificate from the server
# 使用ssl.SSLSocket
ssl_sock = ssl.wrap_socket(s,
                           ca_certs="zxcert.pem",
                           cert_reqs=ssl.CERT_REQUIRED)
ssl_sock.connect(('127.0.0.1', 10023))
print("socket connect success")
pprint.pprint(ssl_sock.getpeercert())
# note that closing the SSLSocket will also close the underlying socket
n = 0
t_send = 0
t_recv = 0
while n < 10:
    n = n + 1
    t1 = time.perf_counter()
    ssl_sock.send(b'a' * 100)  # 符合
    t2 = time.perf_counter()
    t_send += t2 - t1
    print("send time:", t2 - t1)
    t1 = time.perf_counter()
    data = ssl_sock.recv(1024)
    t2 = time.perf_counter()
    t_recv += t2 - t1
    print("receive time:", t2 - t1)
print("avg send time:", t_send / n, "avg receive time:", t_recv / n)
ssl_sock.close()
```

**【反例】**
1：服务端
- 错误示例：使用socket.Socket进行数据交互

```python
import socket

# Symbolic name meaning all available interfaces
HOST = ''
# Arbitrary non-privileged port
PORT = 50007
# 使用socket.Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((HOST, PORT))
s.listen(1)
conn, addr = s.accept()
print('Connected by', addr)
while True:
    data = conn.recv(1024)  # 不符合
    if not data:
        break
    conn.sendall(data)  # 不符合
conn.close()
```
2：客户端
- 错误示例：使用socket.Socket进行数据交互

```python
import socket

# The remote host
HOST = ''
# The same port as used by the server
PORT = 50007
# 使用socket.Socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))
s.sendall(b'Hello, world')  # 不符合
data = s.recv(1024)
s.close()
print('Received', repr(data))
```

## 9.4 禁止在用户界面、日志中暴露不必要信息
**【描述】**
如果攻击者获取了系统所涉及的操作系统、中间件、应用程序、实现细节、密钥、个人隐私信息等，则会根据这些信息进行有针对性的漏洞分析或直接窃取有效价值信息
因此，要避免将系统信息暴露出来，具体而言：
- 禁止在未经认证的用户界面明文显示软件信息
- 禁止在用户界面、日志中明文显示或打印会话标识、敏感个人信息等
- 禁止在用户界面、日志中明文或密文显示或打印认证凭据。若因为特殊原因需要显示或记录的，可用固定长度的星号(*)代替

**用户界面认证前禁止显示或打印的信息包括：产品安装和使用的软件和冷热补丁名称和版本号、软件版本号、操作系统及版本号、单板名称、内存地址、内存布局、处理器类型、二进制文件名、设备架构、进程名称、文件结构、服务器物理绝对路径、设备文件结构、函数调用痕迹、主机列表、SQL语句、数据库名/表名、堆栈、SAL指令、会话标识、认证凭据、敏感个人信息数据**

用户界面认证后可以根据业务必须的信息按照最小化原则显示：产品安装和使用的软件和冷热补丁名称和版本号、软件版本号、操作系统及版本号、单板名称、内存地址、内存布局、处理器类型、二进制文件名、设备架构、进程名称、文件结构、服务器物理绝对路径、设备文件结构、函数调用痕迹、主机列表、SQL语句、数据库名/表名、堆栈、SAL指令， **禁止显示或打印会话标识、认证凭据、敏感个人信息数据**

后台日志中可以根据问题定位等业务必须场景，按照最小化原则显示或打印：产品安装和使用的软件和冷热补丁名称和版本号、软件版本号、操作系统及版本号、单板名称、内存地址、内存布局、处理器类型、二进制文件名、设备架构、进程名称、文件结构、服务器物理绝对路径、设备文件结构、函数调用痕迹、主机列表、SQL语句、数据库名/表名、堆栈、SAL指令， **禁止显示或打印会话标识、认证凭据、敏感个人信息数据**

**【正例】**
```python
# Python调用Python，可以使用import和函数调用的方式传递敏感信息
from demo import send_requests

results = send_requests(url, headers={"x-auth-token": "xxx"})
```

Bash调用Python，可以使用标准输入或环境变量的方式传递敏感信息。
注意，使用标准输入方式时，攻击者可以通过读取进程句柄获取对应数据（如linux环境使用`cat /proc/{pid}/fd/0`命令），需要结合用户权限进行访问控制。
```python
#!/bin/bash
python ./get_env.py <<< "xxx"

#!/usr/bin/env python
import sys

if __name__ == '__main__':
    password = sys.stdin.read()
```

使用环境变量方式时，攻击者可以通过读取进程环境变量获取对应数据（如linux环境使用`cat /proc/{pid}/environ`命令），需要结合用户权限进行访问控制。
```python
#!/bin/bash
env password=xxx python ./get_env.py

#!/usr/bin/env python
import os

if __name__ == '__main__':
    if 'password' in os.environ:
        password = os.environ['password']
        del os.environ['password']
```

Python调用Bash，可以使用环境变量的方式传递敏感信息
使用环境变量方式时，攻击者可以通过读取进程环境变量获取对应数据（如linux环境使用`cat /proc/{pid}/environ`命令），需要结合用户权限进行访问控制。
```python
#!/usr/bin/env python
import os

def change_password(password):
    os.putenv("PASSWORD", password)
    execute_command("change_password.sh")
    os.unsetenv("PASSWORD")

#!/bin/bash
password=${PASSWORD}
```

**【反例】**
```python
# 在日志中打印敏感信息
import logging

...
logging.info("Login success, user is:%s, password is:%s", user_name, encrypt(password))
```