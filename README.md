# JavaScript轻量级函数式编程

个人翻译作品，原文仓库：[Functional-Light-JS](https://github.com/getify/Functional-Light-JS)。如果有翻译的不对的地方，欢迎指正。

<img src="https://cloud.githubusercontent.com/assets/150330/18719908/644f7630-7ff7-11e6-92d9-4abc3cf2b4a9.png" border=0 vspace=20/>

　　这本书探讨了可以应用于JavaScript的函数式编程（FP）的核心原理。但是，这本书的不同之处在于，我们并不会直接拿一堆术语来淹没读者。在这里我们将会讲述FP基础概念的一个子集，我称之为“轻量级函数式编程”（FLP），并且将之应用于Javascript之中。

<p class="note">
　　尽管标题中有“Light”字样，但我并不认为这本书时面向初学者的，当然它也并不“简单”更不是入门级的书籍。这本书是严谨的，并充满了细腻的细节，它是我们深入探究JS知识的基础。“Light”在这里指的是有限的，也就是说这本书注重的是深度，而不是广度。它将会在几个主题中达到其他FP-Javascript书籍所没有的深度。
<p>

　　让我们来面对它吧！除非你已经是FP小家伙俱乐部的成员了（我不是！）。“单子是自函子范畴上的一个半幺群”像这样的语句，对我们而言没有任何意义。  
　　我并不是说这些术语是没有意义的或者FP编程不适合它们。一旦你从Functional-Light毕业，你可能会希望更加正式的学习函数式编程，到那时你才会更加确实的体会到它们的意义和作用。  
　　但是我希望你能够在JavaScript中应用一些函数式编程的基础，因为我相信它会帮助你写出更好更合理的代码来。

　　**在读这本书之前，如果你想知道关于我写这本书更多的动机和观点，请看这里[前言](preface.md)。**

## 中文版

[目录](toc.md)

* 序言 (TBA)
* [前言](preface.md)
* [第一章: 为什么需要函数式编程？](ch1.md)
* [第二章: 函数式函数的基础](ch2.md)
* 第三章: 管理函数入口
* 第四章: 编写函数
* 第五章: 减少副作用
* 第六章: 值的不变性
* 第七章: 闭包与对象
* 第八章: 排列操作
* 第九章: 递归
* 第十章: 函数式异步
* 第十一章: 将之合而为一
* 附录A: 转换
* 附录B: The Humble Monad
* 附录C: FP Libraries

## 英文原版

[Table of Contents](https://github.com/getify/Functional-Light-JS/blob/master/toc.md)

* Foreword (TBA)
* [Preface](https://github.com/getify/Functional-Light-JS/blob/master/preface.md)
* [Chapter 1: Why Functional Programming?](https://github.com/getify/Functional-Light-JS/blob/master/ch1.md)
* [Chapter 2: Foundations of Functional Functions](https://github.com/getify/Functional-Light-JS/blob/master/ch2.md)
* [Chapter 3: Managing Function Inputs](https://github.com/getify/Functional-Light-JS/blob/master/ch3.md)
* [Chapter 4: Composing Functions](https://github.com/getify/Functional-Light-JS/blob/master/ch4.md)
* [Chapter 5: Reducing Side Effects](https://github.com/getify/Functional-Light-JS/blob/master/ch5.md)
* [Chapter 6: Value Immutability](https://github.com/getify/Functional-Light-JS/blob/master/ch6.md)
* [Chapter 7: Closure vs Object](https://github.com/getify/Functional-Light-JS/blob/master/ch7.md)
* [Chapter 8: List Operations](https://github.com/getify/Functional-Light-JS/blob/master/ch8.md)
* [Chapter 9: Recursion](https://github.com/getify/Functional-Light-JS/blob/master/ch9.md)
* [Chapter 10: Functional Async](https://github.com/getify/Functional-Light-JS/blob/master/ch10.md)
* [Chapter 11: Putting It All together](https://github.com/getify/Functional-Light-JS/blob/master/ch11.md)
* [Appendix A: Transducing](https://github.com/getify/Functional-Light-JS/blob/master/apA.md)
* [Appendix B: The Humble Monad](https://github.com/getify/Functional-Light-JS/blob/master/apB.md)
* [Appendix C: FP Libraries](https://github.com/getify/Functional-Light-JS/blob/master/apC.md)

## 出版

　　这本书最终计划是要出版的，但是现在这事儿八字还没一撇。有什么进展我会在这里更新的。  
　　如果你觉得我的努力是值得的（或者是我在其他的OSS方面的工作），除了购买书籍之外，我还有一个[patreon](https://www.patreon.com/getify)，我会永远感谢你慷慨的捐款。

<a href="https://www.patreon.com/getify">[![patreon.png](https://s11.postimg.org/axpzguh77/patreon.png)](https://www.patreon.com/getify)</a>

## 一对一培训
　　本书的内容主要来源于我以同名的名义进行的专业教授（公共和私人以及公司研讨会）的培训研讨会。  
　　如果您喜欢这个内容，并希望就此或者其他各种JS/HTML/Node.js的主题进行培训，请通过以下渠道和我们联系：
[http://getify.me](http://getify.me)

## 在线视频培训
　　我还有几个JS的视频培训课程，我会在[Frontend Masters](https://FrontendMasters.com)授课，比如[Functional-Lite JS](https://frontendmasters.com/courses/functional-js-lite/)。还有些课程你可以在[PluralSight](https://www.pluralsight.com/search?q=kyle%20simpson&categories=all)观看。

## Contributions
　　您对此工作的任何贡献**当然非常感谢**。  
　　但，在提交你的PR之前，**请**仔细阅读[Contributions Guidelines](https://github.com/getify/Functional-Light-JS/blob/master/CONTRIBUTING.md)。

## 许可证 & 著作权
The materials herein are all (c) 2016 Kyle Simpson.

<a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-nd/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-nd/4.0/">Creative Commons Attribution-NonCommercial-NoDerivs 4.0 Unported License</a>.