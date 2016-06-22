title: uCrop使用及源码浅析
date: 2016-06-20 08:52:32
tags: uCrop
category: SourceAnalysis
---
# uCrop使用

## github地址
[https://github.com/Yalantis/uCrop](https://github.com/Yalantis/uCrop)
然后clone或下载到本地，运行之。

## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/ucrop0.gif?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)

<!--more-->

## app/build.gradle
```java
compile 'com.yalantis:ucrop:1.5.0'
```

## AndroidManifest.xml
```java
<activity
    android:name="com.yalantis.ucrop.UCropActivity"
    android:screenOrientation="portrait"
    android:theme="@style/Theme.AppCompat.Light.NoActionBar" />
```
这里theme可以改成自己的

## 配置uCrop 
```java
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

## 其他配置
```java
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
```java
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
uCrop源码能学习的东西有很多，比如左右滑的标尺，不过我们这里源码浅析只关注裁剪部分。

## 类关系
首先有个大概了解：
![](http://7q5c2h.com1.z0.glb.clouddn.com/ucrop1.jpg?watermark/2/text/5ZC05bCP6b6Z5ZCM5a24/font/5qW35L2T/fontsize/500/fill/I0VGRUZFRg==/dissolve/100/gravity/SouthEast/dx/10/dy/10)
	
GestureCropImageView：负责监听各种手势
CropImageView：主要完成图片裁剪工作，和判断裁剪图片是否充满裁剪框
TransformImageView：负责图片旋转、缩放、位移操作

## 入口
由上面的效果图可知，点击右上角，调用裁剪操作，代码如下：
```java
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    if (item.getItemId() == R.id.menu_crop) {
        cropAndSaveImage();
    }
    return super.onOptionsItemSelected(item);
}
//裁剪和保存图片
protected void cropAndSaveImage() {
	……
    mGestureCropImageView.cropAndSaveImage(mCompressFormat, mCompressQuality, new BitmapCropCallback() {
        @Override
        public void onBitmapCropped(@NonNull Uri resultUri) {
            setResultUri(resultUri, mGestureCropImageView.getTargetAspectRatio());
            finish();
        }
        @Override
        public void onCropFailure(@NonNull Throwable t) {
            setResultError(t);
            finish();
        }
    });
}
```
这里调用了GestureCropImageView&cropAndSaveImage方法，如下：
```java
/**
 * @param compressFormat  图片压缩格式
 * @param compressQuality 图片压缩质量
 * @param cropCallback    图片压缩回调
 */
public void cropAndSaveImage(@NonNull Bitmap.CompressFormat compressFormat, int 											compressQuality,@Nullable BitmapCropCallback cropCallback) {
    //取消所有动画
    cancelAllAnimations();
    //判断裁剪图片是否充满裁剪框
    setImageToWrapCropBounds(false);
    //进行裁剪
    new BitmapCropTask(getViewBitmap(), mCropRect, RectUtils.trapToRect(mCurrentImageCorners),
            getCurrentScale(), getCurrentAngle(),
            mMaxResultImageSizeX, mMaxResultImageSizeY,
            compressFormat, compressQuality,
            getImageInputPath(), getImageOutputPath(),
            cropCallback).execute();
}
```

## 裁剪之前

### setImageToWrapCropBounds
裁剪之前，先判断裁剪图片是否充满裁剪框，如果没有，进行移动和缩放让其充满。
```java
 public void setImageToWrapCropBounds(boolean animate) {
     //mBitmapLaidOut图片加载OK，isImageWrapCropBounds()检查图片是否充满裁剪框
     if (mBitmapLaidOut && !isImageWrapCropBounds()) {
         //当前图片中心X点
         float currentX = mCurrentImageCenter[0];
         //当前图片中心Y点
         float currentY = mCurrentImageCenter[1];
         //当前图片缩放值
         float currentScale = getCurrentScale();
         //差量
         float deltaX = mCropRect.centerX() - currentX;
         float deltaY = mCropRect.centerY() - currentY;
         float deltaScale = 0;
         //临时矩阵重置
         mTempMatrix.reset();
         //临时矩阵移动
         mTempMatrix.setTranslate(deltaX, deltaY);
         //复制到新的数组
         final float[] tempCurrentImageCorners = Arrays.copyOf(mCurrentImageCorners, mCurrentImageCorners.length);
         //将此矩阵应用于二维点的数组，并编写转换后的指向数组的点
         mTempMatrix.mapPoints(tempCurrentImageCorners);
         //再检查图片是否充满裁剪框
         boolean willImageWrapCropBoundsAfterTranslate = isImageWrapCropBounds(tempCurrentImageCorners);
         if (willImageWrapCropBoundsAfterTranslate) {
             //图片缩进的数组
             final float[] imageIndents = calculateImageIndents();
             deltaX = -(imageIndents[0] + imageIndents[2]);
             deltaY = -(imageIndents[1] + imageIndents[3]);
         } else {
             RectF tempCropRect = new RectF(mCropRect);
             mTempMatrix.reset();
             mTempMatrix.setRotate(getCurrentAngle());
             mTempMatrix.mapRect(tempCropRect);
             //获取裁剪图片的边
             final float[] currentImageSides = RectUtils.getRectSidesFromCorners(mCurrentImageCorners);
             deltaScale = Math.max(tempCropRect.width() / currentImageSides[0],
                     tempCropRect.height() / currentImageSides[1]);
             deltaScale = deltaScale * currentScale - currentScale;
         }
         if (animate) {
             //移动或缩放图片（有动画效果）
             post(mWrapCropBoundsRunnable = new WrapCropBoundsRunnable(
                     CropImageView.this, mImageToWrapCropBoundsAnimDuration, currentX, currentY, deltaX, deltaY,
                     currentScale, deltaScale, willImageWrapCropBoundsAfterTranslate));
         } else {
             //移动图片
             postTranslate(deltaX, deltaY);
             if (!willImageWrapCropBoundsAfterTranslate) {
                 //缩放图片
                 zoomInImage(currentScale + deltaScale, mCropRect.centerX(), mCropRect.centerY());
             }
         }
     }
 }
```

## 进行裁剪
裁剪放到了异步，即BitmapCropTask继承AsyncTask，先设置原始图片resizeScale值，然后通过ExifInterface保存新的图片，即裁剪后的图片。
```java
public class BitmapCropTask extends AsyncTask<Void, Void, Throwable> {
	
	……

    /**
     * @param viewBitmap          裁剪图片bitmap
     * @param cropRect            裁剪矩形
     * @param currentImageRect    当前图片矩形
     * @param currentScale        当前图片缩放值
     * @param currentAngle        当前图片角度
     * @param maxResultImageSizeX 图片裁剪后最大宽值
     * @param maxResultImageSizeY 图片裁剪后最大高值
     * @param compressFormat      图片裁剪的格式
     * @param compressQuality     图片裁剪的质量
     * @param imageInputPath      裁剪图片路径
     * @param imageOutputPath     图片裁剪后路径
     * @param cropCallback        裁剪回调
     */
    public BitmapCropTask(@Nullable Bitmap viewBitmap,
                          @NonNull RectF cropRect, @NonNull RectF currentImageRect,
                          float currentScale, float currentAngle,
                          int maxResultImageSizeX, int maxResultImageSizeY,
                          @NonNull Bitmap.CompressFormat compressFormat, int compressQuality,
                          @NonNull String imageInputPath, @NonNull String imageOutputPath,
                          @Nullable BitmapCropCallback cropCallback) {

      ……
    }

    @Override
    @Nullable
    protected Throwable doInBackground(Void... params) {
        if (mViewBitmap == null || mViewBitmap.isRecycled()) {
            return new NullPointerException("ViewBitmap is null or already recycled");
        }
        if (mCurrentImageRect.isEmpty()) {
            return new NullPointerException("CurrentImageRect is empty");
        }
		//设置resizeScale值
        float resizeScale = resize();

        try {
	        //裁剪
            crop(resizeScale);
            //回收
            mViewBitmap.recycle();
            mViewBitmap = null;
        } catch (Throwable throwable) {
            return throwable;
        }

        return null;
    }

    private float resize() {
        //初始Options
        final BitmapFactory.Options options = new BitmapFactory.Options();
        //查询该位图，而无需分配存储器，可获取outHeight(图片原始高度)和 outWidth(图片的原始宽度)
        options.inJustDecodeBounds = true;
        //裁剪图片解码
        BitmapFactory.decodeFile(mImageInputPath, options);
        //原始图片和裁剪后图片比值
        float scaleX = options.outWidth / mViewBitmap.getWidth();
        float scaleY = options.outHeight / mViewBitmap.getHeight();

        float resizeScale = Math.min(scaleX, scaleY);

        mCurrentScale /= resizeScale;
        //初始化值为1
        resizeScale = 1;
        if (mMaxResultImageSizeX > 0 && mMaxResultImageSizeY > 0) {
            float cropWidth = mCropRect.width() / mCurrentScale;
            float cropHeight = mCropRect.height() / mCurrentScale;

            if (cropWidth > mMaxResultImageSizeX || cropHeight > mMaxResultImageSizeY) {

                scaleX = mMaxResultImageSizeX / cropWidth;
                scaleY = mMaxResultImageSizeY / cropHeight;
                //设置resizeScale，如果是2就是高度和宽度都是原始的一半
                resizeScale = Math.min(scaleX, scaleY);

                mCurrentScale /= resizeScale;
            }
        }
        return resizeScale;
    }

    private boolean crop(float resizeScale) throws IOException {
        //ExifInterface这个接口提供了图片文件的旋转，gps，时间等信息，从原始图片读出Exif标签
        ExifInterface originalExif = new ExifInterface(mImageInputPath);

        int top = Math.round((mCropRect.top - mCurrentImageRect.top) / mCurrentScale);
        int left = Math.round((mCropRect.left - mCurrentImageRect.left) / mCurrentScale);
        int width = Math.round(mCropRect.width() / mCurrentScale);
        int height = Math.round(mCropRect.height() / mCurrentScale);
		//复制图片
        boolean cropped = cropCImg(mImageInputPath, mImageOutputPath,
                left, top, width, height, mCurrentAngle, resizeScale,
                mCompressFormat.ordinal(), mCompressQuality);
        if (cropped) {
	        //拿到裁剪后图片
            copyExif(originalExif, width, height);
        }

        return cropped;
    }

    @SuppressWarnings("JniMissingFunction")
    native public boolean cropCImg(String inputPath, String outputPath,
                                   int left, int top, int width, int height, float angle, float resizeScale,
                                   int format, int quality) throws IOException, OutOfMemoryError;
	/**
	 * @param originalExif 原始图片Exif
	 * @param width        裁剪后图片宽
	 * @param height       裁剪后图片高
	 * @throws IOException 是否异常
	 */
    public void copyExif(ExifInterface originalExif, int width, int height) throws IOException {
        //Exif标签数组
        String[] attributes = new String[]{
                ExifInterface.TAG_APERTURE,
                ……
        };
        //指定裁剪后图片路径，初始化新的ExifInterface
        ExifInterface newExif = new ExifInterface(mImageOutputPath);
        String value;
        for (String attribute : attributes) {
            value = originalExif.getAttribute(attribute);
            if (!TextUtils.isEmpty(value)) {
                //设置Exif标签
                newExif.setAttribute(attribute, value);
            }
        }
        newExif.setAttribute(ExifInterface.TAG_IMAGE_WIDTH, String.valueOf(width));
        newExif.setAttribute(ExifInterface.TAG_IMAGE_LENGTH, String.valueOf(height));
        newExif.setAttribute(ExifInterface.TAG_ORIENTATION, "0");
        //保存
        newExif.saveAttributes();
    }

    @Override
    protected void onPostExecute(@Nullable Throwable t) {
        if (mCropCallback != null) {
            if (t == null) {
                //接口回调，over
                mCropCallback.onBitmapCropped(Uri.fromFile(new File(mImageOutputPath)));
            } else {
                mCropCallback.onCropFailure(t);
            }
        }
    }

}
```

# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidProgrammerLogo.jpg)

# 总结
uCrop功能强大，对于我来说，有很多东西值得学习，难点如Rect包含问题（其实这块还不是很理解），新知识如ExifInterface操作图片，BitmapFactory显示图片的知识点温故等，还有自定义左右滑的标尺，都是不错的学习源码。抛砖引玉至此，over。