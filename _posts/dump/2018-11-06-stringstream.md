---
layout: post
category: dump
title: stringstream
description: C++中提供的一个好用的类型转换的库
---

　　因为我们想要转换的变量在编译的时候就已经确定了，所以编译器拥有足够的信息来判断哪些信息需要转换。<sstream>库中声明的标准类就利用了这一点，自动选择所必要的转换。而且转换的结果保存在stringstream对象的内部缓冲中，我们不需要担心缓冲区会溢出，因为对象会自动分配内存空间。<br>
　　如果我们在多次转换的时候都使用的是同一个stringstream的对象的话，每次转换之前都需要使用clear()方法。

```C++
string resuilt="1000";
int n=0;
stringstream stream;
stream<<result;
stream>>n;
//此时n的结果等于1000

```