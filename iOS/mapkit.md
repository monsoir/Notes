# MapKit

Location and Maps Programming Guide 文档阅读过程中的 demo, 只对可能常用的一些属性和方法进行了尝试

## 获取用户当前位置

有两种服务可以获取到用户的当前位置

- *standard location service* 可配置，普遍使用的方案，可配置精确度
- *significant-change-location* 只会在位置有重大变化的时候才会更新，重大变化，如 500m 

---

- 获取位置信息是一个电量敏感的操作
- 通常做法是，一开始获取到用户的位置信息，随后间隔性地获取位置信息

---

举例如何选择获取位置信息服务？

- App 必须持续监听位置，甚至是在后台，使用 *standard location service*, 并指定 `UIBackgroundModes` 键值来后台运行 App 和获取位置信息更新信息
    - 同时，最好确保 `location manager` 的 `pausesLocationUpdatesAutomatically` 属性为 `YES`, 节省电量
- 如果 GPS 级的精确度并不是很重要，那就不需要持续追踪了。这是可以使用 `significant-change location`, 但使用这项服务需要注意，因为这会持续监听位置，直到人为停止

### 判断位置信息服务是否可用

位置信息服务不可用的情况

- 用户在系统菜单，或 App 菜单中禁用了
- 用户在某个 App 中拒绝了赋予权限
- 处在飞行模式，电量不足

获知地理位置信息是否可用

```objc
[CLLocationManager locationServicesEnabled];
```

### 开启 *standard location service*

配置项

- 位置信息的精确度
- 报告新位置信息前经历的距离

---

- 适用于导航类 App 、需要高精度位置信息、特定时间间隔更新位置信息的 App
- 由于这功能一般会长时间使用，所以比较费电

#### 使用

- 创建 `CLLocationManager` 实例，配置 `desiredAccuracy` 和 `distanceFilter` 属性
- 为实例分配代理对象
- 调用 `startUpdatingLocation()` 方法

---

- 当位置信息可用时，`location manager` 将会通知代理对象
- 停止获取位置信息，调用 `stopUpdatingLocation()` 方法

### 开启 *significant-change-location*

使用 *significant-change-location*, 需要获取 `kCLAuthorizationStatusAuthorizedAlways` 权限

#### 使用

- 创建 `CLLocationManager` 实例
- 为实例分配代理对象
- 调用 `startMonitoringSignificantLocationChanges()` 方法

---

- 当位置更新信息可用时，会传递到代理方法
- 同时，也可以手动从 `CLLocationManager` 实例中获取位置信息，但这只会获取最近的数据，即，如果位置变化不是很大，那位置数据可能会一样

#### 注意点

- 开启了 *significant-change-location* 服务，若随后 App 被挂起或终止，服务会在新的位置信息到达时，自动唤醒 App
    - 自动唤醒 App 后，将会后台运行，并有 10 秒左右的时间来启动位置服务和处理位置信息
- 若 App 后台需要更多的时间来处理数据，可以申请后台执行时间
    - `UIApplication` 的 `beginBackgroundTaskWithName:expirationHandler:` 方法
- 当用户禁用了 Background App Refresh 功能后，*significant-change-location* 服务并不会自动重启 App, 同时，App 也不会接收到 significant-change 或 region monitoring 事件，即时 App 在前台运行
- 当使用 *significant-change-location* 服务时，当 App 从终止状态唤醒时，位置服务并没有自动开启
- 当 App 由于位置信息更新而被唤醒时，`launchOptions` 将会传递到 `application:willFinishLaunchingWithOptions:` 或 `application:didFinishLaunchingWithOptions: ` 方法，位置信息位于 `UIApplicationLaunchOptionsLocationKey` 键值中

## 获取方向信息

Core Location 支持两种方式来获取方向信息

- 拥有磁感应器的设备可以获取设备的指向，这叫做 *heading*
- 拥有 GPS 硬件的设备可以获取设备正向哪个方向移动，这叫做 *course*

> *heading* 指的是设备真正的指向，即 真•北 或者 磁场的北
> *course* 指的是设备的运动方向，并不考虑设备的地理指向

### 获取 *heading* 事件

*heading* 值可以是 **地磁场的北** 或者 **地图意义上的北（真•北）**

> 设备附近的磁场可能会受到能散发的其他设备影响，如音响，但 Core Location 能够过滤掉干扰

#### 使用

1. 创建 `CLLocationManager` 实例
2. 判断设备是否支持 *heading* 事件，`headingAvailable` 类方法
3. 为 `CLLocationManager` 实例分配委托对象
4. 若要获取 真•北，则开启位置服务
5. 调用 `startUpdatingHeading` 方法，开始传递 *heading* 事件

> *course* 事件包含的是设备的 **运动方向** 与 **运动速度**，从 `CLLocation` 实例中可以获取

几个蜜汁属性

- `CLHeading.trueHeading`
- `CLHeading.magneticHeading`
    - 分别是获取对于 真•北 和 磁场北 的偏移
    - `0` 表示设备的指向与指向北的直线重合
    - 若值小于 `0`, 表示无法判断
- `CLHeading.headingOrientation`
    - 指的是利用设备的那个方向用来指向，默认是设备 protrait 是的顶部作为指向
- `CLHeading.headingAccuracy`
    - 获取到的指向与磁场北的偏差，值越小，结果越准确
    - 负值表示获取指向失败，通常在设备未校准或有干扰磁场的情况下发生
    - 值的大小可能也影响更新的频率

## 配置文件

- 如果 App 需要获取位置信息才能正常工作的话，在 `Info.plist` 文件中，写入 `UIRequiredDeviceCapabilities` 字段
- 该字段将会用于：若 `Info.plist` 中包含了该字段，App Store 将会阻止不能正常使用该功能的设备下载该 App
- `UIRequiredDeviceCapabilities` 是一个字符串数组，可以填写一下预定义的值
    - `location-services` App 需要位置信息服务才能正常使用
    - `gps` App 需要 GPS 提供的精确定位才能正常使用

> 若 App 在没有位置信息服务也能正常使用，就千万不要添加 `UIRequiredDeviceCapabilities` 对应的值了

---

使用地理位置信息涉及隐私，涉及的描述字段有

- `NSLocationWhenInUseUsageDescription`: String
- `NSLocationAlwaysUsageDescription`: String

使用设备方向指向信息，涉及的描述字段有

- `UIRequiredDeviceCapabilities`: Array
    - 预定义值
        - `magnetometer`
        - `gps`
    - App Store 将会使用此字段来判断设备硬件是否满足条件

## 地图显示

MapKit 中的地图显示方式

- standard street-level 街景
- satellite imagery 卫星图片
- 上面两种的结合体

在 MapKit 上的地图，可以通过代码

- zoom 放大
- pan 拖动
- pitch 推动
- 3D 显示建筑
- 用自定义的信息来标记地图

MapKit 自身提供放大与拖动的触摸事件

### 地图坐标系统

- MapKit 使用了 Mercator 地图投影
- Mercator 投影是一个某种圆柱形的地图投影
- 在这个圆柱形的地图投影中，球体的坐标被影射到圆柱面上，然后这个圆柱面又通过转换生成一个平面图
- 在这样的影射后，原本的汇集在极点的经度线变成了平行
- Mercator 投影的好处是，地图内容被转换为易于导航的方式显示

对地图的使用方式不同，用于描述的数据点也不同

- map coordinate
- map point
- point
 
#### map coordinate

- 球形地图上的经纬度
- 使用 `CLLocationCoordinate2D` 结构体来定义 map coordinate
- 使用 `MKCoordinateSpan` 和 `MKCoordinateRegion` 结构体来定义区域信息

#### map point

- Mercator 投影上的 x, y 值
- 用于与地图相关的计算，这种模型简化了数学上的计算
- 当需要对自定义的地图图层指定其形状和位置时，使用这个模型
- 使用 `MKMapPoint` 结构体定义一个 map point
- 使用 `MKMapSize` 和 `MKMapRect` 结构体定义区域

#### point

- 与 view 对象的坐标系统相关的图形单位
- map point 和 map coordinate 都在显示在 view 上前，转换为 point
- 使用 `CGPoint` 结构体来定义一个 point
- 使用 `CGSize` 和 `CGRect` 结构体来定义一个区域

> 大多数情况下，使用的 point 类型都会由 MapKit 的接口决定，一般都是 map coordinate
> 当我们存储数据时，map coordinate 会更准确，可以移植性更强

### 坐标系统间的坐标转换

from | to | rouines
--- | --- | ---
Map coordinates | Points | convertCoordinate:toPointToView: (MKMapView) <br /> convertRegion:toRectToView: (MKMapView)
Map coordinates | Map points | MKMapPointForCoordinate
Map points | Map coordinates | MKCoordinateForMapPoint <br /> MKCoordinateRegionForMapRect
Map points | Points | pointForMapPoint: (MKOverlayRenderer) <br /> rectForMapRect: (MKOverlayRenderer)
Points | Map coordinates | convertPoint:toCoordinateFromView: (MKMapView) <br /> convertRect:toRegionFromView: (MKMapView)
Points | Map points | mapPointForPoint: (MKOverlayRenderer) <br /> mapRectForRect: (MKOverlayRenderer)

### 添加 Map view 到 UI 界面

- `MKMapView` 类本身提供了 *显示地图数据* ，*处理交互手势* ，*管理自定义视图* 的功能
- 不要继承 `MKMapView`, 而是将其继承在视图层次结构中，构成 as-is 的关系

> Never subclass `MKMapView`, 需要持保留态度，`UIAlertController` 在文档中也曾经说过不要 subclass, 但实践发现，subclass `AlertController` 可以创建出一个更加灵活的选择菜单

---

- `MKMapView` 可以像对待其他 `UIView` 一样对待它
- 添加到 `MKMapView` 的 view, 会保持着定义它们的 `frame` 值，并且不会跟随着 `MKMapView` 一起滚动，就是固定着。如果需要让 subview 跟随 `MKMapView` 一起滚动，就必须使用 annotation 或者 overlay
- 避免对 `MKMapView` 的视图层级结构进行修改

---

- 新的 `MKMapView` 对象只可以接收用户交互动作和显示地图数据
- 默认，`MKMapView` 对象使用的是 3D 角度显示地图，pitch 手势可用，还有一个现时的方向指示
- 通过创建 `MKMapCamera` 对象来定制 pitch 手势和旋转
- 通过改变 `mapType` 来配置地图的显示模式（3D, 卫星地图，3D + 卫星地图）
- 限定用户交互手势的属性 `rotateEnabled`, `pitchEnabled`, `zoomEnabled`, `scrollEnabled`
- 相应手势，则实现对应手势的委托方法

---

### 配置地图

#### 设定地图的可视部分

`region: MKCoordinateRegion`

- 控制地图当前的可视部分
- 地图刚创建的时候，可视部分为整个世界，通过对 `region` 重新赋值来改变可视部分

---

`MKCoordinateRegion` 分析

```objc
typedef struct {
   CLLocationCoordinate2D center; // 区域中心点
   MKCoordinateSpan span;
} MKCoordinateRegion;
```

着重分析 `span`

- 类似与长方形的宽高，但使用的是 degree, minute, second
- 一个纬度的距离相当于 111km, 但一个经度的距离会随着维度的变化而变化，比如
    - 在赤道上，一个经度的距离相当于 111km
    - 在极点上，一个经度的距离是 0km
- 若想通过单位米来指定 `span` 值，可以通过 `MKCoordinateRegionMakeWithDistance` 来生成 `MKCoordinateRegion` 数据

---

- 对 `region` 属性进行赋值，或使用 `setRegion:animated:` 方法设置 `region` 值时，最后的结果，`region` 存储的值可能与之前的设定值不同
- 设定 `region` 值时，可能会隐含设定了放大的倍数
- `MKMapView` 并不能随意放大或缩小地图的显示范围，只支持特定的倍数，因此，`region` 可能会对新值进行调整
- 可以调用 `MKMapview` 的 `regionThatFits:` 方法来获取新的 `region` 值调整之后的值

#### 显示 3D 地图

3D 地图是一个鸟瞰形式的 2D 地图

`MKMapCamera` 对象使用以下属性来定义 3D 地图的样貌

- Altitude: Camera 的高度，单位是 meter
- Pitch: Camera 的倾斜角度，以地面为起点
- Heading: Camera 面向的方向
- Center: 地图上，显示在屏幕或窗口中心的点

> 具体的操作看对应的代码

同时，`MKMapCamera` 的信息支持持久化存储

```objc
MKMapCamera *camera = [map camera]; // Get the map's current camera.
[NSKeyedArchiver archiveRootObject:camera toFile:stateFile]; // Archive the camera.
…
MKMapCamera *camera = [NSKeyedUnarchiver unarchiveObjectWithFile:stateFile]; // Unarchive the camera.
[map setCamera:camera]; // Restore the map.
```

---

放大和移动地图

- 若只想移动地图内容，保持地图的缩放倍数，则改变 `centerCoordinate` 属性，可以调用
    - `setCenterCoordinate:animated:`
    - `setCamera:animated:`
- 若想改变地图的缩放倍数，则改变 `region` 属性，可以调用
    - `setRegion:animated:`

> 改变 `region` 值的时候，会触发 `MKMapView` 重新计算缩放倍数并设定显示的内容，所以，如果只想移动地图，则只改变 `coordinate` 的值

#### 显示设备当前位置

- MapKit 的 `MKMapView` 内置支持显示用户当前的位置，设置 `showUserLocation` 属性为 `true`, 默认是 `false`
    - 这会使 `MKMapView` 使用 Core Location 去寻找设备的位置，并添加 `MKUserLocation` 类型的 annotation 到地图
- 可以通过实现 `mapView:viewForAnnotation:` 委托方法来返回一个 view 用作 annotation

### 创建地图的截图

- 有些时候，显示的地图并不需要有过于丰富的手势交互，此时，可以创建一个地图截图来显示
- 使用 `MKMapSnapshotter` 的对象来异步创建一个静态的地图图片，快照的结果包含了一个 `UIImageView`

> 对于地图的截图，推荐使用 `MKMapSnapshotter` 的对象来完成

#### 创建地图截图步骤

1. 保证网络畅通，App 在前台运行
2. 创建并配置一个 `MKMapSnapshotOptions` 对象，它会配置地图的样貌及输出的大小，iOS 的 App 同时可以配置缩放的倍数
3. 创建一个 `MKMapSnapshotter` 对象，并使用上面的 option 对象进行初始化
4. 调用 `startWithCompletionHandler:` 来开始异步的截图任务
5. 当截图任务完成后，从回调中获取截图，并添加上需要的 overlay 或 annotation

---

打印地图

直接看文档了

### 使用委托来响应用户交互

`MKMapView` 的 `MKMapViewDelegate` 提供的响应事件

- 地图的可视区域发生改变
- 地图上从网络加载每一块地图的内容
- 用户地理位置发生改变
- annotation 和 overlay 发生改变


## References

- [Maps for Developers](https://developer.apple.com/maps/)
- [About Location Services and Maps](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/LocationAwarenessPG/Introduction/Introduction.html)
- [Getting the User’s Location](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/LocationAwarenessPG/CoreLocation/CoreLocation.html#//apple_ref/doc/uid/TP40009497-CH2-SW1)
- [Getting the Heading and Course of a Device](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/LocationAwarenessPG/GettingHeadings/GettingHeadings.html#//apple_ref/doc/uid/TP40009497-CH5-SW1)
- [Displaying Maps](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/LocationAwarenessPG/MapKit/MapKit.html#//apple_ref/doc/uid/TP40009497-CH3-SW1)

