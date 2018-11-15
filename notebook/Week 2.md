# 第二周

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
  3. zx
  4. nx
  5. zy
  6. ny
  7. f
  8. no

其中xy就是要计算的两个值，zx与nx对x进行预处理，zy与ny对y进行预处理，之后根据f的值对处理后的x与y进行计算：
  1. f = 1，计算Or(x, y)
  2. f = 0，计算And(x, y)

最后no处理输出，如果no=1，输出就是Not(上面计算的结果)，否则就直接输出结果。

有三个输出端，out，zr，ng：
  1. out 就是计算结果
  2. zr 如果out = 0，就zr = 1，否则zr = 0
  3. ng 如果out < 0，就ng = 1，否则ng = 0



