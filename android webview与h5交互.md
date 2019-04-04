## android webview与h5交互

#### 一、**WebView基本配置**

##### 1、布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="50dp">
        <Button
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="match_parent"
            android:id="@+id/btn1"
            android:text="无参调用"/>
        <Button
            android:layout_width="0dp"
            android:layout_weight="1"
            android:layout_height="match_parent"
            android:text="有参调用"
            android:id="@+id/btn2"/>
    </LinearLayout>
    <WebView
        android:id="@+id/web_view"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_weight="9" />
</LinearLayout>
```

##### 2、初如化控件

```java
//初始化控件
Button btn1 = (Button) findViewById(R.id.btn1);

btn1.setOnClickListener(this);

Button btn2 = (Button) findViewById(R.id.btn2);

btn2.setOnClickListener(this);

mWebView = (WebView) findViewById(R.id.web_view);
//获取webSettings
WebSettings settings = mWebView.getSettings();
//让webView支持JS
settings.setJavaScriptEnabled(true);
//加载目标网页
mWebView.loadUrl("http://www.xxxxxx.com/");
```

#### 二、WebView和H5的交互

##### 1、Android调用JS方法

```java
//这个是button的点击事件
@Override
public void onClick(View v) {
  switch (v.getId()) {
    case R.id.btn1:
      //参数 “javascript:”  +  js方法名
      mWebView.loadUrl("javascript:message()");
      break;
    case R.id.btn2:
      String text = "java牛逼";
      //在android调用js有参的函数的时候参数要加单引号
      mWebView.loadUrl("javascript:message2('" + text + "')");
      break;
  }
}
```

```javascript
//android要调用的js方法
var content = document.getElementById("content");

function message(){
  content.innerHTML = "调用了有参的js函数"
};

function message2(des){
  content.innerHTML = "调用了" + des
};

function sum(a,b){
  return a+b
}
```

如果要调用有返回值方法,需要调用evaluatjavascript方法

```java
mWebView.evaluateJavascript("sum(1,2)", new ValueCallback<String>() {
  @Override
  public void onReceiveValue(String value) {
    //对返回值的操作
    Toast.makeText(ViewPagerGalleryDemoActivity.this, "value = " + value, Toast.LENGTH_SHORT).show();
  }
});
```

##### 2、JS调用Android方法 

在Android初始化中加入

```java
//第一个参数把自身传给js 第二个参数是this的一个名字
//这个方法用于让H5调用android方法
mWebView.addJavascriptInterface(this, "android");
```

点击元素

```javascript
//点击H5元素调用android中的方法
var name = "hello world"
document.getElementById("btn0").onclick = function(){
  //android是传过来的对象名称，setmessage是android中的方法
  //若有需求H5也要在pc端能看，加try/catch可避免报错
  try{
    android.setMessage();
  }catch(e){
    //console.log(e)
  }
};
document.getElementById("btn1").onclick = function(){
  try{
    android.setMessage(name);
  }catch(e){
    //console.log(e)
  }
};
```

被调用的Android方法 

```java
//下面的两个方法是让网页来调的
//这个注解必须加 因为 兼容问题
@JavascriptInterface
public void setMessage() {
  Toast.makeText(this, "我弹", Toast.LENGTH_SHORT).show();
}

@JavascriptInterface
public void setMessage(String name) {
  Toast.makeText(this, "我弹弹" + name, Toast.LENGTH_SHORT).show();
}
```

**到这仅能有效展示**，若想在WebView中有H5间的跳转，比如列表点击到详情

```java
mWebView.setWebChromeClient(new WebChromeClient());

//当然，还有一些别的方法也可以，比如：
mWebView.setJavaScriptCanOpenWindowsAutomatically(true);//允许js弹出窗口

//不过现在普遍使用第一种，功能更多更强
```

或

```java
mWebView.setWebViewClient(new WebViewClient(){
  @Override
  public boolean shouldOverrideUrlLoading(WebView view, String url) {
    view.loadUrl(url);
    return true;
  }
};
```

原因在此：

在使用 WebView 当加载网页时，默认会调用系统的默认外部浏览器来加载页面，原因是因为 WebViewClient 中的 shouldOverrideUrlLoading 方法默认返回为false。 

要使用内部的 WebView 加网页就要重写 shouldOverrideUrlLoading 方法，使其返回 true。

##### 3、第二种交互方式 scheme + cookie *

scheme设置：对于要启动的Activity

```xml
<activity android:name=".SecondActivity">
  <intent-filter>
    <data android:scheme="aa"/>
    <action android:name="android.intent.action.VIEW"/>
    <category android:name="android.intent.category.DEFAULT"/>
    <category android:name="android.intent.category.BROWSABLE"/>
  </intent-filter>
</activity>
```

H5代码

```html
<a href='aa://atguigu/path'>点我试试</a>
```

配置解释

```
//Url地址  aa://atguigu/path 
下面的是Activity清单文件的配置
<data android:scheme="aa" android:host="atguigu" android:path="/path"/>

上下对比其实和我们的URL地址是一样的
aa 是 scheme
host 是主机名称
path 是路径
当然还可以配置端口和加参数
aa://atguigu:8080/path?id=10

通过activity配置那么就可以跳转到相应的界面里，如果activity只配置scheme = aa那么只要是aa的Url都是适配的
```

android中

```java
mWebView.setWebViewClient(new WebViewClient() {
  //当页面开始加载的时候调用此方法
  @Override
  public void onPageStarted(WebView view, String url, Bitmap favicon) {
    super.onPageStarted(view, url, favicon);
    //通过对URl的解析来决定调转到哪个页面
    //这边只是简单做一些判断当前是否是Scheme跳转
    if (url.contains("aa")) {
      Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(url));
      startActivity(intent);
    }
}
```

##### 4、H5代码总览

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title></title>
</head>
<body>

<button id="btn0">调用android无参参数</button>
<button id="btn1">调用android有参参数</button>

<a href='aa://atguigu/path'>点我试试</a>
<a href='https://www.baidu.com'>百度</a>

<div id="content"></div>

</body>

<script type="text/javascript">
  //点击H5元素调用android中的方法
  var name = "hello world"
  document.getElementById("btn0").onclick = function(){
    //android是传过来的对象名称，setmessage是android中的方法
    android.setMessage();
  };
  document.getElementById("btn1").onclick = function(){
    //android是传过来的对象名称，setmessage是android中的方法
    android.setMessage(name);
  };

  //android要调用的js方法
  var content = document.getElementById("content");

  function message(){
    content.innerHTML = "调用了有参的js函数"
  };

  function message2(des){
    content.innerHTML = "调用了" + des
  };
</script>
</html>
```

#### 三、交互的一些注意点

1、Java 调用 js里面的函数、效率低、估计要200ms左右，而js去调Java的方法、速度很快、50ms左右、所以尽量用js调用Java方法

2、Js 调用 Java 的方法、返回值如果是字符串、你会发现这个字符串是 native 的、转成 locale 的才能正常使用

3、网页中尽量不要使用jQuery、执行起来需要5-6秒、最好使用原生的js写业务脚本、以提升加载速度、改善用户体验

4、Android4.2以下的系统存在着webview的js对象注入漏洞。Android API 16 没有正确限制使用webview.addJavaScripteInterface方法，远程攻击者 使用JavaReflectionAPI利用执行任意java对象的方法

5、webview硬件加速导致页面渲染问题－白屏展示（关闭硬件加速）

6、后台耗电问题 ：activity不可见时要停用webview 

* **第一种**：WebView 动态加载。就是不在xml中写WebView，写一个layout，然后把WebView add进去

```java
WebView mWebView = new WebView(getApplicationgContext());
LinearLayout mll = findViewById(R.id.xxx);
mll.addView(mWebView);
```

```java
protected void onDestroy() {
  super.onDestroy();
  mWebView.removeAllViews();
  mWebView.destroy()
}
```

* **第二种**：

  为加载WebView的界面开启新进程，在该页面退出之后关闭这个进程。但是在这个其中，杀死自己进程的时候又遇到了问题，网上介绍的各种方法都不好使，killBackgroundProcesses(getPackageName())；各种不好用。

  最后在Androidmanifest.xml的activity标签里加上Android:process=”packagename.web”，在Activity.onDestroy()中直接使用System.exit(0)；直接退出虚拟机（Android为每一个进程创建一个虚拟机的）。一旦退出，内存里面释放。

7、后台耗电问题 ：无法释放js

​	在Activity的onstop和onresume里分别把 setJavaScriptEnabled() 给设置成false和true

#### 四、WebView错误页面处理

1、先做一个错误的H5页面放在本地，或者加载Activity都可以 

2、android中

```java
mWebView.setWebViewClient(new WebViewClient() {
  
  ...
    
  @Override
  public void onReceivedError(WebView view, int errorCode, String description, String failingUrl) {
    super.onReceivedError(view, errorCode, description, failingUrl);
    Log.i(TAG, "onReceivedError: " + errorCode + "  " + description);
    //判断错误类型
    if (errorCode == ERROR_UNSUPPORTED_SCHEME) {
      Log.i(TAG, "onReceivedError: " + "true");

      //停止加载错误页面，否则会显示原来H5加载的错误页面
      //再跳到现在的错误页面，体验不好，当然也可以做其它操作
      view.stopLoading();
      view.loadUrl("file:///android_asset/error.html");
    }
  }
}
```

#### 五、WebView中的Cookie操作

场景：webView需要保存从网页中获取到的Cookie 在涉及到账户体系的产品中，包含了一种登录状态的传递。比如，在Native（原生）界面的登录操作，进入到Web界面时，涉及到账户信息时，需要将登录状态传递到Web里面，避免用户二次登录。这里就涉及到WebView加载网页时的Cookie操作了。

通常我们在登录时获取到用户的Cookie信息，然后将其保存到sdcard的WebView缓存文件当中，这样在加载网页时，WebView会自动将当前url的本地Cookie信息放在http请求的request中，传递给服务器

* 设置cookie

```java
//创建CookieSyncManager 参数是上下文
CookieSyncManager.createInstance(context);
//得到CookieManager
CookieManager cookieManager = CookieManager.getInstance();
//得到向URL中添加的Cookie的值
String cookieString;//获取方法不再详述，以项目要求而定
//使用cookieManager.setCookie()向URL中添加Cookie
cookieManager.setCookie(url, cookieString);
CookieSyncManager.getInstance().sync();
```

* 获取cookie

```java
//加载业务页面
mWebView.loadUrl("http://www.XXXX.com");

mWebView.setWebViewClient(new WebViewClient() {
  @Override
  public void onPageStarted(WebView view, String url, Bitmap favicon) {
    super.onPageStarted(view, url, favicon);

    //得到CookieManager
    CookieManager instance = CookieManager.getInstance();
    //这样就可以获取到Cookie了
    String cookie = instance.getCookie(url);
    //操作Cookie
    Log.i(TAG, "onPageStarted: "+cookie);
  }
}
```

* 清理cookie

```java
CookieSyncManager.createInstance(this);   
CookieSyncManager.getInstance().startSync();   
CookieManager.getInstance().removeSessionCookie();
```

