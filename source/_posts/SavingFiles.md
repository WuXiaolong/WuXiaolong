title: Saving Data-Saving Files
date: 2015-07-13
categories: [Android]
tags: [Android,SQLite,WeiYan]
---
>微言App中有个阅读量的功能，是直接将用户的阅读量保存在SD卡上，我封装成了工具类。

## 效果预览
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyanAppScreenshots.gif)

<!--more-->

## 实例APK
![](http://7q5c2h.com1.z0.glb.clouddn.com/weiyanAppDownload.png)

## 需要权限

```js
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

## ReadUtil.java

```java
 public class ReadUtil {
    public static final File SDCardDir = Environment.getExternalStorageDirectory(); //获取SDCard目录
    public static final String SD_ROOT_NAME = "WeiYan/";
    public static final String SD_FILE_NAME = "reading";

    public static void saveToFile(int readTotal) {
        try {
            // 判断SD卡是否存在，并且是否具有读写权限
            if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
                File saveFile = new File(SDCardDir + File.separator + SD_ROOT_NAME, SD_FILE_NAME);
                if (!saveFile.getParentFile().exists()) {
                    saveFile.getParentFile().mkdirs();
                }
                int nowReadingTotal = getReadedTotal() + readTotal;
                String readedTotal = nowReadingTotal + "";
                FileOutputStream outStream = new FileOutputStream(saveFile);
                outStream.write(readedTotal.getBytes());
                outStream.close();
            } else {
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static int getReadedTotal() {
        int readedTotal = 0;
        File file = new File(SDCardDir + File.separator + SD_ROOT_NAME, SD_FILE_NAME);
        if (file.exists() && file.isFile()) {
            StringBuffer stringBuffer = new StringBuffer();
            try {
                BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(new FileInputStream(file), "UTF-8"));
                int c;
                while ((c = bufferedReader.read()) != -1) {
                    stringBuffer.append((char) c);
                }
                bufferedReader.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
            String result = stringBuffer.toString();
            readedTotal = Integer.parseInt(result);
        }
        return readedTotal;
    }
}
```

## 如何调用
### 读取数据
```java
 ReadUtil.getReadedTotal()
```

### 保存阅读量
```java
ReadUtil.saveToFile(readTotal);
```