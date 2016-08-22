title: Android调用系统相机和相册-填坑篇
date: 2016-05-24 08:56:59
categories: [Android]
tags: [Camera，Album]
---

> 之前分享了[Android调用系统相机和相册](http://wuxiaolong.me/2014/11/10/Android-Photograph_Album/)，但是由于国内Android机型繁多，各家都自己的rom，调用系统的还是会出现不少问题：

# 拍照后照片被旋转
这种情况是使用Camera拍照以后，得到的照片会被自动旋转（90°、180°、270°）。解决方案：

1、读取图片的旋转属性
<!--more-->
```java 
/**
 * 读取图片的旋转的角度
 *
 * @param path
 *            图片绝对路径
 * @return 图片的旋转角度
 */
private int getBitmapDegree(String path) {
    int degree = 0;
    try {
        // 从指定路径下读取图片，并获取其EXIF信息
        ExifInterface exifInterface = new ExifInterface(path);
        // 获取图片的旋转信息
        int orientation = exifInterface.getAttributeInt(ExifInterface.TAG_ORIENTATION,
                ExifInterface.ORIENTATION_NORMAL);
        switch (orientation) {
        case ExifInterface.ORIENTATION_ROTATE_90:
            degree = 90;
            break;
        case ExifInterface.ORIENTATION_ROTATE_180:
            degree = 180;
            break;
        case ExifInterface.ORIENTATION_ROTATE_270:
            degree = 270;
            break;
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    return degree;
}
```
2、将图片按照某个角度进行旋转
```java
/**
 * 将图片按照某个角度进行旋转
 *
 * @param bm
 *            需要旋转的图片
 * @param degree
 *            旋转角度
 * @return 旋转后的图片
 */

public static Bitmap rotateBitmapByDegree(Bitmap bm, int degree) {
    Bitmap returnBm = null;
  
    // 根据旋转角度，生成旋转矩阵
    Matrix matrix = new Matrix();
    matrix.postRotate(degree);
    try {
        // 将原始图片按照旋转矩阵进行旋转，并得到新的图片
        returnBm = Bitmap.createBitmap(bm, 0, 0, bm.getWidth(), bm.getHeight(), matrix, true);
    } catch (OutOfMemoryError e) {
    }
    if (returnBm == null) {
        returnBm = bm;
    }
    if (bm != returnBm) {
        bm.recycle();
    }
    return returnBm;
}
```
详见博客：https://www.baidufe.com/item/4bb733d9999c53cb8fed.html

# 调用系统相册，拿不到绝对路径
上篇的方法，我在大部分手机测试是可行，但是一些机型还是拿不到，解决方案：
```java
public class AbsolutePathUtil {
    public static String getAbsolutePath(final Context context, final Uri uri) {
        // DocumentProvider
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT && DocumentsContract.isDocumentUri(context, uri)) {
            // ExternalStorageProvider
            if (isExternalStorageDocument(uri)) {
                final String docId = DocumentsContract.getDocumentId(uri);
                final String[] split = docId.split(":");
                final String type = split[0];

                if ("primary".equalsIgnoreCase(type)) {
                    return Environment.getExternalStorageDirectory() + "/" + split[1];
                }

                // TODO handle non-primary volumes
            }
            // DownloadsProvider
            else if (isDownloadsDocument(uri)) {

                final String id = DocumentsContract.getDocumentId(uri);
                final Uri contentUri = ContentUris.withAppendedId(
                        Uri.parse("content://downloads/public_downloads"), Long.valueOf(id));

                return getDataColumn(context, contentUri, null, null);
            }
            // MediaProvider
            else if (isMediaDocument(uri)) {
                final String docId = DocumentsContract.getDocumentId(uri);
                final String[] split = docId.split(":");
                final String type = split[0];

                Uri contentUri = null;
                if ("image".equals(type)) {
                    contentUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
                } else if ("video".equals(type)) {
                    contentUri = MediaStore.Video.Media.EXTERNAL_CONTENT_URI;
                } else if ("audio".equals(type)) {
                    contentUri = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI;
                }

                final String selection = "_id=?";
                final String[] selectionArgs = new String[]{
                        split[1]
                };

                return getDataColumn(context, contentUri, selection, selectionArgs);
            }
        }
        // MediaStore (and general)
        else if ("content".equalsIgnoreCase(uri.getScheme())) {
            return getDataColumn(context, uri, null, null);
        }
        // File
        else if ("file".equalsIgnoreCase(uri.getScheme())) {
            return uri.getPath();
        }

        return null;
    }

    /**
     * Get the value of the data column for this Uri. This is useful for
     * MediaStore Uris, and other file-based ContentProviders.
     *
     * @param context       The context.
     * @param uri           The Uri to query.
     * @param selection     (Optional) Filter used in the query.
     * @param selectionArgs (Optional) Selection arguments used in the query.
     * @return The value of the _data column, which is typically a file path.
     */
    public static String getDataColumn(Context context, Uri uri, String selection, String[] selectionArgs) {

        Cursor cursor = null;
        final String column = "_data";
        final String[] projection = {
                column
        };

        try {
            cursor = context.getContentResolver().query(uri, projection, selection, selectionArgs,
                    null);
            if (cursor != null && cursor.moveToFirst()) {
                final int column_index = cursor.getColumnIndexOrThrow(column);
                return cursor.getString(column_index);
            }
        } finally {
            if (cursor != null)
                cursor.close();
        }
        return null;
    }


    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is ExternalStorageProvider.
     */
    public static boolean isExternalStorageDocument(Uri uri) {
        return "com.android.externalstorage.documents".equals(uri.getAuthority());
    }

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is DownloadsProvider.
     */
    public static boolean isDownloadsDocument(Uri uri) {
        return "com.android.providers.downloads.documents".equals(uri.getAuthority());
    }

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is MediaProvider.
     */
    public static boolean isMediaDocument(Uri uri) {
        return "com.android.providers.media.documents".equals(uri.getAuthority());
    }
}
```
详见stackoverflow：http://stackoverflow.com/questions/13209494/how-to-get-the-full-file-path-from-uri

# 裁剪
拿到图片的绝对路径，有可能需要对它进行裁剪，这里当然也可以调用系统的裁剪，不过我推荐UCrop，功能好强大，简单的使用方法：
```java
/**
 * 启动裁剪
 */
public static String startUCrop(Activity activity, String sourceFilePath, int requestCode, float aspectRatioX, float aspectRatioY) {
    Uri sourceUri = Uri.fromFile(new File(sourceFilePath));
    File outDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
    if (!outDir.exists()) {
        outDir.mkdirs();
    }
    File outFile = new File(outDir, System.currentTimeMillis() + ".jpg");
    String cameraScalePath = outFile.getAbsolutePath();
    Uri destinationUri = Uri.fromFile(outFile);
    UCrop uCrop = UCrop.of(sourceUri, destinationUri);
    UCrop.Options options = new UCrop.Options();
    options.setAllowedGestures(UCropActivity.SCALE, UCropActivity.ROTATE, UCropActivity.ALL);
    options.setHideBottomControls(true);
    options.setFreeStyleCropEnabled(true);
    uCrop.withOptions(options);
    uCrop.withAspectRatio(aspectRatioX, aspectRatioY);
    uCrop.start(activity, requestCode);
    return cameraScalePath;
}
```
详见github：https://github.com/Yalantis/uCrop

# 微信公众号
我的微信公众号：吴小龙同学，不止于技术分享，每天进步一点点，欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/qrcode_wuxiaolong.jpg)
