# cordova
[打包教程](https://www.w3cschool.cn/cordova/cordova_environment_setup.html)

# 一、环境

1. NodeJS
2. NPM
3. Android SDK

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
# 提示
- 跳转内部webView,在config.xml中添加以下代码
```xml
    <!--配置WebView可以打开的外部链接-->
    <allow-navigation href="http://*/*" />
    <allow-navigation href="https://*/*" />  
```
