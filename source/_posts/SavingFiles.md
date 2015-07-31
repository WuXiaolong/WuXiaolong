title: Saving Data-Saving Files
date: 2015-07-13
categories: [Android]
tags: [Android,SQLite]
---
1、需要权限

```js
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

2、存数据

```java
 public static final File SDCardDir = Environment.getExternalStorageDirectory(); //获取SDCard目录
    public static final String NAME = "ApkSample/";

    public static void saveToFile(String result, String filename) {
        try {
            // 判断SD卡是否存在，并且是否具有读写权限
            if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)) {
                File saveFile = new File(SDCardDir + File.separator + NAME, filename);
                if (!saveFile.getParentFile().exists()) {
                    saveFile.getParentFile().mkdirs();
                }

                FileOutputStream outStream = new FileOutputStream(saveFile);
                outStream.write(result.getBytes());
                outStream.close();
            } else {
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
```

3、读取数据

```java
 public static String getFileData(String filename) {
        String result = null;
        File file = new File(SDCardDir + File.separator + NAME, filename);
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
            result = stringBuffer.toString();
        }
        return result;
    }
```
