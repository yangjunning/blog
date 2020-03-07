---
title: react-native-exception-handler 中文文档
categories:
  - [前端开发, React Native]
tags:
  - [exception]
  - [error]
  - [翻译]
date: 2019-06-11 14:20:44
---

# react-native-exception-handler ![npm](https://img.shields.io/npm/dm/react-native-exception-handler.svg)

[![https://nodei.co/npm/react-native-exception-handler.png?downloads=true&downloadRank=true&stars=true](https://nodei.co/npm/react-native-exception-handler.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/react-native-exception-handler)

一个允许您注册一个全局错误处理程序，可以捕获致命/非致命未捕获异常 的 react native 模块。该模块有助于防止 RN 应用程序突然崩溃，而无需向用户发送优雅消息

在当前的情况下：

- `DEV mode`：你会得到一个程序错误的红屏警告
- `Bundled mode`：App 在没有任何提示的情况下退出！🙄

为了解决这个问题，我们注册了一个全局错误处理程序，可用于例如：

1. 当 App 崩溃时给开发团队发送 BUG 反馈
2. 展示一个交互式的弹窗告诉用户应该重启应用

#### 升级 - V2:

**V2 现在支持捕获未处理的原生和 JS 异常 ✌🏻🍻**，这是向后兼容的。因此你可以安全地从 V1 升级到 V2，快快升级吧。

**V2.9**

- 添加对执行先前设置的错误处理程序的支持（现在这个模块可以与其他分析模块一起使用）
- 添加了一种改进的方法来覆盖本机错误处理程序。
- 感谢 @ [Damien Solimando](https://github.com/dsolimando)

> Demo：https://github.com/master-atul/react-native-exception-handler-example

## 安装

```sh
$ yarn add react-native-exception-handler
```

## 自动化配置

```sh
$ react-native link react-native-exception-handler
```

## 手动配置

#### iOS

1. In XCode, in the project navigator, right click `Libraries` ➜ `Add Files to [your project's name]`
2. Go to `node_modules` ➜ `react-native-exception-handler` and add `ReactNativeExceptionHandler.xcodeproj`
3. In XCode, in the project navigator, select your project. Add `libReactNativeExceptionHandler.a` to your project's `Build Phases` ➜ `Link Binary With Libraries`
4. Run your project (`Cmd+R`)<

##### Using Cocoapods

1. add `pod 'ReactNativeExceptionHandler', :podspec => '../node_modules/react-native-exception-handler/ReactNativeExceptionHandler.podspec'` to your Podfile
2. run `pod install`

#### Android

1. Open up `android/app/src/main/java/[...]/MainApplication.java`

- Add `import com.masteratul.exceptionhandler.ReactNativeExceptionHandlerPackage;` to the imports at the top of the file
- Add `new ReactNativeExceptionHandlerPackage()` to the list returned by the `getPackages()` method

2. Append the following lines to `android/settings.gradle`:
   ```
   include ':react-native-exception-handler'
   project(':react-native-exception-handler').projectDir = new File(rootProject.projectDir, 	'../node_modules/react-native-exception-handler/android')
   ```
3. Insert the following lines inside the dependencies block in `android/app/build.gradle`:
   ```
     compile project(':react-native-exception-handler')
   ```

## 请在使用部分之前阅读

让我先给你介绍下 RN App 中的错误：

- 由 Javascript 代码引起的（包括所有的 react 代码）。我们将把这些标记为 **JS_Exceptions**
- 由原生代码引起的。我们将把他们标记为 **Native_Exceptions**

未处理的异常会使应用程序处于严重状态

在 **JS_Exceptions** 的情况下，您可以捕获这些未处理的异常并执行显示警报或弹出窗口，在关闭应用程序之前，先进行清理甚至调用 API 以通知开发团队

在 **Native_Exceptions** 的情况下，它变得更糟。虽然您可以捕获这些未处理的异常并执行清理或注销等任务，甚至可以在关闭应用程序之前点击 API 以通知开发团队，但是你不能展示 JS 弹窗或者通过 JS 代码做任何 UI 相关的事情。你必须通过原生层来展示 UI，这个模块提供一个默认的错误页面，当然你也可以自定义，请参考后边自定义的部分

## 使用

捕获 **JS_Exceptions**

```js
import { setJSExceptionHandler, getJSExceptionHandler } from 'react-native-exception-handler'

// 对于大多数用例:
// 注册错误处理器（你可以在 index.js 中做这个事情）
setJSExceptionHandler((error, isFatal) => {
  // 这是你自定义的全局错误处理器
  // 你可以做一些像是展示弹窗、触发谷歌分析来跟踪崩溃或触发自定义的api来通知开发团队
})

// 高级用例:
const JSExceptionhandler = (error, isFatal) => {
  // 你的错误处理程序
}
// exceptionhandler：错误处理回调函数
// allowInDevMode：一个可选的布尔类型参数，默认false。如果你设置为 true，在开发模式下也会组织红屏
setJSExceptionHandler(exceptionhandler, allowInDevMode)

// getJSExceptionHandler 给出当前设置的 JS exception handler
const currentHandler = getJSExceptionHandler()
```

捕获 **Native_Exceptions**

> 注意：正如前文提到的，对于**Native_Exceptions** 弹窗提示或者通过 JS 展示任何 UI 都是不起作用的

```js
import { setNativeExceptionHandler } from 'react-native-exception-handler'

// 对于大多数用例:
setNativeExceptionHandler(errorString => {
  // 这是你自定义的全局错误处理器
  // 你可以触发谷歌分析来跟踪崩溃或触发自定义的api来通知开发团队
})

// 高级用例:
const NativeExceptionhandler = exceptionString => {
  // your exception handler code here
}
// exceptionhandler：错误处理回调函数
// forceAppQuit：是一个可选的 ANDROID 特定参数，用于定义错误发生时是否应用程序应该强制退出。默认是 true
// executeDefaultHandler：是一个可选的布尔类型参数，如果你通过其它模块设置了，它将会先执行。当您使用任何其他崩溃分析模块时，它会派上用场，默认值是 false，如果你使用其他分析模块，请设置为 true
setNativeExceptionHandler(exceptionhandler, forceAppQuit, executeDefaultHandler)
```

> 注意：**setNativeExceptionHandler** 只在 bundled 模式下起作用 - 开发模式下还是红屏

## 自定义

默认情况下，如果您使用了 `setNativeExceptionHandler`，则每当发生 `Native_Exceptions` 时，连同指定的回调，您将看到一个弹出窗口（这是此模块设置的默认原生处理程序）

在 Android 和 iOS 中你会看到类似的东西：

<p align="center">
  <img src="https://github.com/master-atul/react-native-exception-handler/raw/master/screens/android_native_exception.png" width="300"/>
  <img src="https://github.com/master-atul/react-native-exception-handler/raw/master/screens/ios_native_exception.png" width="300"/>
</p>

### Android

（必须写原生代码）建议您在 android studio 中执行此操作

- In the `android/app/src/main/java/[...]/MainApplication.java`

```java
import com.masteratul.exceptionhandler.ReactNativeExceptionHandlerModule;
import com.masteratul.exceptionhandler.NativeExceptionHandlerIfc
...
public class MainApplication extends Application implements ReactApplication {
...
  @Override
  public void onCreate() {
    ....
    ReactNativeExceptionHandlerModule.setNativeExceptionHandler(new NativeExceptionHandlerIfc() {
      @Override
      public void handleNativeException(Thread thread, Throwable throwable, Thread.UncaughtExceptionHandler originalHandler) {
        // TODO: 在这放置你的错误处理代码
      }
    });// This will override the default behaviour of displaying the recover activity.
  }
```

### ios

（必须写原生代码）建议您在 Xcode 中执行此操作

与 Android 不同，在 iOS 的情况下，如果应用程序崩溃，则无法重新启动应用程序。此外，在 Native_Exceptions 期间，由于主 UI 线程上发生异常，UI 变得非常不稳定。因此，点击或按下处理程序都不会起作用。

> 牢记这些，我们最多只能向用户显示一个对话框并通知用户重新打开该应用程序

如果您注意到默认的本机异常弹出窗口就是这样做的。自定义弹出窗口的 UI。

- In XCode, open the file `AppDelegate.m`

```objective-c
#import "AppDelegate.h"

#import <React/RCTBundleURLProvider.h>
#import <React/RCTRootView.h>

//Add the header file
#import "ReactNativeExceptionHandler.h"
...
...
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
...
...

[ReactNativeExceptionHandler replaceNativeExceptionHandlerBlock:^(NSException *exception, NSString *readeableException){

    // THE CODE YOU WRITE HERE WILL REPLACE THE EXISTING NATIVE POPUP THAT COMES WITH THIS MODULE.
    //We create an alert box
    UIAlertController* alert = [UIAlertController
                                alertControllerWithTitle:@"Critical error occurred"
                                message: [NSString stringWithFormat:@"%@\n%@",
                                          @"Apologies..The app will close now \nPlease restart the app\n",
                                          readeableException]
                                preferredStyle:UIAlertControllerStyleAlert];

    // We show the alert box using the rootViewController
    [rootViewController presentViewController:alert animated:YES completion:nil];

    // THIS IS THE IMPORTANT PART
    // By default when an exception is raised we will show an alert box as per our code.
    // But since our buttons wont work because our click handlers wont work.
    // to close the app or to remove the UI lockup on exception.
    // we need to call this method
    // [ReactNativeExceptionHandler releaseExceptionHold]; // to release the lock and let the app crash.

    // Hence we set a timer of 4 secs and then call the method releaseExceptionHold to quit the app after
    // 4 secs of showing the popup
    [NSTimer scheduledTimerWithTimeInterval:4.0
                                     target:[ReactNativeExceptionHandler class]
                                   selector:@selector(releaseExceptionHold)
                                   userInfo:nil
                                    repeats:NO];

    // or  you can call
    // [ReactNativeExceptionHandler releaseExceptionHold]; when you are done to release the UI lock.
  }];

...
...
...

 return YES;
}

@end
```

What is this `[ReactNativeExceptionHandler releaseExceptionHold];`?

In case of iOS we lock the UI thread after we show our popup to prevent the app from closing. Hence once we are done showing the popup we need to close our app after some time. But since our buttons wont work as our click handlers wont work (explained before). To close the app or to remove the UI lockup on exception, we need to call this method `[ReactNativeExceptionHandler releaseExceptionHold];`

Hence we set a timer of 4 secs and then call the method releaseExceptionHold to quit the app after 4 secs of showing the popup

```objective-c
[NSTimer scheduledTimerWithTimeInterval:4.0
                                 target:[ReactNativeExceptionHandler class]
                               selector:@selector(releaseExceptionHold)
                               userInfo:nil
                                repeats:NO];
```

## 给安卓平台的高级用法

- react-native-restart：错误发生时，允许用户手动重启应用

- BackAndroid.exitApp：错误发生时，允许用户手动退出应用

## 测试原生错误

`rn-test-exception-handler` 模块只做一件事，引起一个 **Native_Exceptions**。这有助于您验证自定义和此模块的能.
