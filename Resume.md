自我介绍

您好，我叫赵升华。现在就职于一家IT公司，工作了有1年了，公司业务是做自己的一款时间管理的软件和接受软件定制服务，我主要负责移动端安卓的开发。作为主程，我做过一个新闻资讯类项目；还参加过几个项目，负责某个功能的开发。

优点是，有解决问题的能力，遇到问题了，我会想尽办法的从各渠道想办法，比如通过stackoverflow，问身边从事开发的朋友等。还有性格稳重，有毅力能坚持。

缺点是，

----------

# 面试题准备 #

# HR #

1.为什么离职？

因为现在工作地点离家太远了，要2个小时，不方便。

----------

# 面试实际问题 #
## 乐视： ##
**技术：**

    1、举例说明ANR发生的情况
    2、事件分发机制
    3、线程间的通信和同步
    4、进程间的通信
    5、用二分法求数组中某个元素的位置
    6、项目介绍
    7、界面是怎么设计的
    8、ListView优化
    9、Activity启动模式
    10、内存溢出的情况
	11、为什么离职

**答案：**

1、**出现场景：**

- 主线程被IO操作（从4.0之后网络IO不允许在主线程中）阻塞。 
- 主线程中存在耗时的计算
- 主线程中错误的操作，比如Thread.wait或者Thread.sleep等 
- Android系统会监控程序的响应状况，一旦出现下面两种情况，则弹出ANR对话框
	- 应用在 5秒 内未响应用户的输入事件（如按键或者触摸） 
	- BroadcastReceiver未在 10秒 内完成相关的处理

**如何避免：

- 使用AsyncTask处理耗时IO操作。
- 使用Thread或者HandlerThread时，调用Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND)设置优先级，否则仍然会降低程序响应，因为默认Thread的优先级和主线程相同。
- 使用Handler处理工作线程结果，而不是使用Thread.wait()或者Thread.sleep()来阻塞主线程。
- Activity的onCreate和onResume回调中尽量避免耗时的代码
- BroadcastReceiver中onReceive代码也要尽量减少耗时，建议使用IntentService处理。

**总结：**anr异常也是在程序中自己经常遇到的问题，主要的解决办法自己最常用的就是不要在主线程中做耗时的操作，而应放在子线程中来实现，比如采用Handler+mesage的方式，或者是有时候需要做一些和网络相互交互的耗时操作就采用asyntask异步任务的方式（它的底层其实Handler+mesage有所区别的是它是线程池）等，在主线程中更新UI。

2、[事件分发机制](http://www.infoq.com/cn/articles/android-event-delivery-mechanism/)

3、[http://blog.csdn.net/peng6662001/article/details/7277851/](http://blog.csdn.net/peng6662001/article/details/7277851/)