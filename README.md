## 一、插件介绍

此插件是 OpoInstall 为了方便 uni-app 集成 OpoInstall 功能而开发的，实现了携带参数安装、渠道统计（H5 渠道等）、一键拉起全部功能。

OpoInstall 可帮助 Android/iOS 开发者精确地获取 App 每一次安装来源；在 App 安装或拉起后，直达指定场景，大大提高用户转化率和留存率。同时，OpoInstall 在精准的 App 安装来源跟踪的技术上，开发了免打包、跨平台的渠道统计功能，轻松创建与管理成千上万的渠道，实现线上线下全渠道覆盖。OpoInstall 统计数据完全独立于第三方平台，提供多维度的统计报表，实时客观地反映渠道效果。

## 二、集成准备

前往 [OpoInstall 官网](https://www.opoinstall.com/) 注册账户，登录管理控制台，创建应用后，跳过“集成指引”，在“应用集成”的对应平台的“应用配置”中获取 `appkey` 和 `scheme` 以及 iOS 的关联域名。

![获取appkey和scheme](https://web.cdn.opoinstallcloud.com/doc/ios-appkey.png)

#### 配置 appkey

在 `manifest.json` 的 **App原生插件配置** 中选择 `opoinstall-plugin` 插件，并在配置框内填入 OpoInstall 分配给应用的 `AppKey`。

Android 会写入 `AndroidManifest.xml` 的 `com.opoinstall.APP_KEY`，iOS 会写入 `Info.plist` 的 `com.opoinstall.APP_KEY`。

![设置appkey](https://web.cdn.opoinstallcloud.com/doc/uniapp-appkey.jpg)

#### 配置 scheme

在 `manifest.json` 的 **App常用其它配置** 中配置 OpoInstall 分配给应用的 `scheme`。

![设置scheme](https://web.cdn.opoinstallcloud.com/doc/uniapp-scheme.jpg)

#### 配置 universal links（iOS 平台）

1、开启 Associated Domains 服务

需要在 [苹果开发者后台](https://developer.apple.com) 为 AppID 开启 Associated Domains。

2、在 HBuilderX 里面配置关联域（Associated Domains）

在 `manifest.json` 的 iOS 关联域名中填入 OpoInstall 控制台提供的关联域名，格式如下：

``` xml
applinks:xxxxxx.opwakeup.com
```

![配置关联域名](https://web.cdn.opoinstallcloud.com/doc/uniapp-applinks.png)

> **环境要求：** Android minSdk 21 及以上，iOS 9.0 及以上。

## 三、使用教程

#### 引用

``` js
const opoinstall = uni.requireNativePlugin('opoinstall-plugin');
```

#### 初始化

`init()`

示例：在 `App.vue` 的 `onLaunch` 方法中初始化。

``` js
opoinstall.init();
```

> **注意：** 必须先初始化，才能调用其他接口；请确保用户同意《隐私政策》后再调用。

#### 调试日志

`setDebug(enabled)`

可选接口，用于开启或关闭 SDK 调试日志；不调用时使用 SDK 默认日志策略。

``` js
opoinstall.setDebug(true);
```

#### 获取安装数据

`getInstall(seconds, callback)`

- `seconds`：回调超时时间，单位秒
- `callback`：数据回调函数

示例：

``` js
opoinstall.getInstall(
    8,
    function(result) {
        console.log('getInstall : channel=' + result.channelCode +
            ', data=' + result.bindData +
            ', shouldRetry=' + result.shouldRetry);
    }
);
```

> **说明：** `bindData` 是 JSON 字符串，使用前需要先 `JSON.parse`；`shouldRetry` 为 `true` 时表示可以稍后重试。

#### 获取拉起数据

`registerWakeUp(callback)`

- `callback`：数据回调函数

示例：在 `App.vue` 的 `onLaunch` 方法中注册拉起回调（在初始化之后调用）。

``` js
opoinstall.registerWakeUp(function(result) {
    console.log('getWakeup : channel=' + result.channelCode + ', data=' + result.bindData);
});
```

#### 注册量统计

`reportRegister()`

示例：

``` js
opoinstall.reportRegister();
```

#### 效果点统计

`reportEffectPoint(pointId, pointValue)`

- `pointId`：效果点 ID
- `pointValue`：效果点值，数值类型

示例：

``` js
opoinstall.reportEffectPoint("effect_test", 1);
```

#### 效果点明细统计

`reportEffectPoint(pointId, pointValue, extras)`

- `pointId`：效果点 ID
- `pointValue`：效果点值，数值类型
- `extras`：效果点自定义参数，key 和 value 都必须是 string 类型

示例：

``` js
var extras = {
    "key1": "value1",
    "key2": "value2",
}
opoinstall.reportEffectPoint("effect_detail", 1, extras);
```

#### 裂变分享上报

`reportShare(shareCode, sharePlatform, callback)`

- `shareCode`：分享码/分享用户标识
- `sharePlatform`：分享平台，如 `QQ`、`WechatSession`
- `callback`：上报结果回调，包含 `shouldRetry`，失败时还会返回 `message`

示例：

``` js
opoinstall.reportShare("10001", "QQ", function(result) {
    console.log('reportShare : shouldRetry=' + result.shouldRetry + ', message=' + result.message);
});
```

## 四、导出 apk/ipa 包并上传

- 代码集成完毕后，需要导出安装包上传 OpoInstall 后台，OpoInstall 会自动完成所有的应用配置工作。

![上传安装包](https://web.cdn.opoinstallcloud.com/doc/upload-ipa-jump.png)

- 上传完成后即可开始在线模拟测试，体验完整的 App 安装/拉起流程；待测试无误后，再完善下载配置信息。

![在线测试](https://web.cdn.opoinstallcloud.com/doc/js-test.png)
