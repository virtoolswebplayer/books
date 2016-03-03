|Navigator| ExNavigator
|--------|:-------------:|
| `getCurrentRoutes()`  | returns the current list of routes
| `jumpBack()`          | Jump backward without unmounting the current scene
| `jumpForward()`       | Jump forward to the next scene in the route stack
| `jumpTo(route)`       | Transition to an existing scene without unmounting
| `push(route)`         | Navigate forward to a new scene, squashing any scenes that you could `jumpForward` to
| `pop()`               | Transition back and unmount the current scene
| `replace(route)`      | Replace the current scene with a new route
| `replaceAtIndex(route, index)` | Replace a scene as specified by an index
| `replacePrevious(route)` | Replace the previous scene
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
