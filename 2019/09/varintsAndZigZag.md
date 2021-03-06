
## 面对小数值的高效率序列化方案:varints和zigzag编码

序列化是在传输数据的时候非常重要的一环, 而对于如何高效的序列化数字一直是人们非常关心的问题, 在历史的长河中也有很多的工程师提出了有效的序列化方案, 这篇文章就讲一下在protobuf中使用到的两种数字序列化编码方案: **varints**和**zigzag**

------

### varints

**可变长**编码方案, 用于把整数进行**序列化**, 在序列化一些大概率比较小的整数的时候效率会好一些.

我们传输时使用的是一个字节流, 也就是由byte组成的bytes. 我们在各个语言中声明的整数都是有固定的大小的: 比如说四个字节大小. 但是如果一些数字本身的取值范围就很小, 那么在序列化时就会产生很多的浪费, 因为前几个字节位很可能都是由0组成的. 因此varints就是为了能够在序列化整数时能够有更好的效率而产生的一种编码方式.

varints将每一个字节(8-bit)中的第一bit抽出用于标识是否还有后续字节, 剩余7-bit用来表示内容, 而在组合的时候则是逆序组合, 就像下面这样:

十进制: ``2019`` -> 二进制: ``111 11100011``

假设这个数字是使用``golang``中的``uint``类型存储的, 那么它应该占用了内存中``32-bit``的空间, 也就是``4``个字节:

```
    byte4      byte3      byte2      byte1
╭──────────┬──────────┬──────────┬──────────╮
│ 00000000 │ 00000000 │ 00000111 │ 11100011 │
╰──────────┴──────────┴──────────┴──────────╯
```

我们在传输的时候真的太难受了: 白白浪费了两个字节的空间! 那么我们使用``varints``来进行一下编码吧! 或许会更好一些!

首先第一步, 把原来的串按照``7-bit``拆分, 舍去多余的前导0:

```
111 11100011 -> 0001111 1100011
```

然后第二步, 把所有的``7-bit``片逆序排列:

```
 byte2   byte1      byte1   byte2
0001111 1100011 -> 1100011 0001111
```

然后在每个非结尾``7-bit``片前面加上``1``, 在最后一个``7-bit``片前加上``0``:

```
╭───┬─────────╮╭───┬─────────╮   ╭───┬─────────╮╭───┬─────────╮
│   │ 1100011 ││   │ 0001111 │ ->│ 1 │ 1100011 ││ 0 │ 0001111 │ 
╰───┴─────────╯╰───┴─────────╯   ╰───┴─────────╯╰───┴─────────╯
```

而最后我们得到了两个``8-bit``字节: 我们可以用它来进行传输啦! 而且我们省去了两个字节!

大家一定可以发现一些使用``varints``的限制和缺陷:

+ 首先, varints会把数字表示的最大范围缩小, 比如uint使用varints就只能表示2^28, 这是因为极端情况缺少了4个标志位.
+ 其次, 在一个变量频繁的大于2^22次方时, 我们编码后也不会节省任何的空间, (为了不失真甚至还有可能变大), 因此varints编码只适用于相对较小的数字的序列化.
+ 如果序列化的数字对象为负数, 则和上一种情况类似, 不会有任何空间的节省. 因为符号位在最前会被视为很大的一个数.

但是在面对**较小的数字**的时候, ``varints``的效果是非常显著的.

------

### zig zag

zig zag是将有符号数统一映射到无符号数的一种编码方式, 从结果来看可以看出其名字的由来: 0, -1, 1, -2, 2 编码后变为了 0, 1, 2, 3, 4. 负数和正数以绝对值不断增大的方式来回在数轴上跳跃穿插.也就是zig zag.

我们从极端情况来看zig zag的解决方案: -1:

```
 1 -> 00000000 00000000 00000000 00000001
-1 -> 11111111 11111111 11111111 11111111
```

对于1而言, 无论是zig zag还是varints都会相对而言觉得很好处理, 因为传输时的主要思想就是对于前导0的省略, 而对于负数的补码而言, 有大量的前导1出现在字节中, 这就导致了编码上的困难.

是什么导致了这种情况呢? 一是因为**符号位放在最前**, 二是因为负数**取补码**时将大量的0都变成了1.

对于第一个原因, 工程师们决定把符号位重新编码放到最后, 而对于第二个原因, 工程师们决定对于负数把除了符号位以外的位都反转过来. 而对于正数则不变(除了将符号位也放在最后)

```
                                             符号位
zig_zag(1): 00000000 00000000 00000000 0000001_0
zig_zag(-1):00000000 00000000 00000000 0000000_1
```

所以就出现了像我们一开头提到的情况: 正数和负数来来回回的映射到正数数轴上.

我们可以使用C++简单的位运算来实现``zig zag``:

```cpp
int zigzag(int num) {
    return (num << 1) ^ (n >> 31); 
}
```

``varints``将无用的字节省略掉, ``zig zag``则解决了负数的问题, 但是这二者在面对绝对值较大的数字时都是非常无力的, 因此在应用时应当分清不同的场景, 仅有在序列化绝对值较小的数字时才会使用这两种编码方式.
