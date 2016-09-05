# Activity #
## 1.概念 ##
简单的说：Activity就是布满整个窗口或者悬浮于其他窗口上的交互界面。在一个应用程序中通常由多个Activity构成，都会在Manifest.xml中指定一个主的Activity，如下设置

    <action android:name="Android.intent.action.MAIN" />

当程序第一次运行时用户就会看这个Activity，这个Activity可以通过启动其他的Activity进行相关操作。当启动其他的Activity时这个当前的这个Activity将会停止，新的Activity将会压入栈中，同时获取用户焦点，这时就可在这个Activity上操作了。都知道栈是先进后出的原则，那么当用户按Back键时，当前的这个Activity销毁，前一个Activity重新恢复
## 2.生命周期 ##
![](http://hi.csdn.net/attachment/201109/1/0_1314838777He6C.gif)
# 以下是各个方法的详细说明： #

**onCreate()**：当 activity 第一次创建时会被调用。在这个方法中你需要完成所有的正常静态设置 ，比如创建一个视图（ view ）、绑定列表的数据等等。如果能捕获到 activity 状态的话，这个方法传递进来的 Bundle 对象将存放了 activity 当前的状态。调用该方法后一般会调用 onStart() 方法。

**onRestart()**：在 activity 被停止后重新启动时会调用该方法。其后续会调用 onStart 方法。

**onStart()**：当 activity 对于用户可见前即调用这个方法。如果 activity回到前台则接着调用 onResume() ，如果 activity 隐藏则调用onStop()

**onResume()**：在 activity 开始与用户交互前调用该方法。在这时该activity 处于 activity 栈的顶部，并且接受用户的输入。其后续会调用 onPause() 方法。

**onPause()**：在系统准备开始恢复其它 activity 时会调用该方法。这个方法中通常用来提交一些还没保存的更改到持久数据 中，停止一些动画或其它一些耗 CPU 的操作等等。无论在该方法里面进行任何操作，都需要较快速完成，因为如果它不返回的话，下一个 activity 将无法恢复出来。如果 activity 返回到前台将会调用 onResume() ，如果 activity 变得对用户不可见了将会调用onStop() 。

**onStop()**：在 activity 对用户不可见时将调用该方法。可能会因为当前 activity 正在被销毁，或另一个 activity （已经存在的activity 或新的 activity ）已经恢复了正准备覆盖它，而调用该方法。如果 activity 正准备返回与用户交互时后续会调用onRestart ，如果 activity 正在被释放则会调用 onDestroy 。
**
onDestroy()**：在 activity 被销毁前会调用该方法。这是 activity 能接收到的最后一个调用。可能会因为有人调用了 finish 方法使得当前activity 正在关闭，或系统为了保护内存临时释放这个 activity的实例，而调用该方法。你可以用 isFinishing 方法来区分这两种不同的情况。
## 3.启动一个新的Activity ##
1. 要启动一个新的Activity，我们可以通过调用Context中的startActivity来启动。像这样： 
 
        Intent intent = new Intent(this, ActivityDemo.class);  
    	startActivity(intent);  // ActivityDemo是需要启动的Activity类

1. 启动带返回值的Activity：

        Intent intent = new Intent(ActivityLifeDemo.this,RevalueActivity.class);  
        startActivityForResult(intent, 0x1001); 
    
## 4.传递数据 ##
    Intent intent = new Intent(this,ActivityDemo.class);  
    Bundle bundle = new Bundle();  
    bundle.putBoolean("bool_key", true);  
    intent.putExtras(bundle);  
    startActivity(intent); 
## 5.保存Activity运行状态 ##
**通过重写onSaveInstanceState（）方法来实现Activity的运行状态，请注意以下几点：**
 > 1）由于activity 对象被暂停或停止时，它仍然保留在内存里面，关于它
> 的成员信息和当前状态都是活动的，所以此时可以保存Activity的状态，从而使用户所作的Activity的更改保存在内存中
> 
> 2）  当系统回收内存而将Activity销毁时，就无法保存其状态，所以需要调用onSaveInstanceState（）方法来实现状态的保存
> 
> 3）  很多情况并不需要保持状态信息，比如按下返回键直接关闭程序，所以并不能保证会调用onSaveInstanceState。如果调用了该方法，一般是在onStop 方法之前且可能在 onPause 之后调用。尽管如此，即使你没做任何操作或没有实现 onSaveInstanceState() 方法，你的 activity 状态也能通过Activity 类里面默认实现的 onSaveInstanceState 方法恢复出来。特别是会为布局中的视图（ View ）默认调用onSaveInstanceState 方法，并在这个方法中允许每一个视图提供它需要恢复的任何信息。几乎每一个 Android框架中的 widget 都视情况实现了这个方法。
> 
> 注：因为 onSaveInstanceState 方法不一定会被调用，所以你应该只是用它来保存一些 activity 的转换过程状态（即 UI 的状态），而不能用来保存永久性数据。但你可以用 onPause 方法在用户离开 activity 时来保存永久性数据，比如需要保存到数据库的数据。
> 
> 有一个很好的方法可以用来检验应用程序保存状态的能力，就是简单地旋转你的设备来改变屏幕的方向。因为当屏幕方向改变时，系统为了给新的方向提供一个可能合适的代替资源，会销毁 activity 并新建一个新的。由于这个原因，你的 activity 是否能在其重新创建时完成保存状态就显得尤为重要，因为用户经常会在使用应用程序时旋转屏幕的。

## 6.Activity 栈 ##
![](http://www.ibm.com/developerworks/cn/opensource/os-cn-android-actvt/image002.jpg)

> 如上所示，除了最顶层即处在 Active 状态的 Activity 外，其它的 Activity 都有可能在系统内存不足时被回收，一个 Activity 的实例越是处在栈的底层，它被系统回收的可能性越大。系统负责管理栈中 Activity 的实例，它根据 Activity 所处的状态来改变其在栈中的位置。

## 一些关于 Activity 的技巧 ##
**锁定 Activity 运行时的屏幕方向**
> Android 内置了方向感应器的支持。在 G1 中，Android 会根据 G1 所处的方向自动在竖屏和横屏间切换。但是有时我们的应用程序仅能在横屏 / 竖屏时运行，比如某些游戏，此时我们需要锁定该 Activity 运行时的屏幕方向，<activity >节点的 android:screenOrientation属性可以完成该项任务，示例代码如下：
   
     <activity android:name=".EX01"`
     android:label="@string/app_name" 
     android:screenOrientation="portrait">// 竖屏 , 值为 landscape 时为横屏
     </activity>
**全屏的 Activity**
> 要使一个 Activity 全屏运行，可以在其 onCreate()方法中添加如下代码实现：

    // 设置全屏模式
     getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, 
    WindowManager.LayoutParams.FLAG_FULLSCREEN); 
     // 去除标题栏
     requestWindowFeature(Window.FEATURE_NO_TITLE);
**在 Activity 的 Title 中加入进度条**
> 为了更友好的用户体验，在处理一些需要花费较长时间的任务时可以使用一个进度条来提示用户“不要着急，我们正在努力的完成你交给的任务”。
如下图：
在 Activity 的标题栏中显示进度条不失为一个好办法，下面是实现代码：
 
     // 不明确进度条
     requestWindowFeature(Window.FEATURE_INDETERMINATE_PROGRESS); 
     setContentView(R.layout.main); 
     setProgressBarIndeterminateVisibility(true); 
    
     // 明确进度条
     requestWindowFeature(Window.FEATURE_PROGRESS); 
     setContentView(R.layout.main); 
     setProgress(5000);
## Activity四种启动模式 ##> 
> 启动模式简单地说就是Activity启动时的策略，在AndroidManifest.xml中的标签的android:launchMode属性设置；
> 
>启动模式有4种，分别为standard、singleTop、singleTask、singleInstance
讲解启动模式之前，有必要先讲解一下“任务栈”的概念;

　　**任务栈**

　　每个应用都有一个任务栈，是用来存放Activity的，功能类似于函数调用的栈，先后顺序代表了Activity的出现顺序；比如Activity1-->Activity2-->Activity3,则任务栈为：

![](http://www.linuxidc.com/upload/2012_07/120721093646151.gif)

(1)standard：每次激活Activity时(startActivity)，都创建Activity实例，并放入任务栈
![](http://www.linuxidc.com/upload/2012_07/120721093646152.gif)
(2)singleTop：如果某个Activity自己激活自己，即任务栈栈顶就是该Activity，则不需要创建，其余情况都要创建Activity实例；
![](http://www.linuxidc.com/upload/2012_07/120721093646153.gif)
(3)singleTask：如果要激活的那个Activity在任务栈中存在该实例，则不需要创建，只需要把此Activity放入栈顶，并把该Activity以上的Activity实例都pop；
![](http://www.linuxidc.com/upload/2012_07/120721093646154.gif)
(4)singleInstance：如果应用1的任务栈中创建了MainActivity实例，如果应用2也要激活MainActivity，则不需要创建，两应用共享该Activity实例
![](http://www.linuxidc.com/upload/2012_07/120721093646155.gif)
**SingTask的应用：**

       可以用来退出整个应用。

       将主Activity设为SingTask模式，然后在要退出的Activity中转到主Activity，然后重写主Activity的onNewIntent函数，并在函数中加上一句finish。

 

**退出单个Activity方法：**

      调用finish

　　杀死该进程：killprocess(Process.mId)

      终止正在运行的虚拟机：system.exit()

 

**退出整个应用：**

　　制造抛异常导致整个程序退出

　　将所有的activity放入到一个list中，然后在需要退出的时候，将所有的activity，finish掉

　　通过广播来完成退出功能

     通过广播来完成退出功能，具体实现过程是这样的：在每个Activity创建时（onCreate时）给Activity注册一个广播接收器，当退出时发送该广播即可。大概的代码如下：


@Override

protected void onCreate(Bundle savedInstanceState) {

       super.onCreate(savedInstanceState);

       IntentFilter filter = new IntentFilter();

       filter.addAction("finish");

       registerReceiver(mFinishReceiver, filter);

       ……

}

private BroadcastReceiver mFinishReceiver = new BroadcastReceiver() {

    @Override

    public void onReceive(Context context, Intent intent) {

           if("finish".equals(intent.getAction())) {

              Log.e("#########", "I am " + getLocalClassName()

                     + ",now finishing myself...");

              finish();

       }

    }

};

　　
> 把上面的代码写在一个基类里面，这样，在需要退出的地方调用close，发送广播，每个Activity接收到广播后，就会将自己finish掉。

