- [1 数据类型](#1-数据类型)
  - [1.1 进行数值运算时，避免整数溢出](#11-进行数值运算时避免整数溢出)
  - [1.2 确保除法运算和取余运算中的除数不为0](#12-确保除法运算和取余运算中的除数不为0)
  - [1.3 禁止使用浮点数作为循环计数器](#13-禁止使用浮点数作为循环计数器)
  - [1.4 内存中的敏感信息使用完毕后立即清0](#14-内存中的敏感信息使用完毕后立即清0)
- [2 表达式](#2-表达式)
  - [2.1 不要在单个表达式中对相同的变量赋值超过一次](#21-不要在单个表达式中对相同的变量赋值超过一次)
  - [2.2 禁止直接使用可能为null的对象，防止出现空指针引用](#22-禁止直接使用可能为null的对象防止出现空指针引用)
  - [2.3 防止通过异常泄露敏感信息](#23-防止通过异常泄露敏感信息)
  - [2.4 方法抛出的异常，应该与本身的抽象层次相对应](#24-方法抛出的异常应该与本身的抽象层次相对应)
- [3 并发与多线程](#3-并发与多线程)
  - [3.1 使用相同的顺序请求和释放锁来避免死锁](#31-使用相同的顺序请求和释放锁来避免死锁)
  - [3.2 对共享变量做同步访问控制时需避开同步陷阱](#32-对共享变量做同步访问控制时需避开同步陷阱)
  - [3.3 在异常条件下，保证释放已持有的锁](#33-在异常条件下保证释放已持有的锁)
- [4 输入和输出](#4-输入和输出)
  - [4.1 使用外部数据构造的文件路径前必须进行校验，校验前必须对文件路径进行规范化处理](#41-使用外部数据构造的文件路径前必须进行校验校验前必须对文件路径进行规范化处理)
  - [4.2 从ZipInputStream中解压文件必须进行安全检查](#42-从zipinputstream中解压文件必须进行安全检查)
  - [4.3 临时文件使用完毕必须及时删除](#43-临时文件使用完毕必须及时删除)
- [5 序列化](#5-序列化)
  - [5.1 序列化操作要防止敏感信息泄露](#51-序列化操作要防止敏感信息泄露)
  - [5.2 防止反序列化被利用来绕过构造方法中的安全操作](#52-防止反序列化被利用来绕过构造方法中的安全操作)
  - [5.3 禁止直接将外部数据进行反序列化](#53-禁止直接将外部数据进行反序列化)
- [6 外部数据校验](#6-外部数据校验)
  - [6.1 外部数据使用前必须进行合法性校验](#61-外部数据使用前必须进行合法性校验)
  - [6.2 禁止直接使用外部数据来拼接SQL语句](#62-禁止直接使用外部数据来拼接sql语句)
  - [6.3 禁止直接使用外部数据构造格式化字符串](#63-禁止直接使用外部数据构造格式化字符串)
  - [6.4 禁止直接向Runtime.exec()方法或java.lang.ProcessBuilder类传递外部数据](#64-禁止直接向runtimeexec方法或javalangprocessbuilder类传递外部数据)
  - [6.5 禁止直接使用外部数据来拼接XML](#65-禁止直接使用外部数据来拼接xml)
  - [6.6 防止解析来自外部的XML导致的外部实体（XML External Entity）攻击](#66-防止解析来自外部的xml导致的外部实体xml-external-entity攻击)
  - [6.7 防止解析来自外部的XML导致的内部实体扩展（XML Entity Expansion）攻击](#67-防止解析来自外部的xml导致的内部实体扩展xml-entity-expansion攻击)
  - [6.8 禁止使用不安全的XSLT转换XML文件](#68-禁止使用不安全的xslt转换xml文件)
  - [6.9 正则表达式要尽量简单，防止ReDos攻击](#69-正则表达式要尽量简单防止redos攻击)
  - [6.10 禁止直接使用外部数据作为反射操作中的类名/方法名](#610-禁止直接使用外部数据作为反射操作中的类名方法名)
- [7 日志](#7-日志)
  - [7.1 禁止直接使用外部数据记录日志](#71-禁止直接使用外部数据记录日志)
- [8 性能和资源管理](#8-性能和资源管理)
  - [8.1 进行IO类操作时，必须在try-with-resource或finally里关闭资源](#81-进行io类操作时必须在try-with-resource或finally里关闭资源)
- [9 其他](#9-其他)
  - [9.1 安全场景下必须使用密码学意义上的安全随机数](#91-安全场景下必须使用密码学意义上的安全随机数)
  - [9.2 必须使用SSLSocket代替Socket来进行安全数据交互](#92-必须使用sslsocket代替socket来进行安全数据交互)
  - [9.3 禁止代码中包含公网地址](#93-禁止代码中包含公网地址)
  - [9.4 禁止在用户界面、日志中暴露不必要信息](#94-禁止在用户界面日志中暴露不必要信息)

# 1 数据类型
## 1.1 进行数值运算时，避免整数溢出
**【描述】**
在进行数值运算过程中，确保运算结果在特定的整数类型的数据范围内，避免溢出，导致非预期的结果。 

内置的整数运算符不会以任何方式来标识运算结果的上溢或下溢。常见的加、减、乘、除都可能会导致整数溢出。另外，Java数据类型的合法取值范围是不对称的（最小值的绝对值比最大值大1），所以对最小值取绝对值( java.lang.Math.abs())时，也会导致溢出。

**【修改建议】**
可以通过先决条件检测、使用Math类的安全方法、向上类型转换或者使用BigInteger 等方法进行规避。

**【正例】**
场景1：使用int类型统计文件大小
- 修复示例：使用long类型变量统计总文件大小，可以有效避免出现整数溢出问题。
 ```java
public void unzip(FileInputStream zipFileInputStream, String dir) throws IOException {
    try (ZipInputStream zis = new ZipInputStream(zipFileInputStream)) {
        long totalFileSize = 0L;
        int readSize = 0;
        while ((entry = zis.getNextEntry()) != null) {
            ...
            while ((readSize = zis.read(buf)) != -1) {
                totalFileSize += readSize; // 使用long型统计文件总大小，可避免出现整数溢出问题
                if(totalFileSize >= MAX_TOTAL_FILE_SIZE) {
                    ...
                }
            ...
```

**【反例】**
场景1：使用int类型统计文件大小
- 错误示例：在统计zip文件中解压文件的总大小时使用了int类型的变量，由于int类型的上限为2G，当统计的文件总大小超过2G时将发生整数溢出。

```java
public void unzip(FileInputStream zipFileInputStream, String dir) throws IOException {
    try (ZipInputStream zis = new ZipInputStream(zipFileInputStream)) {
        int totalFileSize = 0;
        int readSize = 0;
        while ((entry = zis.getNextEntry()) != null) {
            ...
            while ((readSize = zis.read(buf)) != -1) {
                totalFileSize += readSize; // 使用int型变量统计文件总大小，容易出现整数溢出问题
                if(totalFileSize >= MAX_TOTAL_FILE_SIZE) {
                    ...
                }
            ...
```

## 1.2 确保除法运算和取余运算中的除数不为0
**【描述】**
除零错误。

如果除法或取余运算中的除数为零可能会导致程序终止或拒绝服务（DoS），因此需要在运算前保证除数不为0。

**【修改建议】**
对除数进行非零判断，然后再进行除法或取余运算。

**【正例】**
场景1：除数未判断是否为0
- 修复示例：

```java
private HostAddress getAddr() {
    List<HostAddress> addr = nebulaSessPoolConfig.getGraphAddrArray();
    if (CollectionUtils.isNotEmpty(addr)) { // 校验addr是否为空
        int newPos = (pos.getAndIncrement()) % addr.size();
        return addr.get(newPos);
    }
    ... // 其他逻辑代码
}
```

**【反例】**
场景1：除数未判断是否为0
- 错误示例1：

```java
public void testcase() {
    long dividendNum = 0;
    int divisorNum1 = 0;
    Integer divisorNum2 = 0;
    // POTENTIAL FLAW: 使用除法运算或模运算没有判断除数大小。
    long result1 = dividendNum / divisorNum1;
    // POTENTIAL FLAW: 使用除法运算或模运算没有判断除数大小。
    long result2 = dividendNum % divisorNum2;
}
```
- 错误示例2：
```java
private HostAddress getAddr() {
    List<HostAddress> addr = nebulaSessPoolConfig.getGraphAddrArray();

    // 【POTENTIAL FLAW】 集合中的元素数量可能为0。
    int newPos = (pos.getAndIncrement()) % addr.size();
    return addr.get(newPos);
}
```

## 1.3 禁止使用浮点数作为循环计数器
**【描述】**
由于浮点数存在精度问题，用作循环计数器可能会导致非预期的结果（如循环次数与预期不符、导致死循环等）。

**【修改建议】**
使用整数作为循环计数器。

**【正例】**
场景1：浮点数作为循环计数器
- 修复示例1：

  ```java
  for (int index = 2000000000; index < 2000000050; index++) {
      ...
  }
  ```

**【反例】**
场景1：浮点数作为循环计数器
- 错误示例：由于浮点数的精度问题导致条件判断结果与预期不符：因为`(float) 2000000000 == 2000000050`结果为true，所以循环体不会执行。

  ```java
  for (float flt = (float) 2000000000; flt < 2000000050; flt++) {
      ...
  }
  ```

## 1.4 内存中的敏感信息使用完毕后立即清0
**【描述】**
内存中的敏感信息使用结束后如果不及时清理，会存在敏感信息泄露的风险。应尽量减少敏感信息在内存中的生命周期，使用结束后立即清0。java中的string是不可变对象（创建后无法更改），使用string保存口令、密钥等敏感信息时，这些敏感信息会一直中内存中直到被垃圾收集器回收（其生命周期不可控），如果进程的内存被dump，会导致敏感信息泄露风险。

内存中的敏感信息不能依赖垃圾收集器回收的机制，应该在使用结束时主动将内存中的信息清0.为了方便内存的清理，推荐优先使用`char[]/byte[]`存储敏感信息。对于必须使用String进行数据处理的场景，不需要将String转为`char[]`这样的无效处理，但要对所有涉及敏感信息的String中的信息进行清理。

**【反例】**
  ```java
  void doSomething() {
    String password = getPassword();
    verifyPassword(password);
    ...
  }

  boolean verifyPassword(String pwd) {
    ...
  }
  ```

**【正例】**
对String类型中的信息进行清0处理
  ```java
  void doSomething() {
    String password = getPassword();
    verifyPassword(password);
    try {
        Field valueFieldOfString = String.class.getDeclaredField("value");
        valueFieldOfString.setAccessible(true);
        char[] value = (char[]) valueFieldOfString.get(password);
        Arrays.fill(value, (char)0x00);
    }
    ...
  }
  ```
使用char[]存储密码
  ```java
  void doSomething() {
    char[] password = getPassword();
    verifyPassword(password);
    ...
    // 清除password
    Arrays.fill(password, (char)0x00);
  }

  boolean verifyPassword(String pwd) {
    ...
  }
  ```

# 2 表达式
## 2.1 不要在单个表达式中对相同的变量赋值超过一次
**【描述】**
对相同的变量进行多次赋值的表达式会产生混淆，并且容易产生非预期的行为。清晰的变量赋值会使代码更易懂，也更能保证程序按预期运行。

**【修改建议】**
不要在单个表达式中对相同的变量赋值超过一次

**【正例】**
场景1：使用count对循环计数，而实际count最终结果却为0
- 修复示例1：

  ```java
  int count = 0;
  for (int i = 0; i < 100; i++) {
      ...
      count++;
  }
  System.out.println(count); // count的值为100   
  ```

**【反例】**
场景1：使用count对循环计数，而实际count最终结果却为0
- 错误示例：

  ```java
  int count = 0;
  for (int i = 0; i < 100; i++) {
      ...
      count = count++;
  }
  System.out.println(count); // count的值为0
  ```

## 2.2 禁止直接使用可能为null的对象，防止出现空指针引用
**【描述】**
访问可能为null的对象的成员。

访问一个为null的对象时，会导致空引用问题，代码中抛出 NullPointerException 。该类问题应该通过预检查的方式进行消解，而不是通过 try...catch 机制处理 NullPointerException 。

**【修改建议】**
可以为null的字段声明为@Nullable。

**【正例】**
场景1：
- 修复示例：对于方法返回值可能为null时，方法返回值使用前进行判空处理
```java
@Nullable
protected JobResult executeJob(EdgeJobContext context) {
    JobResult jobResponse = null;
    if (StrUtil.equals(EdgeJobConstants.ShellJobExecuteType.COMMAND, executeType)) {
        jobResponse = edgeShellClient.executeByCommand(command);
    } else if (StrUtil.equals(EdgeJobConstants.ShellJobExecuteType.SCRIPT, executeType)) {
        jobResponse = edgeShellClient.executeByCommand(remotePath, command); 
    }

    return jobResponse;
}

JobResult jobResult = executeJob(context);
if （jobResult  != null）{
    jobResult.setId(startJobRequest.getId()); //jobResult有可能会返回null值，所有这个jobResult需要增强null处理
 }
```

**【反例】**
场景1：
- 错误示例：对于方法返回值可能为null时，方法返回值使用前未做判空处理

```java
@Nullable
protected JobResult executeJob(EdgeJobContext context) {
    JobResult jobResponse = null;
    if (StrUtil.equals(EdgeJobConstants.ShellJobExecuteType.COMMAND, executeType)) {
        jobResponse = edgeShellClient.executeByCommand(command);
    } else if (StrUtil.equals(EdgeJobConstants.ShellJobExecuteType.SCRIPT, executeType)) {
        jobResponse = edgeShellClient.executeByCommand(remotePath, command); 
    }

    return jobResponse;
}

JobResult jobResult = executeJob(context);
jobResult.setId(startJobRequest.getId()); //jobResult有可能会返回null值，所有这个jobResult需要增加null处理
```

## 2.3 防止通过异常泄露敏感信息
**【描述】**
异常信息通过日志造成敏感信息泄露。

程序抛出的异常中，可能会包含一些敏感信息，将这些异常直接记录到日志或反馈给用户，会导致敏感信息泄露风险。另外，即使异常中不含敏感信息，但是直接将异常反馈给用户，该动作本身可能就会导致敏感信息泄露风险，比如系统访问用户指定的文件路径，当该路径不存在时，系统给用户反馈一个过滤了敏感信息的异常，恶意用户可以根据系统是否抛出异常来构造文件路径，达到对系统的文件目录结构进行探测的目的。除此之外，三方件也可能会抛出携带敏感信息的异常，如 JSONException 等。

**【修改建议】**
提取异常信息的具体内容，并对敏感信息做匿名化处理后再输出到日志中。

**【正例】**
```java    
public class Exception_Sensitive_Information_into_Log {
    public void good() {
        try {
            ...
        } catch (Exception e) {
            // 对异常信息做匿名化处理
            Log.error(sensitiveInfoFilter(e));
        }
    }
}
```

**【反例】**
```java  
public class Exception_Sensitive_Information_into_Log {
    public void bad(){
      try {
          ...
      } catch (Exception e) {
          // 直接输出会打印全路径、堆栈等系统敏感信息
          Log.error(e);
      }
    }
}
```

## 2.4 方法抛出的异常，应该与本身的抽象层次相对应

**【描述】**
方法抛出异常时，应该避免直接抛出RuntimeException，更不应该直接抛出Exception或Throwable，因为这些父类异常无法与异常发生的场景相关联，直接抛出父类异常会降低代码可读性。方法抛出的异常应该与方法本身的抽象层次相对应，这些异常可以是JDK中定义的标准异常，也可以是业务层自定义的异常。另外，抛出的异常中应该包含理解该异常产生原因的所有信息。

**【修改建议】**
方法抛出的异常应该是含有明确异常产生原因的具体异常类型（非基类异常）。

**【正例】**
场景1：
- 修复示例： 

  ```java
    public class Employee {
        ...
        public String getSomeInfo() throws MyBizException{
            ...
            throw new MyBizException("xxx");
        }
        ...
    }
  ```

**【反例】**
场景1：
- 错误示例：直接抛出RuntimeException

  ```java
    public class Employee {
        ...
        public String getSomeInfo() {
            ...
            throw new RuntimeException("xxx");
        }
        ...
    }
  ```

# 3 并发与多线程
## 3.1 使用相同的顺序请求和释放锁来避免死锁
**【描述】**
当两个不同的线程试图以相反的顺序获取两个锁时，就会发生死锁。
当程序涉及到使用多个锁对资源进行同步时，编码过程中，要仔细考虑锁的顺序，尽量以相同的顺序来请求和释放锁，避免发生死锁。不能为了避免死锁的发生，扩大锁的使用范围，影响系统性能。

**【修改建议】**
两个不同的线程需要以相同的顺序获取两个锁。

**【正例】**
相同的顺序获取两个锁。
```java
final class BankAccount implements Comparable<BankAccount> {
    private static AtomicLong nextId = new AtomicLong(0); // 下一个未使用的ID
    private double balanceAmount; // 银行账户中的总金额
    private final long id; // 每一个银行账户的id都是唯一的
    private BankAccount(double balance) {
        this.balanceAmount = balance;
        this.id = nextId.getAndIncrement();
    }
    @Override
    public int compareTo(BankAccount ba) {
        return (this.id > ba.id) ? 1 : (this.id < ba.id) ? -1 : 0;
    }
    // 将当前账户对象的存款转寄给另一个银行账号实例ba
    public void depositAmount(BankAccount ba, double amount) {
        BankAccount former;
        BankAccount latter;
        if (compareTo(ba) < 0) {
            former = this;
            latter = ba;
        } else {
            former = ba;
            latter = this;
        }
        synchronized (former) {
            synchronized (latter) {
                if (amount > balanceAmount) {
                    throw new IllegalArgumentException("XXX...");
                }
                ba.balanceAmount += amount;
                this.balanceAmount -= amount;
            }
        }
    }
    public static void initiateTransfer(BankAccount first, BankAccount second, double amount) {
        Thread transfer = new Thread(new Runnable() {
            @Override
            public void run() {
                first.depositAmount(second, amount);
            }
        });
        transfer.start();
    }
}
```

**【反例】**
不同顺序获取两个锁。
```java
final class BankAccount {
    private double balanceAmount; // 银行账户中的总金额
    BankAccount(double balance) {
        this.balanceAmount = balance;
    }
    // 将当前账户对象的存款转寄给另一个银行账号实例ba
    private void depositAmount(BankAccount ba, double amount) {
        synchronized (this) {
            synchronized (ba) {
                if (amount > balanceAmount) {
                    throw new IllegalArgumentException("Transfer cannot be completed");
                }
                ba.balanceAmount += amount;
                this.balanceAmount -= amount;
            }
        }
    }
    public static void initiateTransfer(BankAccount first, BankAccount second, double amount) {
        Thread transfer = new Thread(new Runnable() {
            public void run() {
              first.depositAmount(second, amount);
            }
        });
        transfer.start();
    }
    public static void main(String[] args) {
        BankAccount bankA = new BankAccount(15000);
        BankAccount bankB = new BankAccount(9000);
        initiateTransfer(bankA, bankB, 500);
        initiateTransfer(bankB, bankA, 1400);
        ...
    }
}
```

## 3.2 对共享变量做同步访问控制时需避开同步陷阱
**【描述】**
避免使用class类对象作为锁传递给synchronized。

如果使用class类对象作为同步对象，父子类继承关系增加了class类对象归属的复杂度，开发人员容易犯错，导致同步行为不符合预期；故应避免使用class这类容易造成歧义的对象，而应使用明确的对象。

**【修改建议】**
禁止基于getClass()返回的类对象进行同步。

**【正例】**
场景1：错误使用getClass()对象
- 修复示例：使用明确Class对象作为锁

```java
class Base {
    static DateFormat format = DateFormat.getDateInstance(DateFormat.MEDIUM);

    public Date parse(String str) throws ParseException {
        try {
            synchronized (Class.forName("Base")) {
                return format.parse(str);
            }
        }
        catch (ClassNotFoundException x) {
            // "Base" not found; handle error
        }
        return null;
    }
}

class Derived extends Base {
    public Date doSomethingAndParse(String str) throws ParseException {
        synchronized (Base.class) {
            ...
            return format.parse(str);
        }
    }
}
```

**【反例】**
场景1：错误使用getClass()对象
- 错误示例：基于getClass()返回的类对象进行同步。

```java
class Base {
    static DateFormat format = DateFormat.getDateInstance(DateFormat.MEDIUM);

    public Date parse(String str) throws ParseException {
        synchronized (getClass()) {
            return format.parse(str);
        }
    }
}

class Derived extends Base {
    public Date doSomethingAndParse(String str) throws ParseException {
        synchronized (Base.class) {
            ...
            return format.parse(str);
        }
    }
}
```

**【描述】**
避免使用 Lock或Condition实现类(高级并发类) 对象本身 作为锁传递给synchronized。

使用了基于高级并发对象的synchronized块。高级并发类是指实现java.util.concurrent.locks包中的Lock或Condition接口的类，其本身提供了lock与unlock来实现同步，不应将这些类的对象作为synchronized块的同步对象使用。当使用基于高层并发对象的synchronized块时，容易被误认为这种方式与正常使用lock接口的方式是同一个锁，而实际是两个不同的锁，会导致无法实现同步控制。

**【修改建议】**
使用Lock接口提供的lock()和unlock()方法。

**【正例】**
场景1：并发对象
- 修复示例1：updateResource() 和 doSomething() 方法中使用了Lock接口提供的lock()和unlock()方法。
```java
public class SomeSharedResource {
    private final Lock lock = new ReentrantLock();
    public void updateResource() {
        lock.lock();
        try {

            // 更新共享的资源
            ...
        } finally {
            lock.unlock();
        }
    }
    public void doSomething() {
        lock.lock();
        try {

            // 更新共享的资源
            ...
        } finally {
            lock.unlock();
        }
    }
}
```

**【反例】**
场景1：并发对象
- 错误示例：updateResource() 和 doSomething() 方法中使用不是同一个锁。

```java
public class SomeSharedResource {
    private final Lock lock = new ReentrantLock();
    public void updateResource() {
        // 【POTENTIAL FLAW】 
        synchronized (lock) {

            // 更新共享的资源
            ...
        }
    }
    public void doSomething() {
        lock.lock();
        try {

            // 更新共享的资源
            ...
        } finally {
            lock.unlock();
        }
    }
}
```

**【描述】**
禁止使用一个实例锁来同步静态共享数据。

实例锁的同步效果仅限于此实例本身，无法用来同步静态共享变量；如果试图使用实例锁来同步静态共享变量，在多实例情况下无法实现符合预期的同步效果。

**【修改建议】**
禁止使用一个实例锁来同步静态共享数据。

**【正例】**
场景1：同步块
- 修复示例：使用静态锁来同步静态共享变量。
```java
public class SomeSharedResource {
    private static volatile int counter;
    private static final Object lock = new Object();
    public void updateResource() {
        synchronized (lock) {
            counter++;
        }
    }
}
```
场景2：同步方法
- 修复示例1：同步静态方法访问静态资源
```java
public class SomeSharedResource {
    private static volatile int counter;

    public static synchronized void updateResource() {
        counter++;
    }
}
```

**【反例】**
场景1：同步块
- 错误示例：使用实例锁来同步静态共享变量。

```java
public class SomeSharedResource {
    private static volatile int counter;

    // 【POTENTIAL FLAW】 非静态
    private final Object lock = new Object();
    public void updateResource() {
        synchronized (lock) {
            counter++;
        }
    }
}
```
场景2：同步方法
- 错误示例：同步非静态方法中访问静态资源

```java
public class SomeSharedResource {
    private static volatile int counter;

    // 【POTENTIAL FLAW】 同步方法
    public synchronized void updateResource() {
        counter++;
    }
}
```

## 3.3 在异常条件下，保证释放已持有的锁
**【描述】**
锁对象如果不能正确释放，可能会导致阻塞或死锁问题。为了保证锁被正确释放，建议在finally中释放锁。

对于加锁操作应在try代码块外部实现，避免因为加锁操作出现异常导致加锁失败，进而导致在finally中释放锁时发生异常。

**【修改建议】**
释放锁操作在finally代码块中实现。

**【正例】**
场景1：加锁期间抛出受检异常
- 修复示例：

```java
public final class Foo {
    private final Lock lock = new ReentrantLock();

    public void correctReleaseLock() {
        lock.lock();
        try {
            doSomething();
        } catch (MyBizException ex) {
            ... // 处理异常
        } finally {
            lock.unlock();
        }
    }

    private void doSomething() throws MyBizException {
        ...
    }
}
```
场景2：加锁期间可能抛出运行时异常
- 修复示例：

```java
final class Foo {
    private final Lock lock = new ReentrantLock();

    public void correctReleaseLock(String value) {
        lock.lock();
        try {
            ...
            int index = Integer.parseInt(value);
            ...
        } finally {
            lock.unlock();
        }
    }
}
```

**【反例】**
场景1：加锁期间抛出受检异常
- 错误示例：

```java
public final class Foo {
    private final Lock lock = new ReentrantLock();

    public void incorrectReleaseLock() {
        try {
            lock.lock();
            doSomething();
            lock.unlock();
        } catch (MyBizException ex) {
            ... // 处理异常
        }
    }

    private void doSomething() throws MyBizException {
        ...
    }
}
```
场景2：加锁期间可能抛出运行时异常
- 错误示例：

```java
final class Foo {
    private final Lock lock = new ReentrantLock();

    public void incorrectReleaseLock(String value) {
        lock.lock();
        ...
        int index = Integer.parseInt(value);
        ...
        lock.unlock();
    }
}
```

# 4 输入和输出
## 4.1 使用外部数据构造的文件路径前必须进行校验，校验前必须对文件路径进行规范化处理
**【描述】**
使用外部数据构造的文件路径。

文件路径来自外部数据时，必须对其合法性进行校验，否则可能会产生路径遍历（Path Traversal）漏洞。

文件路径有多种表现形式，如绝对路径、相对路径，路径中可能会含各种链接、快捷方式、影子文件等，这些都会对文件路径的校验产生影响，所以在文件路径校验前要对文件路径进行规范化处理，使用规范化的文件路径进行校验。对文件路径的规范化处理必须使用getCanonicalPath() ，禁止使用getAbsolutePath() （该方法无法保证在所有的平台上对文件路径进行正确的规范化处理）。

**【修改建议】**
对外部数据进行白名单校验。

**【正例】**
场景1：使用外部数据构造文件路径
- 修复示例1：对外部构造的文件路径先规范化，在进行校验
```java
public void doSomething() {
    File file = new File(HOME_PATH, fileName);
    try {
        String canonicalPath = file.getCanonicalPath();
        if (!validatePath(canonicalPath)) {
            throw new IllegalArgumentException("Path Traversal vulnerability!");
        }
        ... // 对文件进行读写等操作
    } catch (IOException ex) {
        throw new IllegalArgumentException("An exception occurred ...", ex);
    }
}
private boolean validatePath(String path) {
    return path.startsWith(HOME_PATH);
}
```

- 修复示例2：按业务场景，自定义实现文件路径校验逻辑
```java
public void doSomething() throws IOException {
    fileName = request.getParameter("filename");
    ...
    if (FileConfig.filePathIsValid(fileName)) {
        File file = new File(fileName);
        ...
    }
}

public class FileConfig {
    public static boolean filePathIsValid(String filePath) throws UnsupportedEncodingException {
        if (filePath != null && !filePath.equals("")) {
            if (filePath.contains("%")) {
                filePath = filePath.replaceAll("%(?![0-9a-fA-F]{2})", "%25");
            }

            filePath = URLDecoder.decode(filePath, "utf-8");
            return !filePath.contains("..") && !filePath.contains("./") && !filePath.contains(".\\\\") && !filePath.contains("%00");
        } else {
            return true;
        }
    }
}
```

**【反例】**
场景1：使用外部数据构造文件路径
- 错误示例：对使用外部数据构造的文件路径，校验前未进行规范化处理（恶意用户可利用../构造访问HOME_PATH之外的路径）。
```java
public void doSomething() {
    fileName = request.getParameter("filename");
    ...
    File file = new File(HOME_PATH, fileName);
    String path = file.getPath();

// 【POTENTIAL FLAW】 使用非规范化的路径进行校验
    if (!validatePath(path)) {
        throw new IllegalArgumentException("Path Traversal vulnerabilities may exist！");
    }
    ... // 对文件进行读写等操作
}
private boolean validatePath(String path) {
    return path.startsWith(HOME_PATH);
}
```

## 4.2 从ZipInputStream中解压文件必须进行安全检查
**【描述】**
使用ZipEntry.getSize()进行解压尺寸大小的检查。

使用java.util.zip.ZipInputStream 解压zip文件时，可能会有两类安全风险：
1. 将文件解压到目标目录之外
   压缩包中的文件名中如果包含.. ，可能导致文件被解压到目标目录之外，造成任意文件注入、文件恶意篡改等风险。因此，压缩包中的文件在解压前，要先对解压的目标路径进行校验，如果解压目标路径不在预期目录之内，要么拒绝将其解压出来，要么将其解压到一个安全的位置。
2. 解压的文件消耗过多的系统资源
   zip压缩算法可能有很大的压缩比，可以把超大文件压缩成很小的zip文件（例如可以将上G的文件压缩为几K大小），这样的文件解压可能会导致zip炸弹（zip bomb）攻击。所以zip文件解压时，要对解压的实际文件大小进行检查，若解压之后的文件大小超过一定的限制，必须拒绝解压。具体的大小限制根据实际情况来确定。除此之外，解压时，还需要对解压出来的文件数量进行限制，防止zip压缩包中是数量巨大的小文件。

**【修改建议】**
禁止通过ZipEntry.getSize()进行解压尺寸判断。

**【正例】**
场景1：
- 修复示例：

```java
    static final int BUFFER = 512;
    static final int TOOBIG = 0x6400000; // max size of unzipped data, 100MB
    static final int TOOMANY = 1024; // max number of files

    // ...
    // The code validates the name of each entry before extracting the entry.
    // If the name is invalid, the entire extraction is aborted.
    private String sanitizeFileName(String entryName, String intendedDir) throws IOException {
        File f = new File(intendedDir, entryName);
        String canonicalPath = f.getCanonicalPath();
        File iD = new File(intendedDir);
        String canonicalID = iD.getCanonicalPath();
        if (canonicalPath.startsWith(canonicalID)) {
            return canonicalPath;
        } else {
            throw new IllegalStateException("File is outside extraction target directory.");
        }
    }

    public final void doSomething(String fileName, String destDir) throws java.io.IOException {
        FileInputStream fis = new FileInputStream(fileName);
        ZipInputStream zis = new ZipInputStream(new BufferedInputStream(fis));
        ZipEntry entry;
        int total = 0;
        int entries = 0;
        try {
            while ((entry = zis.getNextEntry()) != null) {
                BufferedOutputStream dest = null;
                int count;
                byte data[] = new byte[BUFFER];

                // Write the files to the disk, but ensure that the entryName is valid,and that the file is not insanely big
                String name = sanitizeFileName(entry.getName(), destDir);

                // process file
                FileOutputStream fos = new FileOutputStream(name);
                dest = new BufferedOutputStream(fos, BUFFER);

                // check every entry's size
                while ((count = zis.read(data, 0, BUFFER)) != -1) {
                    total += count;
                    if (total > TOOBIG) {
                        break;
                    }
                    dest.write(data, 0, count);
                }
                entries++;

                // if the total number of entry is larger than the max number,it will throw exception.
                if (entries > TOOMANY) {
                    //handle exception
                }
                // if the total size of zip file is bigger than the max size value,it will throw exception.
                if (total > TOOBIG) {
                    //handle exception
                }
                …
            }
        } finally {
            zis.close();
        }
    }
```

**【反例】**
场景1：
- 错误示例：

```java
    public static final int BUFFER = 512;
    public static final int TOOBIG = 0x6400000; // 100MB

    public final void doSomething(String filename) throws java.io.IOException {
        FileInputStream fis = new FileInputStream(filename);
        ZipInputStream zis = new ZipInputStream(new BufferedInputStream(fis));
        ZipEntry entry;
        try {
            while ((entry = zis.getNextEntry()) != null) {
                System.out.println("Extracting: " + entry);
                int count;
                byte data[] = new byte[BUFFER];

                // Write the files to the disk, but only if the file is not
                // insanely big
                int temp = (int) entry.getSize();

                // 【POTENTIAL FLAW】 检查if语句中是否使用ZipEntry.getSize()来做文件大小的判断，如果是，则报告警。
                if (temp > TOOBIG) {
                    throw new IllegalStateException("File to be unzipped is huge.");
                }
                // 【POTENTIAL FLAW】 检查if语句中是否使用ZipEntry.getSize()来做文件大小的判断，如果是，则报告警。
                if (entry.getSize() == -1) {
                    throw new IllegalStateException("File to be unzipped might be huge.");
                }
                FileOutputStream fos = new FileOutputStream(entry.getName());
                BufferedOutputStream dest = new BufferedOutputStream(fos, BUFFER);
                while ((count = zis.read(data, 0, BUFFER)) != -1) {
                    dest.write(data, 0, count);
                }
                dest.flush();
                dest.close();
                zis.closeEntry();
            }
        } finally {
            zis.close();
        }
    }

```

## 4.3 临时文件使用完毕必须及时删除
**【描述】**
临时文件使用完毕必须及时删除。

程序中有很多使用临时文件的场景，比如用于进程间的数据共享，缓存内存数据，动态构造的类文件，动态连接库文件等。临时文件可能创建于操作系统的共享临时文件目录，例如，POSIX系统下的/tmp与/var/tmp目录，Windows系统下的C:\TEMP目录，这类目录中的文件可能会被定期清理。创建在其他路径下的临时文件不会被自动清理。如果文件未被安全地创建或者用完后还是可访问的，具备本地文件系统访问权限的恶意用户便可以利用共享目录中的文件进行恶意操作，另外，临时文件不清理也可能会导致大量垃圾文件占用磁盘的存储空间。

**【修改建议】**
在临时文件使用完毕之后、系统终止之前，及时对其进行删除。

**【正例】**
场景1：临时文件未删除
- 修复示例：使用了delete方法删除了tempFile文件

```java
private void doSomething(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException{
    File tempFile = null;
    try {
        tempFile = File.createTempFile("temp", "1234");

        /* Set the permissions to avoid insecure temporary file incidentals  */
        if (!tempFile.setWritable(true, true)) {
            System.out.println("Could not set Writable permissions");
        }
        ...
    } catch (IOException exceptIO) {
        System.out.println("Could not create temporary file");
    } finally {
        /* Delete the temporary file manually */
        if (tempFile.exists()) {
            tempFile.delete();
        }
    }
}
```

**【反例】**
场景1：临时文件未删除
- 错误示例：tempFile没有删除操作

```java
@Override
public void doSomething(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    File tempFile = null;
    try {
        // POTENTIAL FLAW: 临时文件使用完毕应及时删除
        tempFile = File.createTempFile("temp", "1234");

        // 当调用deleteOnExit()方法时，只是相当于对deleteOnExit()作一个声明，当程序运行结束，JVM终止时才真正调用deleteOnExit()方法实现删除操作。
        tempFile.deleteOnExit();

        /* Set the permissions to avoid insecure temporary file incidentals */
        if (!tempFile.setWritable(true, true)) {
            System.out.println("Could not set Writable permissions");
        }
        ...
    } catch (IOException exceptIO) {
        System.out.println("Could not create temporary file");
    }
}
```

# 5 序列化
## 5.1 序列化操作要防止敏感信息泄露
**【描述】**
敏感数据传输过程中要防止被窃取和恶意篡改。使用安全的加密算法加密传输对象可以保护数据。这就是所谓的对对象进行密封。而对密封的对象进行数字签名则可以防止对象被非法篡改，保持其完整性。在以下场景中， 需要对对象密封和数字签名来保证数据安全：**支持序列化的对象中含有敏感信息，该对象需要通过非加密通道传递（如http）或该需要在本地持久化保存（如在硬盘驱动器上）**。
应该避免使用私有加密算法，因为这样会引入不必要的漏洞。在readObject()和writeObject()函数中使用私有加密算法的应用是典型的反面示例。
含有敏感数据（如口令、秘钥等）的对象进行序列化操作，其中的敏感数据会被明文转为文本或二进制格式，容易出现敏感信息泄露问题。

**【修改建议】**
含有敏感数据的对象，在序列化操作前要对敏感数据进行防泄漏和防篡改处理，即先签名后加密处理。
对于敏感信息字段，建议使用transient修饰符，该字段不写入序列化结果中；或者是重写writeObject(),不将敏感字段写入序列化结果中。

**【正例】**
场景1：重写writeObject方法，避免将敏感信息写入序列化结果中
- 修复示例1：不应该将含有敏感信息的数据直接写入磁盘
```java
public class SomeInfo implements Serializable {
    private static final long serialVersionUID = 9078808681344666097L;
    private String pwd;
    private String user;

    private void writeObject(ObjectOutputStream out) throws IOException {
        out.writeObject(this.user);     
    }
}
```
场景2：通过`transient`修饰符，避免将敏感信息写入序列化结果中
- 修复示例1：
```java
public class SomeInfo implements Serializable {
    private static final long serialVersionUID = 9078808681344666097L;
    private transient String pwd;
    private String user;
    ...
}
```
场景3：对象序列化后本次存储
- 修复示例：先为对象签名然后再加密。 这样既能保证数据的真实可靠性，又能防止“中间人攻击”（man-inmiddle attacks）。
```java
public class UserInfo implements Serializable {
    private static final long serialVersionUID = -2589766491699675794L;
    private String id;
    private String name;
    private String phone;
    private String address;
    ...
    private void writeObject(ObjectOutputStream outputStream) throws IOException {
        outputStream.writeObject(id);
        outputStream.writeObject(name);

        // 敏感信息序列化前先进行加密
        outputStream.writeObject(encryptData(phone));
        outputStream.writeObject(encryptData(address));
    }
}
// 将UserInfo中的信息通过序列化的方式写入指定文件中
public void saveUserInfoToFile(UserInfo info) {
    ...
    FileOutputStream fos = null;
    try {
        fos = new FileOutputStream("bizFile");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(info);
        ...
    } catch (IOException ex) {
        ... // 处理异常
    }
    ...
}
```
场景4：对象序列化后通过网络传递
- 修复示例：先为对象签名然后再加密。 这样既能保证数据的真实可靠性，又能防止“中间人攻击”（man-inmiddle attacks）。
```java
public void sendUserInfoOverNetwork(UserInfo info) {
    ...
    try {
        ...
        String json = JSON.toJSONString(info);
        String signJson = sign(json);
        String encryptJson = encrypt(signJson);

        // 发送加密后的encryptJson
        ...
    } catch (IOException ex) {
        ... // 处理异常
    }
    ...
}
```

**【反例】**
场景1：重写writeObject方法，避免将敏感信息写入序列化结果中
- 错误示例：直接将敏感数据通过writeObject方法写入序列化结果中

```java
public class SomeInfo implements Serializable {
    private static final long serialVersionUID = 9078808681344666097L;
    private String pwd;
    private String user;

    private void writeObject(ObjectOutputStream out) throws IOException {
        // 【POTENTIAL FLAW】 敏感信息被明文写入序列化结果中
        out.writeObject(this.user);     
        out.writeObject(this.pwd);
    }
}
```
场景2：通过`transient`修饰符，避免将敏感信息写入序列化结果中
- 错误示例：

```java
public class SomeInfo implements Serializable {
    private static final long serialVersionUID = 9078808681344666097L;
    private String pwd;
    private String user;
    ...
}
```
场景3：对象序列化后本次存储
- 错误示例：含敏感数据的对象序列化后写入文件，未对数据进行加密、签名处理。
```java
// 将UserInfo中的信息通过序列化的方式写入指定文件中
public class UserInfo implements Serializable {
    private static final long serialVersionUID = 6562477636399915529L;
    private String id;
    private String name;
    private String phone;
    private String address;
    ...
}

// 将UserInfo中的信息通过序列化的方式写入指定文件中
public void saveUserInfoToFile(UserInfo info) {
    ...
    FileOutputStream fos = null;
    try {
        fos = new FileOutputStream("bizFile");
        ObjectOutputStream oos = new ObjectOutputStream(fos);
        oos.writeObject(info);
        ...
    } catch (IOException ex) {
        ... // 处理异常
    }
    ...
}
```
场景4：对象序列化后通过网络传递
- 错误示例：对敏感数据先加密后签名，无法防止信息被恶意篡改
```java
public void sendUserInfoOverNetwork(UserInfo info) {
    ...
    try {
        ...
        String json = JSON.toJSONString(info);
        String encryptJson = encrypt(signJson);
        String signJson = sign(encryptJson);

        // 发送加密后的signJson
        ...
    } catch (IOException ex) {
        ... // 处理异常
    }
    ...
}
```

## 5.2 防止反序列化被利用来绕过构造方法中的安全操作
**【描述】**
防止反序列化被利用来绕过构造方法中的安全操作。

反序列化操作可以在不执行构造方法的情况下创建对象的实例，所以反序列化操作中的行为应该设计为与构造方法保持一致，这些行为包括：

1. 对参数的校验；

2. 安全管理器的检查；

3. 对属性赋初始值，特别是transient修饰的属性反序列化操作默认不会赋值；

否则，攻击者就可能会通过反序列化操作构造出与预期不符合的对象实例。

**【修改建议】**
如果可序列化的类的构造方法中存在SecurityManager检查，请确保readObject()方法中存在相同的SecurityManager检查。

**【正例】**
场景1：安全管理器的检查。
- 修复示例1：方法中有安全检查器：
```java
public final class SecureSerializeDemo implements Serializable {
    private static final long serialVersionUID = 9078808681344666097L;

    // Private internal state
    private String town;

    private static final String UNKNOWN = "UNKNOWN";

    void performSecurityManagerCheck() throws SecurityException {
        // verify whether current user has rights to access the file
        SecurityManager securityManager = System.getSecurityManager();
    }

    public SecureSerializeDemo () {
        performSecurityManagerCheck();

        // Initialize town to default value
        town = UNKNOWN;
    }

    private void readObject(ObjectInputStream in) throws Exception {
        performSecurityManagerCheck();
        in.defaultReadObject();
    }
}
```

**【反例】**
场景1：安全管理器的检查。
- 错误示例：方法中没有安全检查器。

```java
public final class SecureSerializeDemo implements Serializable {
    private static final long serialVersionUID = 9078808681344666097L;

    // Private internal state
    private String town;

    private static final String UNKNOWN = "UNKNOWN";

    void performSecurityManagerCheck() throws SecurityException {
        // verify whether current user has rights to access the file
        SecurityManager securityManager = System.getSecurityManager();
    }

    public SecureSerializeDemo () {
        performSecurityManagerCheck();
        // Initialize town to default value
        town = UNKNOWN;
    }

    // 【POTENTIAL FLAW】 实现Serializable接口，并且在实现类的构造函数中包含安全检查器 2）基于第一点，工具检测readObject和writeObject方法中是否包含安全检查器，如果没有则报告警。
    private void readObject(ObjectInputStream in) throws Exception {
        in.defaultReadObject();
    }

}
```

## 5.3 禁止直接将外部数据进行反序列化
**【描述】**
直接将外部数据进行反序列化。

反序列化操作是将一个二进制流或字符串反序列化为一个Java对象。当反序列化操作的数据是外部数据时，恶意用户可利用反序列化操作构造指定的对象、执行恶意代码、向应用程序中注入有害数据等。不安全反序列化操作可能导致任意代码执行、特权提升、任意文件访问、拒绝服务等攻击。

实际应用中，通常采用三方件实现对json、xml、yaml格式的数据序列化和反序列化操作。常用的三方件包括：fastjson、jackson、XMLDecoder、XStream、SnakeYmal等。

**【修改建议】**
使用白名单的方式对外部数据进行校验。

**【正例】**
场景1：使用`ScriptEngineManager`执行脚本代码
- 修复示例：
```java
private static void doSomething() {        
    String jsCode = request.getParameter("jscode");
    ...
    NashornSandbox sandbox = new NashornSandboxes();
    sandbox.allow(File.class);  // 使用sandbox.allow()来设置允许使用的java类
    sandbox.eval(jsCode);
    ...
}
```

场景2：使用Fastjson 1.2.68以前版本
- 修复示例：禁用AutoType功能,并设置白名单校验
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ParserConfig.getGlobalInstance().setAutoTypeSupport(false); // 或者是直接删除该行代码
    ParserConfig.getGlobalInstance().addAccept("com.func.test.User"); // 进一步设置白名单校验规则
    User user = (User) JSONObject.parse(jsonStr);
    ...
}
```

场景3：使用Fastjson 1.2.68及以上新版本
- 修复示例1：开启SafeMode
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ParserConfig.getGlobalInstance().setSafeMode(true);
    User user = (User) JSONObject.parse(jsonStr);
    ...
}
```
- 修复示例2：解析Json字符串时，开启SafeMode模式
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ParserConfig.getGlobalInstance().setSafeMode(true);
    User user = JSON.parseObject(jsonStr, User.class, Feature.SafeMode);
    ...
}
```

场景4：使用Jackson
- 修复示例1：对于Jackson 2.10.0以前的版本
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ObjectMapper mapper = new ObjectMapper();
    mapper.disableDefaultTyping();
    UserInfo demo = (UserInfo)mapper.readValue(jsonStr, UserInfo.class);
    ...
}
```
- 修复示例2：对于Jackson 2.10.0及新版本
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ObjectMapper mapper = new ObjectMapper();
    mapper.activeDefaultTyping(...);
    UserInfo demo = (UserInfo)mapper.readValue(jsonStr, UserInfo.class);
    ...
}
```

场景5：使用XStream
- 修复示例1：启用白名单校验
```java
private static void doSomething() {        
    String xmlStr = request.getParameter("xmlData");
    ...
    XStream xst = new XStream(new DomDriver());

    // 首先清除默认设置，然后进行自定义设置
    xst.addPermission(NoTypePermission.NONE);

    // 添加一些基础的类型，如Array、NULL、primitive
    xst.addPermission(ArrayTypePermission.ARRAYS);
    xst.addPermission(NullPermission.NULL);
    xst.addPermission(PrimitiveTypePermission.PRIMITIVES);

    // 添加自定义的类列表
    xst.addPermission(new ExplicitTypePermission(new Class[] { XStreamTest.class }));

    // 添加同一个package下的多个类型
    xst.allowTypesByWildcard(new String[] { XStreamTest.class.getPackage().getName() + ".*" });
    xst.fromXML(xmlStr);
}
```
- 修复示例2：XStream-1.4.10及以上版本,设置默认安全校验
```java
private static void doSomething() {        
    String xmlStr = request.getParameter("xmlData");
    ...
    XStream xst = new XStream(new DomDriver());
    XStream.setupDefaultSecurity(xst);
    xst.fromXML(xmlStr);
}
```

场景6：使用XMLDecoder
- 修复示例：将XMLDecoder改为XStream
```java
private static void doSomething() {        
    String xmlStr = request.getParameter("xmlData");
    ...
    XStream xst = new XStream(new DomDriver());
    XStream.setupDefaultSecurity(xst);
    xst.fromXML(xmlStr);
}
```

场景7：使用SnakeYaml
- 修复示例1：
```java
private static void doSomething() {        
    String yamlStr = request.getParameter("yamlData");
    ...
    Yaml yaml = new Yaml(new SnakeYamlSecureConstructor());
    yaml.load(yamlStr);
}

class SnakeYamlSecureConstructor extends Constructor {
    SnakeYamlSecureConstructor(DeserializeAcceptedClassList whiteList) {
        super();

        this.yamlConstructors.put(null, undefinedConstructor); // 禁止所有的未定义的类进行构造
        addWhiteList(whiteList);  // 增加白名单类的构造
    }

    private void addWhiteList(DeserializeAcceptedClassList whiteList) {
        if (whiteList == null) {
            return;
        }

        for (String whiteName: whiteList) {
            this.yamlConstructors.put(new Tag(Tag.PREFIX + whiteName), new SecureConstructObject());
        }
    }

    /**
     * ConstructYamlObject是Constructor类中的保护类，非静态，所以内部含有Constructor的指针，不能在外部直接引用
     * 所以使用继承的方式，将SnakeYamlDeserializeConstructor指针赋值到SecureConstructObject内部，并保持ConstructYamlObject
     * 的行为不变化
     */
    protected class SecureConstructObject extends ConstructYamlObject {
        SecureConstructObject() {
            super();
        }
    }
}
```
- 修复示例2：使用自定义函数校验外部数据。
```java
private static void doSomething() {        
    String yamlStr = request.getParameter("yamlData");
    ...
    Yaml yaml = new Yaml();
    if (!validYaml(yamlStr)) { // valid开头的自定义校验函数校验
        throw new Exception("XXX");
    }
    yaml.load(yamlStr);
}

private static Boolean validYaml(String yamlStr){
    ...// 自定义校验逻辑
}
```

**【反例】**
场景1：使用`ScriptEngineManager`执行脚本代码
- 错误示例：
```java
private static void doSomething() {        
    String jsCode = request.getParameter("jscode");
    ...
    ScriptEngineManager manager = new ScriptEngineManager();
    ScriptEngine engine = manager.getEngineByName("javascript");
    engine.eval(jsCode);
    ...
}
```

场景2：使用Fastjson 1.2.68以前版本
- 错误示例：开启AutoType功能
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ParserConfig.getGlobalInstance().setAutoTypeSupport(true);
    User user = (User) JSONObject.parse(jsonStr);
    ...
}
```

场景3：使用Fastjson 1.2.68及以上新版本
- 错误示例：禁用SafeMode
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ParserConfig.getGlobalInstance().setSafeMode(false);
    User user = (User) JSONObject.parse(jsonStr);
    ...
}
```

场景4：使用Jackson
- 错误示例：
```java
private static void doSomething() {        
    String jsonStr = request.getParameter("jsonData");
    ...
    ObjectMapper mapper = new ObjectMapper();
    mapper.enableDefaultTyping();
    UserInfo demo = (UserInfo)mapper.readValue(jsonStr, UserInfo.class);
    ...
}
```

场景5：使用XStream
- 错误示例：
```java
private static void doSomething() {        
    String xmlStr = request.getParameter("xmlData");
    ...
    XStream xst = new XStream(new DomDriver());
    xst.fromXML(xmlStr);
    ...
}
```

场景6：使用XMLDecoder
- 错误示例：
```java
private static void doSomething() {
    ...
    InputStream is = request.getInputStream();
    ...
    XMLDecoder decoder = new XMLDecoder(is);
    decoder.readObject();
}
```

场景7：使用SnakeYaml
- 错误示例：
```java
private static void doSomething() {        
    String yamlStr = request.getParameter("yamlData");
    ...
    Yaml yaml = new Yaml();
    yaml.load(yamlStr);
}
```

# 6 外部数据校验
## 6.1 外部数据使用前必须进行合法性校验
**【描述】**
- LDAP注入：
    当不受控制的数据被传递给 LDAP 查询时，就会产生此类漏洞。注入被污染的数据可能会更改查询的目的，这可能会绕过安全检查或泄露未经授权的数据。

- OGNL注入：
    禁止外部数据直接拼接(OGNL)语句以执行任意代码。
    对象图导航语言(OGNL)是一种针对Java的开源表达式语言(EL)，它允许在由开发者提供的环境中计算并执行EL表达式。允许在任何环境下计算未验证的表达式将允许攻击者执行任意代码。

- Eval注入：
    当前程序执行不可信的代码，就会产生code injection问题，比如对当前的用户对象进行计算或修改用户的状态。

- 跨站脚本攻击：
    当应用程序收到含有不可信的数据，在没有进行适当的校验和转义的情况下就将它发送给Web浏览器，这就会产生跨站脚本攻击（简称XSS）。XSS允许攻击者在受害者的浏览器上执行脚本，从而劫持用户会话、危害网站、或者将用户转向恶意网站。

- 外部输入导致XPath表达式注入：
    直接使用外部数据更改XPath查询的意图，此漏洞会造成信息泄露或允许对应用程序功能进行未经授权的访问。

- 外部控制的哈希算法：
    使用外部不可信的数据来初始化哈希算法。

- 指向未可信站点的URL重定向(开放重定向)：
    用户控制输入被用于指定至外部站点的链接的情况。攻击者可以创建链接，指向被重定向至恶意网站的受信任网站。这可以让攻击者实施钓鱼攻击，以允许他们窃取用户凭证。

- 外部数据进入Map：
    来自程序外部的数据通常被认为是不可信的，在使用这些数据前需要进行合法性校验，否则可能会导致不正确的计算结果、运行时异常、不一致的对象状态，甚至引起各种注入攻击，对系统造成严重影响。当程序使可信赖和不可信赖的分界线模糊不清时，就会发生Trust Boundary Violation漏洞。

- 资源注入：
    使用来自上游组件的输入来标识某个资源（文件、端口号）前，没有对其进行限制或限制不正确，导致可能访问预期受控范围之外的资源。

**【修改建议】**
- 使用白名单的方式对外部数据进行校验。
- 对于Java的标准库或开源组件已经提供的功能，应使用标准库或开源组件的API，避免产生XSS注入问题。
- 对外部参数进行白名单校验。
- 对用户输入的URL进行合法校验，只允许使用合法的URL，从而过滤不可信URL。

**【正例】**
场景1：外部数据直接作为响应头的值
- 修复示例：
```java
private static void doSomething(String userSN, String userPassword) {        
    ...
    DirContext dctx = new InitialDirContext(env);
    String base = ""dc=example,dc=com"";
    if (!userSN.matches(""[\\\\w\\\\s]*"") || !userPassword.matches(""[\\\\w]*"")) {
        throw new IllegalArgumentException(""Invalid input"");
    }
    String filter = ""(&(sn="" + userSN + "")(userPassword="" + userPassword + ""))"";
    dctx.search(base, filter, sc);
    ...
}
```

通过安全框架中的API或者业界开源组件ESAPI中的API进行转义。
```java
void foo(HttpServletRequest request, HttpServletResponse response) {
    String data = request.getCookies()[0].getValue();
    OgnlContext ctx = new OgnlContext();

    Object expr = null;
    try {
        data = HWEncoder.encodeForOS(new WindowsCodec(), data);
        expr = Ognl.parseExpression(data);
        Object value = Ognl.getValue(expr, ctx, ""xxx"");
    } catch (OgnlException e) {
        IO.writeLine(e.getMessage());
    }
}
```

OWASP开源组件esapi就提供了XSS防护的编码API。
```java
@Override
public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    String safe = ESAPI.encoder().encodeForHTML(request.getParameter(""xss""));
    Object[] obj = { ""a"", ""b"" };
    response.getWriter().format(safe, obj);
}
```

对外部不可信数据algorithm进行校验。
```java
public void userControlledAlgorithm() throws Throwable {
    Properties properties = new Properties();
    properties.load(new FileInputStream(""file""));
    String algorithm = properties.getProperty(""hashAlg2"");
    if (isSafeAlgorithm(algorithm)) {
        MessageDigest md = MessageDigest.getInstance(algorithm);
    }            
}
```

对外部输入进行白名单校验，确定输入的url是合法的url。
```java
void sendRedirect(HttpServletRequest request, HttpServletResponse response) {
    String url = request.getCookies()[0].getValue();
    if (checkUrl(url)) {
      return;
  }
    response.sendRedirect(url); 
}
public boolean checkUrl(String url) {
  //TODO 使用白名单进行校验
}
```

**【反例】**
外部数据直接作为响应头的值
- 错误示例：

```java
private static void doSomething(String userSN, String userPassword) {        
    ...
    DirContext dctx = new InitialDirContext(env);
    String base = ""dc=example,dc=com""; 
    String filter = ""(&(sn="" + userSN + "")(userPassword="" + userPassword + ""))"";
    dctx.search(base, filter, sc);
    ...
}
```

不可信数据从cookie中读取，允许攻击者远程执行任意代码。
```java
void foo(HttpServletRequest request, HttpServletResponse response) {
    String data = request.getCookies()[0].getValue();
    OgnlContext ctx = new OgnlContext();

    Object expr = null;
    try {
        expr = Ognl.parseExpression(data);
        Object value = Ognl.getValue(expr, ctx, ""xxx"");
    } catch (OgnlException e) {
        IO.writeLine(e.getMessage());
    }
}
```

不可信数据未经校验就被执行。
```java
String data = req.getParameter(""input"");
ScriptEngineManager manager = new ScriptEngineManager();
ScriptEngine engine = manager.getEngineByName(""javascript"");
try {
    Object res = engine.eval(data);
} catch (ScriptException e) {
    IO.writeLine(e.getMessage());
}
```

将不可信的外部参数直接通过response传递到web浏览器。
```java
@Override
public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    String param = request.getParameter(""xss"");
    Object[] obj = { ""a"", ""b"" };
    response.getWriter().format(param, obj);
}
```

通过用户提供的（被污染的）数据用户名和密码注入了 XPath 查询。如果攻击者的用户名为 `admin` 并且密码为 `' or @loginID='admin`，则完整的 XPath 查询现在为 `/employees/employee[@loginID='admin' and @passwd='' or @loginID='admin']`。如果此查询被用于验证用户身份，攻击者可能会被验证为管理员用户。

```java
public void xpathInjection() {
    XPathFactory factory = XPathFactory.newInstance();
    XPath xPath = factory.newXPath();
    String expression = ""/employees/employee[@loginID='"" + username + ""' and @passwd='"" + password + ""']"";
    nodes = (NodeList) xPath.evaluate(expression, inputSource, XPathConstants.NODESET);
}
```

使用外部不可信数据algorithm初始化hash算法。
```java
public void userControlledAlgorithm() throws Throwable {
    Properties properties = new Properties();
    properties.load(new FileInputStream(""file""));
    String algorithm = properties.getProperty(""hashAlg2"");
    MessageDigest md = MessageDigest.getInstance(algorithm);
}
```

外部输入可能导致重定向至恶意网站。
```java
void sendRedirect(HttpServletRequest request, HttpServletResponse response) {
    String url = request.getCookies()[0].getValue();
    response.sendRedirect(url); 
}
```

Map中将可信数据和不可信数据混合使用，会导致错误地信赖未验证的数据。
```java
public void foo(HttpServletRequest request, HttpServletResponse response) {
    String data = request.getCookies()[0].getValue();
    Model model = new ConcurrentModel();
    Map map = new HashMap();
    map.put(""data"", data);
    map.put(""name"", ""张三"")
    model.mergeAttributes(map);
}
```

外部不可信数据未经校验直接创建URl。
```java
    public HttpURLConnection getUrlConnection(String serviceUrl,String requestMethodType) throws IOException {
        URL url = new URL(serviceUrl);
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        conn.setRequestMethod(requestMethodType);
        return conn;
    }
```

## 6.2 禁止直接使用外部数据来拼接SQL语句
**【描述】**
使用外部数据构建SQL语句。  

SQL注入是指使用外部数据构造的SQL语句所代表的数据库操作与预期不符，这样可能会导致信息泄露或者数据被篡改。SQL注入产生的根本原因是使用外部数据直接拼接SQL语句，参数化查询是一种简单有效的防止SQL注入的查询方式，应该被优先考虑使用。另外，参数化查询还能提高数据库访问的性能，例如，SQL Server与Oracle数据库会为其缓存一个查询计划，以便在后续重复执行相同的查询语句时无需编译而直接使用。对于常用的ORM框架（如Hibernate、iBATIS等），同样支持参数化查询。

**【修改建议】**
SQL注入产生的根本原因是使用外部数据直接拼接SQL语句，防护措施主要有以下三类
* 使用参数化查询：最有效的防护手段，但对SQL语句中的表名、字段名等不适用。
* 对外部数据进行白名单校验：适用于拼接SQL语句中的表名、字段名。
* 对外部数据中的与SQL注入相关的特殊字符进行转义：适用于必须通过字符串拼接 构造SQL语句的场景，转义仅对由引号限制的字段有效。

**【正例】**
场景1：使用参数化查询替代SQL语句拼接
- 修复示例1：使用参数化查询
```java
PreparedStatement stmt = null;
ResultSet rs = null;
try {
    String userName = request.getParameter("name");
    String password = request.getParameter("password");
    ... // 确保userName和password的长度是合法的
    String sqlStr = "SELECT * FROM t_user_info WHERE name=? AND password =?";
    stmt = connection.prepareStatement(sqlStr);
    stmt.setString(1, userName);
    stmt.setString(2, password);

    // 【GOOD】
    rs = stmt.executeQuery();
    ... // 结果集处理
} catch (SQLException ex) {
    ... // 处理异常
}
```
- 修复示例2：使用自定义校验函数去校验外部数据。
```java
Statement stmt = null;
ResultSet rs = null;
try {
    String userName = request.getParameter("name");
    String password = request.getParameter("password");
    ...
    String sqlStr = "SELECT * FROM t_user_info WHERE name = '" + userName
        + "' AND password = '" + password + "'";
    stmt = connection.createStatement();
    if (!validSqlStr(sqlStr) ) { // validSqlStr 校验函数，校验 sqlStr
        return;
    }
    rs = stmt.executeQuery(sqlStr);
    ... // 结果集处理
} catch (SQLException ex) {
    ... // 处理异常
}
```

**【反例】**
场景1：使用参数化查询替代SQL语句拼接
- 错误示例：使用外部数据拼接SQL语句

```java
Statement stmt = null;
ResultSet rs = null;
try {
    String userName = request.getParameter("name");
    String password = request.getParameter("password");
    ...
    String sqlStr = "SELECT * FROM t_user_info WHERE name = '" + userName
        + "' AND password = '" + password + "'";
    stmt = connection.createStatement();

    // POTENTIAL FLAW
    rs = stmt.executeQuery(sqlStr);
    ... // 结果集处理
} catch (SQLException ex) {
    ... // 处理异常
}
```

## 6.3 禁止直接使用外部数据构造格式化字符串
**【描述】**
直接使用外部数据构造格式化字符串。

Java中的Format可以将对象按指定的格式转为某种格式的字符串，格式化字符串可以控制最终字符串的长度、内容、样式，当格式化字符串中指定的格式与格式对象不匹配时还可能会抛出异常。当攻击者可以直接控制格式化字符串时，可导致信息泄露、拒绝服务、系统功能异常等风险。

**【修改建议】**
对外部数据白名单进行校验。

**【正例】**
对外部格式化字符串做白名单校验。
```java
public String formatInfo() {
    String formatStr = req.getParameter("format");

    if(!whiteListVarify(formatStr)){
        return "";
    }

    String value = getData();
    return String.format(formatStr, value);
}
```

**【反例】**
直接使用外部指定的格式对字符串数据进行格式化，当外部指定的格式为非字符类型 如%d，会导致格式化操作出现异常。
```java
public String formatInfo(String formatStr) {
    String value = getData();
    return String.format(formatStr, value);
}
String formatStr = req.getParameter("format");
String formattedValue = formatInfo(formatStr);
```

## 6.4 禁止直接向Runtime.exec()方法或java.lang.ProcessBuilder类传递外部数据
**【描述】**
命令注入。

Runtime.exec()方法或java.lang.ProcessBuilder类被用来启动一个新的进程，在新进程中执行命令。命令执行通常会有两种方式：

* 直接执行具体命令：例如Runtime.getRuntime().exec("ping 127.0.0.1")；
* 通过shell方式执行命令：windows下使用cmd.exe、linux下通过sh方式执行命令，或通过脚本文件（*.bat/*.sh）执行命令。

直接使用外部数据构造命令行，会存在以下风险：
* shell方式执行命令时，需要命令行解释器对命令字符串进行拆分，该方式可执行多条命令，存在命令注入风险；
* 直接执行具体的命令时，可以通过空格、双引号或以 -/ 开头的字符串向命令行中注入参数，存在参数注入风险。

**【修改建议】**
针对命令注入或参数注入，具体的解决方案如下：
* 对于Java的标准库或开源组件已经提供的功能，应使用标准库或开源组件的API，避免执行命令。
* 外部数据用于拼接命令行时，可使用白名单方式对外部数据进行校验，保证外部数据中不含注入风险的特殊字符。
* 在执行命令行时，如果输入校验不能禁止有风险的特殊字符，需先外部输入进行转义处理，转义后的字段拼接命令行可有效防止命令注入的产生。

**【正例】**
场景1：
- 修复示例1：外部数据用于拼接命令行时，可使用白名单方式对外部数据进行校验，保证外部数据中不含注入风险的特殊字符。

```java
// str值来自用户输入
public void doSomething() {
    String data = System.getenv("data");
    String osCommand;
    if (System.getProperty("os.name").toLowerCase().indexOf("win") >= 0) {
        osCommand = "c:\\WINDOWS\\SYSTEM32\\cmd.exe /c dir ";
    } else {
        osCommand = "/bin/ls ";
    }
    Process process = null;
    try {
        // 【POTENTIAL FLAW】command injection
        if (!Pattern.matches("[0-9A-Za-z@]+", data )) {
             throw new IllegalArgumentException("...");
        }
        process = Runtime.getRuntime().exec(osCommand + data);
    } catch (IOException e) {
        IO.writeLine(e.getMessage());
    }
    ...
}
```
- 修复示例2：对外部数据中存在命令注入风险的特殊字符进行转义

```java
String encodeIp = HWEncoder.encodeForOS(new WindowsCodec(), ip);
String cmd = "cmd.exe /c ping " + encodeIp;
Runtime rt = Runtime.getRuntime();
Process proc = rt.exec(cmd);
...
```

**【反例】**
场景1：
- 错误示例：对于外部数据直接调用exec，未做合法性校验

```java
public void doSomething() {
    String data = System.getenv("data");
    String osCommand;
    if (System.getProperty("os.name").toLowerCase().indexOf("win") >= 0) {
        osCommand = "c:\\WINDOWS\\SYSTEM32\\cmd.exe /c dir ";
    } else {
        osCommand = "/bin/ls ";
    }
    Process process = null;
    try {
        // 【POTENTIAL FLAW】command injection
        process = Runtime.getRuntime().exec(osCommand + data);
        ...
    } catch (IOException e) {
        IO.writeLine(e.getMessage());
    }
    ...
}
```

## 6.5 禁止直接使用外部数据来拼接XML
**【描述】**
使用外部数据来拼接XML，可能导致XML注入。

使用未经校验的数据来构造XML会导致XML注入漏洞。如果用户被允许输入结构化的XML片段，则用户可以在XML的数据域中注入XML标签来改写目标XML文档的结构和内容，XML解析器会对注入的标签进行识别和解释，引起注入问题。

**【修改建议】**
使用白名单的方式对外部数据进行校验。

**【正例】**
场景1：对象是外部用户可控，字段被恶意设置
- 修复示例1：白名单校验
```java
private static final Pattern INPUT_VALIDATER = Pattern.compile("[\\w\\d]+"); // 【GOOD】利用正则匹配对数据做白名单处理。

public String buildXmlStr(User user) {
    if (INPUT_VALIDATER.matches(user.getUserId()) && INPUT_VALIDATER.matches(user.getDescription())) {
        return "<user><role>operator</role><id>" + user.getUserId() + "</id><description>" + user.getDescription()
            + "</description></user>";
    }
}
```
- 修复示例2：转义
```java
public String buildXmlStr(User user) {
    String encodeUserId = HWEncoder.encodeForXML(user.getUserId()); // 【GOOD】对数据进行编码转义。
    String encodeDec = HWEncoder.encodeForXML(user.getDescription()); // 【GOOD】对数据进行编码转义。
    return "<user><role>operator</role><id>" + encodeUserId + "</id><description>" + encodeDec
        + "</description></user>";

}
```
- 修复示例3：使用自定义校验函数去校验外部数据
```java
public String buildXmlStr(User user) {
    String xmlString = "<user><role>operator</role><id>" + user.getUserId() + "</id><description>"
        + user.getDescription() + "</description></user>";
	...
    if (!validXml(xmlString)) { // validXml 校验函数，校验 xmlString
        return;
    }
    return xmlString;
}
```

**【反例】**
场景1：对象是外部用户可控，字段被恶意设置。
- 错误示例：
```java
// 【POTENTIAL FLAW】如果User对象是外部用户可控的，最终构造的XML可被恶意篡改
public String buildXmlStr(User user) {
    String xmlString = "<user><role>operator</role><id>" + user.getUserId() + "</id><description>"
        + user.getDescription() + "</description></user>";
	...
    return xmlString;
}
```

## 6.6 防止解析来自外部的XML导致的外部实体（XML External Entity）攻击
**【描述】**
XML外部实体攻击。

XML实体包括内部实体和外部实体。外部实体格式：<!ENTITY 实体名 SYSTEM "URI">  或者 <!ENTITY 实体名 PUBLIC "public_ID" "URI"> 。Java中引入外部实体的协议包括http、https、ftp、file、jar、netdoc、mailto等。XXE漏洞发生在应用程序解析来自外部的XML数据或文件时没有禁止外部实体的加载，造成任意文件读取、内网端口扫描、内网网站攻击、DoS攻击等危害。

**【修改建议】**
为了避免 XXE 注入，应对 XML 解析器进行安全配置，使它不允许将外部实体包含在传入的 XML 文档中。

**【正例】**
禁止解析外部一般实体和外部参数实体。

```java
private void parserXmlFileDisableExternalEntityes(String filePath) {
    try {
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        dbf.setFeature("http://apache.org/xml/features/disallow-doctype-decl", false);
        dbf.setFeature("http://xml.org/sax/features/external-general-entities", false);
        dbf.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
        DocumentBuilder db = dbf.newDocumentBuilder();
        Document doc = db.parse(new File(filePath));
        ... // 解析xml文件中的内容
    } catch (ParserConfigurationException ex) {
        // 处理异常
    }
        ...
}

```

**【反例】**
下列示例中解析XML文件时未进行安全防护，当解析的XML文件是恶意用户精心构造的，系统会受到XXE攻击。
```java
private void parseXmlFile(String filePath) {
    try {
        DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
        DocumentBuilder db = dbf.newDocumentBuilder();
        Document doc = db.parse(new File(filePath));
        ... // 解析xml文件中的内容
    } catch (ParserConfigurationException ex) {
        // 处理异常
    }
    ...
}
```

## 6.7 防止解析来自外部的XML导致的内部实体扩展（XML Entity Expansion）攻击
**【描述】**
XML内部实体攻击。

XML内部实体格式： <!ENTITY 实体名 "实体的值"\> 。内部实体攻击比较常见的是XML Entity Expansion攻击，它主要试图通过消耗目标程序的服务器内存资源导致DoS攻击。

**【修改建议】**
为了避免 XXE 注入，应对 XML 解析器进行安全配置，使它不允许将外部实体包含在传入的 XML 文档中。

**【正例】**
场景1：不使用XML实体
- 修复示例：完全禁用该工厂的DTD。
```java
XMLInputFactory xmlInputFactory = XMLInputFactory.newInstance();

// 【GOOD】这将完全禁用该工厂的DTD
xmlInputFactory.setProperty(XMLInputFactory.SUPPORT_DTD, false);
XMLStreamReader reader = xmlInputFactory.createXMLStreamReader(new FileInputStream(fileName));
```
场景2：不使用外部实体，需要使用内部实体
- 修复示例：禁止外部实体解析，且限制内部实体数量为10个以内
```java
XMLInputFactory xmlInputFactory = XMLInputFactory.newInstance();

// 【GOOD】禁用外部实体
xmlInputFactory.setProperty(XMLInputFactory.IS_SUPPORTING_EXTERNAL_ENTITIES, false);

// 【GOOD】使用系统属性限制
System.setProperty("entityExpansionLimit", "10");
XMLStreamReader reader = xmlInputFactory.createXMLStreamReader(new FileInputStream(fileName));
```

**【反例】**
场景1：不使用XML实体
- 错误示例：XML解析器默认开启实体解析
```java
XMLInputFactory xmlInputFactory = XMLInputFactory.newInstance();
XMLStreamReader reader = xmlInputFactory.createXMLStreamReader(new FileInputStream(fileName)); // POTENTIAL FLAW: 未禁止实体解析
```
场景2：不使用外部实体，需要使用内部实体
- 错误示例：禁止外部实体解析，但未限制内部实体数量
```java
XMLInputFactory xmlInputFactory = XMLInputFactory.newInstance();

// POTENTIAL FLAW: 未限制内部实体数量
xmlInputFactory.setProperty(XMLInputFactory.IS_SUPPORTING_EXTERNAL_ENTITIES, false);
XMLStreamReader reader = xmlInputFactory.createXMLStreamReader(new FileInputStream(fileName));
```

## 6.8 禁止使用不安全的XSLT转换XML文件
**【描述】**
XSLT是一种样式转换标记语言，可以将XML数据转换为另外的XML或其他格式，如HTML网页，纯文字。因为XSLT的功能十分强大，可以导致任意代码执行，当使用TransformerFactory转换XML格式数据的时候，需要添加安全策略禁止不安全的XSLT代码执行。

**【修改建议】**
使用TransformerFactory对xml进行格式转换操作时，要开启其安全防护策略，参考修复示例。

**【正例】**
场景1：使用TransformerFactory转换XML格式数据需开启安全策略
- 修复示例：开启安全防护策略
```java
//create transformer after executing setFeature
public static void XsltTrans(String src, String dst, String xslt) {
    TransformerFactory tf = TransformerFactory.newInstance();
    tf.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
    tf.setAttribute(XMLConstants.ACCESS_EXTERNAL_STYLESHEET, "");
    try {

        // 【GOOD】转换器工厂设置黑名单，禁用一些不安全的方法，类似XXE防护
        tf.setFeature("http://javax.xml.XMLConstants/feature/secure-processing", true);

        // 获取转换器对象实例
        Transformer transformer = tf.newTransformer(new StreamSource(xslt));

        // 进行转换
        transformer.transform(new StreamSource(src), new StreamResult(new FileOutputStream(dst)));
    } catch (Exception e) {
        LOGGER.error(e.getMessage());
    }
}
```

**【反例】**
场景1：使用TransformerFactory转换XML格式数据需开启安全策略
- 错误示例：不添加安全策略。

```java
// transformer of StreamSource without setFeature
public static void XsltTrans(String src, String dst, String xslt) {
    TransformerFactory tf = TransformerFactory.newInstance();
    tf.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
    tf.setAttribute(XMLConstants.ACCESS_EXTERNAL_STYLESHEET, "");
    try {
        // 获取转换器对象实例
        /* 【POTENTIAL FLAW】XSLT是一种样式转换标记语言，可以将XML数据档转换为另外的XML或其它格式，
         * 如HTML网页，纯文字。因为XSLT的功能十分强大，可以导致任意代码执行，
         * 当使用TransformerFactory转换XML格式数据的时候，需要添加安全策略禁止不安全的XSLT代码执行。
         */
        Transformer transformer = tf.newTransformer(new StreamSource(xslt));

        // 进行转换
        transformer.transform(new StreamSource(src), new StreamResult(new FileOutputStream(dst)));
    } catch (Exception e) {
        LOGGER.error(e.getMessage());
    }
}
```

## 6.9 正则表达式要尽量简单，防止ReDos攻击
**【描述】**
正则编写不当导致ReDos攻击。

ReDos攻击是正则编写不当导致的常见安全风险。Java中的正则匹配使用的是NFA引擎。NFA引擎的回溯机制，导致当字符串文本与正则表达式不匹配时，所花费的时间要比匹配时多，即要确定匹配失败， 需要与所有可能的路径进行对比匹配，证明都不匹配时，才返回匹配失败。当使用简单的非分组正则表 达式时，一般不会存在ReDos攻击。容易存在ReDos攻击的正则表达式主要有两类：
* 包含具有自我重复的重复性分组的正则，例如：^(\d+)+$、^(\d*)*$、^(\d+)*$、^(\d+|\s+)*$
* 包含替换的重复性分组，例如：^(\d|\d|\d)+$、^(\d|\d?)+$

**【修改建议】**
对于ReDos攻击的防护手段主要包括：
* 进行正则匹配前，先对匹配的文本的长度进行校验；
* 在编写正则时，尽量不要使用过于复杂的正则，尽量少用分组，例如对于正则 ^(([a-z])+\.)+[A-Z]([a-z])+$ （存在ReDos风险），可以将多余的分组删除： ^([a-z]+\.)+[A-Z][a-z]+$ ，这样在不改变检查规则的前提下消除了ReDos风险；
* 避免动态构建正则，当使用外部数据构造正则时，要使用白名单进行严格校验。

**【正例】**
将正则表达式精简为a[bc]+d。与a(b|c+)+d相比，可以在实现相同功能的前提下消除ReDos风险。
```java
private static final Pattern REGEX_PATTER = Pattern.compile("a[bc]+d");
public static void main(String[] args) {
    ...
    Matcher matcher = REGEX_PATTER.matcher(args[0]);
    if (matcher.matches()) {
        ...
    } else {
        ...
    }
    ...
}
```

**【反例】**
正则表达式 a(b|c+)+d 存在ReDos风险，当匹配的字符串格式类似"accccccccccccccccx"时，随中间的字符"c"的增加，代码执行时间将成指数级增长。
```java
private static final Pattern REGEX_PATTER = Pattern.compile("a(b|c+)+d");
public static void main(String[] args) {
    ...
    Matcher matcher = REGEX_PATTER.matcher(args[0]);
    if (matcher.matches()) {
        ...
    } else {
        ...
    }
    ...
}
```

## 6.10 禁止直接使用外部数据作为反射操作中的类名/方法名
**【描述】**
直接使用外部数据作为反射操作中的类名/方法名。

反射操作中直接使用外部数据作为类名或方法名，会导致系统执行非预期的逻辑流程（Unsafe Reflection）。这可被恶意用户利用来绕过安全检查或执行任意代码。当反射操作需要使用外部数据时，必须对外部数据进行白名单校验，明确允许访问的类或方法列表；另外也可以通过让用户在指定范围内选择的方式进行防护。

**【修改建议】**
使用白名单的方式对外部类名/方法名进行校验。

**【正例】**
场景1：
- 修复示例1：外部只能指定要反射的类的代号

```java
String classIndex = request.getParameter("classIndex");
String className = (String) reflectClassesMap.get(classIndex);
if (className != null) {
    Class objClass = Class.forName(className);
    BaseClass obj = (BaseClass) objClass.newInstance();
    obj.doSomething();
} else {
    throw new IllegalStateException("Invalid reflect class!");
}
...
```
- 修复示例2：对外部数据进行白名单校验
```java
String classIndex = request.getParameter("classIndex");
if (whiteList.contains(classIndex)) {
    Class objClass = Class.forName(className);
    BaseClass obj = (BaseClass) objClass.newInstance();
    obj.doSomething();
} else {
    throw new IllegalStateException("Invalid reflect class!");
}
...
```

**【反例】**
场景1：
- 错误示例：直接使用外部指定的类名通过反射构造了一个对象

```java
String className = request.getParameter("class");
...
Class objClass = Class.forName(className);
BaseClass obj = (BaseClass) objClass.newInstance();
obj.doSomething();
```

# 7 日志
## 7.1 禁止直接使用外部数据记录日志
**【描述】**
日志注入。

直接将外部数据记录到日志中，可能存在以下风险：
* 日志注入：恶意用户可利用回车、换行等字符注入一条完整的日志；
* 敏感信息泄露：当用户输入敏感信息时，直接记录到日志中可能会导致敏感信息泄露；
* 垃圾日志或日志覆盖：当用户输入的是很长的字符串，直接记录到日志中可能会导致产生大量垃圾 日志；当日志被循环覆盖时，这样还可能会导致有效日志被恶意覆盖。

**【修改建议】**
外部数据应尽量避免直接记录到日志中，如果必须要记录到日志中，要进行必要的校验及过滤处理，对于较长字符串可以截断。对于记录到日志中的数据含有敏感信息时，对于秘钥、口令类的敏感信息，将这些敏感信息替换为固定长度的*，对于其他类的敏感信息（如手机号、邮箱等）需要做匿名化处理。

**【正例】**
场景1：
- 修复示例：外部数据写入日志前，先将其中的`\r\n`等导致换行的字符进行替换，消除日志注入风险。

```java
String jsonData = getRequestBodyData(request);
if (!validateRequestData(jsonData)) {
    LOG.error("Request data validate fail! Request Data : " + replaceCRLF(jsonData));
}

public String replaceCRLF(String message) {
    if (message == null) {
        return "";
    }
    return message.replace('\n', '_').replace('\r', '_');
}

```

**【反例】**
场景1：
- 错误示例：当请求的json数据校验失败，会直接将json字符串记录到日志中，可能导致发生敏感数据泄露、或者日志注入、日志冗余。

```java
String jsonData = getRequestBodyData(request);
if (!validateRequestData(jsonData)) {
    LOG.error("Request data validate fail! Request Data : " + jsonData);
}
```

# 8 性能和资源管理
## 8.1 进行IO类操作时，必须在try-with-resource或finally里关闭资源
**【描述】**
申请的资源不再使用时，需要及时释放，否则会导致资源泄露问题。释放后的资源不要继续使用，否则可能导致系统抛出异常或其他未知不安全行为。

**【修改建议】**
系统异常可能导致资源释放操作被跳过，因此对于IO、数据库操作等需要显式调用关闭方法（如`close()`）来释放资源的场景，必须在try-catch-finally的finally中调用关闭方法。如果有多个资源需要`close()`，需要分别对每个资源`close()`时的异常进行try-catch处理，防止某个资源关闭失败导致其他资源无法正常关闭，最终保证所有资源都能被正确释放。

Java 7有自动资源管理的特性try-with-resource，不需手动关闭。该方式应该优先于try-finally，这样得到的代码将更加简洁、清晰，产生的异常也更有价值。特别是对于多个资源关闭发生异常时，try-finally可能丢失掉前面的异常，而try-with-resource会保留第一个异常，并把后续的异常作为Suppressed exceptions，可通过`getSuppressed()`获取这些异常信息。

**【正例】**
场景1：
- 修复示例1：在finally中释放资源

  ```java
  public void doSomething() {
      FileInputStream in = null;
      try {
          in = new FileInputStream(inputFileName);
      } catch (IOException e) {
          ...
      } finally {
          ...
          in.close();
      }
  }
  ```
- 修复示例2：使用try-with-resource机制，保证资源正确释放

  ```java
  try (FileOutputStream fop = new FileOutputStream(file)) {
      fop.write(buf);
  }
  ```

**【反例】**
场景1：
- 错误示例：方法抛出异常时，资源可能无法正确释放

  ```java
  public void doSomething() {
      FileInputStream in = null;
      try {
          in = new FileInputStream(inputFileName);
          in.close();
      } catch (IOException e) {
          int aaaa = 1;
      }
  }
  ```

# 9 其他
## 9.1 安全场景下必须使用密码学意义上的安全随机数
**【描述】**
安全场景下必须使用密码学意义上的安全随机数。

不安全的随机数可能被部分或全部预测到，导致系统存在安全隐患，安全场景下使用的随机数必须是密码学意义上的安全随机数。密码学意义上的安全随机数分为两类：
1.真随机数产生器产生的随机数；
2.以真随机数产生器产生的少量随机数作为种子的密码学安全的伪随机数产生器产生的大量随机数。

已知的可供产品使用的密码学安全的非物理真随机数产生器有：Linux操作系统的/dev/random设备接口（存在阻塞问题）和Windows操作系统的CryptGenRandom接口。Java中的SecureRandom是一种密码学安全的伪随机数产生器，对于使用非真随机数产生器产生随机数时，要使用少量真随机数作为种子。常见安全场景包括但不限于以下场景：
1.用于密码算法用途，如生成IV、盐值、秘钥等；
2.会话标识（sessionId）的生成；
3.挑战算法中的随机数生成；
4.验证码的随机数生成。

**【修改建议】**
使用安全的随机数。

**【正例】**
明确指定采用sun.security.provider.SecureRandom作为随机数产生器，然后使用generateSeed()方法产生的随机数作为种子，该方法产生的随机数默认为真随机数（如linux下从/dev/random获取）。下述代码实际是使用少量真随机数作为种子（种子长度推荐不少于64bytes），然后采用伪随机数产生器来产生随机数，避免linxu下阻塞问题。对于需要生成大量随机数的场景，需要周期性补充种子，SHA1PRNG算法目前业界没有明确标准，推荐获取2^32次随机数后设置一次种子（调用一次nextBytes()、nextInt()等都计为一次获取随机数操作）。
```java
public byte[] generateSalt() {
    byte[] salt = new byte[8];
    try {
        SecureRandom random = SecureRandom.getInstance("SHA1PRNG", "SUN");
        random.setSeed(random.generateSeed(SEED_LEN));
        random.nextBytes(salt);
    } catch (NoSuchAlgorithmException ex) {
        // 处理异常
    }
    return salt;
}
```

**【反例】**
Random生成是不安全随机数，不能用做盐值。
```java
public byte[] generateSalt() {
    byte[] salt = new byte[8];
    Random random = new Random();
    random.nextBytes(salt);
    return salt;
}
```

## 9.2 必须使用SSLSocket代替Socket来进行安全数据交互
**【描述】**
在 Web 应用程序中使用基于套接字的通信往往容易出错。

当网络通信中涉及明文的敏感信息时，需要使用SSLSocket而不是Socket，Socket是明文通信，攻击者可以通过网络监听获取其中的敏感信息，通过中间人攻击对报文进行恶意篡改。SSLSocket是在Socket的基础上进行了一层安全性保护，包括身份认证、数据加密和完整性保护。

**【修改建议】**
当网络通信中涉及明文的敏感信息时，需要使用SSLSocket而不是Socket。

**【正例】**
场景1：
- 修复示例：使用加密通道传递敏感信息

```java
// server端
try {
    byte[] ip = new byte[4];
    // ...

    // 【GOOD】在敏感数据传递时，使用SSLSocket代替Socket来进行安全数据交互
    SSLServerSocketFactory sslServerSocketFactory =
        (SSLServerSocketFactory) SSLServerSocketFactory.getDefault();
    SSLServerSocket sslServerSocket =
        (SSLServerSocket) sslServerSocketFactory.createServerSocket(9999, 10, InetAddress.getByAddress(ip));
    SSLSocket sslSocket = (SSLSocket) sslServerSocket.accept();
    // ...sslSocket交由其他线程处理
} catch (IOException ex) {
    // ...处理异常
}

// client端
try {
    // 【GOOD】在敏感数据传递时，使用SSLSocket代替Socket来进行安全数据交互
    SSLSocketFactory sslSocketFactory =
        (SSLSocketFactory) SSLSocketFactory.getDefault();
    SSLSocket sslSocket = (SSLSocket) sslSocketFactory.createSocket(ip, port);
    os = sslSocket.getOutputStream();
    os.write(userInfo.getBytes(StandardCharsets.UTF_8));
    ...
} catch (IOException ex) {
    // ...处理异常
} finally {
    // ...关闭流
}
```

**【反例】**
场景1：
- 错误示例：使用明文通道传递敏感信息

```java
// server端
try {
    // 【POTENTIAL FLAW】在敏感数据传递时，直接使用了Socket，易导致信息被窃取或受中间人攻击
    ServerSocket serverSocket = new ServerSocket(8080, 10);
    Socket socket = serverSocket.accept();

    // ...socket交由其他线程处理
}catch(IOException ex) {
    // ...处理异常
}

// client端
try {
    // 【POTENTIAL FLAW】在敏感数据传递时，直接使用了Socket，易导致信息被窃取或受中间人攻击
    Socket socket = new Socket();
    socket.connect(new InetSocketAddress(ip, port), 10000);
    os = socket.getOutputStream();
    os.write(userInfo.getBytes(StandardCharsets.UTF_8));
    ...
} catch (IOException ex) {
    // ...处理异常
} finally {
    // ...关闭流
}
```

## 9.3 禁止代码中包含公网地址
**【描述】**
硬编码公网IP地址。

代码或脚本中包含用户不可见，不可知的公网地址，可能会引起客户质疑。对产品发布的软件（包含软件包/补丁包）中包含的公网地址（包括公网IP地址、公网URL地址/域名、 邮箱地址）要求如下：

1. 禁止包含用户界面不可见、或产品资料未描述的未公开的公网地址。

2. 已公开的公网地址禁止写在代码或者脚本中，可以存储在配置文件或数据库中。对于开源/第三方软件自带的公网地址必须至少满足上述第1条公开性要求。

**【修改建议】**
不要硬编码公网IP地址

**【正例】**
场景1：代码中存在硬编码IP。
- 修复示例1：可以将IP放入配置文件中：

```java
config.ip = 10.90.0.1 // 【GOOD】公网IP地址调整为在配置文件中配置，代码从配置文件中读取该IP地址并使用。
```

**【反例】**
场景1：代码中存在硬编码IP。
- 错误示例：代码中硬编码IP。

```java
public void test01Bad() {
    String publicIp = "10.90.0.1"; // 【POTENTIAL FLAW】IP地址硬编码
}
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
```java
// 用户界面友好提示
"系统暂时不可用，请稍后再试。如问题持续，请联系客服。"

// 安全的日志记录
logger.error("用户登录验证失败 - 用户名: {}，原因: {}", 
             maskSensitiveData("johndoe"), 
             "无效凭证");
             
// 敏感数据处理工具方法
public String maskSensitiveData(String input) {
    if(input == null || input.length() < 3) {
        return "***";
    }
    return input.substring(0, 2) + "****" + input.substring(input.length() - 1);
}
```

**【反例】**
```java
// 用户界面错误提示
"数据库连接失败 - 无法连接到 MySQL@10.0.0.123:3306，用户名: admin，密码验证失败"

// 日志记录
logger.error("用户登录失败 - 用户名: johndoe，尝试密码: Password123，来源IP: 192.168.1.100");
```
