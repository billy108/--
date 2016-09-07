	/**
     * 检查网络
     */
    private void checkNetwork() {
        ConnectivityManager manager = (ConnectivityManager) getSystemService(CONNECTIVITY_SERVICE);
        NetworkInfo info = manager.getActiveNetworkInfo();
        if (info != null && info.isAvailable()) {
            isConnected = true;
        } else {
            Toast.makeText(this, "网络异常,请检查", Toast.LENGTH_SHORT).show();
        }
    }

## 用FragmentManager可以做的工作有： ##

**1.得到Activity中存在的fragment**：

使用findFragmentById()或findFragmentByTag()方法。

**2.将fragment弹出back stack：**

popBackStack()：将back stack中最后一次的fragment转换弹出。如果没有可以出栈的东西，返回false。

这个函数是异步的：它将弹出栈的请求加入队列，但是这个动作直到应用回到事件循环才会执行。

**3.为back stack加上监听器：**

addOnBackStackChangedListener()
## 继承Application的应用场景 ##
> 实现应用程序级的全局变量，这种全局变量方法相对静态类更有保障，直到应用的所有Activity全部被destory掉之后才会被释放掉。

**实现步骤：**
**
1、继承Application**

    public class CustomApplication extends Application
    {
    private static final String VALUE = "Harvey";
    
    private String value;
    
    @Override
    public void onCreate()
    {
        super.onCreate();
        setValue(VALUE); // 初始化全局变量
    }
    
    public void setValue(String value)
    {
        this.value = value;
    }
    
    public String getValue()
    {
        return value;
    }
	}

注：继承Application类，主要重写里面的onCreate（）方法（android.app.Application包的onCreate（）才是真正的Android程序的入口点），就是创建的时候，初始化变量的值。然后在整个应用中的各个文件中就可以对该变量进行操作了。

**2、在ApplicationManifest.xml文件中配置自定义的Application**

    <application
    	android:name="CustomApplication">
    </application>
实例代码：

CustomApplication.java


    /**
     * 继承Application
     * 
     * @author admin
     * 
     */
    public class CustomApplication extends Application
    {
    private static final String VALUE = "Harvey";
    
    private String value;
    
    @Override
    public void onCreate()
    {
    super.onCreate();
    setValue(VALUE); // 初始化全局变量
    }
    
    public void setValue(String value)
    {
    this.value = value;
    }
    
    public String getValue()
    {
    return value;
    }
    }

FirstActivity.java

    public class FirstActivity extends Activity
    {
    private CustomApplication app;
    
    @Override
    public void onCreate(Bundle savedInstanceState)
    {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
    
    app = (CustomApplication) getApplication(); // 获得CustomApplication对象
    
    Log.i("FirstActivity", "初始值=====" + app.getValue()); // 获取进程中的全局变量值，看是否是初始化值
    
    app.setValue("Harvey Ren"); // 重新设置值
    
    Log.i("FirstActivity", "修改后=====" + app.getValue()); // 再次获取进程中的全局变量值，看是否被修改
    
    Intent intent = new Intent();
    intent.setClass(this, SecondActivity.class);
    startActivity(intent);
    }
    }

注：只需要调用Context的 getApplicationContext或者Activity的getApplication方法来获得一个Application对象，然后再得到相应的成员变量即可。它是代表我们的应用程序的类，使用它可以获得当前应用的主题和资源文件中的内容等，这个类更灵活的一个特性就是可以被我们继承，来添加我们自己的全局属性。

SecondActivity.java

    public class SecondActivity extends Activity
    {
    private CustomApplication app;
    
    @Override
    protected void onCreate(Bundle savedInstanceState)
    {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        app = (CustomApplication) getApplication(); // 获取应用程序

       Log.i("SecondActivity", "当前值=====" + app.getValue()); // 获取全局值
    }
    }

AndroidManifest.xml

	<?xml version="1.0" encoding="utf-8"?>
	<manifest
    xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.android.test"
    android:versionCode="1"
    android:versionName="1.0">
    <uses-sdk
        android:minSdkVersion="8" />

    <application
        android:icon="@drawable/icon"
        android:label="@string/app_name"
        android:name="CustomApplication">
        <!-- 将我们以前一直用的默认Application设置成自定义的CustomApplication -->
        <activity
            android:name=".FirstActivity"
            android:label="@string/app_name">
            <intent-filter>
                <action
                    android:name="android.intent.action.MAIN" />
                <category
                    android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".SecondActivity"
            android:label="@string/app_name">
        </activity>
    </application>
	</manifest>
结束于 8/15/2016 6:12:04 PM 

----------

Fragment.setArguments(Bundle bundle)这种方式来传递参数

**ExpandableListView组件**

> ExpandableListView组件是android中一个比较常用的组件，当点击一个父item的时候可以将它的子item显示出来，像手机QQ中的好友列表就是实现的类型效果。
> 
> 使用ExpandableListView组件的关键就是设置它的adapter，这个adapter必须继承BaseExpandbaleListAdapter类，所以实现运用ExpandableListView的核心就是学会继承这个BaseExpanableListAdapter类。

## 再按一次退出程序 ##
	 /**
     * 再按一次退出程序
     */

    private long exitTime = 0;
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if(keyCode == KeyEvent.KEYCODE_BACK && event.getAction() == KeyEvent.ACTION_DOWN){
            if((System.currentTimeMillis()-exitTime) > 2000){
                Toast.makeText(getApplicationContext(), "再按一次退出程序", Toast.LENGTH_SHORT).show();
                exitTime = System.currentTimeMillis();
            } else {
                finish();
                System.exit(0);
            }
            return true;
        }
        return super.onKeyDown(keyCode, event);
    }

## [Google自己的下拉刷新组件SwipeRefreshLayout](http://www.cnblogs.com/JinyaoLi/p/3852424.html) ##


**9/7/2016**

    expandableListView.setGroupIndicator(null);//去掉控件的箭头
	ViewPager.setOffscreenPageLimit(0);//0表示不缓存

ViewPager可以设置setOnPageChangeListener监听，重写onPageScrolled（）方法，就可以控制Tab条的位移

	@Override
    public void onPageScrolled(int i, float v, int i1) {
       scllorTabView_title_underline.setOffset(i, v);
    }

ViewPager的自动切换

	private Handler mHandler = new Handler() {
        public void handleMessage(android.os.Message msg) {
            switch (msg.what) {
                case 1:
                    if (dots != null) {
                        int currentItem = viewPager.getCurrentItem();
                        int toItem = (currentItem + 1) % viewPagerList.size();
                        viewPager.setCurrentItem(toItem, true);
                    }
                    // 每2秒钟发送一个message，用于切换viewPager中的图片
                    this.sendEmptyMessageDelayed(1, 2000);
            }
        }
    };

类似git加载动画（比如刚打开主页时）可以用多个png连接而成。
[Android 用Animation-list实现逐帧动画](http://blog.csdn.net/aminfo/article/details/7847761)