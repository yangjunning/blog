---
title: React Navigation 4 完全指南
date: 2020-01-10 10:29:14
tags:
---

## 适用范围

- react-native 0.60+
- react-navigation 4+
- react-navigation 2+

## 优势

- 易于使用：用内置的导航器快速开始项目, 提供开箱即用的无缝体验。
- 兼容 ios 和 android：平台拥有独特的外观、流程的动画和手势。
- 完全可定制：如果你知道如何使用 JavaScript 编写应用程序，那么你就可以自定义 React Navigation 的任何部分。
- 平台可扩展：React Navigation 在每一层都是可扩展的-你可以编写自己的 navigators，甚至替换面向用户的 API。

<!--more-->

## 安装

```sh
$ yarn add react-navigation
$ yarn add react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context
```

## 链接

```sh
$ cd ios && pod install && cd ..
```

为了完成 react-native-screens 在 Android 上的安装, 请在`android/app/build.gradle`中 dependencies 选项中添加下面这两行:

```groovy
implementation 'androidx.appcompat:appcompat:1.1.0-rc01'
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0-alpha02'
```

## createAppContainer

createAppContainer 是一个函数，该函数返回一个 React 组件 并将 createStackNavigator 创建的 React 组件 作为参数，并且可以直接从 App.js 导出,用作我们应用程序的根组件。

### onNavigationStateChange(prevState, newState, action)

每当导航器管理的 navigation state 发生变化时，都会调用该函数。 它接收之前的 state、navigation 的新 state 以及发布状态更改的 action。 默认情况下，它将 state 的更改打印到控制台。我们可以利用该 props 进行页面跟踪并将其发送到 APM 系统。

```tsx
<AppContainer onNavigationStateChange={onNavigationStateChange} />
```

```ts
import { NavigationState, NavigationLeafRoute, NavigationParams, NavigationRoute } from 'react-navigation'

/**
 *  从 navigation state 中获取当前页面名
 * @param navigationState NavigationState
 */
const getCurrentRoute = (
  navigationState: NavigationState | NavigationLeafRoute<NavigationParams>,
): NavigationRoute<NavigationParams> | null => {
  if (!navigationState) {
    return null
  }
  const route = navigationState.routes[navigationState.index]
  // 在嵌套的导航中快速翻找
  if (route.routes) {
    return getCurrentRoute(route)
  }
  return route
}

const onNavigationStateChange = (prevState, currentState) => {
  const prevScreen = getCurrentRoute(prevState)
  const currentScreen = getCurrentRoute(currentState)
  if (currentScreen && prevScreen !== currentScreen) {
    console.log(currentScreen)
  }
}

export default onNavigationStateChange
```

## StatusBar 处理

### 隐藏 header

```ts
// stack级别
createStackNavigator(routeConfigMap, {
  headerMode: 'none',
})
// screen级别
createStackNavigator(
  {
    DemoScreen: {
      screen: DemoScreen,
      navigationOptions: {
        headerShown: null,
      },
    },
  },
  NavigationRouteConfigMap,
)
```

### 透明 StatusBar

> 这个场景是 UI 的背景图是渐变的情况下没办法指定 StatusBar 的背景色

```tsx
import React from 'react'
import { Text, StatusBar, View } from 'react-native'
import { getStatusBarHeight } from 'react-native-status-bar-height'

interface Props {
  navigation: NavigationStackProp
}

export default class StatusBarDemoScreen extends React.Component<Props> {
  render() {
    return (
      <>
        <StatusBar translucent barStyle="light-content" backgroundColor="rgba(0,0,0,0)" />
        <View style={{ flex: 1, backgroundColor: '#6a51ae', paddingTop: getStatusBarHeight(), alignItems: 'center' }}>
          <Text style={{ fontSize: 20, color: '#ffffff' }}>Screen</Text>
        </View>
      </>
    )
  }
}
```

### 不透明 StatusBar

如果设置 StatusBar translucent 属性为 false，则需要借助 SafeAreaView 来解决 ios 内容上移的问题，好处是不会 StatusBar 高度不会发生变化，用户体验更好。

```tsx
import React from 'react'
import { Text, StatusBar, View, Platform } from 'react-native'
import { getStatusBarHeight } from 'react-native-status-bar-height'

interface Props {
  navigation: NavigationStackProp
}

export default class StatusBarDemoScreen extends React.Component<Props> {
  render() {
    return (
      <>
        <StatusBar barStyle="dark-content" translucent={false} backgroundColor="#ecf0f1" />
        <SafeAreaView style={{ flex: 1, alignItems: 'center', backgroundColor: '#ecf0f1' }}>
          <Text style={{ fontSize: 40 }}>Detail Screen</Text>
        </SafeAreaView>
        <!--由于SafeAreaView支持ios 11 以上的系统，我们可以通过判断平台的方式解决这个问题-->
        <View style={{ flex: 1, alignItems: 'center', backgroundColor: '#ecf0f1', paddingTop: Platform.OS === 'ios'? getStatusBarHeight(): 0 }}>
          <Text style={{ fontSize: 40 }}>Detail Screen</Text>
        </View>
      </>
    )
  }
}
```
