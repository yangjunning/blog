---
title: 配置 react native splash screen 防止启动白屏
date: 2020-02-21 12:41:18
categories:
  - [移动开发, React Native]
tags:
  - 启动屏
---

<!--more-->

## Android

1、通过创建 launch_screen.png 文件并把它们放到 mipmap- 文件夹下来自定义你的启动图。安卓会自动选择合适的分辨率，因此你不是必须为所有手机分辨率提供图片。不过，你可以为以下所有分辨率提供启动图：

- mipmap-mdpi
- mipmap-hdpi
- mipmap-xhdpi
- mipmap-xxhdpi
- mipmap-xxxhdpi

2、更新你的 `MainActivity.java` 文件如下：

```java
import android.os.Bundle;
import org.devio.rn.splashscreen.SplashScreen;

public class MainActivity extends ReactActivity {
   @Override
   protected void onCreate(Bundle savedInstanceState) {
     SplashScreen.show(this, true);  // 添加这一句
     super.onCreate(savedInstanceState);
  }
  // ...other code
}
```

2、创建一个名为 launch_screen.xml 的布局文件来自定义你的启动屏幕。

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ImageView android:layout_width="match_parent" android:layout_height="match_parent" android:src="@mipmap/launch_screen" android:scaleType="centerCrop" />
</RelativeLayout>
```

3、你也可以启用app主题透明选项来解决在APP启动时因主题原因导致的短暂白屏的问题,具体步骤如下:

打开 `android/app/src/main/res/values/styles.xml` 文件,添加 `<item name="android:windowIsTranslucent">true</item>`,如下 :

```xml
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <!--设置透明背景-->
        + <item name="android:windowIsTranslucent">true</item>
    </style>
</resources>
```

4、Add a color called `primary_dark` in `app/src/main/res/values/colors.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="primary_dark">#000000</color>
</resources>
```

## iOS

1、配置LaunchImage: https://bre.is/7xYzrkhs

2、更新你的AppDelegate.m 文件如下：

```obj-c
#import "AppDelegate.h"

#import <React/RCTBundleURLProvider.h>
#import <React/RCTRootView.h>
#import "RNSplashScreen.h"  // 添加这一句

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // ...other code

    [RNSplashScreen show];  // 添加这一句，这一句一定要在最后
    return YES;
}

@end
```

## 隐藏启动图

### 类组件

```js
import SplashScreen from 'react-native-splash-screen'

export default class WelcomePage extends Component {
    componentDidMount() {
        SplashScreen.hide();
    }
}
```

### 函数组件

```js
const App = () => {
  React.useEffect(() => {
    SplashScreen.hide()
  }, [])
  return (...)
```
