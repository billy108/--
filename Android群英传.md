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

## 第10章 Android性能优化##
一.布局优化

1、UI渲染机制：画面流畅，保持大概60帧/秒，相当于16ms/帧。

2、避免OverDraw。

3、优化布局层次：降低View树的高度，不宜超过10层。

4、避免嵌套过多无用布局：嵌套很多布局会使View树过高。

1）使用<include>标签重用Layout。比如TopBar，BottomBar

2）使用<ViewStub>实现View的延迟加载，不可视，大小为0.比如布局在特定的情况下才会显示出来。

显示方法：
mViewStub.setVisibility(View.VISIBLE);

View v = mViewStub.inflate();返回View

提问：<ViewStub>与View.GONE的区别？
<ViewStub>只会在显示的时候渲染布局，而View.GONE在初始化的时候已经添加到布局树了。

5、Hierarchy Viewer

## 二、内存优化（堆内存管理） ##
1.内存：手机RAM，包括寄存器、栈、堆、静态存储空间和常量池。

寄存器：最快的存储场所，程序无法控制。
栈：存放基本类型的数据和对象引用，当作用域结束后，内存被分给新的变量。
堆：存放new创建的对象和数组，由GC管理。
静态存储空间：固定的区域来管理特殊的数据变量，如静态数据变量。
常量池：每个被装载的类型所用到常量的一个有序集合。

堆内存大小：
![](http://i.imgur.com/s7kOtuy.png)

2.内存优化实例
1）Bitmap优化

--使用适当分辨率和大小的图片
--及时回收内存
--使用图片缓存（通过内存缓存LruCache和硬盘缓存DiskLruCache）

2）代码优化
--对常量使用static修饰符
--使用静态方法，比普通方法提高15%左右的速度
--减少不必要的成员变量，通过Lint工具可以检测。
--减少不必要的对象，避免频繁创建短作用域的变量
--尽量不用枚举，少用迭代器
--对Cursor、Recvice、Sensor、File等对象，要非常注意创建、回收和注册、解注册
--避免使用IOC框架
--使用RenderScript、OpenGL来进行非常复杂的绘图操作
--使用SurfaceView来替代View进行大量、频繁的绘图操作
--尽量使用视图缓存，而不是每次都执行inflate（）方法解析视图

## 三.Lint工具 ##
## 四、Memory Monitor工具 ##
## 五、TraceView工具 ##
用于分析TraceView日志

1.通过代码生成精确范围的TraceView日志。
用Debug.startMethodTracing()和Debug.stopMethodTracing()来包围要监听的代码块，日志将会保存在“/sdcard/dmtrace.trace”目录下。

2.通过Android Device Monitor生成TraceView日志

## 六、使用MAT工具 ##
1、判断内存泄漏的小技巧：不停的点击“Cause GC”时，如果“data object”一栏中的“Total Size”有明显的变化，就代表有可能存在内存泄漏。	

2、点击“Dump HPROF File”,生成.hprof文件，保存之。
使用hprof-conv工具进行转换






