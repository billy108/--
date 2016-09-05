# Android Camera #
## (一) 前言 ##
最近看Camera的api，觉得写的真的不错。现在翻译过来，给大家分享分享，译文可能不太好，大家将就着看哈。

## (二) 正文 ##
**1. Camera**

Camera是Android framework里面支持的，允许你拍照和拍摄视频的设备，那么，本文在接下来就会讨论如何为你的用户提供快速，简单的图片和视频拍摄方法。

**2. 注意事项**

> a. 在确定你的程序使用Camera之前，你应该问自己几个问题：
> 
> i. Camera Requirement -- 使用照相机是不是对你的程序真的如此重要？如果确定如此，那么请在AndroidMainfest.xml文件中申明要使用照相机。
> 
> ii. Quick Picture or Customized Camera -- 快速拍照还是自定义照相机。你只是简单对拍照或者拍摄视频感兴趣还是你的程序要定义一套全新的照相机使用方法？如果只是简单的拍照或者录制视频，我们可以用一种非常简单的方法，如果想自定义照相机，那么请阅读自定义照相机片段。
> 
> iii. Storage -- 你的照片或者视频只想让你自己的程序看见，还是想和应用程序共享？比如Gallery,或者其他的多媒体和社交应用。 你是想让你的照片或者视频,即使你的应用已经被用户卸载，还仍然保持可见吗？那么请参考Saving Media Files章节。

ok.. 那么接下来，我们一个个讲解。

**3. 在AndroidMainfest.xml中声明使用照相机.**

a. Camera权限， 你必须在AndroidMainfest.xml中声明使用照相机的权限，例如

    <uses-permission android:name="android.permission.CAMERA" />

注：如果你是调用系统Camera程序的话，就不必声明

b. Camera特性，你必须在AndroidMainfest.xml中声明照相机特性，例如：

    <uses-feature android:name="android.hardware.camera" />

如果你的程序可能需要使用照相机，但并不是一定的，那么可以设置android:required属性，比如：

    <uses-feature android:name="android.hardware.camera" android:required="false" />

c. 存储权限，如果你的程序想在扩展存储设备上(如sd卡)存储你的照片或者拍摄的视频，那么必须声明如下权限：

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

d. 音频录制权限， 为了录制音频或者视频，你必须在AndroidMainfest.xml文件中设置如下权限：

    <uses-permission android:name="android.permission.RECORD_AUDIO" />

**4. 使用系统Camera程序**

a. 这是一种可以让你的程序以最少的编码，然后可以快速使用照相机的方法，它通过一个Intent来调用系统的Camera程序。一个Camera的intent可以发起一个请求，来让系统的Camera应用程序去拍摄照片或者录制视频，而且可以返回拍摄或者录制的结果给你自己的应用程序，

那么接下来，我们将讨论如何使用这种方法。

> i. 构造一个Camera Intent -- 创建一个拍摄照片或者视频的Intent, 我们可以使用如下两种方法：
> 
>    MediaStore.ACTION_IMAGE_CAPTURE -- 一个intent action,用来向系统Camera程序请求拍摄图片。
>    
>    MediaStore.ACTION_VIDEO_CAPTURE -- 一个intent action, 用来向系统Camera程序请求录制视频。
>    
> ii. 开启Camera intent -- 通过调用startActivityForResult()来执行Camera intent, 在你调用这个方法之后，系统Camera程序就是出现在用户界面，然后用户就可以拍摄照片或者视频了。
> 
> iii. 接收Intent 结果 -- 在你的应用程序里面建立一个onActivityResult()方法来接收来自系统Camera程序的执行结果。当用户拍摄了照片或者视频(也许取消了拍摄操作)，系统就会调用这个方法。

b. 图片拍摄Intent
使用系统Camera来拍摄图片，是一种以最快的速度，最少的代码，让你的程序能够拍照的方法。一个图片拍摄的intent可以包含如下扩展信息：

i. MediaStore.EXTRA_OUTPUT -- 指定一个Uri对象，系统Camera程序会把拍摄的图片存储到指定位置。这个设置一般是强烈推荐的。如果你不指定这个Uri路径，那么系统Camera程序会把图片以一个默认的名字，存储在一个默认的位置。我们可以通过返回的intent，然后用Intent.getData()方法来获取这个值。

ii. 下面这个例子可以告诉大家怎么构造一个拍摄图片的intent,并且执行它。

    
    private static final int CAPTURE_IMAGE_ACTIVITY_REQUEST_CODE = 100;
    
    private Uri fileUri;
    
     
    <a href="http://home.51cto.com/index.php?s=/space/5017954" target="_blank">@override</a>
    
    public void onCreate(Bundle savedInstanceState) {
    
    super.onCreate(savedInstanceState);
    
    setContentView(R.layout.main);
    
    // create Intent to take a picture and return control to the calling application
    
    Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
    

    fileUri = getOutputMediaFileUri(MEDIA_TYPE_IMAGE); // create a file to save the image
    
    intent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri); // set the image file name

    // start the image capture Intent
    
    startActivityForResult(intent, CAPTURE_IMAGE_ACTIVITY_REQUEST_CODE);
    
    }

当startActivityForResult()方法执行之后，用户就会看到系统的Camera界面。当用户完成拍摄照片(或者取消拍摄)之后，用户界面就会返回你自己的应用程序。那么你必须拦截onActivityResult方法，以便接收执行结果和继续你自己代码的逻辑。

c. 视频录制intent

使用系统Camera来录制视频，是一种以最快的速度，最少的代码，让你的程序能够录制视频的方法。一个视频录制的intent可以包含如下扩展信息：

MediaStore.EXTRA_OUTPUT -- 指定一个Uri对象，系统Camera程序会把录制的视频存储到指定位置。这个设置一般是强烈推荐的。如果你不指定这个Uri路径，那么系统Camera程序会把视频以一个默认的名字，存储在一个默认的位置。我们可以通过返回的intent，然后用Intent.getData()方法来获取这个值。

MediaStore.EXTRA_VIDEO_QUALITY -- 视频质量，0(低质量，比较小的文件来存储视频)， 1(高质量，比较大的文件来存储视频)

MediaStore.EXTRA_DURATION_LIMIT  -- 设置一个值来限制视频时间长度，秒为单位。

MediaStore.EXTRA_SIZE_LIMIT -- 设置一个值来限制视频大小，byte为单位。

下面这个例子可以告诉大家怎么构造一个录制视频的intent,并且执行它。

    private static final int CAPTURE_VIDEO_ACTIVITY_REQUEST_CODE = 200;
    
    private Uri fileUri;
    
    <a href="http://home.51cto.com/index.php?s=/space/5017954" target="_blank">@override</a>
    
    public void onCreate(Bundle savedInstanceState) {

    super.onCreate(savedInstanceState);

    setContentView(R.layout.main);

    //create new Intent

    Intent intent = new Intent(MediaStore.ACTION_VIDEO_CAPTURE);

    fileUri = getOutputMediaFileUri(MEDIA_TYPE_VIDEO);  // create a file to save the video

    intent.putExtra(MediaStore.EXTRA_OUTPUT, fileUri);  // set the image file name

    intent.putExtra(MediaStore.EXTRA_VIDEO_QUALITY, 1); // set the video image quality to high

    // start the Video Capture Intent

    startActivityForResult(intent, CAPTURE_VIDEO_ACTIVITY_REQUEST_CODE);
    }

当startActivityForResult()方法执行之后，用户就会看到系统的Camera界面。当用户完成录制视频(或者取消拍摄)之后，用户界面就会返回你自己的应用程序。那么你必须拦截onActivityResult方法，以便接收执行结果和继续你自己代码的逻辑。

d. 接受Camera返回的结果
一旦你执行了拍摄图片或者视频的intent之后，那么你必须去接收执行结果。那么，下面的内容就是告诉你怎么去接收执行结果，以及进一步处理Camera拍摄的图片或者视频。

为了接受Camera拍摄的结果，你必须重写onActivityResult()方法，如下：

    private static final int CAPTURE_IMAGE_ACTIVITY_REQUEST_CODE = 100;
    
    private static final int CAPTURE_VIDEO_ACTIVITY_REQUEST_CODE = 200;
    
    <a href="http://home.51cto.com/index.php?s=/space/5017954" target="_blank">@override</a>
    
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {

    if (requestCode == CAPTURE_IMAGE_ACTIVITY_REQUEST_CODE) {

        if (resultCode == RESULT_OK) {

            // Image captured and saved to fileUri specified in the Intent

            Toast.makeText(this, "Image saved to:\n" +

                     data.getData(), Toast.LENGTH_LONG).show();

        } else if (resultCode == RESULT_CANCELED) {

            // User cancelled the image capture

        } else {

            // Image capture failed, advise user

        }

    }

    if (requestCode == CAPTURE_VIDEO_ACTIVITY_REQUEST_CODE) {

        if (resultCode == RESULT_OK) {

            // Video captured and saved to fileUri specified in the Intent

            Toast.makeText(this, "Video saved to:\n" +

                     data.getData(), Toast.LENGTH_LONG).show();

        } else if (resultCode == RESULT_CANCELED) {

            // User cancelled the video capture

        } else {

            // Video capture failed, advise user

       }

    }

    
一旦你的Activity接收到成功执行拍摄的结果，那么存储在指定位置的图片或者视频，就能被我们的程序使用了。

**5. 构造自己的Camera程序**

一些开发者可能需要自己定制Camera应用程序，定制自定义的Camera程序可能比调用系统的Camera需要更多的代码，但是这真的能给你的用户带来不一样的用户体验。。

    下面是开发自定义Camera的一般步骤：
    a. 检测Camera设备的可访问性 -- 书写代码确认Camera设备是否存在并且请求访问。
    b. 创建一个预览窗口 -- 创建一个预览窗口，一般继承SurfaceView并且实现SurfaceHolder接口，这个预览窗口可以通过Camera动态预览图片。
    c. 创建一个预览布局 --  一旦你拥有了预览窗口，那么创建一个视频Layout来封装预览窗口。
    d. 设置拍摄监听器 -- 设置图片或者视频拍摄的监听器，比如按下一个Button
    e. 拍摄和保存文件 
    d. 释放Camera设备 -- 拍摄完毕之后，你的程序必须释放Camera设备，以便其他程序使用Camera。

Camera是一个共享的硬件资源，所以你的程序必须非常小心的管理它，以便不能和其他程序发生冲突。下面的内容就是告诉大家怎么去检测Camera设备，怎么去请求访问Camera,以及当你的程序关闭的时候怎么释放Camera。

**6. 检测Camera硬件设备**
如果你的程序没有在AndroidMainfest.xml中申明请求使用Camera,那么你应该在运行期去检查Camera是否可用。使用PackageManager.hasSystemFeature() 方法，比如：

    /** Check if this device has a camera */
    
    private boolean checkCameraHardware(Context context) {

    if (context.getPackageManager().hasSystemFeature(PackageManager.FEATURE_CAMERA)){

        // this device has a camera

        return true;

    } else {

        // no camera on this device

        return false;

    }

    }

> 注： 一个Android设备可能含有多个Camera设备，比如背部摄像头用来拍照，前部摄像头用来拨打视频电话，那么，Android2.3(API Level 9) 以及以后的版本将允许你检查设备上可以用的Camera设备，通过调用Camera.getNumberOfCameras()方法。

**7. 访问Camera设备**

为了打开主Camera设备，我们可以调用Camera.open() 方法，如下：

    /** A safe way to get an instance of the Camera object. */
    
    public static Camera getCameraInstance(){

    Camera c = null;

    try {

        c = Camera.open(); // attempt to get a Camera instance

    }

    catch (Exception e){

        // Camera is not available (in use or does not exist)

    }

    return c; // returns null if camera is unavailable

    }

注: 当使用Camera.open()方法的时候，无论如何都需要检查是否发生了异常。如果忘记检查异常的话，有可能会因为Camera设备不存在而导致你的应用程序崩溃。

在Android2.3(API Level 9)或者更高版本，你可以通过Camera.open(int)方法来访问指定的Camera设备，

**8. 检查Camera属性**

一旦你可以访问Camera设备之后，你可以调用Camera.getParameters()方法来获取更多的Camera信息，当使用API Level 9或者更高版本时，可以使用Camera.getCameraInfo()方法来检测Camera设备是前端设备还是背部设备。

**9. 创建预览窗口**

    /** A basic Camera preview class */
    
    public class CameraPreview extends SurfaceView implements SurfaceHolder.Callback {
    
    private SurfaceHolder mHolder;

    private Camera mCamera;

 

    public CameraPreview(Context context, Camera camera) {

        super(context);

        mCamera = camera;

 

        // Install a SurfaceHolder.Callback so we get notified when the

       // underlying surface is created and destroyed.

        mHolder = getHolder();

        mHolder.addCallback(this);

        // deprecated setting, but required on Android versions prior to 3.0

        mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);

    }

    public void surfaceCreated(SurfaceHolder holder) {

        // The Surface has been created, now tell the camera where to draw the preview.

        try {

            mCamera.setPreviewDisplay(holder);

            mCamera.startPreview();

        } catch (IOException e) {

            Log.d(TAG, "Error setting camera preview: " + e.getMessage());

        }

    }

    public void surfaceDestroyed(SurfaceHolder holder) {

        // empty. Take care of releasing the Camera preview in your activity.

    }

    public void surfaceChanged(SurfaceHolder holder, int format, int w, int h) {

        // If your preview can change or rotate, take care of those events here.

        // Make sure to stop the preview before resizing or reformatting it.

 

        if (mHolder.getSurface() == null){

          // preview surface does not exist

          return;

        }

        // stop preview before making changes

        try {

            mCamera.stopPreview();

        } catch (Exception e){

          // ignore: tried to stop a non-existent preview

        }

        // make any resize, rotate or reformatting changes here

        // start preview with new settings

        try {

            mCamera.setPreviewDisplay(mHolder);

            mCamera.startPreview();

        } catch (Exception e){

            Log.d(TAG, "Error starting camera preview: " + e.getMessage());

        }

    }

    }

**10. 把预览窗口设置到一个Layout里面**

定义Activity配置文件，
    
    <?xml version="1.0" encoding="utf-8"?>
    
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

    android:orientation="horizontal"

    android:layout_width="fill_parent"

    android:layout_height="fill_parent"

    >
    
      <FrameLayout

    android:id="@+id/camera_preview"

    android:layout_width="fill_parent"

    android:layout_height="fill_parent"

    android:layout_weight="1"

    />

 
    
      <Button

    android:id="@+id/button_capture"

    android:text="Capture"

    android:layout_width="wrap_content"

    android:layout_height="wrap_content"

    android:layout_gravity="center"

    />
    
    </LinearLayout>

在大多数设备上，拍摄照片一般是横屏的，所以，你需要添加如下代码：
    
    <activity android:name=".CameraActivity"

          android:label="@string/app_name"

          android:screenOrientation="landscape">

         <!-- configure this activity to use landscape orientation -->

          <intent-filter>

        <action android:name="android.intent.action.MAIN" />

        <category android:name="android.intent.category.LAUNCHER" />

    </intent-filter>
    
    </activity>

在Activity中，把预览窗口添加到Layout里面去
    
    public class CameraActivity extends Activity {

    private Camera mCamera;

    private CameraPreview mPreview;

    <a href="http://home.51cto.com/index.php?s=/space/5017954" target="_blank">@override</a>

    public void onCreate(Bundle savedInstanceState) {

        super.onCreate(savedInstanceState);

        setContentView(R.layout.main);

        // Create an instance of Camera

        mCamera = getCameraInstance();

        // Create our Preview view and set it as the content of our activity.

        mPreview = new CameraPreview(this, mCamera);

        FrameLayout preview = (FrameLayout) findViewById(id.camera_preview);

        preview.addView(mPreview);

    }
    
    }

**11. 拍照**

a. 首先定义一个接口，用来处理拍摄完毕之后的动作。
    
    private PictureCallback mPicture = new PictureCallback() {
    
    <a href="http://home.51cto.com/index.php?s=/space/5017954" target="_blank">@override</a>

    public void onPictureTaken(byte[] data, Camera camera) {

        File pictureFile = getOutputMediaFile(MEDIA_TYPE_IMAGE);

        if (pictureFile == null){

            Log.d(TAG, "Error creating media file, check storage permissions: " +

                e.getMessage());

            return;

        }

        try {

            FileOutputStream fos = new FileOutputStream(pictureFile);

            fos.write(data);

            fos.close();

        } catch (FileNotFoundException e) {

            Log.d(TAG, "File not found: " + e.getMessage());

        } catch (IOException e) {

            Log.d(TAG, "Error accessing file: " + e.getMessage());

        }

    }
    
    };

getOutputMediaFile(int) 方法的实现
    
    public static final int MEDIA_TYPE_IMAGE = 1;
    
    public static final int MEDIA_TYPE_VIDEO = 2;
    
     
    
    /** Create a file Uri for saving an image or video */
    
    private static Uri getOutputMediaFileUri(int type){

      return Uri.fromFile(getOutputMediaFile(type));
    
    }
    
     
    
    /** Create a File for saving an image or video */
    
    private static Uri getOutputMediaFile(int type){

    // To be safe, you should check that the SDCard is mounted

    // using Environment.getExternalStorageState() before doing this.

 

    File mediaStorageDir = new File(Environment.getExternalStoragePublicDirectory(

              Environment.DIRECTORY_PICTURES), "MyCameraApp");

    // This location works best if you want the created images to be shared

    // between applications and persist after your app has been uninstalled.

 

    // Create the storage directory if it does not exist

    if (! mediaStorageDir.exists()){

        if (! mediaStorageDir.mkdirs()){

            Log.d("MyCameraApp", "failed to create directory");

            return null;

        }

    }



    // Create a media file name

    String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());

    File mediaFile;

    if (type == MEDIA_TYPE_IMAGE){

        mediaFile = new File(mediaStorageDir.getPath() + File.separator +

        "IMG_"+ timeStamp + ".jpg");

    } else if(type == MEDIA_TYPE_VIDEO) {

        mediaFile = new File(mediaStorageDir.getPath() + File.separator +

        "VID_"+ timeStamp + ".mp4");

    } else {

        return null;

    }

 

    return mediaFile;

    }

设置我们刚才的监听器
预览源代码打印
    
    // Add a listener to the Capture button
    
    Button captureButton = (Button) findViewById(id.button_capture);

    captureButton.setOnClickListener(

        new View.OnClickListener() {

        <a href="http://home.51cto.com/index.php?s=/space/5017954" target="_blank">@override</a>

        public void onClick(View v) {

            // get an image from the camera

            mCamera.takePicture(null, null, mPicture);

        }

    }

    );
