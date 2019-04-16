# Run Loop 的周期性事件

Run Loop 的循环事件如下，sequence matters

1. 通知 observers, 应用进入了某个 Run Loop
2. 通知 observers, 会否有 timers 要触发
3. 通知 observers, 会否有非 port-based 的 input source 要触发
4. 触发所有准备好的非 port-based 的 input source 执行
5. 如果有 port-based 的 input source 准备好执行，那就立刻执行吧，并转到第 9 步
6. 通知 observers, 线程要休眠了
7. 把线程带去休眠，直到有下面某个事件发生
    - 某个 port-based 的 input source 事件来了
    - 某个 timer 触发了
    - 当前这个 Run Loop 的超时时间到了
    - 当前这个 Run Loop 被叫醒了
8. 通知 observers, 线程都被叫起来了
9. 处理挂起的事件
    - 如果自定义的 timer 触发了，就执行 timer 的处理方法，并重新回到了第 2 步
    - 如果某个 input source 触发了，传递该事件
    - 如果当前这个 Run Loop 被显式叫醒了，但还没有超时，重启循环，回到第 2 步
10. 通知 observers, 当前这个 Run Loop 要退出了

