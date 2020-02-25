JVM
====

# 1.初识JVM

## 1.1.JVM和操作系统的关系？

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;JVM全称Java Virtual Machine，也就是我们耳熟能详的Java虚拟机。它能识别**.class**后缀的文件，并且能够解析它的指令，最终调用操作系统上的函数，完成我们想要的操作。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Java是一门抽象程度特别高的语言，提供了自动内存管理等一系列的特性。这些特性直接在操作系统上实现是不太可能的，所以就需要JVM进行一番转换。

![Java执行过程](http://processon.com/chart_image/5e5474aee4b0c037b5fd6cc3.png?_=1582593585640)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;从图上可以看到，有了JVM这个抽象层之后，Java就可以实现跨平台了。JVM只需要保证能够正确执行.class文件，就可以运行在诸如Linux、Windows、MacOS等平台上了。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一句话概括JVM与操作系统之间的关系：JVM上承开发语言，下接操作系统，它的中间接口就是字节码。

![C++程序生产加载过程](http://processon.com/chart_image/5e54b8d9e4b0cc44b5aad0f6.png)
![Java程序生产加载过程](http://processon.com/chart_image/5e54bcf4e4b0c037b5fe39e0.png)

## 1.2.JVM、JRE、JDK的关系

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;仅仅是JVM，是无法完成一次编译，处处运行的。它需要一个基本的类库，比如怎么操作文件、怎么连接网络等。而Java体系很慷慨，会一次性将JVM运行所需的类库都传递给它。JVM标准加上实现的一大堆基础类库，就组成了Java的运行时环境，也就是我们常说的JRE（Java Runtime Environment）。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;对于JDK来说，就更庞大了一些。除了JRE，JDK还提供一些非常好用的小工具，比如javac、java、jar等。它是Java开发的核心。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;JVM、JRE、JDK它们三者之间的关系，可以用一个包含关系表示。JDK>JRE>JVM。

![JVM、JRE、JDK的关系](http://processon.com/chart_image/5e54c26ce4b0d4dc876cedd9.png)

## 1.3.Java虚拟机规范和Java语言规范的关系

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们通常谈到JVM，首先会想到它的垃圾回收器，其实它还有很多部分，比如对字节码进行解析的执行引擎等。广义来讲，JVM是一种规范，它是最为官方、最为准确的文档；狭义来讲，由于我们使用Hotspot更多一些，我们一般谈到这个概念时，会将它等同起来。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果再加上我们平常使用的Java语言的话，可以得出下面这样一张图。这是Java开发人员必须要搞懂的两个规范。

![Java虚拟机规范和Java语言规范](http://processon.com/chart_image/5e54c5e1e4b0cc44b5ab0219.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;左半部分是Java虚拟机规范，其实就是为输入和执行字节码提供一个环境。右半部分是我们常说的Java语言规范，比如switch、for、泛型、lambda等相关的程序，最终都会编译成字节码。而连接左右两部分的桥梁依然是Java的字节码。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果.class文件的规格是不变的，这两部分是可以独立进行优化的。但Java也会偶尔扩充一下.class文件的格式，增加一些字节码指令，以便支持更多的特性。

## 1.4.Java代码到底是如何运行起来的

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;简单看下下面的Java程序是如何运行的。

```
public class HelloWorld {
	public static void main(String[] args) {
		System.out.println("Hello World");
	}
}
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用JDK的工具javac进行编译后，会产生HelloWorld的字节码。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我们一直在说Java字节码是沟通JVM与Java程序的桥梁，下面使用javap来稍微看一下字节码到底长什么样。

```
public class HelloWorld {
  public HelloWorld();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #3                  // String Hello World
       5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Java虚拟机采用基于栈的架构，其指令由操作码和操作数组成。这些字节码指令，就叫作opcode。其中，getstatic、ldc、invokevirtual、return等，就是opcode，可以看到是比较容易理解的。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;继续使用hexdump看一下字节码的二进制内容。与以上字节码对应的二进制，就是下面这几个数字。

```
b2	00	02	12	03	b6	00	04	b1
```

可以看一下它们的对应关系。

```
0xb2	getstatic	获取静态字段的值
0x12	ldc			常量池中的常量值入栈
0xb6	invokevirtual	运行时方法绑定调用方法
0xb1	return		void函数返回
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;opcode有一个字节的长度（0~255），意味着指令集的操作码个数不能操作256条。而紧跟在opcode后面的是被操作数。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;JVM就是靠解析这些opcode和操作数来完成程序的执行的。当我们使用Java命令运行.class文件的时候，实际上相当于启动了一个JVM进程。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;然后JVM会翻译这些字节码，它有两种执行方式。常见的就是解释执行，将opcode + 操作数翻译成机器代码；令一种执行方式就是JIT，也就是我们常说的即时编译，它会在一定条件下将字节码编译成机器码之后再执行。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这些.class文件会被加载、存放到metaspace中，等待被调用，这里会有一个类加载器的概念。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;而JVM的程序运行，都是在栈上完成的，这和其他普通程序的执行是类似的，同样分为堆和栈。比如我们运行到了main方法，就会给它分配一个栈帧。当退出方法体时，会弹出相应的栈帧。你会发现，大多数字节码指令，就是不断地对栈帧进行操作。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;而其他大块数据，是存放在堆上的。

## 1.5.问题

### 1.5.1.为什么Java研发系统需要JVM

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;JVM解释的是类似于汇编语言的字节码，需要一个抽象的运行时环境。同时，这个虚拟环境也需要解决字节码加载、自动垃圾回收、并发等一系列问题。JVM其实是一个规范，定义了.class文件的结构、加载机制、数据存储、运行时栈等诸多内容，最常用的JVM实现是Hotspot。

### 1.5.2.Java代码到底是如何运行起来的

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;一个Java程序，首先经过javac编译成.class文件，然后JVM将其加载到元数据区，执行引擎将会通过混合模式执行这些字节码。执行时，会翻译成操作系统相关的函数。JVM作为.class文件的黑盒存在，输入字节码，调用操作系统函数。过程如下：

		Java文件->编译器->字节码->JVM->机器码

# 2.JVM内存管理

