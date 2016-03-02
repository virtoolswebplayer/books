# ExNavigator [![Slack](http://slack.exponentjs.com/badge.svg)](http://slack.exponentjs.com)

ExNavigator是基于Navigator实现的一个场景导航器,它是一个路由控制中心，. You define ExRoutes, which are 纯javascript对象 with functions to render the scene for the route and the components to display in the nav bar.

The API is in ExRoute.js and ExRouteRenderer.js.

ExNavigator 和 Navigator 十分相似

## 为什么?
[这篇文章](https://medium.com/the-exponent-log/routing-and-navigation-in-react-native-6b27bee39603#.13j2waolq) explains the ideas that are in ExNavigator and how we use it so you can assess whether it is useful for your project, too.

## 安装

```
npm install @exponent/react-native-navigator --save
```

ExNavigator 兼容 React Native 0.16+.

## 实例工程
Github user [Thorenandresen](https://github.com/Thorbenandresen) has two very simple bare bones projects showing ExNavigator in use:
* [ExNavigatorExample](https://github.com/Thorbenandresen/ExNavigatorExample)
* [ExNavigatorExampleModal](https://github.com/Thorbenandresen/ExNavigatorExampleModal)


## 用法

ExNavigator组件提供的api和 Navigator的类似. 你指定一个 `routeStack`（路由堆）和 `initialRoute`（初始路由）, along with some styles. ExNavigator will render the navigation bar for you and accepts some props to style its contents. See `ExNavigator.propTypes` for the list of accepted props.

**你必须使用javascript 的 `import` 关键字来引入 ExNavigator. 不能使用 `require()`.**

```js
import ExNavigator from '@exponent/react-native-navigator';

class Example extends React.Component {
  render() {
    return (
      <ExNavigator
        initialRoute={YourRouter.getHomeRoute()}
        style={{ flex: 1 }}
        sceneStyle={{ paddingTop: 64 }}
      />
    );
  }
}
```

### 路由

ExNavigator 和 Navigator 的最主要的区别是路由对象的样式. With ExNavigator, 你定义（sence）场景, 导航条的样式, 当场景失去焦点或正被激活的时候要怎么什么. A common pattern is to define a router where you create routes:

```js

let YourRouter = {
  getHomeRoute() {
    return {
      // Return a React component class for the scene. It receives a prop
      // called `navigator` that you can use to push on more routes.
      getSceneClass() {
        return require('./HomeScene');
      },

      // When this scene receives focus, you can run some code. We're just
      // proxying the `didfocus` event that Navigator emits, so refer to
      // Navigator's source code for the semantics.
      onDidFocus(event) {
        console.log('Home Scene received focus.');
      },

      // Return a string to display in the title section of the navigation bar.
      // This route's title is displayed next to the back button when you push
      // a new route on top of this one.
      getTitle() {
        return 'Home';
      },
    };
  },


  getProfileRoute(profile) {
    return {
      // You can also render a scene yourself when you need more control over
      // the props of the scene component
      renderScene(navigator) {
        let ProfileScene = require('./ProfileScene');
        return <ProfileScene navigator={navigator} profile={profile} />;
      },

      // There are onWillBlur and onDidBlur events when the scene loses focus.
      // These events occur when another scene will focus or did focus,
      // respectively. The difference between "will" and "did" is the start and
      // end of the scene transition.
      onDidBlur(event) {
        console.log(`Profile Scene for ${profile} lost focus.`);
      },

      // You can render arbitrary views for the title component. Note that you
      // also need to implement getTitle if you want the title of this route to
      // show up in the back button to it.
      renderTitle() {
        return (
          <View style={{ flexDirection: 'row' }}>
            <Image source={profile.photoUrl} style={styles.titlePhoto} />
            <Text style={styles.titleName}>{profile.name}</Text>
          </View>
        );
      },

      getTitle() {
        return profile.name;
      },

      // Render the view to display on the right side of the navigation bar. It
      // is typically a button but doesn't have to be.
      renderRightButton() {
        return (
          <Button onPress={() => { console.log('Tapped right button'); }}>
            Log
          </Button>
        );
      },
    };
  },
};
```

### Navigating

To navigate to a new scene, you use an API very similar to Navigator's. Create a route object—perhaps with the help of your router—and give it to the ExNavigator:

```js
class HomeScene extends React.Component {
  render() {
    return (
      <View style={{ justifyContent: 'center' }}>
        <Button onPress={() => {
          // Get a route object from the router
          let profile = {
            name: 'Alex',
            photoUrl: 'https://images.example.com/alex.jpeg',
          };
          let route = YourRouter.getProfileRoute(profile);

          // `navigator` is passed into your scene component when you have
          // implemented getSceneClass in your route
          this.props.navigator.push(route);
        }}>
          Navigate to a profile
        </Button>
      </View>
    );
  }
}
```

### iOS Scene Transition Shadow

The default scene config, `FloatFromRight`, has the ability to display a shadow just like it does natively on iOS. However, you need to define your own shadow. Add the following styles in the `sceneStyle` property on your `ExNavigator` component:

```
sceneStyle={{
  overflow: 'visible',
  shadowColor: '#000',
  shadowOpacity: 0.5,
  shadowRadius: 6
}}
```

### Flux-based navigation

You can use the ExNavigator routing framework using Actions to change the route instead of passing around a navigator. [react-native-router-flux](https://github.com/aksonov/react-native-router-flux) is built on top of ExNavigator.
