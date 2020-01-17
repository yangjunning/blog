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

## 标题栏 Header

### 隐藏 Header

```ts
// stack级别
createStackNavigator(routeConfigMap, {
  headerMode: 'none', // <---
})
// screen级别
createStackNavigator(
  {
    DemoScreen: {
      screen: DemoScreen,
      navigationOptions: {
        headerShown: null, // <---
      },
    },
  },
  NavigationRouteConfigMap,
)
```

### 隐藏 Header background

> 这个场景是 UI 的背景图是渐变的情况下没办法指定 StatusBar 的背景色

```tsx
static navigationOptions = ({ navigation }) => ({
  headerTransparent: true // <---
})
```

### 获取 Header Height

```js
import { useHeaderHeight, Header } from 'react-navigation-stack'

const headerHeight = useHeaderHeight()
console.log(Header.HEIGHT)
```

### 去掉标题栏下的阴影

```js
static navigationOptions = ({ navigation }) => ({
  headerStyle: {
    // 隐藏导航栏下的阴影效果
    elevation: 0, // android
    shadowOpacity: 0, // ios
  },
})
```

### 改变标题栏标题颜色

```js
static navigationOptions = ({ navigation }) => ({
  headerTintColor: '#ffffff', // 返回按钮和标题都使用这个属性作为它们的颜色
  headerStyle: {
    color: '#000000' // 优先级高
  },
})
```

## Navigation options 解析

### 选项卡导航器包含一个堆栈，在选项卡导航器设置 tabBarLabel

```jsx
class A extends React.Component {
  // etc..
}

class B extends React.Component {
  // etc..
}

const HomeStack = createStackNavigator(
  { A },
  {
    navigationOptions: {
      tabBarLabel: '首页',
    },
  },
)

const SettingsStack = createStackNavigator(
  { B },
  {
    navigationOptions: {
      tabBarLabel: '设置',
    },
  },
)

export default createAppContainer(
  createBottomTabNavigator({
    HomeStack,
    SettingsStack,
  }),
)
```

### 堆栈包含一个选项卡导航器，在堆栈标题栏上设置 headerTitle

每个 tab 都有自己的 stack， 当 tab 页面处于顶层时，你可以隐藏 stack 的 header。

```js
const FeedStack = createStackNavigator({
  FeedHome: FeedScreen,
  /* other routes here */
})

const ProfileStack = createStackNavigator({
  ProfileHome: ProfileScreen,
  /* other routes here */
})

const TabNavigator = createBottomTabNavigator(
  {
    Feed: FeedStack,
    Profile: ProfileStack,
  },
  {
    navigationOptions: {
      // Hide the header from AppNavigator stack
      headerShown: false,
    },
  },
)

const AppNavigator = createStackNavigator({
  Home: TabNavigator,
  Settings: SettingsScreen,
})
```

### 选项卡导航器包含堆栈，你希望隐藏特定页面上的 tabbar

添加另一个 Stack Navigator 作为 Tab Navigator 的父级, 并将 Detail 页面放在里面。

```js
const FeedStack = createStackNavigator({
  FeedHome: FeedScreen,
  /* any other route you want to render under the tab bar */
})

const TabNavigator = createBottomTabNavigator({
  Feed: FeedStack,
  Profile: ProfileScreen,
})

const AppNavigator = createStackNavigator({
  Tabs: TabNavigator,
  Details: DetailsScreen,
  /* any other route you want to render above the tab bar */
})

const AppNavigator = createSwitchNavigator(AppNavigator)
```

## 自定义 Android 物理返回键行为

```jsx
import React from 'react'
import { Text, View, Alert } from 'react-native'
import { AndroidBackHandler } from 'react-navigation-backhandler'

export default class DetailsScreen extends React.Component<Props> {
  onBackButtonPressAndroid = () => {
    if (!this.props.navigation.isFocused()) {
      // 确保当页面聚焦时才执行后边的代码
      return false
    }
    Alert.alert('自定义安卓物理返回键行为')
    return true
  }

  render() {
    return (
      <AndroidBackHandler onBackPress={this.onBackButtonPressAndroid}>
        <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center', backgroundColor: '#ecf0f1' }}>
          <Text style={{ fontSize: 40 }}>Detail Screen</Text>
        </View>
      </AndroidBackHandler>
    )
  }
}
```

## 在任意组件访问 navigation props

```tsx
import React from 'react'
import { Button } from 'react-native'
import { withNavigation } from 'react-navigation'
import { NavigationStackProp } from 'react-navigation-stack'

interface Props {
  navigation: NavigationStackProp
}

class MyBackButton extends React.Component<Props> {
  render() {
    return (
      <Button
        title="Back"
        onPress={() => {
          this.props.navigation.goBack()
        }}
      />
    )
  }
}

export default withNavigation(MyBackButton)
```

## 在没有 navigation props 的情况下执行路由跳转

### AppContainer

```tsx
import { setTopLevelNavigator } from 'path/to/NavigationService'
...
<AppContainer
  ref={navigatorRef => {
    if (navigatorRef) {
      setTopLevelNavigator(navigatorRef)
    }
  }}
/>
```

### NavigationService

```ts
import {
  NavigationState,
  NavigationLeafRoute,
  NavigationParams,
  NavigationRoute,
  NavigationActions,
  StackActions,
} from 'react-navigation'

let navigator

export const setTopLevelNavigator = navigatorRef => {
  navigator = navigatorRef
}

/**
 *  从 navigation state 中获取当前页面名
 * @param navigationState NavigationState
 */
export const getCurrentRoute = (
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

/**
 * 在没有 navigation 这个 prop 的情况下进行页面跳转
 * @param routeName 路由名
 * @param params 路由参数
 */
export const navigate = (routeName: string, params?: object) => {
  navigator.dispatch(
    NavigationActions.navigate({
      routeName,
      params,
    }),
  )
}

/**
 * 在没有 navigation 这个 prop 的情况下进行页面goBack操作
 */
export const goBack = () => {
  navigator.dispatch(NavigationActions.back())
}

/**
 * 在没有 navigation 这个 prop 的情况下进行页面reset操作
 * @param routeName 路由名
 * @param params 路由参数
 */
export const reset = (routeName: string, params?: object) => {
  const resetAction = StackActions.reset({
    index: 0,
    actions: [NavigationActions.navigate({ routeName, params })],
  })
  navigator.dispatch(resetAction)
}

/**
 * 在没有 navigation 这个 prop 的情况下进行页面 replace 操作
 * @param number 返回的层数
 */
export const replace = (routeName: string, params?: object) => {
  navigator.dispatch(StackActions.replace({ routeName, params }))
}

/**
 * 在没有 navigation 这个 prop 的情况下进行页面 push 操作
 * @param number 返回的层数
 */
export const push = (routeName: string, params?: object) => {
  navigator.dispatch(StackActions.push({ routeName, params }))
}

/**
 * 在没有 navigation 这个 prop 的情况下进行页面 pop 操作
 * @param number 返回的层数
 */
export const pop = (n: number) => {
  navigator.dispatch(StackActions.pop({ n }))
}

/**
 * 在没有 navigation 这个 prop 的情况下进行页面 pop 操作
 */
export const popToTop = () => {
  navigator.dispatch(StackActions.popToTop())
}

export default {
  setTopLevelNavigator,
  getCurrentRoute,
  navigate,
  goBack,
  reset,
}
```
