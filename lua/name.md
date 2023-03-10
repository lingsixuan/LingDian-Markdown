# 术语解释

## 魔数

又叫魔法值，指代一些没有任何意义，数值完全是出于某种目的人工规定的数值。  
例如，Linuxi系统下的可执行文件，头部四个字节是固定的0x7f454c46，这串数字本身并没有任何意义，而是人为规定EFI文件必须以它们作为文件开始。  

## 回调函数

[回调函数](https://zhuanlan.zhihu.com/p/326902537)是指作为参数传递给被调用的函数，由被调用的函数在某个时机调用的函数。

考虑以下代码
~~~ lua
--假设此函数内部的代码在子线程中执行，
function thread(call)
    ling.sleep(5000)    --假设这是由于业务逻辑引起的延迟
    call()      --假设参数中携带了上面的业务逻辑计算的结果
end

--调用运行时间较长的函数
--将他放进其他线程中运行
--不影响自身业务
--处理完成后，由对方调用回调函数来通知主线程
thread(function()
    ling.toast("处理完毕")
end)

--其他业务代码
~~~

示例中，主线程在调用需要执行很长时间的逻辑时，可以吧它放在子线程中执行，将执行结果使用回调函数的形式来通知自己。

回调函数，一般用在多线程场景下，由一个线程通知另一个线程一些事件。例如数据处理完毕，例如用户点击按钮。