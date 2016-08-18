## 1. 相关文件夹介绍 ##
在Android项目文件夹里面，主要的资源文件是放在res文件夹里面的。assets文件夹是存放不进行编译加工的原生文件，即该文件夹里面的文件不会像xml，java文件被预编译，可以存放一些图片，html，js, css等文件。在后面会介绍如何读取assets文件夹的资源！

res文件夹里面的多个文件夹的各自介绍(来自网上的Android开发指南中文版内容)：
![](http://i.imgur.com/hyp5qmL.png)
![](http://i.imgur.com/SDQiA0q.png)
 
## 2.自动生成的R class ##
> 在项目文件夹的gen文件夹里面有个R.java，我们平常引用的资源主要引用这个类的变量。
>  
> 注意：R类是自动生成的，并且它不能被手动修改。当资源发生变动时，它会自动修改。
 
## 3. 在代码中使用资源 ##

 下面是一个引用资源的语法：
> 
> R.resource_type.resource_name  或者 android.R.resource_type.resource_name
>  
> 其中resource_type是R的子类，保存资源的一个特定类型。resource_name是在XML文件定义的资源的name属性，或者有其他文件类型为资源定义的文件名（不包含扩展名，这指的是drawable文件夹里面的icon.png类似的文件，name=icon）。 Android包含了很多标准资源，如屏幕样式和按钮背景。要在代码中引用这些资源，你必须使用android进行限定，
> 
> 如android.R.drawable.button_background。
 
下面是官方给出的一些在代码中使用已编译资源的正确和错误用法的例子：

    // Load a background for the current screen from a drawable resource. 
    this.getWindow().setBackgroundDrawableResource(R.drawable.my_background_image); 
     
    // WRONG Sending a string resource reference into a  
    // method that expects a string. 
    this.getWindow().setTitle(R.string.main_title); 
     
    // RIGHT Need to get the title from the Resources wrapper. 
    this.getWindow().setTitle(Resources.getText(R.string.main_title)); 
     
    // Load a custom layout for the current screen. 
    setContentView(R.layout.main_screen); 
     
    // Set a slide in animation for a ViewFlipper object. 
    mFlipper.setInAnimation(AnimationUtils.loadAnimation(this,  
    R.anim.hyperspace_in)); 
     
    // Set the text on a TextView object. 
    TextView msgTextView = (TextView)findViewByID(R.id.msg); 
    msgTextView.setText(R.string.hello_message);  

**查了SDK Doc，才明白为什么window.setTitle要先Resources.getText，原来setTitle的参数是CharSequence，Resources.getText(int)返回的是CharSequence；而其他setText的参数有的是CharSequence，有的是int（这就是Resources变量值）。**
 
同时官方还给了两个使用系统资源的例子：
    
    //在屏幕上显示标准应用程序的图标
    public class MyActivity extends Activity { 
    public void onStart() { 
        requestScreenFeatures(FEATURE_BADGE_IMAGE); 
        super.onStart(); 
        setBadgeResource(android.R.drawable.sym_def_app_icon); 
    } 
    } 
     
    //应用系统定义的标准"绿色背景"视觉处理 
    public class MyActivity extends Activity 
    public void onStart() { 
        super.onStart(); 
        setTheme(android.R.style.Theme_Black); 
    } 
    } 
 
## 4. xml文件内引用资源 ##
**1) 引用自定义的资源android:text="@string/hello"**
> 
> 这里使用"@"前缀引入对一个资源的引用--在@[package:]type/name形式中后面的文本是资源的名称。在这种情况下，我们不需要指定包名，因为我们引用的是我们自己包中的资源。type是xml子节点名，name是xml属性名：

    <?xml version="1.0" encoding="utf-8"?> 
    <resources> 
    <string name="hello">Hello World, HelloDemo!</string> 
    </resources> 
 
**2) 引用系统资源**

       android:textColor="@android:color/opaque_red"   指定package: android
 
**3) 引用主题属性**
> 
> 另外一种资源值允许你引用当前主题中的属性的值。这个属性值只能在样式资源和XML属性中使用；它允许你通过将它们改变为当前主题提供的标准变化来改变UI元素的外观，而不是提供具体的值。
> 
        android:textColor="?android:textDisabledColor"
    
注意，这和资源引用非常类似，除了我们使用一个"?"前缀代替了"@"。当你使用这个标记时，你就提供了属性资源的名称，它将会在主题中被查找--因为资源工具知道需要的属性资源，所以你不需要显示声明这个类型(如果声明，其形式就是?android:attr/android:textDisabledColor)。

除了使用这个资源的标识符来查询主题中的值代替原始的资源，其命名语法和"@"形式一致：?[namespace:]type/name，这里类型可选。
 
## 5. 替换资源（为了可替换的资源和配置） ##

> 个人理解这个替换资源主要用于适应多种规格的屏幕，以及国际化。对于这部分的内容，请参考http://androidappdocs.appspot.com/guide/topics/resources/resources-i18n.html，以后再研究！ 
 
## 6. Color Value ##
语法：

    <color name="color_name">#color_value</color> 
    可以保存在res/values/colors.xml (文件名可以任意)。
    xml引用：android:textColor="@color/color_name"
    Java引用： int color = Resources.getColor(R.color.color_name)
 
其中#color_value有以下格式（A代表Alpha通道）：
> RGB
> 
> ARGB
> 
> RRGGBB
> 
> AARRGGBB
 
xml示例(声明两个颜色，第一个不透明，第二个透明色)：
    
    <?xml version="1.0" encoding="utf-8"?>     
    <resources> 
    <color name="opaque_red">#f00</color> 
    <color name="translucent_red">#80ff0000</color>     
    </resources> 
 
## 7.Color Drawables ##
语法：

    <drawable name="color_name">color_value</drawable
    可以保存在res/values/colors.xml。
    xml引用：android:background="@drawable/color_name"
    java引用：Drawable redDrawable = Resources.getDrawable(R.drawable.color_name)
 
> color_name和上面的一样。个人认为，一般情况下使用color属性，当需要用到paintDrawable时才使用drawable属性。
 
xml示例：

    <?xml version="1.0" encoding="utf-8"?> 
    <resources> 
    <drawable name="opaque_red">#f00</drawable> 
    <drawable name="translucent_red">#80ff0000</drawable> 
    </resources> 
 
## 8. 图片 ##
> 一般放在res/drawable/里面。官方提示png (preferred), jpg (acceptable), gif (discouraged)，看来一般使用png格式比较好！
> 
> xml引用  @[package:]drawable/some_file
> java引用 R.drawable.some_file 引用是不带扩展名
 
## 9. dimension ##
语法：

    <dimen name="dimen_name">dimen_value单位</dimen> 
    一般保存为res/values/dimen.xml。

度量单位：
> px(象素): 屏幕实际的象素，常说的分辨率1024*768pixels，就是横向1024px, 纵向768px，不同设备显示效果相同。
 
> in(英寸): 屏幕的物理尺寸, 每英寸等于2.54厘米。
>  
> mm(毫米): 屏幕的物理尺寸。
>  
> pt(点)  : 屏幕的物理尺寸。1/72英寸。
>  
> dp/dip  : 与密度无关的象素，一种基于屏幕密度的抽象单位。在每英寸160点的显示器上，1dp = 1px。但dp和px的比例会随着屏幕密度的变化而改变，不同设备有不同的显示效果。设置宽度或者高度等属性时，推荐使用dp(dip)作为单位
>  
> sp      : 与刻度无关的象素，主要用于字体显示best for textsize，作为和文字相关大小单位。
>  
> XML: android:textSize="@dimen/some_name"
> Java: float dimen = Resources.getDimen(R.dimen.some_name)
 
xml示例：    

    <?xml version="1.0" encoding="utf-8"?> 
    <resources> 
    <dimen name="one_pixel">1px</dimen> 
    <dimen name="double_density">2dp</dimen> 
    <dimen name="sixteen_sp">16sp</dimen> 
    </resources> 
 
## 10. string ##
下面是官方给出的正确/错误的例子:

    //不使用转义符则需要用双引号包住整个string 
    <string name="good_example">"This'll work"</string> 
     
    //使用转义符 
    <string name="good_example_2">This\'ll also work</string> 
     
    //错误 
    <string name="bad_example">This won't work!</string> 
     
    //错误 不可使用html转义字符 
    <string name="bad_example_2">XML encodings won&apos;t work either!</string> 

对于带格式的string，例如在字符串中某些文字设置颜色，可以使用html标签。对于这类型的string，需要进行某些处理，在xml里面不可以被其他资源引用。官方给了一个例子来对比普通string和带格式

string的使用：    

    <?xml version="1.0" encoding="utf-8"?> 
    <resources> 
    <string name="simple_welcome_message">Welcome!</string> 
    <string name="styled_welcome_message">We are <b><i>so</i></b> glad to see you.</string>     
    </resources> 

Xml代码
    
    <TextView 
    android:layout_width="fill_parent" 
    android:layout_height="wrap_content" 
    android:textAlign="center" 
    android:text="@string/simple_welcome_message"/> 

Java代码

    // Assign a styled string resource to a TextView on the current screen. 
    CharSequence str = getString(R.string.styled_welcome_message); 
    TextView tv = (TextView)findViewByID(R.id.text); 
    tv.setText(str); 

另外对于带风格/格式的string的处理，就麻烦一点点。官方给了一个例子：

    <?xml version="1.0" encoding="utf-8"?> 
    <resources> 
      <string name="search_results_resultsTextFormat">%1$d results for &lt;b>&amp;quot;%2$s&amp;quot;&lt;/b></string> 
    </resources> 

这里的%1$d是个十进制数字，%2$s是字符串。当我们把某个字符串赋值给%2$s之前，需要用htmlEncode(String)函数处理那个字符串：

//title是我们想赋值给%2$s的字符串 

String escapedTitle = TextUtil.htmlEncode(title);
 
 然后用String.format() 来实现赋值，接着用fromHtml(String) 得到格式化后的string：

    String resultsTextFormat = getContext().getResources().getString(R.string.search_results_resultsTextFormat); 
    String resultsText = String.format(resultsTextFormat, count, escapedTitle); 
    CharSequence styledResults = Html.fromHtml(resultsText); 
 
## 11. assets文件夹资源的访问 ##
assets文件夹里面的文件都是保持原始的文件格式，需要用AssetManager以字节流的形式读取文件。

>  1. 先在Activity里面调用getAssets()来获取AssetManager引用。
>  2. 再用AssetManager的open(String fileName, int accessMode)方法则指定读取的文件以及访问模式就能得到输入流InputStream。 
>  3. 然后就是用已经open file 的inputStream读取文件，读取完成后记得inputStream.close()。
>  4.调用AssetManager.close()关闭AssetManager。