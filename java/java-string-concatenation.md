# java String Concatenation

java的字符串拼接问题，在微博上看到大佬讨论了好多，这里整理总结下：

1. @老赵 看到java性能调优里说，Java的`+`操作符在编译器会优化为 `StringBuilder.append`的方式，说这种`SB`的方式在内存优化的角度并不好，说出了C\#中 `String::Concat` 字符拼接实现方式。
2. 字符拼接优化直到 java9 才得到优化 \[jep280 Indify String Concatenation\]，使用 `invokeDynamic` 和 库实现 `StringConcatFactory` 的 `makeConcat` 和 `makeConcatWithConstants`，具体实现还要深入看看
3. 然后 zhh和@老赵 撕逼半天，说 javac 是做了一些事情的，java9和c\#的字符拼接是不一样的，我对javac 不懂，也不知道如何去验证
4. 说到编译器优化，会在遵守memory model的前提下做一些 reorder 等。 还是需要多了解编译器优化手段才能直到自己写的程序到底是如何跑下去的。
5. 此外还有求值序和结合性的语义讨论
6. 老赵说：

   > 其实StringBuilder在拼接字符串时也不一定是最优的，因为它其实是把每次Append进去的东西复制展开，因此内存占用是和目标字符串长度相关的。有时候，你拿一个字符串数组/List保留输入字符串，最后用自己写的Concat\(string\[\] input, beginIndex, length\)拼起来，此时额外的内存占用就是和字符串数量相关，就远小于目标字符串长度了。而这个临时字符串数组甚至都可以复用，最终效果便又是零（额外）分配了。 当然，这种方法并不是没有值得讨论的地方。一是实际开发时，复用一个巨型的StringBuilder，每线程一个（ThreadStatic），这可能也够了，开发起来也更方便（效果相对略差）。二是用StringBuilder时，每次Append的字符串可能可以被立即回收，而用上边描述的方法会导致字符串被长时间引用而被升代（假如它们本身就会被其他地方引用着那么自然就没这方面问题了）。 总之内存优化时很多时候就是围绕字符串来的，毕竟字符串代表的是一大块连续内存，而且太容易生成新字符串（Split，ToUpper，Substring等等），操作起来要么浪费要么麻烦。更有甚者是，它很容易/已经被滥用了，例如目标函数就是要接受一个字符串时，你除了生成一个新的就没其他办法了。 用Span可以解决部分问题，但它一是太新用不上，二是也不能解决所有问题。

最后关于争辩，

> [有时为什么明知道错了却还要坚持下去了？](https://zhidao.baidu.com/question/571298637.html) _自己明知走的是错的，却偏偏要走下去。这样的心态是什么心态？_ 紧张不安的心境，自我保护的一种方式。人们对于自我认识和评价，都趋向与积极的一面，对消极的评价持反对排斥态度，自身会羞与承认自己不佳的状态，为了证明自己是对的，就会进行辩解以维持自身形象，即一种自我保护。

正面认识自己的不足，及时矫正自己的心态才是对的。

### Reference

* [jep280 Indify String Concatenation](http://openjdk.java.net/jeps/280)
* [http://cr.openjdk.java.net/~briangoetz/lambda/lambda-translation.html](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-translation.html)
* [https://stackoverflow.com/questions/46512888/how-is-string-concatenation-implemented-in-java-9](https://stackoverflow.com/questions/46512888/how-is-string-concatenation-implemented-in-java-9)
* [http://en.cppreference.com/w/c/language/eval\_order](http://en.cppreference.com/w/c/language/eval_order)

