# cordova
[打包教程](https://www.w3cschool.cn/cordova/cordova_environment_setup.html)

# 一、环境

1. NodeJS
2. NPM
3. [Android SDK(找其中的SDK Tools)](https://www.androiddevtools.cn/)

- 查看版本 `cordova -v`

# 二、创建目录
`cordova create app #app为入口目录` 

# 三、添加平台
`$ cordova platform add android`

# 四、构建和运行
- 构建
`cordova build android`

- 运行模拟器
`cordova emulate android`

- 输出目录查看
`app\platforms\android\build`

# 重要插件
- [调用摄像头](https://www.w3cschool.cn/cordova/cordova_camera.html)


- 二维码扫描插件
 `cordova plugin add https://github.com/wildabeast/BarcodeScanner.git`
 - 并引入 
 ```js
 <script type="text/javascript" src="cordova_plugins.js"></script>
 <a href="#" class="btn" onclick="scanCode();">Scan Code</a>
<script type="text/javascript">
function scanCode() {
    cordova.plugins.barcodeScanner.scan(
      function (result) {
          alert("We got a barcode\n" +
                "Result: " + result.text + "\n" +
                "Format: " + result.format + "\n" +
                "Cancelled: " + result.cancelled);
      }, 
      function (error) {
          alert("Scanning failed: " + error);
      }
   );
}
</script>
 ```
# 内部跳转网页
- 跳转内部webView,在config.xml中添加以下代码
```xml
    <!--配置WebView可以打开的外部链接-->
    <allow-navigation href="http://*/*" />
    <allow-navigation href="https://*/*" />  
```

# h5页面调用本地插件接口
```html
<!--<script src="file:///android_asset/www/cordova.js" type="text/javascript" charset="UTF-8"></script> -->
<script src="http://injection/www/cordova.js" type="text/javascript" charset="UTF-8"></script>
    <!-- <script type="text/javascript" src="js/index.js"></script>
    <script type="text/javascript" src="cordova_plugins.js"></script> -->

```



- 首先找到文件
打开platforms/andriod/CordovaLib\src\org\apache\cordova\engine\SystemWebViewClient.java

```java
public void clearAuthenticationTokens() {
    this.authenticationTokens.clear();
}

-- 新增开始 --
private static final String INJECTION_TOKEN = "http://injection/"; 
-- 新增结束 --

@TargetApi(Build.VERSION_CODES.HONEYCOMB)
@Override
public WebResourceResponse shouldInterceptRequest(WebView view, String url) {

    ---新增开始---
    if(url != null && url.contains(INJECTION_TOKEN)) {
        String assetPath = url.substring(url.indexOf(INJECTION_TOKEN) + INJECTION_TOKEN.length(), url.length());
        try {
            WebResourceResponse response = new WebResourceResponse(
                    "application/javascript",
                    "UTF8",
                    view.getContext().getAssets().open(assetPath)
            );
            return response;
        } catch (IOException e) {
            e.printStackTrace(); // Failed to load asset file
            return new WebResourceResponse("text/plain", "UTF-8", null);
        }
    }
    ---新增结束---
    try {

        // Check the against the whitelist and lock out access to the WebView directory
        // Changing this will cause problems for your application
        if (!parentEngine.pluginManager.shouldAllowRequest(url)) {
            LOG.w(TAG, "URL blocked by whitelist: " + url);
            // Results in a 404.
            return new WebResourceResponse("text/plain", "UTF-8", null);
        }

        CordovaResourceApi resourceApi = parentEngine.resourceApi;
        Uri origUri = Uri.parse(url);
        // Allow plugins to intercept WebView requests.
        Uri remappedUri = resourceApi.remapUri(origUri);

        if (!origUri.equals(remappedUri) || needsSpecialsInAssetUrlFix(origUri) || needsKitKatContentUrlFix(origUri)) {
            CordovaResourceApi.OpenForReadResult result = resourceApi.openForRead(remappedUri, true);
            return new WebResourceResponse(result.mimeType, "UTF-8", result.inputStream);
        }
        // If we don't need to special-case the request, let the browser load it.
        return null;
    } catch (IOException e) {
        if (!(e instanceof FileNotFoundException)) {
            LOG.e(TAG, "Error occurred while loading a file (returning a 404).", e);
        }
        // Results in a 404.
        return new WebResourceResponse("text/plain", "UTF-8", null);
    }
}
```
