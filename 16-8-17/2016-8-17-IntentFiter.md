## 什么是Intent ##

**1、Intent的概念：**

Intent不仅可用于应用程序之间，也可用于应用程序内部的activity, service和broadcast receiver之间的交互。

> activity、service和broadcast receiver之间是通过Intent进行通信的，而另外一个组件Content Provider本身就是一种通信机制，不需要通过Intent。

**2、对于向这三种组件发送intent有不同的机制：**

![](http://i.imgur.com/nZopR8V.png)

## Intent的相关属性： ##

**Intent由以下各个组成部分：**

> component(组件)：目的组件
> 
> action（动作）：用来表现意图的行动
> 
> category（类别）：用来表现动作的类别
> 
> data（数据）：表示与动作要操纵的数据
> 
> type（数据类型）：对于data范例的描写
> 
> extras（扩展信息）：扩展信息
> 
> Flags（标志位）：期望这个意图的运行模式

**Intent类型**

> Intent类型分为显式Intent（直接类型）、隐式Intent（间接类型）。官方建议使用隐式Intent。上述属性中，component属性为直接类型，其他均为间接类型。

> 相比与显式Intent，隐式Intnet则含蓄了许多，它并不明确指出我们想要启动哪一个活动，而是指定一系列更为抽象的action和category等信息，然后交由系统去分析这个Intent，并帮我们找出合适的活动去启动。

**Activity 中 Intent Filter 的匹配过程 ：**

![](http://images.cnitblog.com/blog/641601/201409/061058457504768.png)

**1、component(组件)：目的组件**

> Component属性明确指定Intent的目标组件的类名称。（属于直接Intent）
> 
> 如果 component这个属性有指定的话，将直接使用它指定的组件。指定了这个属性以后，Intent的其它所有属性都是可选的。

例如，启动第二个Activity时，我们可以这样来写：

     1 button1.setOnClickListener(new OnClickListener() {
     2 @Override
     3 public void onClick(View v) {
     4 //创建一个意图对象
     5 Intent intent = new Intent();
     6 //创建组件，通过组件来响应
     7 ComponentName component = new ComponentName(MainActivity.this, SecondActivity.class);
     8 intent.setComponent(component);
     9 startActivity(intent);
    10 }
    11 });

如果写的简单一点，监听事件onClick()方法里可以这样写：
    
    1 Intent intent = new Intent();
    2 //setClass函数的第一个参数是一个Context对象
    3 //Context是一个类，Activity是Context类的子类，也就是说，所有的Activity对象，都可以向上转型为Context对象
    4 //setClass函数的第二个参数是一个Class对象，在当前场景下，应该传入需要被启动的Activity类的class对象
    5 intent.setClass(MainActivity.this, SecondActivity.class);
    6 startActivity(intent);
    复制代码
    再简单一点，可以这样写：（当然，也是最常见的写法）
    
    1 Intent intent = new Intent(MainActivity.this,SecondActivity.class);
    2 startActivity(intent); 

**2、Action（动作）：用来表现意图的行动**

> 当日常生活中，描述一个意愿或愿望的时候，总是有一个动词在其中。比如：我想“做”三个俯卧撑；我要“写” 一封情书，等等。在Intent中，Action就是描述做、写等动作的，当你指明了一个Action，执行者就会依照这个动作的指示，接受相关输入，表现对应行为，产生符合的输出。在Intent类中，定义了一批量的动作，比如ACTION_VIEW，ACTION_PICK等， 基本涵盖了常用动作。加的动作越多，越精确。

> Action 是一个用户定义的字符串，用于描述一个 Android 应用程序组件，一个 Intent Filter 可以包含多个 Action。在 AndroidManifest.xml 的Activity 定义时，可以在其 <intent-filter >节点指定一个 Action列表用于标识 Activity 所能接受的“动作”。

**3、category（类别）：用来表现动作的类别**

> Category属性也是作为<intent-filter>子元素来声明的。例如：

    <intent-filter>
    
    　　<action android:name="com.vince.intent.MY_ACTION"></action>
    
    　　<category android:name="com.vince.intent.MY_CATEGORY"></category> 
    
    　　<category android:name="android.intent.category.DEFAULT"></category> 
    
    </intent-filter>   

Action 和category通常是放在一起用的，所以这里一起介绍一下。我们来先来举一个例子：

新建一个工程文件smyh006_Intent01，在默认文件的基础之上，新建文件SecondActicity.java和activity_second.xml。

紧接着，我们要到清单文件中进行注册，打开AndroidManifest.xml，添加SecondActivity的action和category的过滤器：

    1 <activity 
    2 android:name=".SecondActivity">
    3 <intent-filter>
    4  <action android:name="com.example.smyh006intent01.MY_ACTION"/>
    5  <category android:name="android.intent.category.DEFAULT" />
    6 </intent-filter>
    7 </activity>

> 上方代码，表示SecondActicity可以匹配第4行的MY_ACTION这个动作，此时，如果在其他的Acticity通过这个action的条件来查找，那SecondActicity就具备了这个条件。类似于相亲时，我要求对方有哪些条件，然后对方这个SecondActicity恰巧满足了这个条件（够通俗了吧）。
> 
> 注：如果没有指定的category，则必须使用默认的DEFAULT（即上方第5行代码）。
> 
> 也就是说：只有<action>和<category>中的内容同时能够匹配上Intent中指定的action和category时，这个活动才能响应Intent。如果使用的是DEFAULT这种默认的category，在稍后调用startActivity()方法的时候会自动将这个category添加到Intent中。

现在来修改MainActivity.java中按钮的点击事件，代码如下：

     1 button1.setOnClickListener(new OnClickListener() {
     2 @Override
     3 public void onClick(View v) {
     4 //启动另一个Activity，（通过action属性进行查找）
     5 Intent intent = new Intent();
     6 //设置动作（实际action属性就是一个字符串标记而已）
     7 intent.setAction("com.example.smyh006intent01.MY_ACTION"); //方法：Intent android.content.Intent.setAction(String action)
     8 startActivity(intent);
     9 }
    10 });

上方代码中，也可以换成下面这种简洁的方式：

    1 button1.setOnClickListener(new OnClickListener() {
    2 @Override
    3 public void onClick(View v) {
    4 //启动另一个Activity，（通过action属性进行查找）
    5 Intent intent = new Intent("com.example.smyh006intent01.MY_ACTION");//方法： android.content.Intent.Intent(String action)
    6 startActivity(intent);
    7 }
    8 });

> 上方第5行代码：在这个Intent中，我并没有指定具体哪一个Activity，我只是指定了一个action的常量。所以说，隐式Intent的作用就表现的淋漓尽致了。此时，点击MainActicity中的按钮，就会跳到SecondActicity中去。
> 
> 上述情况只有SecondActicity匹配成功。如果有多个组件匹配成功，就会以对话框列表的方式让用户进行选择。我们来详细介绍一下：

我们新建文件ThirdActicity.java和activity_third.xml，然后在清单文件AndroidManifest.xml中添加ThirdActivity的action和category的过滤器：

    1 <activity 
    2 android:name=".ThirdActivity">
    3 <intent-filter>
    4  <action android:name="com.example.smyh006intent01.MY_ACTION"/>
    5  <category android:name="android.intent.category.DEFAULT" />
    6 </intent-filter>
    7 </activity> 

**总结：**

在自定义动作时，使用activity组件时，必须添加一个默认的类别

具体的实现为：

    <intent-filter>
    
       <action android:name="com.example.action.MY_ACTION"/>
    
       <category android:name="android.intent.category.DEFAULT"/>
    
    </intent-filter>

如果有多个组件被匹配成功，就会以对话框列表的方式让用户进行选择。

每个Intent中只能指定一个action，但却能指定多个category；类别越多，动作越具体，意图越明确（类似于相亲时，给对方提了很多要求）。

> 目前我们的Intent中只有一个默认的category，现在可以通过intent.addCategory()方法来实现。修改MainActivity中按钮的点击事件，代码如下：

     1 button1.setOnClickListener(new OnClickListener() {
     2 @Override
     3 public void onClick(View v) {
     4 //启动另一个Activity，（通过action属性进行查找）
     5 Intent intent = new Intent();
     6 //设置动作（实际action属性就是一个字符串标记而已）
     7 intent.setAction("com.example.smyh006intent01.MY_ACTION"); //方法：Intent android.content.Intent.setAction(String action)
     8 intent.addCategory("com.example.smyh006intent01.MY_CATEGORY");
     9 startActivity(intent);
    10 }
    11 });

> 既然在Intent中增加了一个category，那么我们要在清单文件中去声明这个category，不然程序将无法运行。代码如下：

    1 android:name=".SecondActivity">
    2 <intent-filter>
    3  <action android:name="com.example.smyh006intent01.MY_ACTION"/>
    4  <category android:name="android.intent.category.DEFAULT" />
    5  <category android:name="com.example.smyh006intent01.MY_CATEGORY" />
    6 </intent-filter>
    7 </activity>

此时，点击MainActicity中的按钮，就会跳到SecondActicity中去。

**总结如下：**

> 自定义类别： 在Intent添加类别可以添加多个类别，那就要求被匹配的组件必须同时满足这多个类别，才能匹配成功。操作Activity的时候，如果没有类别，须加上默认类别

**4、data（数据）：表示与动作要操纵的数据**

> Data属性是Android要访问的数据，和action和Category声明方式相同，也是在<intent-filter>中。
> 多个组件匹配成功显示优先级高的； 相同显示列表。
> Data是用一个uri对象来表示的，uri代表数据的地址，属于一种标识符。通常情况下，我们使用action+data属性的组合来描述一个意图：做什么
> 
> 使用隐式Intent，我们不仅可以启动自己程序内的活动，还可以启动其他程序的活动，这使得Android多个应用程序之间的功能共享成为了可能。比如应用程序中需要展示一个网页，没有必要自己去实现一个浏览器（事实上也不太可能），而是只需要条用系统的浏览器来打开这个网页就行了。

【实例】打开指定网页：

MainActivity.java中，监听器部分的核心代码如下：

     1 button1.setOnClickListener(new OnClickListener() {
     2 @Override
     3 public void onClick(View v) {
     4 Intent intent = new Intent();
     5 intent.setAction(Intent.ACTION_VIEW);
     6 Uri data = Uri.parse("http://www.baidu.com");
     7 intent.setData(data);
     8 startActivity(intent);
     9 }
    10 });

当然，上方代码也可以简写成：

    1 button1.setOnClickListener(new OnClickListener() {
    2 @Override
    3 public void onClick(View v) {
    4 Intent intent = new Intent(Intent.ACTION_VIEW);
    5 intent.setData(Uri.parse("http://www.baidu.com"));
    6 startActivity(intent);
    7 }
    8 });

> 第4行代码：指定了Intent的action是 Intent.ACTION_VIEW，表示查看的意思，这是一个Android系统内置的动作；
> 
> 第5行代码：通过Uri.parse()方法，将一个网址字符串解析成一个Uri对象，再调用intent的setData()方法将这个Uri对象传递进去。

此时， 调用的是系统默认的浏览器，也就是说，只调用了这一个组件。现在如果有多个组件得到了匹配，应该是什么情况呢？

我们修改修改清单文件中对SecondAcivity的声明：

    1 <activity 
    2 android:name=".SecondActivity">
    3 <intent-filter>
    4  <action android:name="android.intent.action.VIEW" />
    5  <category android:name="android.intent.category.DEFAULT" />
    6  <data android:scheme="http" android:host="www.baidu.com"/> 
    7 </intent-filter>
    8 </activity>

**我们可以总结如下：**

> 当Intent匹配成功的组件有多个时，显示优先级高的组件，如果优先级相同，显示列表让用户自己选择
> 优先级从-1000至1000，并且其中一个必须为负的才有效
> 注：系统默认的浏览器并没有做出优先级声明，其优先级默认为正数。

优先级的配置如下：

在清单文件中修改对SecondAcivity的声明，即增加一行代码，通过来android:priority设置优先级，如下：

    1 <activity 
    2 android:name=".SecondActivity">
    3 <intent-filter android:priority="-1">
    4  <action android:name="android.intent.action.VIEW" />
    5  <category android:name="android.intent.category.DEFAULT" />
    6  <data android:scheme="http" android:host="www.baidu.com"/>  
    7 </intent-filter>
    8 </activity>

> 注：
> 
> Data属性的声明中要指定访问数据的Uri和MIME类型。可以在<data>元素中通过一些属性来设置：
> 
> android:scheme、android:path、android:port、android:mimeType、android:host等，通过这些属性来对应一个典型的Uri格式scheme://host:port/path。例如：http://www.google.com。

 

**5、type（数据类型）：对于data范例的描写**

> 如果Intent对象中既包含Uri又包含Type，那么，在<intent-filter>中也必须二者都包含才能通过测试。
> 
> Type属性用于明确指定Data属性的数据类型或MIME类型，但是通常来说，当Intent不指定Data属性时，Type属性才会起作用，否则Android系统将会根据Data属性值来分析数据的类型，所以无需指定Type属性。
> 
> data和type属性一般只需要一个，通过setData方法会把type属性设置为null，相反设置setType方法会把data设置为null，如果想要两个属性同时设置，要使用Intent.setDataAndType()方法。

【任务】：data+type属性的使用 【实例】：播放指定路径的mp3文件。

具体如下：

新建工程文件smyh006_Intent02，MainActivity.java中按钮监听事件部分的代码如下：

     1 button.setOnClickListener(new OnClickListener(){
     2 @Override
     3 public void onClick(View v) {
     4 Intent intent = new Intent();
     5 intent.setAction(Intent.ACTION_VIEW);
     6 Uri data = Uri.parse("file:///storage/sdcard0/平凡之路.mp3");
     7 //设置data+type属性
     8 intent.setDataAndType(data, "audio/mp3"); //方法：Intent android.content.Intent.setDataAndType(Uri data, String type)
     9 startActivity(intent);
    10 }
    11 });

代码解释：
> 
> 第6行："file://"表示查找文件，后面再加上我的小米手机存储卡的路径：/storage/sdcard0，再加上具体歌曲的路径。
> 
> 第8行：设置data+type属性　　

**6、extras（扩展信息）：扩展信息**

> 是其它所有附加信息的集合。使用extras可以为组件提供扩展信息，比如，如果要执行“发送电子邮件”这个
> 
> 动作，可以将电子邮件的标题、正文等保存在extras里，传给电子邮件发送组件。

**7、Flags（标志位）：期望这个意图的运行模式**

> 一个程序启动后系统会为这个程序分配一个task供其使用，另外同一个task里面可以拥有不同应用程序的activity。那么，同一个程序能不能拥有多个task？这就涉及到加载activity的启动模式，这个需要单独讲一下。
> 
> 注：android中一组逻辑上在一起的activity被叫做task，自己认为可以理解成一个activity堆栈。
 
# Intent-Filter #
> IntentFilter就是用于描述intent的各种属性， 比如action, category等

I**ntentFilter过滤Intent时，一般是通过Action、Data及Category三方面进行监测的。**。

> （1）检查Action
> 一个Intent只能设置一种Action，但是一个IntentFilter却可以设置多个Action过滤。当IntentFilter设置了多个Action时，只需一个满足即可完成Action验证。当IntentFilter中没有说明任何一个Action时，那么任何的Action都不会与之匹配。而如果Intent中没有包含任何Action，那么只要IntentFilter中含有Action时，便会匹配成功。

> （2）检查Data
> 数据的监测主要包含两部分，即数据的URI及数据类型，而数据URI又被分成三部分进行匹配（scheme、authority、path），只有这些全部匹配时，Data的验证才会成功。

> （3）检查Category
> IntentFilter同样可以设置多个Category，当Intent中的Category与IntentFilter中的一个Category完全匹配时，便会通过Category的检查，而其他的Category并不受影响。但是当IntentFilter没有设置Category时，只能与没有设置Category的Intent相匹配。

一些属性设置的例子：

    <action android:name="com.example.project.SHOW_CURRENT" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:mimeType="video/mpeg" android:scheme="http" . . . />
    <data android:mimeType="image/*" />
    <data android:scheme="http" android:type="video/*" />

**关于IntentFilter的几点注意事项：**

**(1).android.intent.action.MAIN 与 android.intent.category.LAUNCHER**
android.intent.action.MAIN决定一个应用程序最先启动那个组件
android.intent.category.LAUNCHER决定应用程序是否显示在程序列表里(说白了就是是否在桌面上显示一个图标)

这两个属性组合情况：
> 
> 第一种情况：有MAIN,无LAUNCHER，程序列表中无图标
> 原因：android.intent.category.LAUNCHER决定应用程序是否显示在程序列表里 
> 
> 第二种情况：无MAIN,有LAUNCHER，程序列表中无图标
> 原因：android.intent.action.MAIN决定应用程序最先启动的Activity，如果没有Main，则不知启动哪个Activity，故也不会有图标出现
> 
> 所以这两个属性一般成对出现。
> 如果一个应用中有两个组件intent-filter都添加了android.intent.action.MAIN和
> android.intent.category.LAUNCHER这两个属性， 则这个应用将会显示两个图标， 写在前面的组件先运行。

**(2).关于隐式intent**

  
> 每一个通过 startActivity() 方法发出的隐式 Intent 都至少有一个 category，就是 "android.intent.category.DEFAULT"，所以只要是想接收一个隐式 Intent 的 Activity 都应该包括 "android.intent.category.DEFAULT" category，不然将导致 Intent 匹配失败.

比如说一个activity组件要想被其他组件通过隐式intent调用， 则其在manifest.xml中的声明如下：

    <activity android:name="com.gesture.QGestureListActivity">
     <intent-filter 
       <action android:name="com.google.test" />
       <category android:name="android.intent.category.DEFAULT" />
     </intent-filter
    </activity>

**(3).关于intent-filter匹配优先级**

首先查看Intent的过滤器(intent-filter),按照以下优先关系查找：action->data->category

**(4).android.intent.category.LAUNCHER与android.intent.category.HOME的区别**

android.intent.category.LAUNCHER：android.intent.category.LAUNCHER决定应用程序是否显示在程序列表里，就是android开机后的主程序列表。
android.intent.category.HOME：按住“HOME”键，该程序显示在HOME列表里。