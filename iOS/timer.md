# NSTimer

## 线程相关

- Timer 的工作必定与 RunLoop 相关
- RunLoop 维护着对 timer 的强引用，因此开发者可以不需要维护着对 timer 的强引用

## 工作效能

### 实时性

- Timer 并不是实时的，即不都是准时的
- 当 **Timer 在 RunLoop 中一个耗时过长的事件** 或 **当前 RunLoop 不是 Timer 工作所在的 RunLoop**, Timer 是不会被触发，在以后也不会补上错过的触发，只会在时间线上不断前行
- 所以，Timer 的触发很有可能会延迟很大

### 重复与不重复

- 在创建时指定 Timer 是否可以重复触发
- 对于不重复的 Timer, 在触发后会自动作废
- 对于重复的 Timer
    - 会不断在相同的时间间隔和同一个 RunLoop 中触发
    - 对于错过的触发，在后续的触发点中，都只会调用一次响应事件
    - 当不再需要 Timer 时，必须调用 `invalidate` 方法
        - 这会将 Timer 从 RunLoop 中移除，并移除 RunLoop 对其的强引用
        - 安排 Timer 到 RunLoop 与将 Timer 从 RunLoop 中移除的时候，必须是要在同一个线程中

### 延时容忍

在 iOS 7 后，可以为 Timer 添加一个 `tolerence` 属性

- 系统可以根据此属性来优化和提升电池使用时长和响应速度
- 触发点位于一个区间 [scheduled-fire-date, scheduled-fire-date + tolerance]
- 对于重复的 Timer, 每一个触发点的计算起点都从最开始的触发点开始计算，并忽略中间的每一次触发点

    >  For repeating timers, the next fire date is calculated from the original fire date regardless of tolerance applied at individual fire times, to avoid drift

- 一般来说，`tolerance` 一般设定为事件间隔的 10%

## 使用

在 RunLoop 中安排 Timer, 开发者可以一次只将 Timer 注册到一个 RunLoop, 也可以一次注册到多个 RunLoop 中

3 中方法创建 Timer

- `scheduledTimerWithTimeInterval:invocation:repeats:` 或 `scheduledTimerWithTimeInterval:target:selector:userInfo:repeats:`
    - 注册到在当前的 RunLoop 的 default mode 中
    - 区别在于如何设定相应方法
- `timerWithTimeInterval:invocation:repeats:` 或 `timerWithTimeInterval:target:selector:userInfo:repeats:`
    - 创建 Timer 后，并没有注册到 RunLoop, 需要开发者自己指定 RunLoop
    - 指定 RunLoop 调用方法：`addTimer:forMode:`
- `initWithFireDate:interval:target:selector:userInfo:repeats:`
    - 创建 Timer 后，并没有注册到 RunLoop, 需要开发者自己指定 RunLoop
    - 指定 RunLoop 调用方法：`addTimer:forMode:`

