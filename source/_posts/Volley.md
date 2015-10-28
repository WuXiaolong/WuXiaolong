title: Android Volley使用
date: 2015-10-16 16:46:49
tags: [Android,Volley]
category: Android
---
## 导入Volley
Volley貌似没有官方的Maven repository，你必须依赖官方的源代码。从它的repository 下载Volley源码。使用Git命令：

```java
git clone https://android.googlesource.com/platform/frameworks/volley
```
不过还有第三种方法，你可以在build.gradle 文件的依赖部分添加这行代码：

```java
compile 'com.mcxiaoke.volley:library-aar:1.0.15'
```
<!--more-->

## 准备工作
* 权限
```js
<uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
* 变量
 RequestQueue mRequestQueue = Volley.newRequestQueue(this);

## 实践
Volley实现了三种常见的请求类型：
StringRequest
JsonObjectRequest
ImageRequest

### StringRequest
```java
private void stringRequest() {

        showMsg.setText("");
        progressBar.setVisibility(View.VISIBLE);
        String url = "http://wuxiaolong.me/";
        StringRequest stringRequest = new StringRequest(Request.Method.GET, url, new Response.Listener<String>() {
            @Override
            public void onResponse(String response) {
                showMsg.setText("stringRequest==" + response);
                progressBar.setVisibility(View.GONE);
                Log.d("wxl", "stringRequest==" + response);
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                Log.d("wxl", "StringRequest error==" + error);
                progressBar.setVisibility(View.GONE);
            }
        });
        stringRequest.setTag("stringRequest");
        //如果要取消stringRequest请求，只需简单的添加下面的一行代码：
        // mRequestQueue.cancelAll("stringRequest");
        mRequestQueue.add(stringRequest);
    }
```

### JsonObjectRequest
```java
 private void jsonObjectRequest() {
        showMsg.setText("");
        progressBar.setVisibility(View.VISIBLE);
        String url = "http://httpbin.org/get?site=code&network=tutsplus";
        JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.GET, url, null, new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                showMsg.setText("JsonObjectRequest==" + response);
                progressBar.setVisibility(View.GONE);
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                showMsg.setText("JsonObjectRequest error==" + error);
                progressBar.setVisibility(View.GONE);
            }
        });
        mRequestQueue.add(jsonObjectRequest);
    }
```
如果请求JSONArray ，只需用JsonArrayRequest 替代aJsonObjectRequest就可以了

### ImageRequest
有三种请求图片的方法。
* ImageRequest
```java
/**
     * 第一个参数是图片的url，
     * 第二个是结果的listener，
     * 第三、第四个参数是maxWidth（最大宽度） 和 maxHeight（最大高度），你可以设置为0来忽略他们。
     * 第五个参数是用于计算图片所需大小的ScaleType，
     * 第六个参数是用于指定图片压缩方式的参数，建议总是使用 Bitmap.Config.ARGB_8888
     */
    private void iamgeRequest() {

        showMsg.setText("");
        progressBar.setVisibility(View.VISIBLE);
        showImage.setImageBitmap(null);
        String url = "http://p3.so.qhimg.com/t012befc69d1b6d8f88.jpg";
        ImageRequest imageRequest = new ImageRequest(url, new Response.Listener<Bitmap>() {
            @Override
            public void onResponse(Bitmap response) {
                showImage.setImageBitmap(response);
                progressBar.setVisibility(View.GONE);
            }
        }, 0, 0, ImageView.ScaleType.FIT_XY, Bitmap.Config.ARGB_8888, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                progressBar.setVisibility(View.GONE);
                showMsg.setText("ImageRequest error==" + error);
            }
        });
        mRequestQueue.add(imageRequest);
    }
```

* imageLoader+NetworkImageView
NetworkImageView xml
```js
 <com.android.volley.toolbox.NetworkImageView
                android:id="@+id/networkImageView"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginTop="10dp"
                android:layout_gravity="center_horizontal"
                android:src="@mipmap/ic_launcher" />
```
NetworkImageView在加载图片的时候会自动获取自身控件的宽高，然后对比网络图片的宽度，再决定是否需要对图片进行压缩。也就是说，压缩过程是在内部完全自动化的，并不需要我们关心，NetworkImageView会始终呈现给我们一张大小刚刚好的网络图片，不会多占用任何一点内存。

如果不想对图片进行压缩的话，只需要在布局文件中把NetworkImageView的layout_width和layout_height都设置成wrap_content就可以了，这样NetworkImageView就会将该图片的原始大小展示出来，不会进行任何压缩。
```java
  /**
     * 数量庞大的ImageRequests，比如生成一个带有图片的ListView
     */
    private void imageLoader() {

        showMsg.setText("");
        showImage.setImageBitmap(null);
        progressBar.setVisibility(View.VISIBLE);
        String url = "http://p0.so.qhimg.com/t0191efbe9e784617e5.jpg";
        int maxSize = 10 * 1024 * 1024;//设置缓存图片的大小为10M
        final LruCache<String, Bitmap> lruCache = new LruCache<String, Bitmap>(maxSize) {
            @Override
            protected int sizeOf(String key, Bitmap bitmap) {
                return bitmap.getRowBytes() * bitmap.getHeight();
            }
        };
        ImageLoader imageLoader = new ImageLoader(mRequestQueue, new ImageLoader.ImageCache() {
            @Override
            public Bitmap getBitmap(String url) {
                return lruCache.get(url);
            }

            @Override
            public void putBitmap(String url, Bitmap bitmap) {
                lruCache.put(url, bitmap);
            }
        });
        imageLoader.get(url, new ImageLoader.ImageListener() {
            @Override
            public void onResponse(ImageLoader.ImageContainer response, boolean isImmediate) {
                showImage.setImageBitmap(response.getBitmap());
                progressBar.setVisibility(View.GONE);
            }

            @Override
            public void onErrorResponse(VolleyError error) {
                progressBar.setVisibility(View.GONE);
                showMsg.setText("ImageRequest error==" + error);
            }
        }, 0, 0, ImageView.ScaleType.FIT_XY);
		//以下是NetworkImageView部分
        networkImageView.setDefaultImageResId(R.mipmap.ic_launcher);
        networkImageView.setErrorImageResId(R.mipmap.ic_launcher);
        networkImageView.setImageUrl("http://p2.so.qhimg.com/t01ac4a335801991667.jpg", imageLoader);
    }

```

### postRequest
```java
 private void postRequest() {
        showMsg.setText("");
        progressBar.setVisibility(View.VISIBLE);
        String url = "http://httpbin.org/post";

        StringRequest postRequest = new StringRequest(Request.Method.POST, url,
                new Response.Listener<String>() {
                    @Override
                    public void onResponse(String response) {
                        try {
                            JSONObject jsonResponse = new JSONObject(response).getJSONObject("form");
                            String site = jsonResponse.getString("site"),
                                    network = jsonResponse.getString("network");
                            System.out.println("Site: " + site + "\nNetwork: " + network);
                            showMsg.setText("PostRequest==" + "Site: " + site + "\nNetwork: " + network);
                            progressBar.setVisibility(View.GONE);
                        } catch (JSONException e) {
                            e.printStackTrace();
                        }
                    }
                },
                new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        error.printStackTrace();
                        showMsg.setText("PostRequest error==" + error);
                        progressBar.setVisibility(View.GONE);
                    }
                }
        ) {
            @Override
            protected Map<String, String> getParams() {
                Map<String, String> params = new HashMap<>();
                // the POST parameters:
                params.put("site", "code");
                params.put("network", "tutsplus");
                return params;
            }
        };
        mRequestQueue.add(postRequest);
    }
```

## 取消Volley
```java
 @Override
    protected void onStop() {
        super.onStop();
        mRequestQueue.cancelAll(new RequestQueue.RequestFilter() {
            @Override
            public boolean apply(Request<?> request) {
                // do I have to cancel this?
                return true; // -> always yes
            }
        });
    }
```
## 源码地址
[https://github.com/WuXiaolong/VolleySample](https://github.com/WuXiaolong/VolleySample)

## 附录
[网络请求库Volley详解](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0526/2934.html)
[Android开源：网络框架volley使用(一)](http://glblong.blog.51cto.com/3058613/1406758)