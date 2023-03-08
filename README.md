# 零点修改器脚本开发文档   

## 声明
本文档针对需要利用零点提供的内存编辑功能和lua运行时，开发自动化的lua脚本的开发者编写。  
阅读前，请明确需要达到的目的，和需要解决的问题。而不是漫无目的的翻阅。

**在使用零点之前，你可能已经接触过GameGuardian脚本的编写，并且已经养成了一些编码习惯。在开始零点的开发之前，请摒弃从GameGuardian养成的全部的编码习惯，因为，零点的设计哲学和GG天差地别。**

!> 当前文档版本适用与零点1.0.1版本！

---

## 排版约定  
本文档中展示的所有API签名都将遵守以下格式：
> [返回值数量 | 各返回值类型]  函数名称(参数类型...)

例如，请求零点显示一则toast消息的API签名如下：
``` lua
void ling.toast([String | Number])
```
- 前面的*void*表示该函数没有返回值
- void后面的*ling*表示该API在ling命名空间中。零点几乎所有的API都在ling命名空间下，类似GameGuardian，它的API都在*GG*命名空间下。
- toast即API名称。你可以在本文档中找到零点所有API的签名。
- 括号中的 **[String | Number]** 表示该API的第一个参数，一般来说，函数签名中的参数会省略 “[]” 但是如果一个参数的类型不固定，那么不会省略 “[]” 此处， **[String | Number]** 表示此API的第一个参数需要传入一个String ~~(字符串)~~ 类型或者一个Number ~~(实数)~~ 。如果有多个参数，不同参数之间用 “,” ~~(英文逗号)~~ 来分隔。
> lua是动态类型的编程语言，这意味着函数定义中不能显式规定参数的类型，对基础不牢靠的开发者来说，这太过于灵活了，不好驾驭。零点大多数的API都能自动转换传入参数到对应的类型，但是，你不应该依赖此特性来完成任何工作，应为它并不保证总是可用。

本文档中，使用“-->”来表示语句的返回值或者语句含义，例如：
~~~ lua
local a = 1     --> 1
local b = 5     --> 5
local c = a + b   -->   6
ling.toast("a+b="..c)   -->     nil     --由于ling.toast没有任何返回值，所以为nil
~~~
由于lua使用两个连续的“-”作为行注释的开始，所以，使用“-->”来表示返回值，语法上不会产生问题，它将被lua虚拟机忽略。

---

## 异常处理

什么是异常？请参阅：[异常-百度百科](https://baike.baidu.com/item/%E5%BC%82%E5%B8%B8/5952477)

零点的很多API都会在一定条件下抛出异常，具体抛出时机请参阅具体的API文档。

当API抛出异常时，如果你没有对应的异常处理，那么这个异常将会终止脚本执行，并且将异常信息显示出来。

如何优雅的处理异常，请参阅[lua错误处理-菜鸟教程](https://www.runoob.com/lua/lua-error-handling.html)

请注意，不是所有异常都适合捕获。请只捕获脚本能处理的异常，对于脚本不能处理的异常，请放任它继续抛向上级节点，而不是把它藏起来。藏起来的问题其实并没有消失，而是藏的更深了。这种设计哲学叫做 <u> **任其崩溃** </u>

[应该如何理解Erlang的任其崩溃思想？-知乎](https://www.zhihu.com/question/21325941)



---

## 脚本的签名
&emsp;&emsp;在发布自己的脚本之前，你需要给你自己的脚本签名。数字签名是零点保护开发者权益的方式之一。  
### 什么是数字签名？
&emsp;&emsp;[数字签名-百度百科](https://baike.baidu.com/item/%E6%95%B0%E5%AD%97%E7%AD%BE%E5%90%8D/212550)  

&emsp;&emsp;发布脚本前，你需要向零点信任的CA(在零点APP中有默认CA)申请证书，证书中将包含你的公钥和私钥，以及证书的一些基本信息。

&emsp;&emsp;发布脚本时，零点将会使用一些算法来加密脚本，然后将加密后的脚本，计算出sha256摘要，使用你的私钥加密(也就是签名)，将加密后的内容以及你证书除去私钥的其他部分一起附加在脚本文件中。

&emsp;&emsp;执行脚本时，零点将会验证脚本的签名是否正常。如果您发现有人对您的脚本进行二次工程后再发布，那么你可以向零点CA申请吊销二改脚本的证书。当然，你需要提交证据来证明你才是原作者。

## 零点安全性的保证
&emsp;&emsp;零点使用安全的lua运行时，修改lua虚拟机指令集，防止通过非法手段获取脚本字节码。对于脚本的加密方面，将使用证书内携带的密钥来加密脚本，使用的加密算法(RSA算法)经过世界顶级密码学专家多年的测试，是现代互联网安全的基石。







