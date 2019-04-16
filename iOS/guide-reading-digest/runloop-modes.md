# Run Loop 模式

## 什么是 Run Loop Mode

Run Loop mode 是两种东西的集合，这两种东西是

- 被监听的 Input sources 和 Timer sources 的集合
- Run Loops 监听者的集合

每次我们开启一个 Run Loop, 都需要显式或隐式地为这个 Run Loop 指定一个 mode. 而且，在 Run Loop 运转的过程中，只有注册到当前 mode 上的 sources 或监听者，才会接收到此时发生的事件。而其他 mode 下的 source 或监听者，则只有在下一次切换到对应的 mode 时，才会接收到事件

## 保证 Run Loop 可用

Run Loop mode 通过名字来进行区别，开发者也可以创建自定义的 Run Loop mode, 只要给自定义的 Run Loop mode 一个名字就好了

虽然自定义的 Run Loop mode 的名字可以是随便定义，但要支撑 Run Loop mode 有效的条件却不能马虎。要使自定义的 Run Loop mode 有效，则需要为它添加一个或多个 Input source, Timers source 或监听者

## 每个 Run Loop mode 都是用来处理特定的事件

Run Loop modes 是用来过滤不同类型的事件的

每个 Run Loop mode 都会有不同的 Input sources, Timer sources 添加到其上，而这些 sources 可以粗略地看成是代表不同类型的事件。但每个 Run Loop 进行循环时，都会有各种类型的事件发生，而当前循环的 Run Loop mode 就决定了是否处理当前接收到的事件

例如，在大多数情况下，应用的 Run Loop 就处于系统定义的 `default` mode

再例如，当一个模态窗口弹出时，应用就处于 `modal` mode. 在这个 mode 下，只有与 `modal` mode 相关的 source 的事件才会被处理，也只有相应的事件才会被发送到这个 mode 下的线程进行处理

## 系统预定义的 Run Loop modes

只列出在 iOS 中可用的 Run Loop Mode

- NSRunLoopCommonModes
- NSDefaultRunLoopMode / kCFRunLoopDefaultMode
- UITrackingRunLoopMode

### NSRunLoopCommonModes

使用这个 mode 意味着使用所有的 mode, 即当 sources S 或监听者 L 添加到这个 mode 上时，等同于为其他所有 mode 添加 S 或 L

### NSDefaultRunLoopMode

最经常使用的一个 mode

### UITrackingRunLoopMode

当用户交互（用户滑动屏幕）时，应用就会切换到这个 mode

当某些定时操作需要在用户交互的过程中也生效，则需要把该定时操作也添加到此 mode 上



