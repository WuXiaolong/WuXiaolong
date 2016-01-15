title: Android Retrofit 2.0使用
date: 2016-01-15 11:23:25
tags: Retrofit 
category: Android
---
# app/build.gradle
```java
compile 'com.squareup.retrofit2:retrofit:2.0.0-beta3'
```
# 声明接口
```java
 /**
 * Call<T> get();必须是这种形式,这是2.0之后的新形式
 * 如果不需要转换成Json数据,可以用了ResponseBody;
 * 你也可以使用Call<GsonBean> get();这样的话,需要添加Gson转换器
 */
public interface ApiStores {
    @GET("adat/sk/{cityId}.html")
    Call<ResponseBody> getWeather(@Path("cityId") String cityId);
}
```
<!--more-->
如果链接是http://ip.taobao.com/service/getIpInfo.php?ip=202.202.33.33
```java
 @GET("http://ip.taobao.com/service/getIpInfo.php?")
    Call<ResponseBody> getWeather(@Query("ip") String ip);
```
# 接口调用
```java
 Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://www.weather.com.cn/")//域名
                .build();
        ApiStores apiStores = retrofit.create(ApiStores.class);
        Call<ResponseBody> call = apiStores.getWeather("101010100");
```
如果@GET("http://ip.taobao.com/service/getIpInfo.php?")，则baseUrl无效。
注意这个任务是网络任务，不要忘记给程序加入网络权限<uses-permission android:name="android.permission.INTERNET" />
## 同步调用
```java
  try {
            Response<ResponseBody> bodyResponse = call.execute();
            String body = bodyResponse.body().string();//获取返回体的字符串
            Log.i("wxl", "body=" + body);
        } catch (IOException e) {
            e.printStackTrace();
        }
```
同步需要处理android.os.NetworkOnMainThreadException
## 异步调用
```java
call.enqueue(new Callback<ResponseBody>() {
            @Override
            public void onResponse(Response<ResponseBody> response) {
                try {
                    Log.i("wxl", "response=" + response.body().string());
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onFailure(Throwable t) {
                Log.i("wxl", "onFailure=" + t.getMessage());
            }
        });
```
## 移除请求
```java
call.cancel();
```
# JSON解析库
Retrofit 2 现在支持许多种解析方式来解析响应数据，包括 Moshi，一个由Square 创建的高效JSON解析库。
## app/build.gradle
```java
compile 'com.squareup.retrofit2:converter-gson:2.0.0-beta3'
```
## jsonschema2pojo
访问[jsonschema2pojo](http://www.jsonschema2pojo.org/)，自动生成Java对象
![](http://7q5c2h.com1.z0.glb.clouddn.com/retrofit1.png)
这里如果选择Gson，生成的代码中存在@Generated注解，Android默认并没有javax.annotation library。如果你希望保留@Generated注解，你需要添加如下的依赖。
```java
compile 'org.glassfish:javax.annotation:10.0-b28'
```
或者，你可以直接删除这个注解，完全没有问题。
解析网站http://ip.taobao.com/service/getIpInfo.php?ip=202.202.33.33
```java
public class JsonObjectResult<T> {

    private T data;


    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
public class Data {

    private String country;
    private String countryId;
    private String area;
    private String areaId;
    private String region;
    private String regionId;
    private String city;
    private String cityId;
    private String county;
    private String countyId;
    private String isp;
    private String ispId;
    private String ip;
    这里省略get和set方法
}
```
ApiStores：
```java
  @GET("service/getIpInfo.php")
    Call<JsonObjectResult<Data>> getIP(@Query("ip") String ip);
```
调用：
```

 Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://ip.taobao.com/")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
        ApiStores apiStores = retrofit.create(ApiStores.class);
        Call<JsonObjectResult<Data>> call = apiStores.getIP("202.202.33.33");
        call.enqueue(new Callback<JsonObjectResult<Data>>() {
            @Override
            public void onResponse(Response<JsonObjectResult<Data>> response) {
                Log.i("wxl", "response=" + response.body().getData().getCity());
            }

            @Override
            public void onFailure(Throwable t) {

            }
        });
}
```

未完……

# 附录
[retrofit官方文档](http://square.github.io/retrofit/)
[用 Retrofit 2 简化 HTTP 请求](https://realm.io/cn/news/droidcon-jake-wharton-simple-http-retrofit-2/)
[使用Retrofit请求API数据](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/1016/3588.html)

# 剩者为王
我的Android技术交流群，群名寓意很简单，经过时间洗礼，最终剩下的才是王者，欢迎“剩友”。
剩者为王③群：370527306 <a target="_blank" href="http://shang.qq.com/wpa/qunwpa?idkey=0a992ba077da4c8325cbfef1c9e81f0443ffb782a0f2135c1a8f7326baac58ac"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="剩者为王③群" title="剩者为王③群"></a>
