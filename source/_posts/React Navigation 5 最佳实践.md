---
title: React Navigation 5 最佳实践
date: 2020-01-17 10:29:14
categories:
  - [移动开发, React Native]
tags:
  - 最佳实践
---

## 安装依赖

```sh
$ yarn add @react-navigation/native @react-navigation/stack @react-navigation/bottom-tabs react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

## 配置

为了完成  `react-native-screens` 的安装，添加下面两行代码到 `android/app/build.gradle` 文件的 `dependencies` 部分中：

```groovy
implementation 'androidx.appcompat:appcompat:1.1.0-rc01'
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0-alpha02'
```

为了完成  `react-native-gesture-handler` 的安装, 在入口文件的顶部添加下面的代码, 比如 `index.js` 或 `App.js`:

```js
import 'react-native-gesture-handler';
```

现在，我们需要把整个 App用 `NavigationContainer`包裹：

```jsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';

const App = () => {
  return (
    <NavigationContainer>
      {/* Rest of your app code */}
    </NavigationContainer>
  );
};

export default App;
```

## Header Button

使用 `react-native-header-buttons` 组件搭配任意 Icon 组件可以自定义自己的 Header Button 组件：

```jsx
import React from 'react';
import {
  HeaderButtons as RNHeaderButtons,
  HeaderButton as RNHeaderButton,
  Item,
} from 'react-navigation-header-buttons';
import {IconOutline} from '@ant-design/icons-react-native';

const HeaderButton = props => {
  return (
    <RNHeaderButton
      {...props}
      IconComponent={IconOutline}
      iconSize={props.iconSize || 23}
      color={props.color || '#000000'}
    />
  );
};

const HeaderButtons = props => {
  return <RNHeaderButtons HeaderButtonComponent={HeaderButton} {...props} />;
};

HeaderButtons.Item = Item;

export default HeaderButtons;
```

## stack navigator

```jsx
import React from 'react';
import {View, Text, StyleSheet} from 'react-native';
import {NavigationContainer} from '@react-navigation/native';
import {createStackNavigator, HeaderBackButton} from '@react-navigation/stack';
import HeaderButtons from './HeaderButtons';

const HomeScreen = ({navigation, route}) => {
  navigation.setOptions({
    headerLeft: props => (
      <HeaderBackButton
        {...props}
        onPress={() => {
          alert('不能再返回了！');
        }}
      />
    ),
    headerRight: () => (
      <HeaderButtons>
        {/* title、iconName、onPress、IconComponent、iconSize、color */}
        <HeaderButtons.Item
          title="添加"
          iconName="plus"
          onPress={() => alert('添加！')}
          iconSize={24}
          color="#ffffff"
        />
      </HeaderButtons>
    ),
  });

  const {author} = route.params || {};
  return (
    <View style={styles.container}>
      <Text>Home Screen</Text>
      <Text>{author}</Text>
      // 使用 setOptions 更新标题
      <Button
        title="Update the title"
        onPress={() => navigation.setOptions({headerTitle: 'Updated!'})}
      />
      <Button
        title="Go to DetailsScreen"
        // 跳转到指定页面，并传递两个参数
        onPress={() =>
          navigation.navigate('DetailsScreen', {
            otherParam: 'anything you want here',
          })
        }
      />
    </View>
  );
};

const DetailsScreen = ({navigation, route}) => {
  // 通过 props.route.params 接收参数
  const {itemId, otherParam} = route.params;
  return (
    <View style={styles.container}>
      <Text>Details Screen</Text>
      <Text>itemId: {itemId}</Text>
      <Text>otherParam: {otherParam}</Text>
      // 返回上一页
      <Button title="Go back" onPress={() => navigation.goBack()} />
      // 如果返回上一个页面需要传递参数，请使用 navigate 方法
      <Button
        title="Go back with Params"
        onPress={() =>
          navigation.navigate('HomeScreen', {author: '杨俊宁'})
        }
      />
    </View>
  );
};

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator
        initialRouteName="HomeScreen"
        // 页面共享的配置
        screenOptions={{
          // 一个应用于 header 的最外层 View 的 样式对象， 如果你设置 backgroundColor ，他就是header 的颜色。
          headerStyle: {
            backgroundColor: '#f4511e',
          },
          // 返回按钮和标题都使用这个属性作为它们的颜色
          headerTintColor: '#ffffff',
          // 如果我们想为标题定制fontFamily，fontWeight和其他Text样式属性，我们可以用它来完成。
          headerTitleStyle: {
            fontWeight: 'bold',
          },
          headerBackTitleVisible: false,
          headerTitleAlign: 'center',
          // 页面公用样式
          cardStyle: {
            flex: 1,
            backgroundColor: '#f5f5f9',
          },
          ...TransitionPresets.SlideFromRightIOS,
        }}>
      >
        <Stack.Screen
          name="HomeScreen"
          component={HomeScreen}
          options={{headerTitle: '首页'}}
        />
        <Stack.Screen
          name="DetailsScreen"
          component={DetailsScreen}
          options={{headerTitle: '详情'}} // headerTitle 用来设置标题栏
          initialParams={{itemId: 42}} // 默认参数
        />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default App;
```

> 路由名称的大小写无关紧要 -- 你可以使用小写字母`home`或大写字母`Home`，这取决于你的喜好。 我们更喜欢将路由名称大写。 我们更喜欢利用我们的路由名称。

> 跳转方法有 `navigate`、 `push` 、`goBack`、`popToTop`

> 可以用 `props.navigation.setParams` 方法更新页面的参数

> 我们可以通过 `options={({ route, navigation }) => ({ headerTitle: route.params.name })}` 的方式在标题中使用参数

> 我们可以用 `props.navigaiton.setOptions` 更新页面配置

- `Stack.Navigator`
  - `initialRouteName` : 用来配置 `Stack.Navigator` 的初始路由
  - `screenOptions`: 页面共享配置对象
- `Stack.Screen`
  - `name`: 页面名
  - `component`: 页面对应组件
  - `options`: 页面配置对象
  - `initialParams`: 默认参数

给页面传递额外的属性：

```jsx
<Stack.Screen
  name="HomeScreen"
  options={{headerTitle: '首页'}}>
  {props => <HomeScreen {...props} extraData={{author: '杨俊宁'}} />}
</Stack.Screen>
```

## 最佳实践

```jsx
import React from 'react'
import { Text, View } from 'react-native'
import { NavigationContainer } from '@react-navigation/native'
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs'
import { createStackNavigator } from '@react-navigation/stack'

function HomeScreen() {
  return (
    <View style={styles.container}>
      <Text>Home!</Text>
    </View>
  )
}

function SettingsScreen() {
  return (
    <View style={styles.container}>
      <Text>Settings!</Text>
    </View>
  )
}

// 为了方便设置title
const Stack = createStackNavigator()
const HomeStackScreen = () => {
  return (
    <Stack.Navigator>
      <Stack.Screen
        name="HomeScreen"
        component={HomeScreen}
        options={{
          headerTitle: '首页',
        }}
      />
    </Stack.Navigator>
  )
}
const SettingsStackScreen = () => {
  return (
    // @ts-ignore
    <Stack.Navigator screenOptions={getScreenOptions()}>
      <Stack.Screen
        name="SettingsScreen"
        component={SettingsScreen}
        options={{
          headerTitle: '设置',
        }}
      />
    </Stack.Navigator>
  )
}

const BottomTab = createBottomTabNavigator()

const App = () =>{
  const routeNameRef = React.useRef(null)
  return (
    <NavigationContainer
    	ref={navigationRef}
      onStateChange={state => {
        const previousRouteName = routeNameRef.current
        const currentRouteName = getActiveRouteName(state)
        if (previousRouteName !== currentRouteName) {
          console.log('[onStateChange]', currentRouteName)
        }
      }}
    >
      <BottomTab.Navigator>
        <BottomTab.Screen name="HomeStackScreen" component={HomeStackScreen} options={{ tabBarLabel: '首页' }}/>
        <BottomTab.Screen name="SettingsStackScreen" component={SettingsStackScreen} options={{ tabBarLabel: '设置' }}/>
      </BottomTab.Navigator>
    </NavigationContainer>
  )
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```
