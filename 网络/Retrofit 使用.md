# Retrofit 注解使用

### 1、官方文档

源码地址：https://github.com/square/retrofit

使用文档:  https://square.github.io/retrofit/

### 

### 2、网络请求方法

#### 1、 @HTTP

```java

public interface GetRequest_Interface {
/**
     * method：网络请求的方法（区分大小写）
     * path：网络请求地址路径
     * hasBody：是否有请求体
     */
    @HTTP(method = "GET", path = "blog/{id}", hasBody = false)
    Call<ResponseBody> getCall(@Path("id") int id);
    // {id} 表示是一个变量
    // method 的值 retrofit 不会做处理，所以要自行保证准确
    
}
```



#### 1、@GET

```java
 
public interface GetRequest_Interface {
// @GET注解的作用:采用Get方法发送网络请求
    @GET("/user/login") 
    Call<Translation>  getCall();
    
}
```

### 3、标记

####  1、@FormUrlEncoded

表示发送form-encoded的数据 、每个键值对需要用@Filed来注解键名，随后的对象需要提供值。

```java
public interface GetRequest_Interface {

		/**
         *表明是一个表单格式的请求（Content-Type:application/x-www-form-urlencoded）
         * Field("username") 表示将后面的 String name 中name的取值作为 username 的值
         */
        @POST("/form")
        @FormUrlEncoded
        Call<ResponseBody> testFormUrlEncoded1(@Field("username") String name, @Field("age") int age);
}

// 使用 
 		GetRequest_Interface service = retrofit.create(GetRequest_Interface.class);
        // @FormUrlEncoded 
        Call<ResponseBody> call1 = service.testFormUrlEncoded1("zyb", 24);
```



#### 2、@Multipart

表示发送form-encoded的数据（适用于 有文件 上传的场景）、每个键值对需要用@Part来注解键名，随后的对象需要提供值。

```java
public interface GetRequest_Interface {	
		/**
         * {@link Part} 后面支持三种类型，{@link RequestBody}、{@link okhttp3.MultipartBody.Part} 、任意类型
         * 除 {@link okhttp3.MultipartBody.Part} 以外，其它类型都必须带上表单字段({@link okhttp3.MultipartBody.Part} 中已经包含了表单字段的信息)，
         */
        @POST("/form")
        @Multipart
        Call<ResponseBody> testFileUpload1(@Part("name") RequestBody name, @Part("age") RequestBody age, @Part 
                                           MultipartBody.Part file);
}

// 使用 
 		RequestBody name = RequestBody.create(textType, "zyb");
        RequestBody age = RequestBody.create(textType, "24");

        MultipartBody.Part filePart = MultipartBody.Part.createFormData("file", "test.txt", file);
        Call<ResponseBody> call3 = service.testFileUpload1(name, age, filePart);

```



### 4、网络请求参数

#### 4 .1、@Header & @Headers

​	添加请求头 &添加不固定的请求头

​	

```java
// @Header
@GET("user")
Call<User> getUser(@Header("Authorization") String authorization)

// @Headers
@Headers("Authorization: authorization")
@GET("user")
Call<User> getUser()

// 以上的效果是一致的。
// 区别在于使用场景和使用方式
// 1. 使用场景：@Header用于添加不固定的请求头，@Headers用于添加固定的请求头
// 2. 使用方式：@Header作用于方法的参数；@Headers作用于方法
```

#### 4 .2、@Body

- 作用：以 `Post`方式 传递 自定义数据类型 给服务器

- 特别注意：如果提交的是一个Map，那么作用相当于 `@Field`

```java
    @POST("/user/register")
    Observable<LoginResultBean> login(@Body RequestBody body);

// 使用1

  Map<String, String> mParam = new HashMap<>();
  mParam.put(AppConstants.PASSWORD, "1111");
  mParam.put(AppConstants.PHONE, AppConstants.PHONE_NUM);
  RequestBody body = RequestBody.create(MediaType.parse("application/json; charset=utf-8"), new          JSONObject(mParam).toString());

 // 使用2
    public HashMap<String, String> login(String xxx...) {
        HashMap<String, String> hashMap = new HashMap<>();
        hashMap.put("参数1", xxx);
        hashMap.put("参数2", xxx);
        hashMap.put("参数3", xxx);
        hashMap.put("参数4", xxx);
        hashMap.put("参数5", xxx);
        hashMap.put("参数6", xxx);
        return hashMap;
    }

    public RequestBody getRequestBody(HashMap<String, String> hashMap) {
        StringBuffer data = new StringBuffer();
        if (hashMap != null && hashMap.size() > 0) {
            Iterator iter = hashMap.entrySet().iterator();
            while (iter.hasNext()) {
                Map.Entry entry = (Map.Entry) iter.next();
                Object key = entry.getKey();
                Object val = entry.getValue();
                data.append(key).append("=").append(val).append("&");
            }
        }
        String jso = data.substring(0, data.length() - 1);
        RequestBody requestBody =
                RequestBody.create(MediaType.parse("application/x-www-form-urlencoded; charset=utf-8"), jso);

        return requestBody;
    }

```

#### 4 .3、@Field & @FieldMap

- 作用：发送 Post请求 时提交请求的表单字段

- 具体使用：与 `@FormUrlEncoded` 注解配合使用

```java
public interface GetRequest_Interface {
        /**
         *表明是一个表单格式的请求（Content-Type:application/x-www-form-urlencoded）
         * <code>Field("username")</code> 表示将后面的 <code>String name</code> 中name的取值作为 username 的值
         */
        @POST("/form")
        @FormUrlEncoded
        Call<ResponseBody> testFormUrlEncoded1(@Field("username") String name, @Field("age") int age);

/**
         * Map的key作为表单的键
         */
        @POST("/form")
        @FormUrlEncoded
        Call<ResponseBody> testFormUrlEncoded2(@FieldMap Map<String, Object> map);

}

// 具体使用
         // @Field
        Call<ResponseBody> call1 = service.testFormUrlEncoded1("Carson", 24);

        // @FieldMap
        // 实现的效果与上面相同，但要传入Map
        Map<String, Object> map = new HashMap<>();
        map.put("username", "Carson");
        map.put("age", 24);
        Call<ResponseBody> call2 = service.testFormUrlEncoded2(map);

}
```

#### 4.4 、@Part & @PartMap

- 作用：发送 Post请求 时提交请求的表单字段

   与@Field的区别：功能相同，但携带的参数类型更加丰富，包括数据流，所以适用于 有文件上传 的场景 

- 具体使用：与 `@Multipart` 注解配合使用

```java
public interface GetRequest_Interface {

          /**
         * {@link Part} 后面支持三种类型，{@link RequestBody}、{@link okhttp3.MultipartBody.Part} 、任意类型
         * 除 {@link okhttp3.MultipartBody.Part} 以外，其它类型都必须带上表单字段({@link okhttp3.MultipartBody.Part} 中已经包含了表单字段的信息)，
         */
        @POST("/form")
        @Multipart
        Call<ResponseBody> testFileUpload1(@Part("name") RequestBody name, @Part("age") RequestBody age, @Part MultipartBody.Part file);

        /**
         * PartMap 注解支持一个Map作为参数，支持 {@link RequestBody } 类型，
         * 如果有其它的类型，会被{@link retrofit2.Converter}转换，如后面会介绍的 使用{@link com.google.gson.Gson} 的 {@link retrofit2.converter.gson.GsonRequestBodyConverter}
         * 所以{@link MultipartBody.Part} 就不适用了,所以文件只能用<b> @Part MultipartBody.Part </b>
         */
        @POST("/form")
        @Multipart
        Call<ResponseBody> testFileUpload2(@PartMap Map<String, RequestBody> args, @Part MultipartBody.Part file);

    @Multipart
    //这里用Multipart,不添加的话会引起崩溃反应
    @POST("/modelGeneration/uploadModelDisplay")//请求方法为POST，里面为你要上传的url
    Observable<CancelModelBean> uploadModelDisplay(@PartMap Map<String, RequestBody> files);
    
}

	// 具体使用
	    MediaType textType = MediaType.parse("text/plain");
        RequestBody name = RequestBody.create(textType, "zyb");
        RequestBody age = RequestBody.create(textType, "24");
        RequestBody file = RequestBody.create(MediaType.parse("application/octet-stream"), "这里是模拟文件的内容");

        // @Part
        MultipartBody.Part filePart = MultipartBody.Part.createFormData("file", "test.txt", file);
        Call<ResponseBody> call3 = service.testFileUpload1(name, age, filePart);
		
        

        // @PartMap
        // 实现和上面同样的效果
        Map<String, RequestBody> fileUpload2Args = new HashMap<>();
        fileUpload2Args.put("name", name);
        fileUpload2Args.put("age", age);
        //这里并不会被当成文件，因为没有文件名(包含在Content-Disposition请求头中)，但上面的 filePart 有
        //fileUpload2Args.put("file", file);
        Call<ResponseBody> call4 = service.testFileUpload2(fileUpload2Args, filePart); //单独处理文件


	    Map<String, RequestBody> images = new HashMap<String, RequestBody>();
        images.put("tag", RequestBody.create(MediaType.parse("text/plain;charset=UTF-8"), tag));
        images.put("phone", RequestBody.create(MediaType.parse("text/plain;charset=UTF-8"), AppConstants.PHONE_NUM));
        images.put("picture\";filename=\"" + file.getName(), RequestBody.create(MediaType.parse("multipart/form-data"), file));
		
}
```



#### 4.5  、@Query和@QueryMap

- 作用：用于 `@GET` 方法的查询参数（Query = Url 中 ‘?’ 后面的 key-value）

   如：url = http://www.println.net/?cate=android，其中，Query = cate 

```java
   @GET("/")    
   Call<String> cate(@Query("cate") String cate);
```

#### 4.6 、 @Path

```java
public interface GetRequest_Interface {

        @GET("users/{user}/repos")
        Call<ResponseBody>  getBlog（@Path("user") String user ）;
        // 访问的API是：https://api.github.com/users/{user}/repos
        // 在发起请求时， {user} 会被替换为方法的第一个参数 user（被@Path注解作用）
    }
```

#### 4.7、@Url

- 作用：直接传入一个请求的 URL变量 用于URL设置

```java
public interface GetRequest_Interface {

        @GET
        Call<ResponseBody> testUrlAndQuery(@Url String url, @Query("showAll") boolean showAll);
       // 当有URL注解时，@GET传入的URL就可以省略
       // 当GET、POST...HTTP等方法中没有设置Url时，则必须使用 {@link Url}提供

}
```

![img](https://ask.qcloudimg.com/http-save/yehe-1001569/62fd4ac592d481afcecc3870f733c790.png?imageView2/2/w/1620)