# 第三周

## 介绍

### 这是啥?
这是一个由希伯来大学的 Shimon Schocken与 Noam Nisan讲授的课程。
教你从最简单的与非门实现计算机，并在计算机上实现操作系统，最后在构建的计算机上完成俄罗斯方块的制作。

官网主页：[http://www.nand2tetris.org](http://www.nand2tetris.org)

Coursera课程主页：[https://www.coursera.org/learn/build-a-computer](https://www.coursera.org/learn/build-a-computer)

我将它的视频课程搬运到了B站，方便大家学习：[https://space.bilibili.com/69824765/#/channel/detail?cid=56426](https://space.bilibili.com/69824765/#/channel/detail?cid=56426)

如果能科学上网的话，也可以在youtube搜索Nand2Tetris。

# 新的东西 - Time

从这一周开始，计算机引进了一个新的东西：时间。在电路中用时钟信号表现。

# 触发器 Flip Flop

触发器是一种可以存储电路状态的电子元件，所以存储器实现的基本单位就是触发器。

项目中已经实现了D触发器，可以像最开始使用Nand门那样实用D触发器(DFF)。

在讨论D触发器的真值表之前，先得搞清楚一个问题：

# Conbinatorial Logic vs. Sequential Logic - 组合逻辑 vs. 时序逻辑

对于组合逻辑，它相当于一个函数f(x)，输入x，输出f(x)。

但是对于时序逻辑，它的作用时是持续的，如果将1秒规定为一个逻辑处理时间单位，时序逻辑将不会更改这一秒的逻辑输出，而是会作用于下一秒的输出。

可以简单的看作：f(x - 1) = f(x)

因为它总是作用于下一个输出，所以我们永远不会知道第一个输出是多少，所以忽略第一个输出。

对于D触发器，时序图为：

![D Flip Flop](https://gss2.bdstatic.com/-fo3dSag_xI4khGkpoWK1HF6hhy/baike/c0%3Dbaike80%2C5%2C5%2C80%2C26/sign=8e35129dbc3eb13550cabfe9c777c3b6/267f9e2f07082838110fe261b899a9014c08f140.jpg)

当CP为1(严格讲应该是上升沿)，Q(n+1) = D，当cp = 0，Q不变化。

项目中的DFF门接受一个输入in，一个输出out。设当前D触发器的值为A

in = 0时，out(t + 1) = A
in = 1时，out(t + 1) = Not(A)


OK，然后我们来实现最小的内存单位：Bit。

# Bit

Bit有两个输入，一个为in，一个为load，一个输出out。

当load为1的时候，意思就是将Bit的值置为in。

load = 0时不更改。

其中一个与之前的门不一样的就是，我们需要把当前的out，传给下一个门的输入。然而时序电路就是为了允许这样的操作，所以实现如下：

![Bit电路图](http://upy.iimt.me/2018/12/17/upload_171a4cbae07127ce62154220ba4b92c8.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

跟着它演绎一遍，即：

```
Mux(a=dffout, b=in, sel=load, out=muxout);
DFF(in=muxout, out=dffout);
Or(a=dffout, b=dffout, out=out);
```

可能你有两个问题:

  - Q1: 第一行的dffout是啥，凭空出现的？
    A1: dffout就是上一个门的输出值，在第二行的out=dffout也可以体现，它将dffout传给了下一个的输入。
  - Q2: 为什么不在第二行中out=out呢？
    A2: A1中已经提到，它必须将out传出去，然后再输出值。所以第三行没有别的作用，只是为了输出out。

# Register


# RAM8


# RAM64



# PC
