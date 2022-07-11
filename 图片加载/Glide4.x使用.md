### 1、使用依赖

```java
   implementation 'com.github.bumptech.glide:glide:4.4.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.4.0'
```



### 2、基本使用



```java
Glide.with(this).load(url).into(imageView);

```

### 3、占位图

#### 3.1普通占位图（placeholder）

```java
RequestOptions options = new RequestOptions()
        .placeholder(R.drawable.loading);
Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

#### 3.2 错误占位图 （error）

```java
RequestOptions options = new RequestOptions()
        .placeholder(R.drawable.ic_launcher_background)
    	// 错误占位图
        .error(R.drawable.error)
        .diskCacheStrategy(DiskCacheStrategy.NONE);
Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```



### 4、不使用缓存

#### 4.1 不使用缓存

```java
RequestOptions options = new RequestOptions()
    	// 占位图
        .placeholder(R.drawable.loading) 
    	// 不使用缓存 禁用掉Glide的硬盘缓存功能
        .diskCacheStrategy(DiskCacheStrategy.NONE);

// DiskCacheStrategy.NONE： 表示不缓存任何内容。
// DiskCacheStrategy.DATA： 表示只缓存原始图片。
// DiskCacheStrategy.RESOURCE： 表示只缓存转换过后的图片。
// DiskCacheStrategy.ALL ： 表示既缓存原始图片，也缓存转换过后的图片。
// DiskCacheStrategy.AUTOMATIC： 表示让Glide根据图片资源智能地选择使用哪一种缓存策略（默认选项）
Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

#### 4.2 禁止缓存

```java
RequestOptions options = new RequestOptions()
        .skipMemoryCache(true);
Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```



### 5、指定图片大小（override）

```java
RequestOptions options = new RequestOptions()
   		// 图片大小 Target.SIZE_ORIGINAL原始大小
        .override(200, 100);
Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);

```

### 6、加载指定格式图片

```java
Glide.with(this)
     .asBitmap()
     .load("http://guolin.tech/test.gif")
     .into(imageView);

```

### 7、into 使用

```java
SimpleTarget<Drawable> simpleTarget = new SimpleTarget<Drawable>() {
    @Override
    public void onResourceReady(Drawable resource, Transition<? super Drawable> transition) {
        imageView.setImageDrawable(resource);
    }
};

public void loadImage(View view) {

    Glide.with(this)
         .load("http://guolin.tech/book.png")
		// 不仅仅只能加载imageView上
         .into(simpleTarget);
}

```

### 8、preload  预加载

```java
Glide.with(this)
     .load("http://guolin.tech/book.png")
     .preload();

Glide.with(this)
     .load("http://guolin.tech/book.png")
     .into(imageView);

```

### 9、图片下载   submit()方法

**当调用了submit()方法后会立即返回一个FutureTarget对象，然后Glide会在后台开始下载图片文件。接下来我们调用FutureTarget的get()方法就可以去获取下载好的图片文件了，如果此时图片还没有下载完，那么get()方法就会阻塞住，一直等到图片下载完成才会有值返回**。

```java
public void downloadImage() {
    new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                String url = "http://www.guolin.tech/book.png";
                final Context context = getApplicationContext();
                FutureTarget<File> target = Glide.with(context)
                        .asFile()
                        .load(url)
                        .submit();
                final File imageFile = target.get();
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(context, imageFile.getPath(), Toast.LENGTH_LONG).show();
                    }
                });
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }).start();
}

```

### 10、listener 监听 加载状态

```java
Glide.with(this)
     .load("http://www.guolin.tech/book.png")
     .listener(new RequestListener<Drawable>() {
         @Override
         public boolean onLoadFailed(@Nullable GlideException e, Object model, Target<Drawable> target, boolean isFirstResource) {
             return false;
         }

         @Override
         public boolean onResourceReady(Drawable resource, Object model, Target<Drawable> target, DataSource dataSource, boolean isFirstResource) {
             return false;
         }
     })
     .into(imageView);

```

### 11、图片变换

**Glide已经内置了几种图片变换操作，我们可以直接拿来使用，比如CenterCrop、FitCenter、CircleCrop等**。

```java
RequestOptions options = new RequestOptions()
        .transforms(...);
Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);

// ---------------------------------------------------------------

RequestOptions options = new RequestOptions()
        .centerCrop();

RequestOptions options = new RequestOptions()
        .fitCenter();

RequestOptions options = new RequestOptions()
        .circleCrop();

```

图片变换，最后我们再来看一个非常优秀的开源库  **https://github.com/wasabeef/glide-transformations 。**

```java
apply plugin: 'kotlin-kapt'

dependencies {
    implementation 'com.github.bumptech.glide:glide:4.11.0'

    implementation 'jp.wasabeef:glide-transformations:4.3.0'

    kapt "com.github.bumptech.glide:compiler:4.11.0"

    implementation "jp.co.cyberagent.android:gpuimage:2.1.0"
}
```



### 12、transformations 库使用

#### 12.1  多个图像转换

多个规则叠加

```kotlin
  Glide.with(context)
          .load(R.drawable.check)
          .apply(overrideOf(266.px, 252.px))
          .apply(bitmapTransform(MultiTransformation<Bitmap>(
              CenterCrop(), MaskTransformation(R.drawable.mask_starfish))))
          .into(holder.image)
```



![](..\图片加载\pic\1657536118686.jpg)

#### 12.2  设置圆角

```kotlin
Glide.with(context)
        .load(R.drawable.demo)
        .apply(bitmapTransform(
            // 设置圆角 参数3，有多种圆角的格式，目前左上角到对角线
         RoundedCornersTransformation(120, 0, RoundedCornersTransformation.CornerType.DIAGONAL_FROM_TOP_LEFT)))
        .into(holder.image)
```

![](..\\图片加载\pic\1657536223159.jpg)



#### 12.3图片截取

```kotlin
CropTop -> Glide.with(context)
        .load(R.drawable.demo)
		// 宽高 CropType.TOP|CropType.CENTER|CropType.BOTTOM
        .apply(bitmapTransform(CropTransformation(300.px, 100.px, CropType.TOP)))
        .into(holder.image)
```

![](..\\图片加载\pic\1657536263280.png)

#### 12.4 正方形

```kotlin
 Glide.with(context)
  .load(R.drawable.demo)
  .apply(bitmapTransform(CropSquareTransformation()))
  .into(holder.image)
```

![](..\\图片加载\pic\1657536429588.jpg)

#### 12.5 圆形

```kotlin
Glide.with(context)
  .load(R.drawable.demo)
  .apply(bitmapTransform(CropCircleTransformation()))
  .into(holder.image)
```

![1657536176604](..\图片加载\pic\1657536176604.jpg)



#### 12.6 加border 圆形

```kotlin
 Glide.with(context)
  .load(R.drawable.demo)
  .apply(bitmapTransform(
    CropCircleWithBorderTransformation(Utils.toDp(4), Color.rgb(0, 145, 86))))
  .into(holder.image)
```

![](..\图片加载\pic\1657536464457.jpg)

#### 12.7  颜色过滤

```kotlin
 Glide.with(context)
  .load(R.drawable.demo)
  .apply(bitmapTransform(ColorFilterTransformation(Color.argb(80, 255, 0, 0))))
  .into(holder.image)
```



#### 12.8 黑白

```kotlin
Glide.with(context)
  .load(R.drawable.demo)
  .apply(bitmapTransform(GrayscaleTransformation()))
  .into(holder.image)
```



![](..\图片加载\pic\1657536542930.jpg)

#### 12.9 模糊

```kotlin
// 浅模糊
Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(BlurTransformation(25))) // 模糊半径
  .into(holder.image)
// 深度模糊
Glide.with(context)
        .load(R.drawable.check)
        .apply(bitmapTransform(BlurTransformation(25, 8)))
        .into(holder.image)
```



![](..\图片加载\pic\1657536616009.jpg)

#### 12.10  Toon 油画

```kotlin
Glide.with(context)
  .load(R.drawable.demo)
  .apply(bitmapTransform(ToonFilterTransformation()))
  .into(holder.image)
```

![](..\图片加载\pic\1657536645181.jpg)

#### 12.11  Sepia 深褐色

```kotlin
 Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(SepiaFilterTransformation()))
  .into(holder.image)
```

![](..\图片加载\pic\1657536668344.jpg)

#### 12.12  明暗对比 

```kotlin
 Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(ContrastFilterTransformation(2.0f)))
  .into(holder.image)
```

![](..\图片加载\pic\1657536675944.jpg)

#### 12.13  Invert

```kotlin
Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(InvertFilterTransformation()))
  .into(holder.image)
```

![](..\图片加载\pic\1657536728124.jpg)

#### 12.14  Pixel

```kotlin
Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(PixelationFilterTransformation(20f)))
  .into(holder.image)
```

![](..\图片加载\pic\1657536733876.png)

#### 12.15  素描  Sketch

```kotlin
Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(SketchFilterTransformation()))
  .into(holder.image)
```

![](..\图片加载\pic\1657536740648.png)

#### 12.16  Swirl 旋转

```kotlin
Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(
    SwirlFilterTransformation(0.5f, 1.0f, PointF(0.5f, 0.5f))).dontAnimate())
  .into(holder.image)
```

![](..\图片加载\pic\1657536877850.png)

#### 12.17   Brightness 亮 



```kotlin
 Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(BrightnessFilterTransformation(0.5f)).dontAnimate())
  .into(holder.image)
```

![](..\图片加载\pic\1657536883712.png)

#### 12.18  Kuawahara

```kotlin
Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(KuwaharaFilterTransformation(25)).dontAnimate())
  .into(holder.image)
```

![](..\图片加载\pic\1657536890440.png)

#### 12.19  Vignette  插图

```kotlin
Glide.with(context)
  .load(R.drawable.check)
  .apply(bitmapTransform(VignetteFilterTransformation(PointF(0.5f, 0.5f),
    floatArrayOf(0.0f, 0.0f, 0.0f), 0f, 0.75f)).dontAnimate())
  .into(holder.image)
```

![](\图片加载\pic\1657536984582.jpg)

中文文档 

https://muyangmin.github.io/glide-docs-cn/doc/transitions.html