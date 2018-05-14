# iOS 布局理解

在主 RunLoop 中，包含了一个 Update Cycle

当主 RunLoop 中的事件处理完毕之后，将会进入到 Update Cycle, 对界面进行需要的更新

在 Update Cycle 中，系统会处理 UI 的布局，约束，显示的更新

当我们对某个 view 进行了一次改变，也就是发起了一次改变请求后，系统就会将这个 view 标识为需要重绘，当在下一个 Update Cycle 时，系统就会对之前标记的 view, 根据相应的改变进行更新

在 iOS 系统中，界面的更新频率是 60 fps(Frames Per Second). 即每秒会传输 60 帧的画面。因此，在每次的需要对 UI 进行计算时，都需要在 1/60 秒内完成，否则，就会造成视觉上的卡顿

### Layout

#### layoutSubviews

做什么

- 用于对一个 view 及其 subview 进行重新定位以及重置大小
- 它会对当前的 view 及其 subview 一个位置及大小的值

调用时机

- 若 view 需要重新计算 frame 值时，系统将会调用此方法

注意

- 这个方法实际上是非常耗费资源的，因为它需要不断调用 view 及其 subview 的对应方法
- 不要直接调用，而是利用系统提供的机制来间接调用

#### viewDidLayoutSubviews

调用时机

- 当 `layoutSubviews` 完成后，将会触发控制器中的这个方法

注意

- 当逻辑需要依赖于布局或 view 大小的时候，将逻辑放到此方法中实现，而不是放到 `viewDidLoad` 或 `viewDidAppear`

#### 通知系统 view 的布局状态发生变化

自动通知

- 对一个 view 进行 resize
- 添加一个 subview
- 滚动 `UIScrollView`, `layoutSubviews` 会在 `UIScrollView` 及其 superview 上进行调用
- 屏幕旋转
- 更新 view 的约束

手动通知

- `setNeedsLayout`
- `layoutIfNeeded`

#### setNeedsLayout

- 触发 `layoutSubviews` 的消耗最小的方法
- 并不会立刻更新 view, 而是等待下一个 Update Cycle

#### layoutIfNeeded

调用时机

- 并不会等待下一个 Update Cycle, 而是直接调用 `layoutSubviews`
- 当调用了 `setNeedsLayout` 或设置了上面能自动通知的属性后，再调用此方法，`layoutSubviews` 将会在那些需要更新的 view 上调用
- 当调用了此方法，但之前并没有 view 的视图发生了变化，那么，`layoutSubviews` 不会被调用
- 当在同一个 RunLoop 调用了两次此方法，并且在两次调用之间没有发生 view 的改变，那么，第二次的调用不会出发 `layoutSubviews`

### Display

#### drawRect

- 在 view 的大小和位置发生变化时调用，但不会调用 subview 的相同方法
- 不要直接调用

#### setNeedsDisplay

是什么

- 与 `setNeedsLayout` 同性质的方法，用来设置标识
- 通常情况下，直接改变 view 的位置大小，系统会自动将 view 标识为 `dirty`, 即会在下一个 Update Cycle 进行重绘。然而，有时候需要间接改变 view 的位置大小，那么此时，就需要调用 `setNeedsLayout`

#### displayIfNeeded

- 并没有这种方法
- 并没有类似 `layoutIfNeeded` 这种，立即执行的方法
- display 都是等到下一个 Update Cycle 来重绘内容

### Contraints

自动布局中，布局与重绘有三个步骤

1. 更新约束
    - 系统计算及设置必须的约束
2. 布局
    - 系统布局系统计算 views 及其 subviews 的 frame 值
3. 显示
    - 在这一步，将会完成一个 Update Cycle
    - 重绘 views 的内容，如有需要，则调用 `drawRect` 方法

#### updateContraints

- 用于动态改变 view 的约束，因为 `updateContraints` 会经常被调用
- 事实上，`updateContraints` 中应该只实现需要动态变化的约束
- 对于静态约束，即不变的约束，应该在 Interface Builder, view 的初始化方法，或 `viewDidLoad` 中进行设置
- 当启用或禁用约束，改变约束优先级或常量，移除 view, 这些操作都会设置一个内置的标识，并在下一个 Update Cycle 中触发 `updateContraints`

#### setNeedsUpdateConstraints

- 保证 `setNeedsUpdateConstraints` 在下一个 Update Cycle 调用 `updateConstraints`
- 即类似 `setNeedsDisplay`, `setNeedsLayout`

#### updateConstraintsIfNeeded

- 将会检查内部的标识，判断是否需要调用 `updateConstraints`
- 内部标识可以通过 `setNeedsUpdateConstraints` 或 `invalidateInstrinsicContentSize` 来设置
- 立即调用 `updateConstraints`, 而不会等到 RunLoop 的执行顺序

#### invalidateIntrinsicContentSize

- 一些使用自动布局的 view 会拥有一个 `intrinsicContentSize` 属性，这是根据 view 的内容而获得的大小
- `intrinsicContentSize` 可以被重写

## References

- [Demystifying iOS Layout](http://tech.gc.com/demystifying-ios-layout/)

