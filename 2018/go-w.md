---
title: 浅谈Go语言中的文件读入方法
date: 2018-04-14 23:06:39
tags: go
---
# /*本篇blog是因为1-10.go而写的*/

听说我居然在文件写入的时候遇到了麻烦？

对了，1-10.go的位置：
[1-10.go](https://github.com/ShiinaOrez/go_practice/blob/master/Go%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E8%AF%AD%E8%A8%80/1/1-10.go)

-----
# Go-文件写入的四种方式

-----

## 第一种：io.WriteString()

在此之前介绍两个函数：
```go
	func checkFileIsExist(filename string) bool
	func check(err error) bool
```

### checkFileIsExist()
```go
func checkFileIsExist(filename string) bool {
    exist:=true
    if _,err:=os.Stat(filename);os.IsNotExist(err){
        exits=false
    }
    return exist
}
```
看上去不是一个很复杂的程序，但是出现了两个我们陌生的函数：os.Stat(filenmae)、os.IsNotExist(err)

-----
#### os.Stat(filename):
返回文件的系统状态相关信息：
	
    os.Stat(filename).Name() string//获取文件名
    os.Stat(filename).IsDir() bool//判断是否是目录
    os.Stat(filename).ModTime() time.Time//获取文件修改时间
    os.Stat(filename).Mode() FileMode//文件的权限
    os.Stat(filename).Size() int64//获取文件大小
    os.Stat(filename).Sys() interface{}//基础数据源接口

#### os.IsNotExist(err error):

函数中有这么一段代码：
```go
var (
    ErrInvalid = errors.New("invalid argument")
    ErrPermission = errors.New("permission denied")
    ErrExist = errors.New("file already exists")
    ErrNotExist = errors.New("file does not exist")
)
```

剩下的不用多说，显而易见。
### check()

```go
func check(err error){
    if(err!=nil){
        panic(err)
    }
    return 
}
```
这个函数是一个错误检查函数，我们传递一个error类型的参数进去，如果它确实是一个错误(没有错误就是nil了)那么使用panic中断进程。

这就涉及了一个很有意思的事情：GO语言中的错误处理。

显而易见，错误在go语言程序中是如此常见，基本所有的函数都会返回一个error。因此在Google上的go group中也有对于error处理的广泛讨论。https://blog.csdn.net/erlib/article/details/24012743

----
回归正题，我们继续说怎么用io.WriteString()来写入文件。

```go
var f *os.File
var err error
if checkFileIsExist(filename) { //如果文件存在
		f, err = os.OpenFile(filename, os.O_APPEND, 0666) //打开文件
		fmt.Println("文件存在")
	} else {
		f, err = os.Create(filename) //创建文件
		fmt.Println("文件不存在")
	}
	check(err)
	n, err := io.WriteString(f, wireteString) //写入文件(字符串)
	check(err)
	fmt.Printf("写入 %d 个字节n", n)
```
**问题接踵而至，这个叫做os.Openfile(filename,os.O_APPEND,0666)**

三个参数：filename,os.O_APPEND,0666

**第一个参数**：文件名
**第二个参数**：打开方式
**第三个参数**：这TM是啥？

----
#### 题外话：0666

wa这个0666是什么鬼畜操作？

0666代表权限。

	4,2,1分别代表读入，写入，执行权限
	6=4+2   就是读写权限
	7=4+2+1 就是读写执行权限
	那么也就是说权限只能在0~7之间，所以采用八进制存储，最前面的一位0代表八进制。

为什么三个6？（因为老铁双击666啊）

	每一位代表一种类型的权限：
		第一位是拥有者的权限
		第二位是同组权限
		第三位是他人的权限

----
## 第二种：ioutil.WriteFile()

```go
d:=[]byte("I love muxi")
err:=ioutil.WriteFile(filename,d,0666)
check(err)
```

-----
## 第三种：File(Write,WriteString)
```go
    var n int64
    f, err = os.Create("./"+filename) //创建文件
	check(err)
	defer f.Close()
	n,err = f.Write(d) //写入文件(字节数组)
	check(err)
	fmt.Printf("写入 %d 个字节n", n)
	n,err = f.WriteString("I LOVE MUXI") //写入文件(字节数组)
	fmt.Printf("写入 %d 个字节n", n)
	f.Sync()
```

-----
## 第四种：
```go
	w := bufio.NewWriter(f) //创建新的 Writer 对象
	n, err = w.WriteString("I love muxi")
	fmt.Printf("写入 %d 个字节n", n)
	w.Flush() //将缓存区提交到底层的io.Writer
	f.Close()
```
-----

![这里写图片描述](http://r.photo.store.qq.com/psb?/V13Gxo2x1UzVE2/Vv8Pj2J71CNGUB1enHftGxNBTkjO8ceaxgCmWiGq0UM!/r/dDEBAAAAAAAA)
