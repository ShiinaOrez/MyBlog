---
title: kotlin学习笔记
date: 2018-07-12 01:28:02
tags: kotlin
---

# hin难受的kotlin学习之路

+ 首先是如何安装命令行编译情况，这样我们就可以欢快的nano了
	+ 使用terminal，输入
		+ curl -s https://get.sdkman.io | bash
    + 然后你就安装sdkman成功了，terminal上会说让你enjoy！
	    + 接下来就是安装kotlin：sdk install kotlin
	    + 这个时候会有一个小问题，你的terminal说command not found:sdk，这时候不要慌，restart your terminal
	    + ：详情：[解决方案-stackoverflow](https://stackoverflow.com/questions/45392965/sdk-command-is-not-found-is-coming-the-terminal)
	    + 这个过程非常的磨人。。我安装了7遍才成功，请确保自己科学上网了
	    
   + 接下来我们确保自己写好了一个hello,world，然后假设名字是hello.kt
   + 在目录下，使用kotlinc命令生成.jar文件，使用java命令运行
```
	kotlinc hello.kt -include-runtime -d hello.jar
	java -jar hello.jar
```

-----

#### kotlin&go

+ 比如一样傻逼的变量声明：

```go
var a int //Go language
```

```kotlin
var a:Int //kotlin language
```
+ 还有函数声明：

```go
func funName(arg int)int{} //go language
```
```kotlin
fun funName(arg:Int):Int{} //kotlin language
```

注意，kotlin的语法中是没有空类型的，空类型的返回值为Unit,但是很体贴的一点是，如果你的返回值是Unit那么可以省略。

在函数中有一个关键字叫做vararg，这个关键字的作用是可变长参数。作用有些类似于接收一个不定长的list。

**kotlin中很耀眼的一点就是函数可以简单声明：**

```kotlin
fun sum(a: Int, b: Int): Int = a + b
```
因此对于一些表达式函数我们可以很简单的去写。

-----

#### 变量定义：

变量定义分为两种：可变变量和不可变变量：

+ 可变变量：      var
+ 不可变变量：  val

val变量是不可变变量和常量有区别，不可变变量自始至终仅可被赋值一次。

-----

#### 注释：

和C、C++、Go的语法一致

-----

#### 基本数据类型：

kotlin中所谓的数据类型，其实是一个类。所有的变量都是实例。

有Byte，Short，Int，Float，Double，Char等等
长整型以L结尾，而十六进制是以0x开头 二进制以0b开头（不支持八进制！
单精度浮点数以f结尾，支持科学计数法

可以使用下划线对数字进行分割：123_456==123456

对于变量的比较：

+ 比较对象地址： ===
+ 比较对象的值： ==

类型强制转换：

每种数据类型都会有以下内置方法：

	toByte()
	toInt()
	.
	.
	toChar()

用作强制类型转换。

**此外，在kotlin中，char类型作为一个单独的数据类型存在，而不是数值类型。**


 

