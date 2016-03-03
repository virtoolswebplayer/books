# React Native Router [![Join the chat at https://gitter.im/aksonov/react-native-router-flux](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/aksonov/react-native-router-flux?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Router for React Native based on [exNavigator](https://github.com/exponentjs/ex-navigator)

## 特点
- 所有"Screen"(routes)(路由)和animation transitions（过度动画）统一在一个文件中定义 例如 app.js
- 可以在任何地方切换路由 (比如: 可以在任意组件中 import {Actions} from 'react-native-router-flux';  `Actions.login`)
- 无需将navigator对象传递给你的组件
- 使用 `Schema` 为一组"screen"定义通用属性. 例如, 定义一个从底部弹出的screen  
   ```
      <Schema name="modal" sceneConfig={Navigator.SceneConfigs.FloatFromBottom}/>
   ```
- 可以控制 navigate bar(导航条)的 显示和隐藏  (see limitations)
- 支持管理 tab bar（选项卡工具条）, using [react-native-tabs](https://github.com/aksonov/react-native-tabs) (see demo)
- 支持嵌套导航. 例如, 每个选项卡都可以有自己的导航器, 嵌套在root navigator(根导航器). 过场动画会自动继承自上一级的导航器

## 安装
```
npm i react-native-router-flux --save
```

## 用法
1. 在 index.js, 定义路由和子路由
    * 如果你的页面具有一些通用的属性, 最好定义一个 `Schema` 元素，可以减入重复定义
2. 在任何页面中:
    * import {Actions} from 'react-native-router-flux'
    * Actions.ACTION_NAME(PARAMS) 会根据你指定的方式传递相应的action和参数给路由

## 配置

##### Router(路由器):
| 属性 | 类型 | 默认 | 说明 |
|---------------|----------|--------------|----------------------------------------------------------------|
| header | object | null | 可选的 头部视图|
| footer | object | null | 可选的 底部视图 (比如. (tab bar)选项卡工具条) |
| hideNavBar | bool | false | 在所有路由状态下隐藏(navigate bar)导航条 |

##### Route（路由）:

| 属性 | 类型 | 默认 | 说明 |
|-----------|--------|---------|--------------------------------------------|
| name | string | required | 路由名称,必须是唯一的。 例如： `Actions.name(params)` |
| component | React.Component | semi-required | 要显示的`Component`组件 . 当定义嵌套路由时不需要填写 `Router` or chilid, |
| type | string | optional | 定义该路由入路由堆(router stack)的方式. One of `push`, `modal`,`actionSheet`,`replace`, `switch`, `reset`.  默认是 'push'. `replace` 告诉导航器用新路由替换现在的路由 `actionSheet` 显示一个弹出式命令菜单, 你必须传递一个回调函数来处理相应的命令, check Example for details. `modal` 类型会在 navigator组件之后插入一个'component'. 可以用作弹出警告框或对话框 (比如登陆处理).``switch` 用于选项卡(tab screen). `reset` is similar to replace except it unmounts the componets in the navigator stack. `modal` component could be dismissed by using Actions.dismiss() |
| initial | bool | false | 如果默认显示 设置为 `true`  |
| title | string | null | 在导航栏上显示的标题文字 |
| schema | string | optional | Set this property to the name of a previously defined `Schema` to inherit its properties |
| wrapRouter | bool | false | 如果为 `true`, 路由会自动嵌套在它的父 `Router`. 做模态对手框时非常有用. 如果 type==switch wrapRouter 默认为 true|
| sceneConfig | Navigator.SceneConfigs | optional | 定义屏幕过场动画  |
| defaultRoute | string | optional | Defines which route to go to if this route is used as a tab and the tab is clicked on when the tab is already selected |
| hideNavBar | bool | false | 当前路由是否隐藏导航条 |
| hideTabBar | bool | false | 是否隐藏选项卡 (如果父`router` 的`footer` 使用了内置的`Tabbar`组件)|
| navigationBarStyle | View style |  | 可选的 导航条样式，如果设置会替换默认样式 |
| titleStyle | Text style |  | 可选的 标题元素样式替换默认样式 |
| renderTitle | Closure | | 可选的 闭包函数返回一个标题组件替换默认标题组件 |
| barButtonIconStyle | Image style |  | 可选的 图标按钮的样式替换默认样式 (比如. 返回图标) |
| leftTitle | string | | 可选的 在导航条的左侧显示的文字，如果前面的路由未设置 `renderBackButton` 属性. `renderBackButton` > `leftTitle` > <上一个路由的 `title`> |
| renderLeftButton | Closure | | 可选的 闭包函数 返回一个组件替换 左标题/按钮元素 |
| renderBackButton | Closure | | 可选的 闭包函数 返回一个‘返回’文字或按钮如果路由状态改变，如从上一个路由切换到当前路由 |
| leftButtonStyle | View style | | 可选的 包含左侧标题及按钮的容器的样式覆盖默认样式 |
| leftButtonTextStyle | Text style | | 可选的 左标题的样式 |
| rightTitle | string | | 可选的 显示在右边的文字. `onRight` must be provided for this to appear. |
| renderRightButton | Closure | | 可选的 闭包函数返回右侧标题或按钮元素|
| rightButtonStyle | View style | | 可选的 包含右侧标题及按钮的容器的样式覆盖默认样式 |
| rightButtonTextStyle | Text style | | 可选的 导航条右侧文字的样式 |

##### Schema:

| Property | Type | Default | Description |
|-----------|--------|---------|--------------------------------------------|
| name | string | required | The name of the schema, to be referenced in the route as `schema={"name"}` |
| property | - |  - | A `Schema` can have any property that you want the `Route` to inherit  |


## 例子
![launch](https://cloud.githubusercontent.com/assets/1321329/11692367/7337cfe2-9e9f-11e5-8515-e8b7a9f230ec.gif)

```javascript
import React, {AppRegistry, Navigator, StyleSheet, Text, View} from 'react-native'
import Launch from './components/Launch'
import Register from './components/Register'
import Login from './components/Login'
import Login2 from './components/Login2'
import {Router, Route, Schema, Animations, TabBar} from 'react-native-router-flux'
import Error from './components/Error'
import Home from './components/Home'
import TabView from './components/TabView'

class TabIcon extends React.Component {
    render(){
        return (
            <Text style={{color: this.props.selected ? 'red' :'black'}}>{this.props.title}</Text>
        );
    }
}

export default class Example extends React.Component {
    render() {
        return (
            <Router hideNavBar={true}>
                <Schema name="modal" sceneConfig={Navigator.SceneConfigs.FloatFromBottom}/>
                <Schema name="default" sceneConfig={Navigator.SceneConfigs.FloatFromRight}/>
                <Schema name="withoutAnimation"/>
                <Schema name="tab" type="switch" icon={TabIcon} />

                <Route name="launch" component={Launch} initial={true} wrapRouter={true} title="Launch"/>
                <Route name="register" component={Register} title="Register"/>
                <Route name="home" component={Home} title="Replace" type="replace"/>
                <Route name="login" schema="modal">
                    <Router>
                        <Route name="loginModal" component={Login} title="Login" schema="modal"/>
                        <Route name="loginModal2" component={Login2} title="Login2"/>
                    </Router>
                </Route>
                <Route name="error" component={Error} title="Error"  type="modal"/>
                <Route name="register2" component={Register} title="Register2"  schema="withoutAnimation"/>
                <Route name="tabbar">
                    <Router footer={TabBar} showNavigationBar={false} tabBarStyle={{borderTopColor:'#00bb00',borderTopWidth:1,backgroundColor:'white'}}>
                        <Route name="tab1" schema="tab" title="Tab #1" >
                            <Router>
                                <Route name="tab1_1" component={TabView} title="Tab #1_1" />
                                <Route name="tab1_2" component={TabView} title="Tab #1_2" />
                            </Router>
                        </Route>
                        <Route name="tab2" schema="tab" title="Tab #2" hideNavBar={true}>
                            <Router onPop={()=>{console.log("onPop is called!"); return true} }>
                                <Route name="tab2_1" component={TabView} title="Tab #2_1" />
                                <Route name="tab2_2" component={TabView} title="Tab #2_2" />
                            </Router>
                        </Route>
                        <Route name="tab3" schema="tab" title="Tab #3" component={TabView} hideTabBar={true}/>
                        <Route name="tab4" schema="tab" title="Tab #4" component={TabView} />
                        <Route name="tab5" schema="tab" title="Tab #5" component={TabView} />
                    </Router>
                </Route>
            </Router>
        );
    }
}
```

components/Launch.js (initial screen)

```javascript
import React, {View, Text, StyleSheet, TouchableHighlight} from 'react-native'
import Button from 'react-native-button'
import {Actions} from 'react-native-router-flux'

class Launch extends React.Component {
    render(){
        return (
            <View style={styles.container}>
                <Text>Launch page</Text>
                <Button onPress={()=>Actions.login({data:"Custom data", title:'Custom title' })}>Go to Login page</Button>
                <Button onPress={Actions.register}>Go to Register page</Button>
                <Button onPress={Actions.register2}>Go to Register page without animation</Button>
                <Button onPress={Actions.error('error message')}>Show error popup</Button>
                <Button onPress={Actions.tabbar}>Go to TabBar page</Button>
            </View>
        );
    }
}

var styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
        backgroundColor: 'transparent',
    }
});

module.exports = Launch;
```

## 模态框
To display a modal use ```type="modal"``` for your route components.
Modal type inserts its 'component' after navigator component. See the ```Examples``` folder for more details.

Note that **ReactNativeRouterFlux will not provide animations for modals** and you'll need to animate the modal yourself (or use a library)

## Sidebar/Drawer support
You can easily configure react-native-router-flux to handle a sidebar/drawer for specific routes:  
**1.** Create a sidebar/drawer component (you can use both [react-native-drawer](https://github.com/root-two/react-native-drawer) and [react-native-side-menu](https://github.com/react-native-fellowship/react-native-side-menu)) and pass its router props to its children:
```javascript
<DrawerLayout>
   {React.Children.map(children, c => React.cloneElement(c, {route: this.props.route}))}
</DrawerLayout>
```  
**2.** In you router component add the sidebar/drawer and nested routes following this pattern:
```javascript
<Router>
  <Route name="without-drawer"/>
  <Route name="main">
   <Drawer>
      <Router>
        <Route name="with-drawer-a"/>
        <Route name="with-drawer-b"/>
      </Router>
    </Drawer>
  </Route>
</Router>
```

There is **an [example](https://github.com/efkan/rndrawer-implemented-rnrouter)** which is worked together with `react-native-drawer`.

Also here is another complete example of a working drawer using a common app with:
- A session screen that just checks if the user is already logged-in on startup (SessionScreen)
- An auth screen that handles signin/signup (AuthScreen)
- Many other screens that show the true content of the app, all using the sidebar

**SideDrawer.js (I'm using 'react-native-drawer')**
```javascript
import Drawer from 'react-native-drawer'

class SideDrawer extends React.Component {
  render() {
    return (
      <Drawer
        type="overlay"
        content={<SideDrawerContent />}
        tapToClose={true}
        openDrawerOffset={0.2} 
        panCloseMask={0.2}
        closedDrawerOffset={-3}
        styles={{ drawer: drawerStyle, main: mainStyle }}
        tweenHandler={(ratio) => ({ main: { opacity: (2 - ratio) / 2 } })}
      >
        {React.Children.map(this.props.children, c => React.cloneElement(c, {
          route: this.props.route
        }))}
      </Drawer>
    )
  }
}
```

**Router.js**
```javascript
const hideNavBar = Platform.OS === 'android'
const paddingTop = Platform.OS === 'android' ? 0 : 8

export default class Routes extends React.Component {
  render() {
    return (
      <Router>
        <Schema
          name='boot'
          sceneConfig={Navigator.SceneConfigs.FadeAndroid}
          hideNavBar={true}
          type='replace'
        />
        <Schema
          name='main'
          sceneConfig={Navigator.SceneConfigs.FadeAndroid}
          hideNavBar={hideNavBar}
        />

        <Route schema='boot' component={SessionScreen} name='session' initial={true} />
        <Route schema='boot' component={AuthScreen} name='auth' />

        <Route name='main' hideNavBar={true} type='reset'>
          <SideDrawer>
            <Router
              sceneStyle={styles.scene}
              navigationBarStyle={styles.navigationBar}
              titleStyle={styles.title}
              barButtonIconStyle={styles.barButtonIcon}
              barButtonTextStyle={styles.barButtonText}
            >
              <Route schema='main' component={PlaceScreen} name='place' title='Places' />
              <Route schema='main' component={PaymentScreen} name='payment' title='Payment' header={Toolbar} />
            </Router>
          </SideDrawer>
        </Route>

      </Router>
    )
  }
}
```

## Redux/Flux 支持
This component is not opinionated and does not depend on any implementation of Flux or Redux, but you can easily connect it to them.

If 'dispatch' prop is passed to the router, it will be called with current route as `route`, `name` as route name and all route props, check Example for more details.

Also all route actions can be hooked by adding handlers for `Actions.onPush`, `Actions.onReplace`, `Actions.onPop` in your store(s).

Here is an example of connecting the router and its routes to Redux and creating a component aware of being focused:  

**1. Connect a `<Route>` to Redux**  
Connecting a `<Route>` to Redux is easy, instead of:  
```javascript
<Route name="register" component={RegisterScreen} title="Register" />
```
you might write:
```javascript
<Route name="register" component={connect(selectFnForRegister)(RegisterScreen)} title="Register" />
```
You can also simply connect the component itself in its own file like you usually do.  
  
**2. Connect a `<Router>` to Redux**  
If you need to inform Redux of the navigation status (i.e. when you pop a route) just override the `<Router>` component included in `react-native-router-flux` with a connected one:  
```javascript
import ReactNativeRouter, { Actions, Router } from 'react-native-router-flux'
const Router = connect()(ReactNativeRouter.Router) 
```
Now when you use a `<Router>` it will be connected to the store and will trigger the following actions:
- `Actions.BEFORE_ROUTE`
- `Actions.AFTER_ROUTE`
- `Actions.AFTER_POP`
- `Actions.BEFORE_POP`
- `Actions.AFTER_DISMISS`
- `Actions.BEFORE_DISMISS`

Take a look at [this](https://github.com/aksonov/react-native-router-flux/blob/master/Example/Example.js) for an example.

**3. Catch the interested actions in your reducer**  
For this example I have a `global` reducer (where I keep the information needed by all my app) where I set the `currentRoute`:
```javascript
case Actions.AFTER_ROUTE:
case Actions.AFTER_POP:
  return state.set('currentRoute', action.name)
```
Now the reducer will catch every route change and update `global.currentRoute` with the currently focused route.  
You also can do many other interesting things from here, like saving an history of the navigation itself in an array!  

**4. Update your component on focus**  
I'm doing it on `componentDidUpdate` of my component of the route named `payment`.
If `global.currentRoute` is `payment` and the previous `global.currentRoute` was different, than the component has just been focused.
```javascript
  componentDidUpdate(prevProps) {
    const prevRoute = prevProps.global.currentRoute
    const currentRoute = this.props.global.currentRoute
    if (currentRoute === 'payment' && prevRoute !== currentRoute) {
      this.props.actions.doSomething()
    }
  }
```
P.S.: Remember to check `currentRoute === 'payment'`, otherwise you'll start doSomething() on every route change!  

## 限制
### 嵌套路由
* If you are using a tab bar where each tab has its own `Router`, modal screens will have to be presented from the root navigator in order to cover the tab bar. To do this, the modal screen should be defined in the root router, and should have the `wrapRouter={true}` property as in the example below.
```
        <Router>
          <Schema name="modal" sceneConfig={Navigator.SceneConfigs.FloatFromBottom}/>
          <Route name="myModal" component={myModal} title="Modal" schema="modal" wrapRouter={true} />
          <Route name="tabbar">
            <Router footer={TabBar}>
              <Route name="tab1" schema="tab" title="Tab 1" component={Tab1}/>
            </Router>
          </Route>
        </Router>
```

