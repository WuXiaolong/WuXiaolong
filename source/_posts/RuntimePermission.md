title: Android 6.0 RuntimePermission
date: 2016-02-04 11:40:57
tags: RuntimePermission
category: Android 
---
> android6.0棉花糖，app将不会在安装的时候授予权限。取而代之的是，app不得不在运行时一个一个询问用户授予权限。

```
android {
    compileSdkVersion 23
    ...
  
    defaultConfig {
        ...
        targetSdkVersion 23
        ...
    }
}
```
如何app/build.gradle是23的话，在6.0上运行一些权限，没有做处理，会直接崩溃：
```xml
...
Caused by: java.lang.SecurityException: Permission Denial: 
opening provider com.android.providers.contacts.ContactsProvider2 from ProcessRecord
{c3b3c66 16712:com.wuxiaolong.apksample/u0a62} (pid=16712, uid=10062)
requires android.permission.READ_CONTACTS or android.permission.WRITE_CONTACTS
...
```
如果targetSdkVersion 22，就不会发生这样的错误，下面就来实践下这个6.0运行的权限。
<!--more-->
# 效果预览
按照惯例，先上效果图:
![](http://7q5c2h.com1.z0.glb.clouddn.com/RuntimePermission1.png)
说明：第一次请求授权，是没有“不再询问”，拒绝后，再次请求授权，就会出现“不再询问”。

# 实践
## AndroidManifest.xml
```xml
<uses-permission android:name="android.permission.READ_CONTACTS" />
```
## 代码调用
在需要使用到权限之前调用以下代码：
```java
private void mayRequestContacts() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            switch (checkSelfPermission(Manifest.permission.READ_CONTACTS)) {
                case PackageManager.PERMISSION_GRANTED:
                    // 已有授权
                    Log.i("wxl", "已有授权");
                    break;
                case PackageManager.PERMISSION_DENIED:
                    // 没有权限：尚未请求过权限，
                    // 或者请求授权被拒绝，
                    // 或者曾经授权过，但被用户在设置中禁用权限
                    Log.i("wxl", "没有权限：尚未请求过权限，或者请求授权被拒绝，" +
                            "或者曾经授权过， 但被用户在设置中禁用权限");
                    /**
                     * 如果用户勾上了“不再询问”，这时候就不应弹请求权限的对话框，
                     * 可以在请求权限之前用
					 * shouldShowRequestPermissionRationale判断用户是否拒绝过，
                     * 如果返回true，表示用户拒绝过。
                     */
                    Log.i("wxl", "shouldShowRequestPermissionRationale==" 
					+ shouldShowRequestPermissionRationale(READ_CONTACTS));
                    if (shouldShowRequestPermissionRationale(READ_CONTACTS)) {
                        Snackbar.make(mEmailView, 
						R.string.permission_rationale, Snackbar.LENGTH_INDEFINITE)
                                .setAction(android.R.string.ok, new View.OnClickListener() {
                                    @Override
                                    @TargetApi(Build.VERSION_CODES.M)
                                    public void onClick(View v) {
                                        requestPermissions(new String[]{READ_CONTACTS}, 
										REQUEST_READ_CONTACTS);
                                    }
                                }).show();
                    } else {
                        requestPermissions(new String[]{READ_CONTACTS}, 
						REQUEST_READ_CONTACTS);
                    }
                    break;
                default:
                    break;
            }
        }

    }
```
checkSelfPermission：检查权限
requestPermissions：请求权限
shouldShowRequestPermissionRationale：判断用户是否拒绝过

## 授权回调
不管是允许或拒绝，都会回调：
```java
 @Override
    public void onRequestPermissionsResult(int requestCode,
     @NonNull  String[] permissions,@NonNull  int[] grantResults) {
        if (requestCode == REQUEST_READ_CONTACTS) {
            if (grantResults.length == 1 &&
                    grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                // 授权请求被通过，读取通讯录
                Log.i("wxl", "授权请求被通过，读取通讯录");
            } else {
                Log.i("wxl", "授权请求不被通过");
            }
        }
    }
```
# 多个权限
```java
 @TargetApi(Build.VERSION_CODES.M)
    private void checkPermissions() {
        List<String> permissionsNeeded = new ArrayList<>();
        final List<String> permissionsList = new ArrayList<>();
        if (deniedPermission(permissionsList, Manifest.permission.ACCESS_FINE_LOCATION))
            permissionsNeeded.add("GPS");
        if (deniedPermission(permissionsList, Manifest.permission.READ_CONTACTS))
            permissionsNeeded.add("Read Contacts");
        if (permissionsList.size() > 0) {
            if (permissionsNeeded.size() > 0) {
                // Need Rationale
                String message = "You need to grant access to " + permissionsNeeded.get(0);
                for (int i = 1; i < permissionsNeeded.size(); i++)
                    message = message + ", " + permissionsNeeded.get(i);
                requestPermissions(permissionsList.toArray(new String[permissionsList.size()]), REQUEST_PERMISSIONS);
            }
        }
    }

    @TargetApi(Build.VERSION_CODES.M)
    private boolean deniedPermission(List<String> permissionsList, String permission) {
        if (checkSelfPermission(permission) == PackageManager.PERMISSION_DENIED) {
            permissionsList.add(permission);
            // true，表示用户拒绝过
            if (shouldShowRequestPermissionRationale(permission)) {
                return true;
            }
        }
        return false;
    }

    /**
     * Callback received when a permissions request has been completed.
     */
    @Override
    public void onRequestPermissionsResult(int requestCode,
                                           String[] permissions, int[] grantResults) {       
        if (requestCode == REQUEST_PERMISSIONS) {
            Map<String, Integer> perms = new HashMap<String, Integer>();
            // Initial
            perms.put(Manifest.permission.ACCESS_FINE_LOCATION, PackageManager.PERMISSION_GRANTED);
            perms.put(Manifest.permission.READ_CONTACTS, PackageManager.PERMISSION_GRANTED);
            // Fill with results
            for (int i = 0; i < permissions.length; i++)
                perms.put(permissions[i], grantResults[i]);
            // Check for ACCESS_FINE_LOCATION
            if (perms.get(Manifest.permission.ACCESS_FINE_LOCATION) == PackageManager.PERMISSION_GRANTED
                    && perms.get(Manifest.permission.READ_CONTACTS) == PackageManager.PERMISSION_GRANTED) {
                // All Permissions Granted
                Log.i("wxl", "授权请求被通过");
            } else {
                // Permission Denied
                Log.i("wxl", "授权请求不被通过");
            }
        }
    }
```
# 权限分组
权限那么多，如果一个个判断，岂不是会疯掉，如图：
![](http://7q5c2h.com1.z0.glb.clouddn.com/RuntimePermission2.png)
同一组的任何一个权限被授权了，其他权限也自动被授权。例如，一旦WRITE_CONTACTS被授权了，app也有READ_CONTACTS和GET_ACCOUNTS了。

# 关于作者
[点击查看](http://wuxiaolong.me/about/)

# 附录
[Android M 新的运行时权限开发者需要知道的一切](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0830/3387.html)
[Android Runtime Permission测试](http://blog.piasy.com/Android-Runtime-Permission-Test/)

