在android平台中ConnectivityManager主要负责查询网络连接状态以及在连接状态有变化的时候发出通知。其主要的功能职责如下：
> 
> 1、  监视网络状态，包括（Wi-Fi、GPRS、UMTS等）
> 
> 2、  当网络状态发生变化时发送广播通知
> 
> 3、  当网络连接失败后会尝试连接其他网络
> 
> 4、  为App提供API，用于获取网络状态信息等

该类中提供了一些常量，比如广播Action、网络类型等等，具体如下：

![](http://images2015.cnblogs.com/blog/708076/201511/708076-20151102091846336-1855234615.png)

公共的方法有：

![](http://images2015.cnblogs.com/blog/708076/201511/708076-20151102091926008-1428409369.png)

在上述方法中，常用的有getActivieNetworkInfo、getAllNetworkInfo、getNetworkInfo(int networkType)等，这些方法返回NetworkInfo，该类用于描述网络的状态（mobile和wifi）。

在NetworkInfo中定义了两个内部类，用于枚举网络的状态，两个内部类分别为NetworkInfo.DetailedState,精确的网络状态；NetworkInfo.State，粗略的网络状态。

NetworkInfo.DetailedState的枚举值：

![](http://images2015.cnblogs.com/blog/708076/201511/708076-20151102092023024-156564182.png)

NetworkInfo.State的枚举值：

![](http://images2015.cnblogs.com/blog/708076/201511/708076-20151102092058696-1476835334.png)

二者之间的对应关系

![](http://images2015.cnblogs.com/blog/708076/201511/708076-20151102092131367-1945415066.png)

在NetworkInfo中除了两个内部类外，还提供了一些方法，如
> 
> 1、  getDetailedState（）：返回当前精确的网络状态。
> 
> 2、  getState（）：返回当前粗略的网络状态。
> 
> 3、  getType():返回当前的网络类型（mobile或者wi-fi）
> 
> 4、  getTypeName():返回当前网络类型的名称。如“WIFI”或者“MOBILE”
> 
> 5、  isAvailable():判断网络是否有效可用。
> 
> 6、  isConnected()：判断网络是否已连接。

下面，我们通过一个案例来展示ConnectityManager的具体用法。核心代码如下
    
    public void getNetworkState(View v){
    //获取
    ConnectivityManager connectivityManager=
            (ConnectivityManager)getSystemService(CONNECTIVITY_SERVICE);

    NetworkInfo networkInfo=connectivityManager.getActiveNetworkInfo();
    StringBuilder sb = new StringBuilder();
    sb.append("TypeName:").append(networkInfo.getTypeName()).append("\n");
    sb.append("Type:").append(networkInfo.getType()).append("\n");
    sb.append("isAvailable:").append(networkInfo.isAvailable()).append("\n");
    sb.append("isConnected:").append(networkInfo.isConnected()).append("\n");
    NetworkInfo.DetailedState state= networkInfo.getDetailedState();
    String detailedState="";
    if(state== NetworkInfo.DetailedState.AUTHENTICATING){
        detailedState="AUTHENTICATING";
    }else if(state== NetworkInfo.DetailedState.BLOCKED){
        detailedState="BLOCKED";
    }else if(state== NetworkInfo.DetailedState.CONNECTED){
        detailedState="CONNECTED";
    }else if(state== NetworkInfo.DetailedState.CONNECTING){
        detailedState="CONNECTING";
    }else if(state== NetworkInfo.DetailedState.DISCONNECTED){
        detailedState="DISCONNECTED";
    }else if(state== NetworkInfo.DetailedState.DISCONNECTING){
        detailedState="DISCONNECTING";
    }else if(state== NetworkInfo.DetailedState.FAILED){
        detailedState="FAILED";
    }else if(state== NetworkInfo.DetailedState.IDLE){
        detailedState="IDLE";
    }else if(state== NetworkInfo.DetailedState.SCANNING){
        detailedState="SCANNING";
    }else if(state== NetworkInfo.DetailedState.OBTAINING_IPADDR){
        detailedState="OBTAINING_IPADDR";
    }else if(state== NetworkInfo.DetailedState.SUSPENDED){
        detailedState="SUSPENDED";
    }
    sb.append("DetailedState:").append(detailedState).append("\n");
    showState.setText(sb.toString());
    }


    /**
     * 获取所有网络
     * @param v
     */
    public void getNetwork(View v){
    ConnectivityManager connectivityManager=
            (ConnectivityManager)getSystemService(CONNECTIVITY_SERVICE);
    StringBuilder stringBuilder = new StringBuilder();
    NetworkInfo[] networkInfos= connectivityManager.getAllNetworkInfo();
    for(NetworkInfo info:networkInfos){
        stringBuilder.append("Name:").append(info.getTypeName())
                .append(";isAvailable:").append(info.isAvailable()).append("\n");
    }

    showNetwork.setText(stringBuilder.toString());
    }
    
    private BroadcastReceiver receiver = new BroadcastReceiver() {
    @Override
    public void onReceive(Context context, Intent intent) {
        if(intent.getAction().equals("android.net.conn.CONNECTIVITY_CHANGE")){
            ConnectivityManager connectivityManager=
                    (ConnectivityManager)getSystemService(CONNECTIVITY_SERVICE);
            NetworkInfo info= connectivityManager.getActiveNetworkInfo();
            if(info!=null){
                if(info.getState()== NetworkInfo.State.CONNECTED){
                    showChange.setText("网络变化：网络已连接！");
                }else{
                    showChange.setText("网络变化：网络已断开！");
                }
            }else{
                showChange.setText("网络变化：无网络！");
            }
        }
    }
    };

案例效果如下：

![](http://images2015.cnblogs.com/blog/708076/201511/708076-20151102092504399-1295480685.jpg)

注意，以上代码需要使用的如下权限：

    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"></uses-permission>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>