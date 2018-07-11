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

# 提示
- 跳转内部webView,在config.xml中添加以下代码
```xml
    <!--配置WebView可以打开的外部链接-->
    <allow-navigation href="http://*/*" />
    <allow-navigation href="https://*/*" />  
```
