title: Android之进程和线程
date: 2015-11-03 14:10:23
tags:  [Processes,Threads]
category: Android
---
## 进程
## 线程
应用启动时，系统会为应用创建一个名为“主线程”的执行线程。 此线程非常重要，因为它负责将事件分派给相应的用户界面小工具，其中包括绘图事件。 此外，它也是应用与 Android UI 工具包组件（来自 android.widget 和 android.view 软件包的组件）进行交互的线程。因此，主线程有时也称为 UI 线程。

系统绝对不会为每个组件实例创建单独的线程。运行于同一进程的所有组件均在 UI 线程中实例化，并且对每个组件的系统调用均由该线程进行分派。因此，响应系统回调的方法（例如，报告用户操作的 onKeyDown() 或生命周期回调方法）始终在进程的 UI 线程中运行。
<!--more-->
例如，当用户触摸屏幕上的按钮时，应用的 UI 线程会将触摸事件分派给小工具，而小工具反过来又设置其按下状态，并将无效请求发布到事件队列中。UI 线程从队列中取消该请求并通知小工具应该重绘自身。

在应用执行繁重的任务以响应用户交互时，除非正确实施应用，否则这种单线程模式可能会导致性能低下。 特别地，如果 UI 线程需要处理所有任务，则执行耗时很长的操作（例如，网络访问或数据库查询）将会阻塞整个 UI。一旦线程被阻塞，将无法分派任何事件，包括绘图事件。从用户的角度来看，应用显示为挂起。 更糟糕的是，如果 UI 线程被阻塞超过几秒钟时间（目前大约是 5 秒钟），用户就会看到一个让人厌烦的“应用无响应”(ANR) 对话框。如果引起用户不满，他们可能就会决定退出并卸载此应用。

此外，Android UI 工具包并非线程安全工具包。因此，您不得通过工作线程操纵 UI，而只能通过 UI 线程操纵用户界面。因此，Android 的`单线程模式`必须遵守两条规则：

* 不要阻塞 UI 线程
* 不要在 UI 线程之外访问 Android UI 工具包

### 工作线程

根据上述单线程模式，要保证应用 UI 的响应能力，关键是不能阻塞 UI 线程。如果执行的操作不能很快完成，则应确保它们在单独的线程（“后台”或“工作”线程）中运行。

例如，以下代码演示了一个点击侦听器从单独的线程下载图像并将其显示在 ImageView 中：
```java
public void onClick(View v) {
    new Thread(new Runnable() {
        public void run() {
            Bitmap b = loadImageFromNetwork("http://example.com/image.png");
            mImageView.setImageBitmap(b);
        }
    }).start();
}
```
乍看起来，这段代码似乎运行良好，因为它创建了一个新线程来处理网络操作。 但是，它违反了单线程模式的第二条规则：不要在 UI 线程之外访问 Android UI 工具包—此示例从工作线程（而不是 UI 线程）修改了 ImageView。这可能导致出现不明确、不可预见的行为，但要跟踪此行为困难而又费时。

为解决此问题，Android 提供了几种途径来从其他线程访问 UI 线程。以下列出了几种有用的方法：
```java
Activity.runOnUiThread(Runnable)
```
```java
View.post(Runnable)
```
```java
View.postDelayed(Runnable, long)
```
例如，您可以通过使用 View.post(Runnable) 方法修复上述代码：
```java
public void onClick(View v) {
    new Thread(new Runnable() {
        public void run() {
            final Bitmap bitmap = loadImageFromNetwork("http://example.com/image.png");
            mImageView.post(new Runnable() {
                public void run() {
                    mImageView.setImageBitmap(bitmap);
                }
            });
        }
    }).start();
}
```
现在，上述实现属于线程安全型：在单独的线程中完成网络操作，而在 UI 线程中操纵 ImageView。

但是，随着操作日趋复杂，这类代码也会变得复杂且难以维护。 要通过工作线程处理更复杂的交互，可以考虑在工作线程中使用 Handler 处理来自 UI 线程的消息。当然，最好的解决方案或许是扩展 AsyncTask 类，此类简化了与 UI 进行交互所需执行的工作线程任务。

使用 AsyncTask

AsyncTask 允许对用户界面执行异步操作。它会先阻塞工作线程中的操作，然后在 UI 线程中发布结果，而无需您亲自处理线程和/或处理程序。

要使用它，必须创建 AsyncTask 子类并实现 doInBackground() 回调方法，该方法将在后台线程池中运行。要更新 UI，必须实现 onPostExecute() 以传递 doInBackground() 返回的结果并在 UI 线程中运行，这样，您即可安全更新 UI。稍后，您可以通过从 UI 线程调用 execute() 来运行任务。

例如，您可以通过以下方式使用 AsyncTask 来实现上述示例：
```java
public void onClick(View v) {
    new DownloadImageTask().execute("http://example.com/image.png");
}

private class DownloadImageTask extends AsyncTask<String, Void, Bitmap> {
    /** The system calls this to perform work in a worker thread and
      * delivers it the parameters given to AsyncTask.execute() */
    protected Bitmap doInBackground(String... urls) {
        return loadImageFromNetwork(urls[0]);
    }
    
    /** The system calls this to perform work in the UI thread and delivers
      * the result from doInBackground() */
    protected void onPostExecute(Bitmap result) {
        mImageView.setImageBitmap(result);
    }
}
```
现在 UI 是安全的，代码也得到简化，因为任务分解成了两部分：一部分应在工作线程内完成，另一部分应在 UI 线程内完成。

下面简要概述了 AsyncTask 的工作方法，但要全面了解如何使用此类，您应阅读 AsyncTask 参考文档：

可以使用泛型指定参数类型、进度值和任务最终值
方法 doInBackground() 会在工作线程上自动执行
onPreExecute()、onPostExecute() 和 onProgressUpdate() 均在 UI 线程中调用
doInBackground() 返回的值将发送到 onPostExecute()
您可以随时在 doInBackground() 中调用publishProgress()，以在 UI 线程中执行 onProgressUpdate()
您可以随时取消任何线程中的任务

## 附录
[Processes And Threads](https://developer.android.com/intl/zh-cn/guide/components/processes-and-threads.html)
[Android--多线程之进程与线程](http://www.cnblogs.com/plokmju/p/android_ProcessOrThread.html)