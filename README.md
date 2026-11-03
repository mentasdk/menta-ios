# Menta iOS SDK — Swift Package Manager

首版提供 `MentaVlionBaseSDK`、`MentaUnifiedSDK`、`MentaVlionSDK` 和
`MentaVlionLiteAdapter`，最低支持 iOS 12.0，使用 Xcode 15 或更高版本。
二进制支持真机 arm64、模拟器 arm64/x86_64。

在 Xcode 的 Add Package Dependencies 中添加仓库地址：

```text
https://github.com/mentasdk/menta-ios.git
```

选择版本 `7.4.4`，再选择
`MentaVlionLiteAdapter` 产品即可同时引入三个核心 SDK。
只使用核心能力时，也可以按需选择其他三个产品。

SDK 版本 `7.04.04` 对应 SPM 语义化版本标签 `7.4.4`。
后续标签同样去掉版本段的前导零。

## 宿主必需配置

在 App target 的 Debug、Release **Other Linker Flags** 中添加：

```text
$(inherited) -ObjC -weak_framework AppTrackingTransparency
```

`-ObjC` 保留通过 `+load` 和类名完成注册的 Adapter；缺少它可能构建成功但无法
加载广告。AppTrackingTransparency 必须弱链接，保证 iOS 12/13 可以启动。
这些是宿主配置，SPM 不会读取原 Podspec 的 `user_target_xcconfig`。
清单没有使用 `unsafeFlags`，以便本包继续被其他 Swift 包依赖。

Objective-C 使用模块导入：

```objc
@import MentaUnifiedSDK;
@import MentaVlionLiteAdapter;
```

Swift 使用：

```swift
import MentaUnifiedSDK
import MentaVlionLiteAdapter
```

SPM 会自动复制 `MVBaseSDK.bundle`，SDK 兼容包资源容器的查找路径。
图片和 GIF 无需手工复制。修改 Info.plist 中的隐私权限、ATT 使用说明及广告业务
所需设置仍由宿主负责。

## 组件边界

Lite 只包含瑞狮 Adapter，不包含 Full 包中的 FunLink、佳投、LTMB、前呈、旺脉，
也不包含百度、优量汇、穿山甲或快手 SDK。首版所提供的四个二进制没有 Swift
实现代码，不需要为本包单独开启 Swift 运行库嵌入。

同一个 App 不要同时通过 CocoaPods、SPM 或手工方式引入同名核心 SDK，
也不要同时链接 Full 和 Lite，以免产生重复符号与重复注册。
