title: Drawable /Bitmap、String/InputStream、Bitmap/byte[]互转
date: 2015-08-10 16:26:25
tags: [Drawable,Bitmap,String,InputStream,byte]
category: Android
---

## Drawable互转Bitmap
### Drawable转Bitmap
```java
Resources res = getResources();
Drawable drawable = res.getDrawable(R.drawable.myimage);
BitmapDrawable bd = (BitmapDrawable) d;
Bitmap bm = bd.getBitmap();
```
<!--more-->

```java
public static Bitmap drawableToBitmap(Drawable drawable) {       
        Bitmap bitmap = Bitmap.createBitmap(
                                        drawable.getIntrinsicWidth(),
                                        drawable.getIntrinsicHeight(),
                                        drawable.getOpacity() != PixelFormat.OPAQUE ? Bitmap.Config.ARGB_8888
                                                        : Bitmap.Config.RGB_565);
        Canvas canvas = new Canvas(bitmap);
        //canvas.setBitmap(bitmap);
        drawable.setBounds(0, 0, drawable.getIntrinsicWidth(), drawable.getIntrinsicHeight());
        drawable.draw(canvas);
        return bitmap;
}
```

### Bitmap转Drawable
```java
Bitmap bm=xxx; //xxx根据你的情况获取
BitmapDrawable bd=BitmapDrawable(bm);
BtimapDrawable是Drawable的子类，最终直接使用bd对象即可。

mPicPath//本地图片路径转成Bitmap格式
Bitmap pic = BitmapFactory.decodeFile(this.mPicPath);
		image.setImageBitmap(pic);
转成Bitmap格式
```

## String与InputStream相互转换

### String to InputStream
```java
String str = "String与InputStream相互转换";
InputStream   in_nocode   =   new   ByteArrayInputStream(str.getBytes());   
InputStream   in_withcode   =   new   ByteArrayInputStream(str.getBytes("UTF-8"));  
```
 
### InputStream to String
    这里提供几个方法。
方法1：
```java
  public String convertStreamToString(InputStream is) {   
   BufferedReader reader = new BufferedReader(new InputStreamReader(is));   
        StringBuilder sb = new StringBuilder();   
    
        String line = null;   
        try {   
            while ((line = reader.readLine()) != null) {   
                sb.append(line + "/n");   
            }   
        } catch (IOException e) {   
            e.printStackTrace();   
        } finally {   
            try {   
                is.close();   
            } catch (IOException e) {   
                e.printStackTrace();   
            }   
        }   
    
        return sb.toString();   
    }   
```
方法2：
```java
public   String   inputStream2String   (InputStream   in)   throws   IOException   { 
        StringBuffer   out   =   new   StringBuffer(); 
        byte[]   b   =   new   byte[4096]; 
        for   (int   n;   (n   =   in.read(b))   !=   -1;)   { 
                out.append(new   String(b,   0,   n)); 
        } 
        return   out.toString(); 
} 
```
方法3：
```java
public   static   String   inputStream2String(InputStream   is)   throws   IOException{ 
        ByteArrayOutputStream   baos   =   new   ByteArrayOutputStream(); 
        int   i=-1; 
        while((i=is.read())!=-1){ 
        baos.write(i); 
        } 
       return   baos.toString(); 
}
```


## Bitmap 和 byte[]互转
### Bitmap → byte[]
```java
private byte[] Bitmap2Bytes(Bitmap bm){
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    bm.compress(Bitmap.CompressFormat.PNG, 100, baos);
    return baos.toByteArray();   }
```
    
### byte[] → Bitmap
```java
   private Bitmap Bytes2Bimap(byte[] b){
                    if(b.length!=0){
                            return BitmapFactory.decodeByteArray(b, 0, b.length);
                    }
                    else {
                            return null;
                    }
          }
```