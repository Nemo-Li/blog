---
title: recyclerView源码分析
date: 2019-07-04 22:01:54
tags: recyclerView
---

可以借鉴的写法

从ViewHolder的源码中可以看到有许多记录状态的flag，源码通过与、或、非以及移位运算实现了添加设置以及清除特定的flag的作用，觉得可以在代码中借鉴使用。

理解了下这样做的原理

00000001 对于2进制的数，每个位都可以做为一个flag的记录，1表示设置了，0代表没有设置。那么用或运算可以实现添加flag的目的。比如

```
00000001 //FLAG_BOUND
00000010 //FLAG_UPDATE
用或运算结果为
00000011 //这里两位的信息都保存了下来。
```

那么怎么判断我有没有设置这个flag呢，可以通过与运算。比如我要看是否设置了FLAG_BOUND

```java
//mFlags表示我现在设置的所有flag，那么怎么去判断我有没有设置了FLAG_BOUND？
boolean isBound() {
	return (mFlags & FLAG_BOUND) != 0;
}
假设我现在设置了FLAG_BOUND和FLAG_UPDATE那么
mFlags = 00000011
00000011 & 00000001 = 00000001 可以看到只要设置了FLAG_BOUND，那么值就会为1，没设置就为0，
这样就可以取到设置的结果。
```

移位运算可以方便的设置每个位的值，依旧那上面的值来说

```
1 << 0 左移0位 00000001 这位置表示FLAG_BOUND
1 << 1 左移1位 00000010 这个位置表示FLAG_UPDATE
移位操作对于寄存器来说很快，所以这样运算很快。
```

我觉得以上在我们编码时对于需要设置一些flag时可以借鉴编码。

后面会继续记录阅读源码发现的有意思的点。

### ViewBoundsCheck

用来校验子view在parent（recyclerview）在边界的位置