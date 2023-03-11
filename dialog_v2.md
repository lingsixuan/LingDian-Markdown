# 对话框操作（完全模式）

## ling.newDialog()：构造完全的对话框

> Integer ling.newDialog(Table)

此API的返回值为一个整数，代表创建的对话框的ID，此API返回后，可以通过对话框操作的其他API来操作这个ID，达到操作对话框的目的。

<u> ***注意！此API的返回值将是从1开始依次递增的值，但是，不应该利用此特性来进行任何工作，因为它随时可能被修改！*** </u>

注意！它需要的参数是一个Table对象，如果传递的参数不是Table类型， <u> **将会抛出异常。** </u>

该Table需要如下字段：

- 标题   &emsp;     [String]
- 内容   &emsp;     [String]
- 按钮1  &emsp;     [String]
- 按钮1事件 &emsp;  [Function]
- 按钮2  &emsp;     [String]
- 按钮2事件 &emsp;  [Function]
- 按钮3  &emsp;     [String]
- 按钮3事件 &emsp;  [Function]
- 可取消  &emsp;    [Boolean]
-  关闭事件  &emsp;     [Function]

其中，String类型的参数很好理解。不做解释。

[Function]，表示需要一个函数作为参数。该函数的签名为：

**void function(int dialogID)**

其中，参数中的dialogID表示触发事件的对话框的ID，可以省略，但是省略的话，就无法得知是那个对话框的事件，也无法操作对应对话框了。

> 在lua中，函数(function)也是第一类值，这意味着它也可以像一个普通的变量一样赋给其他变量，也可以作为参数传递给其他函数。将函数作为参数传递给其他函数时，作为参数的函数并不会立马被调用，它的调用时机由被调用的函数决定。这就是[回调函数](https://blog.csdn.net/angciyu/article/details/80794273)  
拿按钮1事件举例，赋给按钮1事件的函数，只有对话框的地一个按钮被用户点击之后，由零点调用。如果用户没有点击按钮，那么它永远不会被调用。

***使用示例***
~~~ lua
--此table并没有给所有的元素都设置值，缺省的节点将由默认值填充。
--需要字符串的节点默认值为空字符串
--需要函数的节点默认值为nil，也就是不进行任何操作
--如果[可取消]缺省，则默认可以取消。
local dialogData = {
    ["标题"] = "我是标题",
    ["内容"] = "我是内容",
    ["按钮1"] = "我是第一个按钮"
    ["按钮1事件"] = function(dialogID)      -->     一个匿名函数，将在按钮1被用户单击时由零点调用
                                    ling.toast("按钮1被单击") 
                                end
    ["可取消"] = false     -->      表示创建的对话框不能通过点击对话框外部来关闭
    ["关闭事件"] = function(dialogID)           -->     将在对话框由任何原因关闭时调用。
                                    ling.toast("对话框关闭")
                                end
}
int id = ling.newDialog(dialogData)
~~~

<u> ***~~注意！调用此API后，lua侧需要让出CPU时间片，即需要快速结束代码的执行（不是调用exit()！）否则零点将无法分发事件到lua侧！~~*** </u>

> ~~什么叫“让出CPU时间片”？  
如果API要求你在调用之后迅速让出CPU时间，则意味着它可能在这之后的某个时间需要操作lua的堆栈（例如调用lua函数）这时如果脚本正在运行，则无法获得堆栈的锁（因为它被其它线程锁定了）那么函数调用将会进入等待队列中，直到持有堆栈锁的线程释放它。  
在lua脚本工作期间，锁由lua虚拟机持有，而且在lua代码结束运行之前一直不会返回。这会阻止零点分发事件。  
所以，在调用要求释放CPU时间的API后，你需要尽快结束lua的运行，并非是调用exit()，而是让调用链断裂。例如调用ling.newDialog()之后，没有了任何代码，这就算是让出了CPU时间。  
在调用此类API之后，你还是可以执行一些其他任务，只需要保证尽快让出即可。  
让出CPU时间之后，当对应事件发生时，零点将回调对应函数，调用完毕之后，CPU时间则又交还给了lua，你可以在这时继续你的任务。~~

!> 自Beta1.0.1版本起，newDialog已经无需开发者让出时间片，对话框事件的回调函数将在独立线程中执行。

---

## ling.closeDialog()：关闭对话框
> void ling.closeDialog(Integer,...)

此API可以用来关闭一个或多个由[ling.newDialog()](dialog_v2.md?id=lingnewdialog：构造完全的对话框)创建的对话框。   
需要传入的参数为[ling.newDialog()](dialog_v2.md?id=lingnewdialog：构造完全的对话框)的返回值，可以传入多个。

***使用示例***
~~~ lua
local dialogData1 = {
    ["标题"] = "提示",
    ["内容"] = "此对话框只能通过ling.closeDialog()来关闭",
    --为了防止用户点击按钮关闭对话框，故按钮字段留空
    --["按钮1"] = "确定",
    --下面这个字段设置为false后，用户将不能通过点击空白处关闭对话框，但点击按钮仍然可以关闭。
    ["可取消"] = false
    ["关闭事件"] = function(dialogID)
        --使用ling.closeDialog()关闭对话框仍然会触发此事件。
        ling.toast("对话框关闭")
    end
}
--显示第一个对话框
local dialog1 = ling.newDialog(dialogData1)
--再显示一个同样的对话框
local dialog2 = ling.newDialog(dialogData1) 
--现在屏幕上有两个对话框。

ling.closeDialog(dialog1)   -->关闭第一个对话框
ling.closeDialog(dialog2)   -->关闭第二个对话框

ling.closeDialog(dialog1,dialog2)   -->也可以这样一次性关闭两个对话框
--需要关闭更多对话框也可以直接添加参数。

~~~