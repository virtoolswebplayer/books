# CodePush安装

安装CodePush

```bash
 npm install --save react-native-code-push
```
  
## 插件安装 Android
通过下面两步将CodePush集成到你的Android工程

1. 修改`android/settings.gradle`，添加下面的代码。 `注：这个操作类似于vs的一个解决方案多个子项目`
 
```bash
  include ':app', ':react-native-code-push'
  project(':react-native-code-push').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-code-push/android/app')
```
2. 修改`android/app/build.gradle`,把 `:react-native-code-push`项目加入到编译依赖
  
```bash
  ...
  dependencies {
      ...
      compile project(':react-native-code-push')
  }
```

## 插件配置 Android
经过上面的步骤就完成了CodePush插件的安装，接下来需要配置CodePush以实现重新定位`index.android.bundle`

1. 修改`MainAcivity.java`让CodePush起作用
  
```java
  ...
// 1. 引用CodePush
import com.microsoft.codepush.react.CodePush;

// 2. 可选: 如果想通过一个对话框提示用户更新，可以将 Activity 替换为 FragmentActivity
// 注：如果替换成 FragmentActivity，编译时会提示找不到 FragentActivity, 具体原因有待解决
public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {
    ...

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        ...
        // 3. 初始化CodePush需要两个参数 develop-key 和 MainActivity的实例 this
        CodePush codePush = new CodePush("d73bf5d8-4fbd-4e55-a837-accd328a21ba", this);
        ...
        mReactInstanceManager = ReactInstanceManager.builder()
                .setApplication(getApplication())
                ...
                // 4. 删除这行 --> .setBundleAssetName("index.android.bundle")

                // 5. setJSBundleFile 是ReactNative提供的，它允许从文件系统中加载 bundle.
                // 现在我们让codePush来决定index.android.bundle的位置，首页codepush会检测
                // 是否有可更新的 bundle,如果有，codepush会自动下载到 android/app/src/main/assets/index.android.bundle
                // 如果没有，刚使用之前已下载过的 android/app/src/main/assets/index.android.bundle
                .setJSBundleFile(codePush.getBundleUrl("index.android.bundle"))

                // 6. 导出CodePush模块给JavaScript.
                .addPackage(codePush.getReactPackage())
                ...
    }
}

```
2. 修改`android/app/build.gradle` versionName `1.0`修改为`1.0.0`

```
android {
...
defaultConfig {
    ...
    versionName "1.0.0"
    ...
}
...
}
```

# codepush命令行支持
1. 全局安装 code-push-cli
  
```bash
npm install -g code-push-cli
```
2. 创建Codepush帐号

```
code-push app add appName
```
3. 添加一个应用

```
  code-push app add appName
```
4. 打包bundle

```sh
#!/usr/bin/env bash

# bundle.sh 需要给执行权限 chmod +x bundle.sh

# android
react-native bundle \
--platform android \
--entry-file index.android.js \
--bundle-output ./release/index.android.jsbundle \
--assets-dest ./release

# ios
react-native bundle \
--platform ios \
--entry-file index.ios.js \
--bundle-output ./release/main.jsbundle \
--assets-dest ./release

# 发布到codepush服务器
#
code-push release wealth  ./release 1.0.0
```
5. 发布到codepush

`特别提醒:` 最后的版本号一定要于 android工程及ios工程中设置的版本号保持一致,因为codepush不会跨软件版本号更新
```
code-push release MyApp android/app/src/main/assets/index.android.jsbundle 1.0.1
```

# 插件安装(IOS)
1. 用xcode打开你的ios工程
2. 找到`CodePush.xcodeproj`,具体路径`node_modules/react-native-code-push`,将其拖拽到`Libraries`下
3. 选中`Build Phases`
4. 将`Libraries.CodePush.xcodeproj/Products`目录下的`libCodePush.a`拖拽到`Link Binary With Libraries`
5. 展开`Link Binary With Libraries` ,点击`+`按钮,选中`libz.tbd`后点击`Add`
6. 项目配置中的`Build Setting`标签,找到`Head Search Paths`编辑它的值,新加一个值`$(SRCROOT)/../node_modules/react-native-code-push`,从下拉里选择`recursive`

# 插件配置(IOS)
1. 编辑 `AppDelegate.m`,导入`CodePush.h`

    ```
    #import "CodePush.h"
    ```
2. 找到下面的代码断

    ```
    jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
    ```
3. 替换为

    ```js
    jsCodeLocation = [CodePush bundleURL];
    ```
通过以上配置使你的apps总是加载最新版的`bundle`,第一次启动应用时,