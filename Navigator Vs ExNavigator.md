### ExNavigator 提供的方法
|Navigator| ExNavigator
|--------|:-------------:|
| `getCurrentRoutes()`  | 返回路由列表
| `jumpBack()`          | 不卸载当前场景转到后一个场景he current scene
| `jumpForward()`       | 不卸载当前场景转到路由堆中的前一个场景
| `jumpTo(route)`       | 不卸载当前场景转到路由堆中存在的指定的场景
| `push(route)`         | Navigate forward to a new scene, squashing any scenes that you could `jumpForward` to
| `pop()`               | 后退场景并卸载当前场景
| `replace(route)`      | 用指定的路由替换当前场景
| `replaceAtIndex(route, index)` | 用给定的路由替换路由中指定的场景
| `replacePrevious(route)` | 替换前一个场景
| `resetTo(route)`      | Navigate to a new scene and reset route stack
| `immediatelyResetRouteStack(routeStack)` | Reset every scene with anarray of routes
| `popToRoute(route)`   | Pop to a particular scene, as specified by its route. All scenes after it will be unmounted
| `popToTop()`          | Pop to the first scene in the stack, unmounting every other scene

### ExNavigator 增加的方法
```js
  /**
   * Replaces the top-most route with the given route and navigates to it
   * with a pop transition
   */
  transitionToTop(route) {
    this.replaceAtIndex(route, 0);
    this.popToTop();
  },

  /**
   * Pops back `n` routes. That is, `pop()` behaves like `popBack(1)`.
   */
  popBack(n) {
    let routes = this.getCurrentRoutes();
    this.popToRoute(routes[routes.length - n - 1]);
  }
```
