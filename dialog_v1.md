# 对话框操作（简单模式）

## ling.dialogMessage()：显示提示对话框
> void ling.dialogMessage(String message)  
> void ling.dialogMessage(String message,Boolean isClose)

- message：对话框中显示的文本
- isClose：用户是否可以通过单击空白处关闭对话框，默认为true，当此参数为false时，用户将只能通过点击对话框的按钮来关闭。

此API在构建出的对话框被关闭之前<u>**不会**</u>返回。