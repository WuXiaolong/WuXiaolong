title: Android之Canvas中内容保存为图片
date: 2015-04-23 
categories: [Android]
tags: [Android,Canvas]
---

```java
Bitmap bm = Bitmap.createBitmap(320, 480, Config.ARGB_8888);  
        Canvas canvas = new Canvas(bm);  
        Paint p = new Paint();  
        canvas.drawRect(50, 50, 200, 200, p);  
        canvas.save(Canvas.ALL_SAVE_FLAG );  
        canvas.restore();  
          
        File f = new File("/sdcard/0.png");  
        FileOutputStream fos = null;  
        try {  
            fos = new FileOutputStream(f);  
            bm.compress(Bitmap.CompressFormat.PNG, 50, fos);  
        } catch (FileNotFoundException e) {  
            // TODO Auto-generated catch block  
            e.printStackTrace();  
        }  
        // 其次把文件插入到系统图库
    try {
        MediaStore.Images.Media.insertImage(context.getContentResolver(),
				file.getAbsolutePath(), fileName, null);
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }
    // 最后通知图库更新
    context.sendBroadcast(new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, Uri.parse("file://" + path)));
```

这样就把Canvas中的内容保存成了sd卡上的一个png图片。简单说一下原理，自己create一个bitmap，然后让Canvas通过这个bitmap创建一个实例，然后在该canvas上绘制的内容都会画
在该bitmap上，保存Canvas的图层，然后把bitmap写到sd卡上的文件。注意：要在AndroidManifest.xml中添加在sd卡上读写文件的权限

```js
<!-- 在SDCard中创建与删除文件权限 -->     
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>     
<!-- 往SDCard写入数据权限 -->     
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>  
```

Canvas加入图片

```java
Canvas canvas;
Bitmap bmp = BitmapFactory.decodeResource(getResources(), R.drawable.pic180);  
            canvas.drawColor(Color.BLACK);  
            canvas.drawBitmap(bmp, 10, 10, null);  
```

