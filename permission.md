# 零点权限相关

## 零点的权限设计

为了限制脚本行为，杜绝一些危害用户设备的流氓行为，规范开发习惯，我们为零点和lua标准库中的部分API施加了一些限制。

零点目前的权限设置如下：

- IS_TCP：完全的网络访问权限。
- IS_ALL_FILE：完全的磁盘访问权限
- IS_OS_EXECUTE：完全的系统控制权限

> &emsp;&emsp;lua标准库提供了一系列操作磁盘文件的API，放在io包下，使用这些API来访问磁盘文件时，将会检查脚本是否具有 **IS_ALL_FILE** 权限，如果没有此权限，那么脚本则只能访问自己私有目录下的文件 ~~(私有目录位置通过ling.getFilesDir()获得)~~ 。如果在没有 **IS_ALL_FILE** 权限的情况下访问私有目录之外的文件，***将会抛出异常***。  
&emsp;&emsp;**IS_OS_EXCEUTE**决定脚本是否有权执行shell命令。不具备此权限的前提下调用os.exceute()API，***将会抛出异常***。  
&emsp;&emsp;请注意！即使具备 **IS_OS_EXCEUTE** 权限，也不能使用rm命令删除磁盘上的文件。要删除文件，请使用io库中的文件操作API。

---

# 权限的检查与申请

## 权限枚举值
在脚本中，以下字段分别指代不同的权限。
~~~ lua
Permission.TCP      -->     完全的网络访问权限
Permission.ALLFile     -->      完全的磁盘访问权限
Permission.OSExecute        -->         完全的系统控制权限
~~~

!> <u>***<font color="#8B0000">注意！以上枚举值只是代码中的权限名称，仅用于告知API您需要操作的权限是什么，它们的值是固定的，并不是脚本当前的权限设置！</font>***</u>

---

 ## ling.getFilesDir()：获取脚本私有目录

> String ling.getFilesDir()
&emsp;-->&emsp;返回私有目录的起始地址，此目录下的所有子目录都属于私有目录。

### 什么是私有目录？

&emsp;&emsp; 私有目录，即脚本天然可以访问，不需要任何权限，且只有自己可以访问的目录。你可以吧脚本的配置文件存放在私有目录中。

!> <u> ***如果你需要保存敏感数据例如用户的帐号密码等数据，你可以使用零点提供的密码学API来加密它们。而不是藏在犄角旮旯里，这没有任何意义。*** </u>

---

## ling.getPermission()：检查权限设置
> Boolean... ling.getPermission(Integer...)

此API的参数数量和返回值数量都是可变的。即你可以传递任意数量的参数，而且，它的返回值的数量和参数的数量一致。

***使用示例：***
~~~ lua
local is_tcp = false        -->     是否可以访问网络
local is_file = false       -->     是否可以访问私有目录之外的文件

is_tcp , is_file = ling.getPermission(Permission.TCP,Permission.ALLFile)    -->   同时检查网络和文件两个权限

if is_tcp == true then
    print("具有网络访问权限")
else
    print("不具有网络访问权限")
end

if is_file then
    print("具有文件访问权限")
else
    print("不具有文件访问权限")
end
~~~

!> <u> ***注意！调用权限操作API时，必须使用枚举变量，即Permission包下的变量来指代对应权限，而不能通过print得到Permission中对应权限的Integer数值后，直接填写该数值！Permission中的权限对象的值不保证不变，如果你直接写他们的值而不是枚举对象，将会出现兼容性问题！而且还会导致代码可读性降低！请不要在不该偷懒的地方偷懒！*** </u>

---

## ling.startPermission()：请求对应权限

> Boolean... ling.startPermission(Integer...)

此API的参数数量和返回值数量也是可变的，而且，它们的数量也相同。

其中，返回值的含义为，用户是否给予对应权限。请看下方实例。

此API用于向用户请求运行时权限。参数中传递的是权限的枚举对象，调用之后，零点将显示一则对话框，向用户询问授权。在用户作出决定之前，此API不会返回。

***使用示例***
~~~ lua
local is_tcp = false    -->    网络权限
local is_file = false    -->    文件权限

is_tcp , is_file = ling.startPermission(Permission.TCP,Permission.ALLFile)      -->     请求权限

if is_tcp == true then
    print("用户授予网络权限")
else
    print("用户拒绝授权")
end

if is_file == true then
    print("用户授予文件权限")
else
    print("用户拒绝授权")
end
~~~

***注意！请尽量避免申请已经具备的权限。这可能会导致用户取消原本已经授予的权限。***




