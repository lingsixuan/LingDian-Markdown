# 零点常用API文档

## ling.toast：请求显示一则toast消息
>  void ling.toast([String | Number])

调用此API，将吧参数中的第一个参数传递给Toast管理器弹出来。默认的显示时间为1秒。

!> **注意！为了避免干扰用户，此API不得频繁调用！短时间内频繁反复调用将会抛出异常！**

---


## ling.sleep()：挂起当前线程一段时间
> void ling.sleep(Integer)

此API将会在调用之后的x毫秒(x由参数指定)后返回。  
当你想暂停一段时间后才继续执行下面的代码时，可以使用它。

***使用示例***

~~~ lua
--阻塞1秒
ling.sleep(1000)
--下面的代码将在一秒钟之后执行
print("Hello world！")
~~~

!> 请注意，sleep的时间并不是非常精准的刚好x毫秒，这需要高精度的计时器硬件支持。

## ling.exit()：退出程序
> void ling.exit()

此API将抛出一个异常以终止脚本执行。

!> 如果你在以保护模式运行此API，那么异常将被捕获，无法起到退出程序的作用。  
请尽量使用此API来终止运行，而不是os.exit()，os.exit()会导致解释器进程同步退出。
