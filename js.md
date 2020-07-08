# Event Loop

## 宏任务

当前调用栈中执行的任务称为宏任务。包含script全部代码、setTimeout、setInterval、setImmediate、I/O、UI Rendering。

## 微任务

当前（此次事件循环中）宏任务执行完，在下一个宏任务开始之前需要执行的任务为微任务。包含Process.nextTick（Node独有）、Promise、Object.observe(废弃)、MutationObserver

# js继承