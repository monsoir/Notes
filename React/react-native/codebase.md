# 代码工具库

## 下载器

简单的下载器，一个实例只能下载一个文件

- 传入的 iOS 与 Android 的文件路径需要区别对待
- 在页面或者持有下载器的实例销毁时，需要手动调用 `stopDownloading` 来停止下载任务

```js
import RNFS from 'react-native-fs';

export default class Downloader {

  constructor() {
    this.downloadID = -1; // 标识有没有正在下载的任务
    this.downloadInfo = {
      destURL: '',
    };
  }

  /**
   * 下载
   */
  download = (from, to, beginCallback, progressCallback, rejectDownloadingCallback, successCallback, failureCallback) => {
    // 在这个 downloader 中已经有任务在下载
    if (this.downloadID !== -1) {
      rejectDownloadingCallback();
    }

    const ret = RNFS.downloadFile({
      fromUrl: from,
      toFile: encodeURI(to),
      begin: beginCallback,
      progress: progressCallback,
      background: false,
      progressDivider: 1,
    });
    this.downloadID = ret.jobId;

    ret.promise
    .then((res) => {
      console.log(res);
      this.downloadID = -1;
      successCallback(to);
    })
    .catch((res) => {
      console.log(res);
      this.downloadID = -1;
      failureCallback(to);
    });
  };

  /**
   * 停止下载
   */
  stopDownloading = () => {
    if (this.downloadID !== -1) {
      RNFS.stopDownload(this.downloadID);
      RNFS.unlink(this.downloadInfo.destURL);
      this.downloadID = -1;
    }
  }
}

```

## 模态显示中植入 Navigation

使用一个模态的 Navigaion 管理模态视图中的跳转，使得我们可以直接在模态 Navigation 中的任意一个页面，直接退出模态显示 Navigation

在任意一个页面直接退出模态 Navigation, 调用

```js
this.props.navigation.dismiss();
```

1. 定义好容器

    ```js
    DismissableStackNavigator = (routes, options) => {
      const StackNav = StackNavigator(routes, options);
    
      return class DismissableStackNav extends PureComponent {
        static router = StackNav.router;
    
        render() {
          const { state, goBack } = this.props.navigation;
          const nav = {
            ...this.props.navigation,
            dismiss: () => goBack(state.key),
          };
          return (
            <StackNav
              navigation={nav}
            />
          );
        }
      };
    };
    ```

1. 定义一个返回按钮，用来返回上一级视图或者退出模态 Navigation, 这样一来，模态 Navigation 中的视图就可以自动销毁，不需要我们管理了

    ```js
    const ModalBackButton = (navigation) => {
      return (
        <TouchableOpacity
          onPress={() => {
            navigation.goBack(null);
          }}
          style={{ width: 50 }}
        >
          <Image source={require('../images/ico-hd-back.png')} style={{ width: 43, height: 43 }} />
        </TouchableOpacity>
      );
    };
    ```

1. 将需要在模态 Navigation 中显示的视图，定义在容器中

    ```js
    const ModelPresentNavigator = DismissableStackNavigator(
      {
        FirstModelPage: { screen: FirstModelPage },
        SecondModelPage: { screen: SecondModelPage },
      },
    
      {
        initialRouteName: 'FirstModelPage',
        navigationOptions: ({ navigation }) => ({
          headerStyle: NavigatorHeaderStyle.header,
          headerTitleStyle: NavigatorHeaderStyle.headerTitle,
          headerTintColor: Color.naviTitleColor,
          headerLeft: ModalBackButton(navigation),
        }),
      },
    );
    ```

1. 在总页面控制器中添加模态 Navigation

    ```js
    export const AppNavigator = StackNavigator(
      {
        MainNavigator: { screen: MainNavigator },
        ModelPresentNavigator: { screen: ModelPresentNavigator },
      },
    
      {
        headerMode: 'none',
        initialRouteName: 'MainNavigator',
        mode: 'modal',
      },
    );
    ```

