# 第二周

![cover](http://upy.iimt.me/2018/11/15/upload_7761d8512a4515ef94836233e15b606e.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

# 符号

1. \+ => 或
2. \* => 与 
3. ⊕  => 异或


# HalfAdder 半加器

计算两个位相加。

真值表

![半加器真值表](http://upy.iimt.me/2018/11/14/upload_fcd2c3e3053043ff2b0dec28dc1c2dc7.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

观察真值表，看到输出C(carry)就是`And(a, b)`，S(sum)就是异或ab`Xor(a, b)`，所以实现：

![半加器实现](http://upy.iimt.me/2018/11/14/upload_1fe11d1571c3696363fa64720e0fdbfe.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

# FullAdder 全加器

计算三个位相加。

![全加器真值表](http://upy.iimt.me/2018/11/14/upload_39323ed8f6db599cb939f8a43b435181.png)

其中`Ci-1`是低位传进来的进位，`Ai`与`Bi`是被加数与加数，`Si`与`Ci`为结果与进位。

公式：
```
Si = Ai⊕Bi⊕Ci-1 => (Ai ⊕ Bi) ⊕ Ci-1
Ci = (Ai*Bi) + Ci-1 * (Ai + Bi)
```

根据公式实现就OK了，最后如下：

![全加器实现](http://upy.iimt.me/2018/11/14/upload_e5bfbf0cd919c22376ffdd25a209dda9.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

# Add16 16位加法器

描述：
```
/**
 * Adds two 16-bit values.
 * The most significant carry bit is ignored.
 */
```
将两个16位的二进制相加，忽略最高位的进位。

实现方案：

调用写好的HalfAdder就好，A[0]与B[0]相加再加上初始进位0得到结果S[0]与进位Carry0，然后A[1]与B[1]相加再加上Carry0得到S[1]与Carry1，以此类推...

最后实现：

![Add16实现](http://upy.iimt.me/2018/11/14/upload_7cef62c1ca518af63843153101117335.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

> 只截了一半的图，后面的自己动手写吧。

# Inc16 增量器

输入一个16位的in，输出in+1。

调用`Adder`就好了，加数为in，被加数的下标0为1，其余都为0。

最后实现

```
Add16(a=in, b[0]=true, out=out);
```

# ALU 算数逻辑单元

维基百科解释如下：

![ALU](http://upy.iimt.me/2018/11/15/upload_748c1cce3388331a7771275834779092.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

更详细的介绍就是：

有8个输入：

  1. x
  2. y
  3. zx
  4. nx
  5. zy
  6. ny
  7. f
  8. no

其中xy就是要计算的两个值，zx与nx对x进行预处理，zy与ny对y进行预处理：
  1. zx就是x=0
  2. nx就是x=!x
  3. 类同zy与ny

之后根据f的值对处理后的x与y进行计算：
  1. f = 1，计算Add(x, y)
  2. f = 0，计算And(x, y)

最后no处理输出，如果no=1，输出就是Not(上面计算的结果)，否则就直接输出结果。

有三个输出端，out，zr，ng：
  1. out 就是计算结果
  2. zr 如果out = 0，就zr = 1，否则zr = 0
  3. ng 如果out < 0，就ng = 1，否则ng = 0

具体思路就是：

#### 第一步，预处理

对于zx，有
```
if(zx == 1) {
  x = 0
} else {
  x = x //也就是不变
}
```
对于这样的选择分支，我们前面已经有一个门了，Mux选择器，用它来实现上面的语句就是：
```
Mux16(a=x, b=false, sel=zx, out=x1); //a是一个16位的二进制数，最后得到的结果是x1
```

然后用同样的方法根据zy对y预处理。

预处理nx与ny则按照下面方法：

假设通过zx与zy已经得到了预处理后的x1，y1，现在我们先计算出not(x1)与not(y1)，然后再用选择器选择到底用不用not之后的值。

```
Not16(in=x1, out=notx);
Mux16(a=x1, b=notx, sel=nx, out=x2);
Not16(in=y1, out=noty);
Mux16(a=y1, b=noty, sel=ny, out=y2);
```

这样预处理就结束了。得到了预处理之后的x2与y2，然后进行下一步：计算

#### 第二步，计算

计算也是一个分支结构，也用Mux就可以了：

```
Add16(a=x2, b=y2, out=addxy);
And16(a=x2, b=y2, out=andxy);
Mux16(a=andxy, b=addxy, sel=f, out=outf);
```

然后是否要对计算结果取反就不多写了，还要注意的一个地方就是，判定out=1还是out<0:

判定out=1，只需要用Or16，结果是1那么out=1，否则out!=1，然后根据结果输出zr就OK。
判定out<0，额外添加了一个门用来判定一个16位二进制数是否小于0：

在02文件夹下新建IsNeg16.hdl 代码如下：

![isNeg16](http://upy.iimt.me/2018/11/15/upload_8c89cc07f003d31806433e5fba9ff0ef.png!/watermark/text/aWltdC5tZQ==/font/helvetica/align/southeast//color/ffffff/opacity/80/size/28/border/33333333)

然后在ALU中调用IsNeg16就能判断是不是小于0了，之后用Mux选择器输出合适的ng就好了。


# 结语

这是Nand2Tetris的第二周，这一周实现了半加器，全加器，增量器，16位加法器，算术逻辑单元。


## 这是啥?
这是一个由希伯来大学的 Shimon Schocken与 Noam Nisan讲授的课程。
教你从最简单的与非门实现计算机，并在计算机上实现操作系统，最后在构建的计算机上完成俄罗斯方块的制作。

官网主页：[http://www.nand2tetris.org](http://www.nand2tetris.org)

Coursera课程主页：[https://www.coursera.org/learn/build-a-computer](https://www.coursera.org/learn/build-a-computer)

我将它的视频课程搬运到了B站，方便大家学习：[https://space.bilibili.com/69824765/#/channel/detail?cid=56426](https://space.bilibili.com/69824765/#/channel/detail?cid=56426)

如果能科学上网的话，也可以在youtube搜索Nand2Tetris。


## 你是谁？

我是iimT，大学生，技术宅，计算机科技爱好者，电音小王子。

我的博客：[www.iimt.me](http://www.iimt.me)

我在Weibo：@_iimT

我在B站：[https://space.bilibili.com/69824765/#/](https://space.bilibili.com/69824765/#/)

**想看到我的更多更新的话，很乐意你关注我！**

下一篇见~

