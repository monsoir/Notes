# UIControl

`UIControl` 使用 Target-Action 机制来将用户交互反馈到 App

制作自定义控件时，不会直接创建 `UIControl` 的实例，而是创建 `UIControl` 的子类来扩展自定义控件的行为，`UIControl` 使用的是继承的思想

`UIControl` 使用状态 state 来决定它的外观和支持用户交互的行为。控件可以有多个状态，全在 [UIControlState](https://developer.apple.com/documentation/uikit/uicontrolstate?language=objc) 中

`UIControl` 响应的事件全在 [UIControlEvents](https://developer.apple.com/documentation/uikit/uicontrolevents)

当一个 `UIControl` 的一个事件发生后，`UIControl` 将会调用关联的 action methods. Action method 通过当前的 `UIApplication` 进行派发，`UIApplication` 将会找到对应的对象去处理消息，若有需要，将会顺着响应者链寻找处理消息的对象

#### 如何继承

继承 `UIControl` 有两种方法的实现

- 重写 `sendAction:to:forEvent:` 方法，进行监听或修改 `UIControl` 关联 target 的 action methods 的派发。可以使用此方法来对特定对象，selector, 事件的派发行为进行修改
- 重写一下方法来跟踪 `UIControl` 的触摸事件。可以使用跟踪信息来执行额外的任务，应该总是使用以下方法来跟踪触摸事件，而不是使用 `UIResponder` 中定义的方法
    - `beginTrackingWithTouch:withEvent:`
    - `continueTrackingWithTouch:withEvent:`
    - `endTrackingWithTouch:withEvent:`
    - `cancelTrackingWithEvent:`

> 继承关系
> 
> UIReponser <- UIView <- UIControl


