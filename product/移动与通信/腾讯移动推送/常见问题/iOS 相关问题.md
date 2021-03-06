### 推送内容为空时，在 iOS 10系统版本及以下的设备无法弹出通知？

在调用 Rest API 推送时 `content` 字段不能设置空，否则将导致在 **iOS 10系统及以下**的设备上无法弹出通知。


### TPNS 支持 p8 证书吗？

p8 证书存在安全隐患。虽然 p8 比 p12 有更长的有效期，但是同时也有更大的推送权限和范围。若泄露，可能会造成更加严重的影响。TPNS 推荐您使用 p12 来分别管理您的应用的推送服务。

### TPNS SDK 1.2.5.4及以下的版本模拟器提示找不到 XGForFreeVersion 符号？

1.2.5.4及以下版本仅支持真机调试，如需使用模拟器调试请升级到最新版本。


### 推送消息无法收到？
消息推送是一个涉及到很多关联模块协作的任务，每一个环节出现异常都可能会导致消息收不到，以下是最为常见的问题：

**客户端排查**
- 检查设备通知设置
请检查【通知】>【应用名】，查看您的应用是否打开了推送消息权限。
- 检查设备网络设置
设备网络问题，可能导致客户端在注册 APNs 时获取接收消息的标识（Token）失败，这会导致无法使用移动推送 TPNS 服务给指定设备推送消息。

即使是客户端正确获取 Token，且已经将 Token 注册到移动推送 TPNS 后台，当使用移动推送 TPNS 服务器推送下发消息成功时，如果是设备未联网的状态，客户端将无法收到消息。若设备在短时内恢复网络连接，可能还会收到消息（APNs 会持有一段时间，然后再次下发消息）。

SDK 接入问题，在接入 SDK 之后，请确保能够获取到接收消息的标识（Device Token），具体请参见 [iOS SDK 集成指南](https://cloud.tencent.com/document/product/548/36663)。


**服务器排查**
- APNs 服务器问题
由于移动推送 TPNS 服务针对 iOS 设备下发消息是通过 APNs 服务下发，若 APNs 出现故障，将直接导致移动推送 TPNS 服务器请求 APNs 给设备下发消息失败。
- 移动推送 TPNS 服务器问题
移动推送 TPNS 服务端使用了多个功能模块之间的协作方式完成消息的下发，若其中任何一个模块有问题，也会导致消息推送出现问题。


**推送证书排查**
移动推送 TPNS 服务器在向 APNs 请求消息下发的时候，需要使用两个必需的参数：消息推送证书和设备标识（Device Token），在进行消息推送的时候，请确保消息推送证书是有效的。关于消息推送证书的设置请参见 [iOS 推送证书获取指引](https://cloud.tencent.com/document/product/548/36664)。





### 账号/标签绑定和解绑为什么不起作用？
使用 SDK API 进行账号和标签的绑定或是解绑操作，移动推送 TPNS 服务器需要10s左右进行数据同步



### 终端出现未找到应用程序的 “aps-environment” 的授权字符串错误？
请检查 Xcode 工程中配置的 bundle id 是否和设置的 Provision Profile 文件匹配，且对应 App 的 Provision Profile 文件是否已配置了消息推送能力。




### 客户端如何播放自定义推送消息音频？

首先，终端开发侧，需将音频文件放到 bundle 目录下：
- 若使用移动推送 TPNS 管理台创建推送时，在【高级设置】中填写音频文件名称（不需要音频文件的全路径）。
- 若使用 REST API 调用时，将 sound 参数设为音频文件名即可（不需要音频文件的全路径）。


### iOS 是否支持离线保存？ 
不支持，移动推送 TPNS 服务器下发消息请求到 APNs，若 APNs 发现设备不在线，APNs 会持有一段时间，具体时长 APNs 并未给出明确的说明。



### 为何 iOS 没有抵达数据？
- iOS 9.x 之前的版本，操作系统未提供 API 接口来监听消息抵达终端，故而无法统计。  
- iOS 10.0+ 的版本，操作系统提供了 Service Extension 接口，可供客户端调用，从而可以监听消息的到达。


### 使用移动推送 TPNS 服务端 SDK ，如何创建静默推送？
请给参数 content-available 赋值1，同时不使用 alert、badge、sound。




### iOS 开发环境下，注册偶现不返回 DeviceToken 或提示 APNs 请求 token 失败？
此问题现象是由于 APNs 服务不稳定导致的，可尝试通过以下方式解决：
1. 给手机插入 SIM 卡后使用4G网络测试。
2. 卸载重装、重启 App、关机重启后测试。
3. 打生产环境的包测试。
4. 更换其它 iOS 系统的手机测试。


### iOS 如何在测试设备有限的情况下扩大测试规模？
1. 企业级证书签名
申请企业级签名证书和企业级推送证书，发布方式如下：
使用企业级签名证书构建并发布App，体验者可以通过企业内部开放的渠道下载安装App。
2. AppStore 发布证书签名
使用当前 AppStore 的发布签名证书，发布方式如下：
TestFlight 发布预览版，先将 ipa 包上传到 [App Store Connect](https://appstoreconnect.apple.com)，然后通过 TestFlight 创建一个灰度版本，并在 TestFlight 上设置指定版本的体验人员名单(Apple ID)，最后体验者可以通过苹果官方【TestFlight】App 下载安装。


### iOS 如何只更改角标而不弹出信息？
可使用 API 在创建推送时使用通知栏消息类型，且标题内容设为空，同时只设置 badge_type 即可，详情可参考 [API 文档说明](https://cloud.tencent.com/document/product/548/39064#.E5.8F.AF.E9.80.89.E5.8F.82.E6.95.B0)。
示例如下：
```
{
    "platform": "ios",
    "audience_type": "token",
    "environment":"dev",
        "token_list": [
    "05a8ea6924590dd3a94480fa1c9fc8448b4e"],
    "message_type":"notify",
    "message":{
    "ios":{
        "aps": {
            "badge_type":-2
        }
    }
 }
}
```


### App 出现 Crash: you can't call -sendResponse: twice nor after encoding it 报错，该如何处理？
如果您的 App 集成了 TPNS iOS SDK（1.2.7.2 - 1.2.5.4），且使用到 TPNS 的【撤回】功能，同时 App 侧实现了如下系统回调：
```
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo  fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
```
则可能会遇到此问题。您可以使用【覆盖】功能来实现已发送消息的处理。
