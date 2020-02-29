---
title: Go语言1.14版本发布
date: 2020-02-28 23:45:38
tags: Golang
---

## Go语言 1.14版本发布的新特性

在2020年2月25日, Go语言的1.14版本发布了, 在Go语言官方的[博客](https://blog.golang.org/go1.14)上Alex Rakoczy写道:

"今天, Go语言团队非常开心的宣布Go 1.14版本的发布, 您可以从[下载页面](golang.org/dl)上下载它"

"这个版本的一些亮点包括:"

+ 现在, Go Module已经准备好用于生产环境了, 我们鼓励所有的用户使用Go Mod进行依赖性管理
+ 嵌入具有重叠方法集的接口
+ 改进defer的性能
+ goroutine异步抢占
+ 页面分配器效率更高
+ 内置的time中的计时器的效率更高了

我们可以看到Alex主要提到了六点, 接下来我将分别讲述这六点

### GO MOD用于生产环境

对于每一个开发者而言，程序的依赖管理都是不可缺少的，因为不管我们使用什么语言，都是站在巨人的肩膀上进行开发。全球的开发者都会互相帮助，这也是开源精神很重要的一部分。
除了Go之外的其他语言，Java有maven，Python有pip，PHP有compose，Node.js有npm等等。对于Go语言而言，就是在1.6版本之后出现的vendor和1.11版本之后的go module了。

#### Go1.5及以前：GOPATH
在Go1.5及以前，都是依靠最简单的GOPATH的方式进行代码导入的。
通过设置全局变量GOPATH的方式来导入本地的代码，而且还存在和内置库冲突的风险。
这个时期的GOPATH不能称之为包管理，因为根本没有依赖性的概念和版本的概念。是非常不方便、不成熟的一种方式。

#### Go1.6到1.10：vendor
vendor的本意是供应商或者小贩，在Go1.6时推出了使用vendor来进行应用依赖性管理的功能。
vendor本身是应用根目录下的一个文件夹，而文件夹内用于存放应用的一些第三方依赖，在build我们的应用时，可以自动从vendor中进行搜索和导入。这样子应用和依赖可以整体进行打包，成为了真正意义上的包管理工具。
接下来进行推测：既然我们自己构建的应用会有vendor，那么意味着我们依赖的第三方的包也会有同样的情况产生，也就是我们的vendor目录下其实会有多级vendor的出现。
vendor的加载流程：

 包根目录下的vendor
 包根目录向上的最近的一个vendor
 ...
 GOPATH src/下的vendor
 GOROOT src/
 GOPATH src/

但是只依靠vendor还是没办法管理依赖的版本，这个时候很多第三方的工具出现了：glide，godep还有govendor等等...

#### Go1.11至今：
Go1.11版本开始，实验性的出现了可以不用定义GOPATH的功能，而且官方有`go mod`进行支持，到了Go1.12更是正式化了这一功能。
很明显的一个标志是源于全局变量`GO111MODULE`，这个全局变量用于设置Go1.11版本以来对于go mod功能的开关：

\+ GO111MODULE=off：关闭MOD
\+ GO111MODULE=on，开启MOD，忽略GOPATH和vendor，只根据go.mod文件进行下载依赖
\+ GO111MODULE=auto，该项目在GOPATH src/外且根目录有go.mod时，才开启模块支持

但是go mod的功能一直不是很稳定，我们团队之前的工程模板就会发生在Go1.13版本下使用go mod导致无法正常编译的错误，不得已更换了依赖（升级到使用uber体验还是很不错的）。

但是Go1.14大声的告诉我们：go mod已经完全可以使用在生产环境了！（虽然在这之前我在公司的项目就一直用的是go mod来着...）

------

### 具有重叠方法集的接口类型

官方原文的描述：

​	Per the overlapping interfaces proposal, Go 1.14 now permits embedding of interfaces with overlapping method sets: methods from an embedded interface may have the same names and identical signatures as methods already present in the (embedding) interface. This solves problems that typically (but not exclusively) occur with diamond-shaped embedding graphs. Explicitly declared methods in an interface must remain unique, as before.
​	根据重叠接口类型提议，Go1.14现在已经允许使用具有重叠的方法集的嵌入接口类型：被嵌入的接口类型可能具有和嵌入的接口类型具有相同名称的方法。这解决了最经典的菱形嵌入问题（虽然导致这个问题出现的不一定是菱形拓扑结构）。和之前一样，在接口中明确声明的方法必须保证唯一性。

在这个描述中，有两点是值得注意的：一是有关于这个改变的提议，另一个则是所谓的菱形嵌入（继承）问题。

#### 关于使接口类型允许嵌入重叠方法集的提议
这个提议源于golang的[ISSUE-6977](https://golang.org/issue/6977)，作者是Robert Griesemer。提议的[传送门](https://github.com/golang/proposal/blob/master/design/6977-overlapping-interfaces.md)。

以下是提议的主要内容（自己翻译的，可能不准确）：
目前，在Go语言的接口类型章节中是这样陈述的：

​	一个接口类型T可以通过嵌入一个另一个接口类型E来代替自身的接口方法声明。这个过程称为嵌入E到T中。它会将E中所有的导出方法和未导出方法都添加到接口T中。

而我们现在希望将其改为：

​	一个接口类型T可以通过嵌入一个另一个接口类型E来代替自身的接口方法声明。这个过程称为嵌入E到T中。接口T的方法集为其显式声明的方法集和嵌入的方法集的并集。

并且添加以下描述：

​	这个并集仅包括所有的方法集的所有方法（导出的和未导出的）一次，并且相同名称的方法必须保持相同的签名。

这个提议可以这样去理解，Go语言的面向对象的方式是以组合（嵌入）的方式实现的。和继承一样，存在着对于成员方法和字段的合并问题。
Go语言以前的方针是不允许任何同名的方法出现在同一个接口类型的合并过程中。哪怕是完全相同的两个方法也不可以。
在这次的修改后，这个方针改变为，可以有相同名称的方法出现在合并的过程中，但是必须函数的签名完全一致。

 Go语言中函数签名包括的几部分：方法接受者（receiver），方法名(name)，参数列表（param_list）和返回值列表（return_list），四者唯一确定一个函数签名

在同一个接口中，接受者是一致的，也就是要保证方法名，参数列表和返回值列表完全一致。这样在合并的过程中才不会出现报错。

我们可以看出，这样的一个举措是完全和重载背道而驰的做法，也算是一个特色了。（重载在遇到重名方法的时候，是允许不同的函数签名同时存在的）

#### 本质：解决菱形问题
![菱形问题](https://www.python-course.eu/images/multiple_inheritance_diamond.png)
在面向对象的语言中，菱形继承问题是不可避免的一个话题，和上面的图一样形象的问题：一个类（类型）D会继承（组合）来自不同方向的不同的类（类型），但是有可能B和C都具有同样的方法，这样调用D的方法时会产生一个究竟走哪条调用链的问题。
比如A有方法a，B和C分别重写了方法a，成为a-B和a-C，这个时候D继承了B和C，在调用a-D的时候，就会产生应该调用a-B还是a-C的问题。
这个问题在C++，Python中都不同的解决方案。分别是虚继承和MRO（Method Resolution Order）+C4算法。而这次Go1.14的重叠方法集应该算是Go语言对于菱形问题的解决方案。

------

### 提升defer的性能

原文描述：

​	This release improves the performance of most uses of defer to incur almost zero overhead compared to calling the deferred function directly. As a result, defer can now be used in performance-critical code without overhead concerns.
​	这个版本提升了defer大多数用法的性能，接近了几乎0开销。因此，现在可以将defer用于高性能场景了。

先来看一下defer在Go1.14以前是如何实现的：
![defer](http://xiaorui.cc/wp-content/uploads/2020/02/deferdefer.jpg)
我们可以很明显的看出一个defer的递归调用栈，以及独立于goroutine的协程切换，defer主要的性能流失就在这里。
Go1.14新加入了Open-Coded defer类型，编译器会将defer中的函数直接插到函数的尾部，避免了runtime的deferproc和deferprocStack操作，免除了在没有runtime判断下的deferreturn调用，如果有runtime判断逻辑，则deferreturn不会进行jmpdefer尾递归调用，而是直接在一个循环里遍历执行，这样效率流失的部分就抓了一部分回来了。

网上也有了很多的benchmark测评，结果是Go1.14比Go1.13的defer的速度快了不少，提升了大概35ns左右，得到的结论是：Go1.14中用不用defer对于性能影响甚微。

更详细的细节在[这里