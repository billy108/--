## ContentProvider的概念 ##
为存储和获取数据提供统一的接口。可以在不同的应用程序之间共享数据,例如我们需要操作系统里的媒体库、通讯录等，Android已经为常见的一些数据提供了默认的ContentProvider

**适用场景**

> 1) ContentProvider为存储和读取数据提供了统一的接口
> 
> 2) 使用ContentProvider，应用程序可以实现数据共享
> 
> 3) Android内置的许多数据都是使用ContentProvider形式，供开发者调用的(如视频，音频，图片，通讯录等)

**ContentProvider使用表的形式来组织数据**

> 无论数据的来源是什么，ContentProvider都会认为是一种表，然后把数据组织成表格


**ContentProvider提供的方法**
> 
> 　　 query：查询
> 
> 　　 insert：插入
> 
> 　　 update：更新
> 
> 　　 delete：删除
> 
> 　　 getType：得到数据类型
> 
> 　　 onCreate：创建数据时调用的回调函数

## URI ##
> 
> 每个ContentProvider都有一个公共的URI，这个URI用于表示这个ContentProvider所提供的数据。

Android所提供的ContentProvider都存放在android.provider包当中：
![](http://www.2cto.com/uploadfile/Collfiles/20140430/20140430090751356.jpg)

> A：标准前缀，用来说明一个Content Provider控制这些数据，无法改变的；"content://"
> 
> B：URI 的标识，用于唯一标识这个ContentProvider，外部调用者可以根据这个标识来找到它。它定义了是哪个Content Provider提供这些数据。对于第三方应用程序，为了保证URI标识的唯一性，它必须是一个完整的、小写的类名。这个标识在 元素的 authorities属性中说明：一般是定义该ContentProvider的包.类的名称
> 
> C：路径（path），通俗的讲就是你要操作的数据库中表的名字，或者你也可以自己定义，记得在使用的时候保持一致就可以了；"content://com.bing.provider.myprovider/tablename"
> 
> D：如果URI中包含表示需要获取的记录的ID；则就返回该id对应的数据，如果没有ID，就表示返回全部； "content://com.bing.provider.myprovider/tablename/#" #表示数据id。


**路径（path）**

可以用来表示我们要操作的数据，路径的构建应根据业务而定，如下:
> 
> 1、要操作person表中id为10的记录，可以构建这样的路径:/person/10
> 
> 2、要操作person表中id为10的记录的name字段， person/10/name
> 
> 3、要操作person表中的所有记录，可以构建这样的路径:/person
> 
> 4、要操作xxx表中的记录，可以构建这样的路径:/xxx
> 
> 5、当然要操作的数据不一定来自数据库，也可以是文件、xml或网络等其他存储方式，如下:
> 要操作xml文件中person节点下的name节点，可以构建这样的路径：/person/name
> 
> 6、如果要把一个字符串转换成Uri，可以使用Uri类中的parse()方法，如下：Uri uri = Uri.parse("content://com.bing.provider.personprovider/person")

## ContentProvider的内部原理 ##
**自定义一个ContentProvider，来实现内部原理**

步骤：

> 1、定义一个CONTENT_URI常量(里面的字符串必须是唯一)
> 　　`Public static final Uri CONTENT_URI = Uri.parse("content://com.WangWeiDa.MyContentprovider");`
> 
> 　　如果有子表，URI为：
> 　　`Public static final Uri CONTENT_URI = Uri.parse("content://com.WangWeiDa.MyContentProvider/users");`
> 
> 2、定义一个类，继承ContentProvider
> 　　`Public class MyContentProvider extends ContentProvider`
> 
> 3、实现ContentProvider的所有方法(query、insert、update、delete、getType、onCreate)

之后，让我们来使用这个定义好的Content Provider:


> 1）为应用程序添加ContentProvider的访问权限。
> 
> 2）通过getContentResolver()方法得到ContentResolver对象。
> 
> 3）调用ContentResolver类的query()方法查询数据，该方法会返回一个Cursor对象。
> 
> 4）对得到的Cursor对象进行分析，得到需要的数据。
> 
> 5）调用Cursor类的close()方法将Cursor对象关闭。
## UriMatcher类使用介绍 ##
> 
> 因为Uri代表了要操作的数据，所以我们经常需要解析Uri，并从Uri中获取数据。Android系统提供了两个用于操作Uri的工具类，分别为UriMatcher和ContentUris 。

UriMatcher类用于匹配Uri，它的用法如下：

首先第一步把你需要匹配Uri路径全部给注册上，如下：

    //常量UriMatcher.NO_MATCH表示不匹配任何路径的返回码
    UriMatcher  sMatcher = new UriMatcher(UriMatcher.NO_MATCH);
    //如果match()方法匹配content://com.ljq.provider.personprovider/person路径，返回匹配码为1
    sMatcher.addURI("com.ljq.provider.personprovider", "person", 1);//添加需要匹配uri，如果匹配就会返回匹配码
    //如果match()方法匹配content://com.ljq.provider.personprovider/person/230路径，返回匹配码为2
    sMatcher.addURI("com.ljq.provider.personprovider", "person/#", 2);//#号为通配符
    switch (sMatcher.match(Uri.parse("content://com.ljq.provider.personprovider/person/10"))) { 
       case 1
     break;
       case 2
     break;
       default://不匹配
     break;
    }

注册完需要匹配的Uri后，就可以使用sMatcher.match(uri)方法对输入的Uri进行匹配，如果匹配就返回匹配码，匹配码是调用addURI()方法传入的第三个参数，假设匹配content://com.ljq.provider.personprovider/person路径，返回的匹配码为1 

 
## ContentUris类使用介绍 ##

ContentUris类用于操作Uri路径后面的ID部分，它有两个比较实用的方法：
withAppendedId(uri, id)用于为路径加上ID部分：

    Uri uri = Uri.parse("content://com.ljq.provider.personprovider/person")
    Uri resultUri = ContentUris.withAppendedId(uri, 10); 
    //生成后的Uri为：content://com.ljq.provider.personprovider/person/10
    parseId(uri)方法用于从路径中获取ID部分：
    
    Uri uri = Uri.parse("content://com.ljq.provider.personprovider/person/10")
    long personid = ContentUris.parseId(uri);//获取的结果为:10
## 使用ContentResolver操作ContentProvider中的数据 ##

> 当外部应用需要对ContentProvider中的数据进行添加、删除、修改和查询操作时，可以使用ContentResolver 类来完成，
> 
> 要获取ContentResolver 对象，可以使用Activity提供的getContentResolver()方法。 ContentResolver 类提供了与ContentProvider类相同签名的四个方法：

    public Uri insert(Uri uri, ContentValues values)：该方法用于往ContentProvider添加数据。
    public int delete(Uri uri, String selection, String[] selectionArgs)：该方法用于从ContentProvider删除数据。
    public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs)：该方法用于更新ContentProvider中的数据。
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder)：该方法用于从ContentProvider中获取数据。

这些方法的第一个参数为Uri，代表要操作的ContentProvider和对其中的什么数据进行操作，

> 假设给定的是：Uri.parse("content://com.ljq.providers.personprovider/person/10")，那么将会对主机名为com.ljq.providers.personprovider的ContentProvider进行操作，操作的数据为person表中id为10的记录。

**使用ContentResolver对ContentProvider中的数据进行添加、删除、修改和查询操作：**

    ContentResolver resolver =  getContentResolver();
    Uri uri = Uri.parse("content://com.ljq.provider.personprovider/person");
    //添加一条记录
    ContentValues values = new ContentValues();
    values.put("name", "linjiqin");
    values.put("age", 25);
    resolver.insert(uri, values);  
    //获取person表中所有记录
    Cursor cursor = resolver.query(uri, null, null, null, "personid desc");
    while(cursor.moveToNext()){
       Log.i("ContentTest", "personid="+ cursor.getInt(0)+ ",name="+ cursor.getString(1));
    }
    //把id为1的记录的name字段值更改新为zhangsan
    ContentValues updateValues = new ContentValues();
    updateValues.put("name", "zhangsan");
    Uri updateIdUri = ContentUris.withAppendedId(uri, 2);
    resolver.update(updateIdUri, updateValues, null, null);
    //删除id为2的记录
    Uri deleteIdUri = ContentUris.withAppendedId(uri, 2);
    resolver.delete(deleteIdUri, null, null);
                        
## 监听ContentProvider中数据的变化 ##

> 如果ContentProvider的访问者需要知道ContentProvider中的数据发生变化，可以在ContentProvider发生数据变化时调用getContentResolver().notifyChange(uri, null)来通知注册在此URI上的访问者，例子如下：

    public class PersonContentProvider extends ContentProvider {
       public Uri insert(Uri uri, ContentValues values) {
      db.insert("person", "personid", values);
      getContext().getContentResolver().notifyChange(uri, null);
       }
    }
> 如果ContentProvider的访问者需要得到数据变化通知，必须使用ContentObserver对数据（数据采用uri描述）进行监听，当监听到数据变化通知时，系统就会调用ContentObserver的onChange()方法：

    getContentResolver().registerContentObserver(Uri.parse("content://com.ljq.providers.personprovider/person"),
       true, new PersonObserver(new Handler()));
    public class PersonObserver extends ContentObserver{
       public PersonObserver(Handler handler) {
      super(handler);
       }
       public void onChange(boolean selfChange) {
      //此处可以进行相应的业务处理
       }
    }

**那么下面来提几个问题：**

1. 在应用程序A里面怎么跨进程拿到ContentProvider的对象呢？
> 
> i.  第一步，它从ActivityThread里面本地缓存寻找ContentProvider对象，所以找到了，就一切ok..
> 
> ii. 第二步，如果第一步没有找到，那么就去ActivityManagerService远程服务中寻找ContentProvider对象。
> 
> iii.第三步，从远程服务中找到ContentProvider对象之后，就把这个对象缓存在本地，那么下次找的话，直接就可以从本地缓存中查找了。
> 那么，它为什么要有这个机制呢？个人猜测：因为跨进程调用是需要时间和资源消耗的，所以，它才有了本地缓存这么个东东。

2.ContentProvider实例对象是保存在哪里呢？
> 
>  它储存在两个位置：
> 
> 1)ActivityThread的本地map缓存中
> 
> 2)ActivityManagerService的远程服务map缓存中

3.ContentProvider的方法实现要注意线程安全吗？

> ContentProvider的代码，比如查询，更新，删除等等，必须注意线程安全的问题
> 
> 那么单例下，我们怎么注意线程安全问题呢？
> 
> 1. ContentProvider尽量少用成员变量，因为我们用的是单例，所以成员变量是共享的。
 
> 2. 所以真的用到了共享资源，建议用synchronized或者TheadLocal来解决。至于synchronized和TheadLocal的区别，这篇文章就不讨论了，下次有机会再写吧。。
