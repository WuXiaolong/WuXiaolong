title: Android Retrofit 2.0 使用-补充篇
date: 2016-06-14 08:48:53
tags: Retrofit 
category: Retrofit 
---
推荐阅读，猛戳：
1、[Android MVP 实例](http://wuxiaolong.me/2015/09/23/AndroidMVPSample/)
2、[Android Retrofit 2.0使用](http://wuxiaolong.me/2016/01/15/retrofit/)
3、[RxJava](http://wuxiaolong.me/2016/01/18/rxjava/)
4、[RxBus](http://wuxiaolong.me/2016/04/07/rxbus/)
5、[Android MVP+Retrofit+RxJava实践小结](http://wuxiaolong.me/2016/06/12/mvpRetrofitRxjava/)

> 之前分享的[Android Retrofit 2.0 使用](http://wuxiaolong.me/2016/01/15/retrofit/)，属于基本的使用，实际开发还远远不够，因此对其补充，主要在Retrofit配置和接口参数。

# 添加依赖
app/build.gradle
```
 compile 'com.squareup.retrofit2:retrofit:2.0.2'
```

# Retrofit配置
首先Builder()，得到OkHttpClient.Builder对象builder 
```
 OkHttpClient.Builder builder = new OkHttpClient.Builder();
```
## Log信息拦截器
Debug可以看到，网络请求，打印Log信息，发布的时候就不需要这些log
1、添加依赖
app/build.gradle
```
 compile 'com.squareup.okhttp3:logging-interceptor:3.1.2'
```

2、Log信息拦截器
```
if (BuildConfig.DEBUG) {
    // Log信息拦截器
    HttpLoggingInterceptor loggingInterceptor = new HttpLoggingInterceptor();
    loggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
    //设置 Debug Log 模式
    builder.addInterceptor(loggingInterceptor);
}
```
## 缓存机制
无网络时，也能显示数据
```
File cacheFile = new File(DemoApplication.getContext().getExternalCacheDir(), "WuXiaolongCache");
Cache cache = new Cache(cacheFile, 1024 * 1024 * 50);
Interceptor cacheInterceptor = new Interceptor() {
    @Override
    public Response intercept(Chain chain) throws IOException {
        Request request = chain.request();
        if (!AppUtils.networkIsAvailable(DemoApplication.getContext())) {
            request = request.newBuilder()
                    .cacheControl(CacheControl.FORCE_CACHE)
                    .build();
        }
        Response response = chain.proceed(request);
        if (AppUtils.networkIsAvailable(DemoApplication.getContext())) {
            int maxAge = 0;
            // 有网络时 设置缓存超时时间0个小时
            response.newBuilder()
                    .header("Cache-Control", "public, max-age=" + maxAge)
                    .removeHeader("WuXiaolong")// 清除头信息，因为服务器如果不支持，会返回一些干扰信息，不清除下面无法生效
                    .build();
        } else {
            // 无网络时，设置超时为4周
            int maxStale = 60 * 60 * 24 * 28;
            response.newBuilder()
                    .header("Cache-Control", "public, only-if-cached, max-stale=" + maxStale)
                    .removeHeader("nyn")
                    .build();
        }
        return response;
    }
};
builder.cache(cache).addInterceptor(cacheInterceptor);
```

## 公共参数
可能接口有某些参数是公共的，不可能一个个接口都去加吧
```
//公共参数
Interceptor addQueryParameterInterceptor = new Interceptor() {
    @Override
    public Response intercept(Chain chain) throws IOException {
        Request originalRequest = chain.request();
        Request request;
        String method = originalRequest.method();
        Headers headers = originalRequest.headers();
        HttpUrl modifiedUrl = originalRequest.url().newBuilder()
                // Provide your custom parameter here
                .addQueryParameter("platform", "android")
                .addQueryParameter("version", "1.0.0")              
                .build();
        request = originalRequest.newBuilder().url(modifiedUrl).build();
        return chain.proceed(request);
    }
};
//公共参数
builder.addInterceptor(addQueryParameterInterceptor);
```

## 设置头
有的接口可能对请求头要设置
```
Interceptor headerInterceptor = new Interceptor() {
    @Override
    public Response intercept(Chain chain) throws IOException {
        Request originalRequest = chain.request();
        Request.Builder requestBuilder = originalRequest.newBuilder()
                .header("AppType", "TPOS")
                .header("Content-Type", "application/json")
                .header("Accept", "application/json")
                .method(originalRequest.method(), originalRequest.body());
        Request request = requestBuilder.build();
        return chain.proceed(request);
    }
};
//设置头
builder.addInterceptor(headerInterceptor );
```

## 设置cookie
服务端可能需要保持请求是同一个cookie，主要看各自需求
1、app/build.gradle
```
 compile 'com.squareup.okhttp3:okhttp-urlconnection:3.2.0'
```
2、设置cookie
```
CookieManager cookieManager = new CookieManager();
cookieManager.setCookiePolicy(CookiePolicy.ACCEPT_ALL);
builder.cookieJar(new JavaNetCookieJar(cookieManager));
```
## 设置超时和重连
希望超时时能重连
```
 //设置超时
 builder.connectTimeout(15, TimeUnit.SECONDS);
 builder.readTimeout(20, TimeUnit.SECONDS);
 builder.writeTimeout(20, TimeUnit.SECONDS);
 //错误重连
 builder.retryOnConnectionFailure(true);
```

最后将这些配置设置给retrofit：
```
OkHttpClient okHttpClient = builder.build();
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(ApiStores.API_SERVER_URL)
        //设置 Json 转换器
        .addConverterFactory(GsonConverterFactory.create())
        //RxJava 适配器
        .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
        .client(okHttpClient)
        .build();
```
## 完整配置
```
public class AppClient {
    public static Retrofit retrofit = null;

    public static Retrofit retrofit() {
        if (retrofit == null) {
	         OkHttpClient.Builder builder = new OkHttpClient.Builder();
            /**
             *设置缓存，代码略
             */
                      
            /**
             *  公共参数，代码略
             */
           
            /**
             * 设置头，代码略
             */           
           
			 /**
             * Log信息拦截器，代码略
             */
            
			 /**
             * 设置cookie，代码略
             */
            
             /**
             * 设置超时和重连，代码略
             */

            //以上设置结束，才能build(),不然设置白搭
            OkHttpClient okHttpClient = builder.build();

            retrofit = new Retrofit.Builder()
                    .baseUrl(ApiStores.API_SERVER_URL)                  
                    .client(okHttpClient)
                    .build();
        }
        return retrofit;

    }
}
```
# 接口参数
## Path
类似这样链接：http://wuxiaolong.me/2016/01/15/retrofit/
```
 @GET("2016/01/15/{retrofit}")
 Call<ResponseBody> getData(@Path("retrofit") String retrofit);
```
即您传的参数retrofit内容会替换大括号里的内容。
## Query
类似这样链接：http://wuxiaolong.me/v1?ip=202.202.33.33&name=WuXiaolong
```
@GET("v1")
Call<ResponseBody> getData(@Query("ip") String ip,@Query("name") String name);
```
# Field
表单提交，如登录
```
 @FormUrlEncoded
 @POST("v1/login")
 Call<ResponseBody> userLogin(@Field("phone") String phone, @Field("password") String password);
```
# 传json格式
如果参数是json格式，如：
```
{		
    "apiInfo": {		
        "apiName": "WuXiaolong",		
        "apiKey": "666"		
    }		
}		

```
建立Bean
```
 public class ApiInfo {
        private ApiInfoBean apiInfo;

        public ApiInfoBean getApiInfo() {
            return apiInfo;
        }

        public void setApiInfo(ApiInfoBean apiInfo) {
            this.apiInfo = apiInfo;
        }

        public class ApiInfoBean {
            private String apiName;
            private String apiKey;
            //省略get和set方法
        }
    }
```
ApiStores
```
@POST("client/shipper/getCarType")
Call<ResponseBody> getData(@Body ApiInfo apiInfo);
```       
代码调用
```
ApiInfo apiInfo = new ApiInfo();
ApiInfo.ApiInfoBean apiInfoBean = apiInfo.new ApiInfoBean();
apiInfoBean.setApiKey("666");
apiInfoBean.setApiName("WuXiaolong");
apiInfo.setApiInfo(apiInfoBean);
//调接口
getData(apiInfo);
```

# 传数组
```
@GET("v1/enterprise/find")
Call<ResponseBody> getData(@Query("id") String id, @Query("linked[]") String... linked);
```
代码调用
```
String id="WuXiaolong"；
String[] s = new String[]{"WuXiaolong"};
 //调接口
getData(id, s);
```

# 传文件-单个
```
@Multipart
@POST("v1/create")
Call<ResponseBody> create(@Part("pictureName") RequestBody pictureName,  @Part MultipartBody.Part picture);
```
代码调用
```
RequestBody pictureNameBody = RequestBody.create(MediaType.parse(AppConstants.CONTENT_TYPE_FILE), "pictureName");
File picture= new File(path);
RequestBody requestFile = RequestBody.create(MediaType.parse(AppConstants.CONTENT_TYPE_FILE), picture);
// MultipartBody.Part is used to send also the actual file name
MultipartBody.Part picturePart = MultipartBody.Part.createFormData("picture", picture.getName(), requestFile);
//调接口
create(pictureNameBody, picturePart);
```

# 传文件-多个
```
@Multipart
@POST("v1/create")
Call<ResponseBody> create(@Part("pictureName") RequestBody pictureName,   @PartMap Map<String, RequestBody> params);
```
代码调用
```
RequestBody pictureNameBody = RequestBody.create(MediaType.parse(AppConstants.CONTENT_TYPE_FILE), "pictureName");
File picture= new File(path);
RequestBody requestFile = RequestBody.create(MediaType.parse(AppConstants.CONTENT_TYPE_FILE), picture);
Map<String, RequestBody> params = new HashMap<>();
params.put("picture\"; filename=\"" + picture.getName() + "", requestFile);
//调接口
create(pictureNameBody, params);
```
# AndroidProgrammer
我的微信公众号：Android高手进阶之路，让我们共同学习，每天进步一点点。欢迎微信扫一扫关注。
![](http://7q5c2h.com1.z0.glb.clouddn.com/AndroidProgrammerLogo.jpg)

# 关于作者
[点击查看](http://wuxiaolong.me/about/)