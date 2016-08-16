## 概念 ##
> BroadcastReceiver，顾名思义就是“广播接收者”的意思，它是Android四大基本组件之一，这种组件本质上是一种全局的监听器，用于监听系统全局的广播消息。它可以接收来自系统和应用的的广播。

由于BroadcastReceiver是一种全局的监听器，因此它可以非常方便地实现系统不同组件之间的通信。比如Activity与通过startService()方法启动的Service之间通信，就可以借助于BroadcastReceiver来实现。

**BroadcastReceiver简介：**

BroadcastReceiver用于接收程序（包括系统程序和一般应用）通过sendBroadcast()方法发出的Broadcast intents。

## 程序启动BroadcastReceiver的步骤（发出广播）： ##

> 1) 创建需要启动BroadcastReceiver的Intent。
> 
> 2) 调用Context的sendBroadcast()或sendOrderedBroadcast()方法来启动指定的BroadcastReceiver。其中sendBroadcast发送的是普通广播，sendOrderedBroadcast发送的是有序广播。
> 
> 当应用发出一个Broadcast Intent之后所匹配该Intent的组件都可能被启动。

**创建BroadcastReceiver的步骤：**

**第一步**：创建BroadcastReceiver的子类：

由于BroadcastReceiver本质上是一种监听器，所以创建BroadcastReceiver的方法也非常简单，只需要创建一个BroadcastReceiver的子类然后重写onReceive (Context context, Intentintent)方法即可。

具体代码如下：

    public class MyBroadcastReceiver extends BroadcastReceiver {
     @Override
     public void onReceive(Context context, Intent intent) {
       // TODO Auto-generated method stub
       String msg=intent.getExtras().get("msg").toString();
       Toast.makeText(context,"intent.getAction()"+intent.getAction().toString(),
     Toast.LENGTH_LONG).show();
       System.out.println("msg:"+msg);
     }
    }

**第二步**：注册BroadcastReceiver

一旦实现了BroadcastReceiver，接下就应该指定该BroadcastReceiver能匹配的Intent即注册BroadcastReceiver。注册BroadcastReceiver的方式有两种：

> 第一种是静态注册：这种方法是在配置AndroidManifest.xml配置文件中注册，通过这种方式注册的广播为常驻型广播，也就是说如果应用程序关闭了，有相应事件触发，程序还是会被系统自动调用运行。例如：

    <!-- 在配置文件中注册BroadcastReceiver能够匹配的Intent -->
    <receiver android:name="com.example.test.MyBroadcastReceiver">
    <intent-filter>
    </action>
    <category android:name="android.intent.category.DEFAULT"></category>
    </intent-filter>
    </receiver>

> 第二种是动态注册：这种方法是通过代码在.Java文件中进行注册。通过这种方式注册的广播为非常驻型广播，即它会跟随Activity的生命周期，所以在Activity结束前我们需要调用unregisterReceiver(receiver)方法移除它。例如：

    //通过代码的方式动态注册MyBroadcastReceiver
    MyBroadcastReceiver receiver=new MyBroadcastReceiver();
    IntentFilter filter=new IntentFilter();
    filter.addAction("android.intent.action.MyBroadcastReceiver");
    //注册receiver
    registerReceiver(receiver, filter);
> 
> 注意：如果我们在Activity中注册了BroadcastReceiver，当这个Activity销毁的时候要主动撤销注册否则会出现异常。方法如下：

    @Override
    protected void onDestroy() {
      // TODO Auto-generated method stub
      super.onDestroy();
      //当Activity销毁的时候取消注册BroadcastReceiver
      unregisterReceiver(receiver);
    }

## BroadcastReceiver的生命周期： ##

> BroadcastReceiver的生命周期，从对象调用它开始，到onReceiver方法执行完成之后结束。另外，每次广播被接收后会重新创建BroadcastReceiver对象，并在onReceiver方法中执行完就销毁，
> 
> 生命周期只有十秒左右，如果BroadcastReceiver的onReceiver方法中不能在10秒内执行完成，Android会出现ANR异常。
> 
> 所以不要在BroadcastReceiver的onReceiver方法中执行耗时的操作。
> 
> 如果需要在BroadcastReceiver中执行耗时的操作，可以通过Intent启动Service来完成。但不能绑定Service。
> 
> 特别是，您可能无法从一个BroadcastReceiver中显示一个对话框，或绑定到服务。对于前者，则应该使用NotificationManager的API。对于后者，你可以使用Context.startService()来启动一个Service。
## 广播的类型: ##

> Broadcast的类型有两种：普通广播和有序广播。

**Normal broadcasts（普通广播）**：Normal broadcasts是完全异步的可以同一时间被所有的接收者接收到。消息的传递效率比较高。但缺点是接收者不能讲接收的消息的处理信息传递给下一个接收者也不能停止消息的传播。


**Ordered broadcasts（有序广播）**：Ordered broadcasts的接收者按照一定的优先级进行消息的接收。如：A,B,C的优先级依次降低，那么消息先传递给A，在传递给B，最后传递给C。优先级别声明在中，取值为[-1000,1000]数值越大优先级别越高。优先级也可通过filter.setPriority(10)方式设置。 另外Ordered broadcasts的接收者可以通过abortBroadcast()的方式取消广播的传播，也可以通过setResultData和setResultExtras方法将处理的结果存入到Broadcast中，传递给下一个接收者。然后，下一个接收者通过getResultData()和getResultExtras(true)接收高优先级的接收者存入的数据。

**例子1：Android开机广播和关机广播**

编写一个继承BroadcastReceiver的类，接受系统启动关闭广播。代码如下：
    
    public class LaunchReceiver extends BroadcastReceiver  {  
      
    @Override  
    public void onReceive(Context context, Intent intent) {  
    System.out.println("系统启动完毕");
    }  
    }
    public class ShutdownReceiver extends BroadcastReceiver  {  
      
    @Override  
    public void onReceive(Context context, Intent intent) {  
    System.out.println("系统要关闭了");
    }  
    }
    
    <receiver android:name=".LaunchReceiver">
    <intent-filter>
    <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
    </receiver>
    <receiver android:name=".ShutdownReceiver">
    <intent-filter>
    <action android:name="android.intent.action.ACTION_SHUTDOWN"/>
    </intent-filter>
    </receiver>
同时应添加所需要的权限：
    
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
**例子2：通过终止广播 阻止用户收到短信**

> 系统收到短信，发出的广播属于有序广播。如果想阻止用户收到短信，可自定义Receiver，设置高优先级，率先获得接收短信的广播，并终止广播。接收短信的广播名android.provider.Telephony.SMS_RECEIVED。注意：程序一旦在某个模拟器运行，将一直阻止短信，只有注释掉abortBroadcast()，重新运行，方可正常。

    public class MySmsResevicer extends BroadcastReceiver{  
      
    @Override  
    public void onReceive(Context context, Intent intent) {
    System.out.println("on receive");  
    abortBroadcast();  
    }  
      
    }
    <receiver android:name=".MySmsResevicer">  
    <intent-filter android:priority="1000">  
    <action android:name="android.provider.Telephony.SMS_RECEIVED"/>  
    </intent-filter>  
    </receiver>