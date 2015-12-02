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
2. 修改`android/app/build.gradle`

# 安装codepush命令行支持
  
  ```bash
  npm install -g code-push-cli
  ```
