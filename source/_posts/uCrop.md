title: uCrop使用及源码浅析
date: 2016-06-16 08:52:32
tags: uCrop
category: source analysis
---
# uCrop使用
## github地址
[https://github.com/Yalantis/uCrop](https://github.com/Yalantis/uCrop)

## app/build.gradle
```
compile 'com.yalantis:ucrop:1.5.0'
```
## AndroidManifest.xml
```
<activity
    android:name="com.yalantis.ucrop.UCropActivity"
    android:screenOrientation="portrait"
    android:theme="@style/Theme.AppCompat.Light.NoActionBar" />
```
这里theme可以改成自己的

## 配置uCrop 
```
 /**
  * 启动裁剪
  * @param activity 上下文
  * @param sourceFilePath 需要裁剪图片的绝对路径
  * @param requestCode 比如：UCrop.REQUEST_CROP
  * @param aspectRatioX 裁剪图片宽高比
  * @param aspectRatioY 裁剪图片宽高比
  * @return
  */
public static String startUCrop(Activity activity, String sourceFilePath, 
	int requestCode, float aspectRatioX, float aspectRatioY) {
    Uri sourceUri = Uri.fromFile(new File(sourceFilePath));
    File outDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
    if (!outDir.exists()) {
        outDir.mkdirs();
    }
    File outFile = new File(outDir, System.currentTimeMillis() + ".jpg");
    //裁剪后图片的绝对路径
    String cameraScalePath = outFile.getAbsolutePath();
    Uri destinationUri = Uri.fromFile(outFile);
    //初始化，第一个参数：需要裁剪的图片；第二个参数：裁剪后图片
    UCrop uCrop = UCrop.of(sourceUri, destinationUri);
    //初始化UCrop配置
    UCrop.Options options = new UCrop.Options();
    //设置裁剪图片可操作的手势
    options.setAllowedGestures(UCropActivity.SCALE, UCropActivity.ROTATE, UCropActivity.ALL);
    //是否隐藏底部容器，默认显示
    options.setHideBottomControls(true);
    //设置toolbar颜色
    options.setToolbarColor(ActivityCompat.getColor(activity, R.color.colorPrimary));
    //设置状态栏颜色
    options.setStatusBarColor(ActivityCompat.getColor(activity, R.color.colorPrimary));
    //是否能调整裁剪框
    options.setFreeStyleCropEnabled(true);
    //UCrop配置
    uCrop.withOptions(options);
    //设置裁剪图片的宽高比，比如16：9
    uCrop.withAspectRatio(aspectRatioX, aspectRatioY);
    //uCrop.useSourceImageAspectRatio();
    //跳转裁剪页面
    uCrop.start(activity, requestCode);
    return cameraScalePath;
}
```
其他配置
```
//设置Toolbar标题
void setToolbarTitle(@Nullable String text)
//设置裁剪的图片格式
void setCompressionFormat(@NonNull Bitmap.CompressFormat format)
//设置裁剪的图片质量，取值0-100
void setCompressionQuality(@IntRange(from = 0) int compressQuality)
//设置最多缩放的比例尺
void setMaxScaleMultiplier(@FloatRange(from = 1.0, fromInclusive = false) float maxScaleMultiplier)
//动画时间
void setImageToCropBoundsAnimDuration(@IntRange(from = 100) int durationMillis)
//设置图片压缩最大值
void setMaxBitmapSize(@IntRange(from = 100) int maxBitmapSize)
//是否显示椭圆裁剪框阴影
void setOvalDimmedLayer(boolean isOval) 
//设置椭圆裁剪框阴影颜色
void setDimmedLayerColor(@ColorInt int color)
//是否显示裁剪框
void setShowCropFrame(boolean show)
//设置裁剪框边的宽度
void setCropFrameStrokeWidth(@IntRange(from = 0) int width)
//是否显示裁剪框网格
void setShowCropGrid(boolean show) 
//设置裁剪框网格颜色
void setCropGridColor(@ColorInt int color)
//设置裁剪框网格宽
void setCropGridStrokeWidth(@IntRange(from = 0) int width)
```
## onActivityResult 
经过裁剪，返回结果，这里我一般只需要裁剪后的图片绝对路径（调用上面startUCrop，即返回图片路径），然后调接口上传服务器。
```
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (resultCode == RESULT_OK && requestCode == UCrop.REQUEST_CROP) {
        final Uri resultUri = UCrop.getOutput(data);
    } else if (resultCode == UCrop.RESULT_ERROR) {
        final Throwable cropError = UCrop.getError(data);
    }
}
```

# uCrop源码浅析
如果对源码不感兴趣，可以不继续往下读了，因为以上内容对于uCrop常规使用已经足够了。