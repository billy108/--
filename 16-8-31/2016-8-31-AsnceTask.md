# android AsyncTask介绍 #

## AsyncTask和Handler对比 ##

1 ） AsyncTask实现的原理,和适用的优缺点

 AsyncTask,是android提供的轻量级的异步类,可以直接继承AsyncTask,在类中实现异步操作,并提供接口反馈当前异步执行的程度(可以通过接口实现UI进度更新),最后反馈执行的结果给UI主线程.
 
> **使用的优点:**
> 
> l  简单,快捷
> 
> l  过程可控
> 
>        
> 
> **使用的缺点:**
> 
> l  在使用多个异步操作和并需要进行Ui变更时,就变得复杂起来.

2 ）Handler异步实现的原理和适用的优缺点

在Handler 异步实现时,涉及到 Handler, Looper, Message,Thread四个对象，实现异步的流程是主线程启动Thread（子线程）àthread(子线程)运行并生成Message-àLooper获取Message并传递给HandleràHandler逐个获取Looper中的Message，并进行UI变更。

> **使用的优点：**
> 
> l  结构清晰，功能定义明确
> 
> l  对于多个后台任务时，简单，清晰
> 
>    
> 
> **使用的缺点：**
> 
> l  在单个后台异步处理时，显得代码过多，结构过于复杂（相对性）

Android的AsyncTask比Handler更轻量级一些，适用于简单的异步处理。
首先明确Android之所以有Handler和AsyncTask，都是为了不阻塞主线程（UI线程），且UI的更新只能在主线程中完成，因此异步处理是不可避免的。
 
Android为了降低这个开发难度，提供了AsyncTask。AsyncTask就是一个封装过的后台任务类，顾名思义就是异步任务。

AsyncTask直接继承于Object类，位置为android.os.AsyncTask。要使用AsyncTask工作我们要提供三个泛型参数，并重载几个方法(至少重载一个)。

 

> AsyncTask定义了三种泛型类型 Params，Progress和Result。
> 
> Params 启动任务执行的输入参数，比如HTTP请求的URL。
> Progress 后台任务执行的百分比。
> Result 后台执行任务最终返回的结果，比如String。


使用过AsyncTask 的同学都知道一个异步加载数据**最少要重写以下这两个方法**：

- doInBackground(Params…) 后台执行，比较耗时的操作都可以放在这里。注意这里不能直接操作UI。此方法在后台线程执行，完成任务的主要工作，通常需要较长的时间。在执行过程中可以调用publicProgress(Progress…)来更新任务的进度。
- onPostExecute(Result)  相当于Handler 处理UI的方式，在这里面可以使用在doInBackground 得到的结果处理操作UI。 此方法在主线程执行，任务执行的结果作为此方法的参数返回

有必要的话你还得重写以下这三个方法，但不是必须的：

- onProgressUpdate(Progress…)   可以使用进度条增加用户体验度。 此方法在主线程执行，用于显示任务执行的进度。
- onPreExecute()        这里是最终用户调用Excute时的接口，当任务执行之前开始调用此方法，可以在这里显示进度对话框。
- onCancelled()             用户调用取消时，要做的操作

使用AsyncTask类，以下是几条**必须遵守的准则**：

1. Task的实例必须在UI thread中创建；
1. execute方法必须在UI thread中调用；
1. 不要手动的调用onPreExecute(), onPostExecute(Result)，doInBackground(Params...), onProgressUpdate(Progress...)这几个方法；
1. 该task只能被执行一次，否则多次调用时将会出现异常；