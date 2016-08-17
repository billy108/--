8/15/2016 4:09:18 PM 
## 第八章 Activity ##

**1.四大组件之一**，出现频率最高，与用户交互的第一接口，采用Activity栈管理Activity。

**2.四种形态**

> 1）Active/Running：Activity处于Activity栈顶，可见，与用户进行交互。
> 
> 2）Paused：失去焦点，但还可见，不能交互，所有状态信息、成员变量都保持，只有在内存极低的情况下，才会被回收。
> 
> 3）Stopped：完全不可见，但依然保持所有状态信息和成员变量。
> 
> 4）Killed：被回收或从来没被创建过，

**3.生命周期**

> 当Activity离开前台时，如果用户调用了finish()方法，则不会调用onSaveInstanceState（）方法。

**4.Andro任务栈（Task）**

> 保存整个App的Activity。
> 
> 注意：一个Task中的Activity可能来自不同的App，同一个App的Activity也可能不在一个Task中。


**5.启动模式**

> ManiFest方法
> 
> 1）standard：
> 
> 2）singleTop:适用于接收到消息后显示的页面，如QQ接收到消息后弹出Activity，如果一次来10个消息，总不能一次弹10个Activity。
> 
> 3）singleTask:如果其它程序以singleTask的模式来启动这个Activity，将会新建一个任务栈。通常适用于退出整个应用：将主Activity设为singleTask，然后在要退出中转到主Activity，从而将主Activity以上的都清除，重写onNewInstance（）方法中加入finish（），都清除掉。
> 
> 4）singleInstance：适用于需要与程序分离的界面，如调用紧急呼叫。


> intent Flag方法
> 
> 1）FLAG_ACTIVITY_NEW_TASK:适用从Service中启动Activity
> 
> 2）FLAG_ACTIVITY_SINGLE_TOP:singleTop模式
> 
> 3）FLAG_ACTIVITY_CLEAR_TOP:singleTask模式
> 
> 4）FLAG_ACTIVITY_NO_HISTORY:当启动后，就消失

**6.清空任务栈**

> 在<activity>标签中使用
> 
> 1）clearTaskOnLaunch:每次返回时，清除以上Activity
> 
> 2）finishOnTaskLaunch:离开Task再返回时，清除本身
> 
> 3）alwaysRetainTaskState:不能清除

结束于 8/16/2016 11:49:57 AM 

----------


