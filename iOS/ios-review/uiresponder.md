# UIResponder

`UIResponder` 对象是构成一个可以进行事件处理的应用背后的支撑。很多关键的对象都是 `UIResponder` 的实例，如

    - `UIApplication`
    - `UIViewController`
    - 所有 `UIView`, 包括 `UIWindow`

很多熟悉的触摸事件的方法，都是由 `UIResponder` 提供的

- `touchesBegan:withEvent:`
- `touchesMoved:withEvent:`
- `touchesEnded:withEvent:`
- `touchesCancelled:withEvent:`

对于事件的处理，`UIResponder` 也管理着未被处理的事件，负责将它们派发到 App 的其他部分。UIKit 自身维护着响应者链，使用预定义的规则来决定下一个处理事件的对象，例如，一个 `view` 对象可以将事件传送到它的 `superview`, 视图结构的 `root view` 可以将事件传送到它的 `view controller`

> 继承关系
> 
> UIResponder <- UIView <- UIControl

