---
title: think
date: 2016-06-30 15:49:16
tags:
---
世人总是恐惧失败，但失败了也大不从头再来

#谈谈对Android中的消息机制的理解

Android中的消息机制主要由Handler、MessageQueue、Looper三个类组成，他们的主要作用是

- Handler负责发送、处理Message
- MessageQueue负责维护Message队列
- Looper负责Message的轮询

Android的消息机制主要是为了在子线程中处理的数据可以在主线程更新UI，完成从子线程到主线程的切换。

而Android的单线程UI模型，则是为了简化UI操作，防止在多线程情况下同时更新UI造成的同步操作和效率问题。

与这种消息机制相关的类还有HandlerThread、IntentService。

HandlerThread内部维护着Handler和消息循环，需要手动退出Looper才会退出循环。

而IntentService内部则维护着HandlerThread和Handler，发送到IntentService的任务会被Handler发送到HandlerThread中执行。

如果要使用Handler，比如在线程中开启Looper才可以，在主线程默认可以使用Handler，是因为在ActivityThread.main()中自动开启了Looper，所以如果在子线程中使用Handler需要

```
Looper.preper();
	......
Looper.loop();
```

另外，使用任何与这种消息机制相关的方法发送一个Runnable对象，都不是开启新线程执行，而只是为了使用Runnable.run()。比如

```
Handler.post(Runnable);
View.post(Runnable);
```