title: Android调用系统相机和相册
date: 2014-11-10 
categories: [Android]
tags: [Camera，Album]
---

> 拍照和相册的功能在实际开发中是最常见的功能，这里记录下。

# 准备工作
权限
```java
<!-- 往SDCard写入数据权限 --> 
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /> 
<!--请求访问使用照相设备-->
<uses-permission android:name="android.permission.CAMERA" /> 
```
常量
```java
public final static int ALBUM_REQUEST_CODE = 1;
public final static int CROP_REQUEST = 2;
public final static int CAMERA_REQUEST_CODE = 3;
public static String SAVED_IMAGE_DIR_PATH = 
Environment.getExternalStorageDirectory().getPath() 
+ "/AppName/camera/";// 拍照路径
String cameraPath;
```
#  相机

```java
// 指定相机拍摄照片保存地址
String state = Environment.getExternalStorageState();
if (state.equals(Environment.MEDIA_MOUNTED)) {
    cameraPath = SAVED_IMAGE_DIR_PATH + System.currentTimeMillis() + ".png";
    Intent intent = new Intent();
    // 指定开启系统相机的Action
    intent.setAction(MediaStore.ACTION_IMAGE_CAPTURE);
    String out_file_path = SAVED_IMAGE_DIR_PATH;
    File dir = new File(out_file_path);
    if (!dir.exists()) {
        dir.mkdirs();
    } // 把文件地址转换成Uri格式
    Uri uri = Uri.fromFile(new File(cameraPath));
    // 设置系统相机拍摄照片完成后图片文件的存放地址
    intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);
    startActivityForResult(intent, CAMERA_REQUEST_CODE);
} else {
    Toast.makeText(getApplicationContext(), "请确认已经插入SD卡",
            Toast.LENGTH_LONG).show();
}
```
onActivityResult
拿到cameraPath，就随便你搞了。
```
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode == Activity.RESULT_OK) {
        if (requestCode == CAMERA_REQUEST_CODE) {
            LogUtil.d("path=" + cameraPath);
        }
 }
```
不需要自定义路径，默认为Environment.DIRECTORY_PICTURES
```java
 /**
     * 启动相机
     */
    public static String startCamera(Activity activity, int requestCode) {
        // 指定相机拍摄照片保存地址
        String state = Environment.getExternalStorageState();
        if (state.equals(Environment.MEDIA_MOUNTED)) {
            Intent intent = new Intent();
            // 指定开启系统相机的Action
            intent.setAction(MediaStore.ACTION_IMAGE_CAPTURE);
            File outDir = Environment
			.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
            if (!outDir.exists()) {
                outDir.mkdirs();
            }
            File outFile = new File(outDir, System.currentTimeMillis() + ".jpg");
            // 把文件地址转换成Uri格式
            Uri uri = Uri.fromFile(outFile);
            LogUtil.d("getAbsolutePath=" + outFile.getAbsolutePath());
            // 设置系统相机拍摄照片完成后图片文件的存放地址
            intent.putExtra(MediaStore.EXTRA_OUTPUT, uri);
            // 此值在最低质量最小文件尺寸时是0，在最高质量最大文件尺寸时是１
            intent.putExtra(MediaStore.EXTRA_VIDEO_QUALITY, 0);
            activity.startActivityForResult(intent, requestCode);
            return outFile.getAbsolutePath();
        } else {
            Toast.makeText(activity, "请确认已经插入SD卡",
                    Toast.LENGTH_LONG).show();
            return null;
        }

    }
```
# 相册

```java
Intent intent = new Intent(Intent.ACTION_PICK, null);
intent.setDataAndType(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, "image/*");
intent.setAction(Intent.ACTION_GET_CONTENT);
startActivityForResult(intent, ALBUM_REQUEST_CODE);
```
onActivityResult
调用系统相册，然后通过Uri拿到图片的绝对地址。
```java
 @Override
 public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (resultCode == Activity.RESULT_OK) {
            if (requestCode == AppConstants.ALBUM_REQUEST_CODE) {
                try {
                    Uri uri = data.getData();
                    final String absolutePath= getAbsolutePath(mActivity, uri);
                    LogUtil.d("path=" + absolutePath);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }

        }
    }
```
getAbsolutePath方法
```java
 public String getAbsolutePath(final Context context, final Uri uri) {
     if (null == uri) return null;
     final String scheme = uri.getScheme();
     String data = null;
     if (scheme == null)
         data = uri.getPath();
     else if (ContentResolver.SCHEME_FILE.equals(scheme)) {
         data = uri.getPath();
     } else if (ContentResolver.SCHEME_CONTENT.equals(scheme)) {
         Cursor cursor = context.getContentResolver().query(uri, 
		 new String[]{MediaStore.Images.ImageColumns.DATA}, null, null, null);
         if (null != cursor) {
             if (cursor.moveToFirst()) {
                 int index = cursor.getColumnIndex(MediaStore.Images.ImageColumns.DATA);
                 if (index > -1) {
                     data = cursor.getString(index);
                 }
             }
             cursor.close();
         }
     }
     return data;
 }
```
# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)
